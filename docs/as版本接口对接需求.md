# xplayer as版本需求对接文档

## 一、关于xplayer

xplayer应用场景定位于公司平台所有适用于视频业务的项目需求，兼容ie6+浏览器。

播放器会优先启用h5标准。如果不被支持则启用Flash播放器。

- 视频弹幕支持ie9+使用canvas，<ie9使用flash
- 视频分享
- 多清晰度切换
- 按键控制


## 二、Flash核心需要提供的接口

接口遵循`GDC Flash插入标准`使用`FlashVars`传入调用方法的接口名称。

### 2.1 获取视频时长

```bash
getDuration             #返回number
```


### 2.2 获取当前播放时间点

```bash
getCurrentTime          #返回number
```


### 2.3 获取已下载数据可播放时间长

```bash
getCacheTime            #返回number，用于处理数据下载进度条和视频的loading状态判断
```


### 2.4 设置当前播放时间点

```bash
setCurrentTime          #传入number
```


### 2.5 获取当前音量

```bash
getCurrentVolume        #返回number，（0-1）允许小数
```


### 2.6 设置当前音量

```bash
setCurrentVolume        #传入number，（0-1）允许小数
```


### 2.7 设置视频源

```bash
setVideoSrc             #传入string，即视频文件地址（用于切换清晰度，切换到下一个视频等...）
```

### 2.8 返回视频当前状态
```bash
getStatus               #返回string，包含以下状态
                        # - loading 视频正在加载
                        # - ready 视频已经加载（能获取到元数据了，能播放的状态）
                        # - play 播放中
                        # - pause 暂停中
                        # - end 播放到最后时间点
```

### 2.9 播放
```bash
play                    #从当前设置的时间点开始播放
```

### 2.10 暂停
```bash
pause                   #暂停当前视频播放
```

### 2.11 事件onProgress

```bash
onProgress              #返回一个回调参数，视频数据正在下载中就会不断去触发
```

#### 示例：
```javascript
onProgress = function(bufferedTime){
    console.log(bufferedTime);   //已缓冲文件的可播放时间
};
```

### 2.12 事件onTimeUpdate

```bash
onTimeUpdate            #返回一个回调参数，视频播放时间有变化就会不断去触发
```

#### 示例：
```javascript
onTimeUpdate = function(currentTime){
    console.log(currentTime);   //当前播放时间
};
```

### 2.13 事件onError

```bash
onError                 #返回一个回调参数对象，当出现错误或异常时触发
```

#### 示例：
```javascript
onError = function(obj){
    //obj.code  错误代码，404）文件不存在  605）网络错误
    //obj.msg   错误提示文字
    console.log(currentTime);   //当前播放时间
};
```

### 2.14 发送弹幕
```bash
danmakuSend             #传入一个对象（示例：见下），往播放器发送一条弹幕
                        # text [必选] 弹幕文字
                        # style [可选] 文字样式，如果有则会覆盖弹幕默认的样式
```

#### 示例：

```javascript
danmakuSend({
    text:'弹幕内容',
    style:{
        fontSize:24,
        fontFamily:'Microsoft YaHei',
        color:'#fff000',
        borderWidth:1,
        borderColor:'#337ab7'
    }
});
```

#### 弹幕数据说明

|参数名称|参数类型|参数说明|备注|
|:--|:--|:--|:--|
|user_id|int|发送者id||
|content|string|弹幕内容||
|video_time|int|弹幕所在时间|弹幕所在视频的该时间点出现|
|comment_time|int|弹幕生成时间||
|color|string|弹幕颜色||
|state|int|弹幕状态|0）正常，-1）屏蔽， -2）删除|
|position|int|弹幕位置|先忽略，原计划是用于控制弹幕的默认显示位置|
|size|int|字号||
|comment_id|int|弹幕id||
|video_id|int|归属视频id||


#### 完整弹幕数据参考

完整的评论数据参照：`http://vod.4399sy.com/service/video/getComments?video_id=91&callback=cb`


### 2.16 弹幕开关

用于设置弹幕开关和获取弹幕功能关闭与否

```bash
danmakuSwitch           #传入boolean，开启&关闭弹幕，（true开启，false关闭）
```

### 2.17 获取弹幕开关

```bash
getDanmakuSwitchStatus  #返回boolean，获取弹幕开关状态
```


## 三、其它补充

flash 播放器不固定大小，会根据页面中嵌入flash的大小去动态调整视频画面大小。

播放器中没有UI元素，所有UI元素都是html dom触发接口来处理。


## 四、技术支持

ActionScript  @良杰
