---
title: H5 录音
date: 2019-10-30 16:50:53
tags: ['h5', 'javascript', 'audio', 'media', 'wechat']
---

> 每日一言：我离开的透明

## 需求背景

公众号中页面支持录音，时长要求 30min 左右

## 方案调研

#### 微信 jssdk

```
wx.startRecord()
wx.stopRecord()
```

- 优点：兼容性高，用户体验好
- 缺点：录音时长限制1分钟，大小限制60m

#### 第三方库

- 几乎没有，偶尔发现的录音库不支持移动端

#### 浏览器原生api getUserMedia 接口

- 优点：可以调起手机原生麦克风进行录音
- 缺点：兼容性差：
    - ios手机：
        - 在http环境下，在微信端打开，调起失败
        - 在https环境下，在微信端打开，调起失败
        - 在http环境下，在safari中打开，调起失败
        - 在https环境下，在safari中打开，调起麦克风成功
    - 安卓手机： 
        - 在http环境下，在微信端打开，调起失败
        - 在https环境下，在微信端打开，调起麦克风成功

#### 结论

通过 getUserMedia 调起麦克风，ios wechat 用户提示 safari 打开，用户信息通过 url 拼接传递，录音完毕提示用户回到微信

经调研，[recorder](https://github.com/xiangyuecn/Recorder) 是一个开源的对 getUserMedia 进行封装并且支持录音进行编码转化mp3 wav 格式，所以直接使用此包

## 开发

引入脚本 `recorder.mp3.min.js`

由于录音 30min 的文件会比较大，所以采用实录实传的方式（前端dom基于vue框架）

#### index.vue

```
<template lang="pug">
    .wrapper
        h1 音频测试
        el-button(@click='handleRecordStart') 开始录音
        el-button.y-mt-sm(@click='handleRecordStop') 停止录音
</template>
<script>
    // 工具
    import _ from 'lodash';
    import { realtimeRecorder } from './realtime-send';


    const record = Recorder({
        type: "unknown",
        onProcess: (buffers, powerLevel, bufferDuration, bufferSampleRate) => {
            realtimeRecorder.RealTimeSendTry(record, false);
            //推入实时处理，因为是unknown格式，这里简化函数调用，没有用到buffers和bufferSampleRate，因为这些数据和record.buffers是完全相同的。
		}
	});
	
    export default {
        data () {
            return {
                timer: null,
                recordIds: [],
            };
        },
        methods: {
            // 开始
            handleRecordStart () {
                if (record) {
                    record.close();
                };

                var t = setTimeout(() => {
                    console.log("无法录音：权限请求被忽略（超时假装手动点击了确认对话框）",1);
                }, 8000);

                record.open(() => { //打开麦克风授权获得相关资源
                    clearTimeout(t);
                    record.start(); //开始录音
                    realtimeRecorder.RealTimeSendTryReset(); //重置
                }, (msg, isUserNotAllow) => {
                    clearTimeout(t);
                    console.log((isUserNotAllow ? " UserNotAllow，" : "") + "无法录音:" + msg, 1);
                });
                
            },

            // 停止
            handleRecordStop () {
                record.close();
                //最后一次发送
                realtimeRecorder.RealTimeSendTry(record, true);
            },
        },
    };
</script>

```

#### realtime-send.js

```
// tools
import Vue from 'vue';

// 实时传输的时间间隔，mp3 格式不要小于 500ms 比较好
const SendInterval = 3000;

const RealTimeSendTryReset = () => {
	realTimeSendTryTime = 0;
};

let realTimeSendTryTime = 0;
let realTimeSendTryNumber;
let transferUploadNumberMax;
let realTimeSendTryChunk;


//=====实时处理核心函数==========
const RealTimeSendTry = (rec, isClose) => {
    const t1 = Date.now();
    const endT = 0;
    const recImpl = Recorder.prototype;

	if ( realTimeSendTryTime === 0) {
		realTimeSendTryTime = t1;
		realTimeSendTryNumber = 0;
		transferUploadNumberMax = 0;
		realTimeSendTryChunk = null;
    };

	if(!isClose && t1 - realTimeSendTryTime < SendInterval) {
		return;
    };

	realTimeSendTryTime = t1;
	const number = ++realTimeSendTryNumber;
	
	const newSampleRate = 16000;
	//借用SampleData函数进行数据的连续处理，采样率转换是顺带的
	const chunk=Recorder.SampleData(rec.buffers,rec.srcSampleRate,newSampleRate,realTimeSendTryChunk);
	
	//清理已处理完的缓冲数据，释放内存以支持长时间录音，最后完成录音时不能调用stop，因为数据已经被清掉了
	for(let i = realTimeSendTryChunk ? realTimeSendTryChunk.index : 0; i < chunk.index; i++){
		rec.buffers[i]=null;
    };

	realTimeSendTryChunk = chunk;
	
	//没有新数据，不能进行mock转码
	if(chunk.data.length === 0) {
		TransferUpload(number, null, 0, null, isClose);
		return;
	};
	
	//通过mock方法实时转码成mp3
	const recMock = Recorder({
        type: 'mp3',
        sampleRate: newSampleRate, //采样率
		bitRate: 16 //比特率
    });

	recMock.mock(chunk.data, chunk.sampleRate);
	recMock.stop((blob,duration) => {
		//转码好就推入传输
		TransferUpload(number,blob,duration,recMock,isClose);
	}, msg => {
		//转码错误？没想到什么时候会产生错误！
        console.log("不应该出现的错误:" + msg);
	});
};

//=====数据传输函数==========
const TransferUpload = (number, blobOrNull, duration, blobRec, isClose) => {
    transferUploadNumberMax = Math.max(transferUploadNumberMax, number);

	if (blobOrNull) {
		const blob = blobOrNull;
		
		//*********Read As Base64***************
		const reader=new FileReader();
		reader.onloadend = () => {
			const base64=(/.+;\s*base64\s*,\s*(.+)$/i.exec(reader.result)||[])[1];
			
			//可以实现
			//WebSocket send(base64) ...
			//WebRTC send(base64) ...
			//XMLHttpRequest send(base64) ...
			
		};
        reader.readAsDataURL(blob);
        
		
		//*********Blob***************
		//可以实现
		//WebSocket send(blob) ...
		//WebRTC send(blob) ...
        //XMLHttpRequest send(blob) ...
        handleAudioUpload(blob);
	};
	
	if (isClose) {
        console.log("No." + ("000"+number).substr(-3) + ": 已停止传输" );
	};
};

const handleAudioUpload  = blob => {
    // create audio object
    const formData = new FormData();
    formData.append('project', 'pangu');
    formData.append('file', blob, 'recorder.mp3');

    Vue.http.post('your url', formData).then(
        // do something
    }, error => {
        // do something
    });
}

export const realtimeRecorder = {
    RealTimeSendTry: RealTimeSendTry,
    RealTimeSendTryReset: RealTimeSendTryReset,
}

```

