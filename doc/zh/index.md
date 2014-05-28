<!---
    Licensed to the Apache Software Foundation (ASF) under one
    or more contributor license agreements.  See the NOTICE file
    distributed with this work for additional information
    regarding copyright ownership.  The ASF licenses this file
    to you under the Apache License, Version 2.0 (the
    "License"); you may not use this file except in compliance
    with the License.  You may obtain a copy of the License at

      http://www.apache.org/licenses/LICENSE-2.0

    Unless required by applicable law or agreed to in writing,
    software distributed under the License is distributed on an
    "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
    KIND, either express or implied.  See the License for the
    specific language governing permissions and limitations
    under the License.
-->

# org.apache.cordova.media

这个插件提供录制和播放设备上的音频文件的能力。

**NOTE**： 当前实现的多媒体采集并不遵循 W3C 规范，并且仅用于提供方便。 将来的实现将坚持以最新的 W3C 规范并可能弃用目前的APIs。

## 安装

    cordova plugin add org.apache.cordova.media
    

## 支持的平台

*   Android 系统
*   黑莓 10
*   iOS
*   Windows Phone 7 和 8
*   Tizen
*   Windows 8

## Windows Phone 的怪异

*   在一个时间内，只有一个媒体文件可以被播放。

*   对您的应用程序如何与其他多媒体交互方式的严格限制。 请参见[Microsoft 文档的详细信息][1].

 [1]: http://msdn.microsoft.com/en-us/library/windowsphone/develop/hh184838(v=vs.92).aspx

## 媒体

    var media = new Media(src, mediaSuccess, [mediaError], [mediaStatus]);
    

### 参数

*   **src**： 包含音频内容的 URI。*(DOMString)*

*   **mediaSuccess**： (Optional) 在`Media` 对象已完成当前的播放、录音或停止以后则执行的回调。*(Function)*

*   **mediaError**： (Optional)如果错误发生时执行的回调。*(Function)*

*   **mediaStatus**： (Optional) 执行以指示状态的更改的回调。*(Function)*

### 常量

以下常量作为唯一的参数到 `mediaStatus` 回调：

*   `Media.MEDIA_NONE` = 0;
*   `Media.MEDIA_STARTING` = 1;
*   `Media.MEDIA_RUNNING` = 2;
*   `Media.MEDIA_PAUSED` = 3;
*   `Media.MEDIA_STOPPED`= 4 ；

### 方法

*   `media.getCurrentPosition`： 返回一个音频文件的当前播放位置。

*   `media.getDuration`： 返回一个音频文件的片长（持续时间）。

*   `media.play`： 启动或继续播放音频文件。

*   `media.pause`： 暂停正在播放的音频文件。

*   `media.release`： 释放底层操作系统的音频资源。

*   `media.seekTo`： 在音频文件内移动到指定的位置。

*   `media.setVolume`： 设置音频播放的音量。

*   `media.startRecord`： 开始录制音频文件。

*   `media.stopRecord`： 停止正在录制的音频文件。

*   `media.stop`： 停止正在播放音频文件。

### 附加只读参数

*   **position**： 音频播放中的位置，以秒为单位。
    
    *   不会播放时自动更新 ；调用 `getCurrentPosition` 来更新。

*   **duration**: 多媒体的片长（持续时间），以秒为单位。

## media.getCurrentPosition

返回一个音频文件内的当前位置。此外可以更新 `Media` 对象的 `position` 参数。

    media.getCurrentPosition(mediaSuccess, [mediaError]);
    

### 参数

*   **mediaSuccess**： 当前的播放位置，以秒为单位。

*   **mediaError**： (Optional) 如果发生错误时执行回调。

### 快速的示例

    // Audio player
    //
    var my_media = new Media(src, onSuccess, onError);
    
    // Update media position every second
    var mediaTimer = setInterval(function () {
        // get media position
        my_media.getCurrentPosition(
            // success callback
            function (position) {
                if (position > -1) {
                    console.log((position) + " sec");
                }
            },
            // error callback
            function (e) {
                console.log("Error getting pos=" + e);
            }
        );
    }, 1000);
    

## media.getDuration

以秒为单位返回一个音频文件的持续时间。如果持续时间是未知的则返回值为-1。

    media.getDuration();
    

### 快速的示例

    // Audio player
    //
    var my_media = new Media(src, onSuccess, onError);
    
    // Get duration
    var counter = 0;
    var timerDur = setInterval(function() {
        counter = counter + 100;
        if (counter > 2000) {
            clearInterval(timerDur);
        }
        var dur = my_media.getDuration();
        if (dur > 0) {
            clearInterval(timerDur);
            document.getElementById('audio_duration').innerHTML = (dur) + " sec";
        }
    }, 100);
    

## media.pause

暂停正在播放的音频文件。

    media.pause();
    

### 快速的示例

    // Play audio
    //
    function playAudio(url) {
        // Play the audio file at url
        var my_media = new Media(url,
            // success callback
            function () { console.log("playAudio():Audio Success"); },
            // error callback
            function (err) { console.log("playAudio():Audio Error: " + err); }
        );
    
        // Play audio
        my_media.play();
    
        // Pause after 10 seconds
        setTimeout(function () {
            media.pause();
        }, 10000);
    }
    

## media.play

开始或重新开始播放音频文件。

    media.play();
    

### 快速的示例

    // Play audio
    //
    function playAudio(url) {
        // Play the audio file at url
        var my_media = new Media(url,
            // success callback
            function () {
                console.log("playAudio():Audio Success");
            },
            // error callback
            function (err) {
                console.log("playAudio():Audio Error: " + err);
            }
        );
        // Play audio
        my_media.play();
    }
    

### iOS 的怪癖

*   **numberOfLoops**： 通过到此选项可将`play` 方法来指定你想让媒体文件播放的次数，例如：
    
        var myMedia = new Media("http://audio.ibeat.org/content/p1rj1s/p1rj1s_-_rockGuitar.mp3")
        myMedia.play({ numberOfLoops: 2 })
        

*   **playAudioWhenScreenIsLocked**： 通过此选项可将 `play` 方法来指定您是否要允许播放时屏幕锁定。 如果设置为 `true` （默认值），将忽略硬件静音按钮的状态，例如：
    
        var myMedia = new Media("http://audio.ibeat.org/content/p1rj1s/p1rj1s_-_rockGuitar.mp3")
        myMedia.play({ playAudioWhenScreenIsLocked : false })
        

*   **order of file search**： 当只有一个文件的名称或简单路径提供时，iOS 在`www` 目录中搜索该文件，然后在应用程序中 `documents/tmp` 目录中：
    
        var myMedia = new Media("audio/beer.mp3")
        myMedia.play()  // first looks for file in www/audio/beer.mp3 then in <application>/documents/tmp/audio/beer.mp3
        

## media.release

释放底层操作系统的音频资源。 这是特别重要的 android 操作系统，因为有了有限数量的 OpenCore 实例播放媒体。 应用程序应当调用 `release` 函数的任何 `Media` 不再需要的资源。

    media.release();
    

### 快速的示例

    // Audio player
    //
    var my_media = new Media(src, onSuccess, onError);
    
    my_media.play();
    my_media.stop();
    my_media.release();
    

## media.seekTo

在音频文件中设置当前的播放位置。

    media.seekTo(milliseconds);
    

### 参数

*   **milliseconds**： 该位置设置为音频的播放位置，以毫秒为单位。

### 快速的示例

    // Audio player
    //
    var my_media = new Media(src, onSuccess, onError);
        my_media.play();
    // SeekTo to 10 seconds after 5 seconds
    setTimeout(function() {
        my_media.seekTo(10000);
    }, 5000);
    

### 黑莓 10 怪癖

*   BlackBerry OS 5 设备上不支持。

## media.setVolume

设置音频文件的音量。

    media.setVolume(volume) ；
    

### 参数

*   **volume**： 为播放设置音量。这个值必须在 0.0 到 1.0 的范围内。

### 支持的平台

*   Android 系统
*   iOS

### 快速的示例

    // Play audio
    //
    function playAudio(url) {
        // Play the audio file at url
        var my_media = new Media(url,
            // success callback
            function() {
                console.log("playAudio():Audio Success");
            },
            // error callback
            function(err) {
                console.log("playAudio():Audio Error: "+err);
        });
    
        // Play audio
        my_media.play();
    
        // Mute volume after 2 seconds
        setTimeout(function() {
            my_media.setVolume('0.0');
        }, 2000);
    
        // Set volume to 1.0 after 5 seconds
        setTimeout(function() {
            my_media.setVolume('1.0');
        }, 5000);
    }
    

## media.startRecord

开始录制一个音频文件。

    media.startRecord() ；
    

### 支持的平台

*   Android 系统
*   iOS
*   Windows Phone 7 和 8
*   Windows 8

### 快速的示例

    // Record audio
    //
    function recordAudio() {
        var src = "myrecording.mp3";
        var mediaRec = new Media(src,
            // success callback
            function() {
                console.log("recordAudio():Audio Success");
            },
    
            // error callback
            function(err) {
                console.log("recordAudio():Audio Error: "+ err.code);
            });
    
        // Record audio
        mediaRec.startRecord();
    }
    

### Android 的怪癖

*   在自适应多速率格式中Android 设备录制音频。指定的文件应以*.amr*扩展名结尾。

### iOS 的怪癖

*   iOS 仅仅录制*.wav*类型的文件并且如果文件扩展名不正确则返回一个错误。

*   如果完整路径未被提供，录音放在应用程序的 `documents/tmp` 目录。 这可以通过 `File`API 使用`LocalFileSystem.TEMPORARY`进行访问 。 在录音时间中指定的任何子目录必须已经存在。

*   文件可以录音和回放使用的文件的 URI：
    
        var myMedia = new Media("documents://beer.mp3")
        

### Tizen 怪癖

*   Tizen 设备上不支持。

## media.stop

停止正在播放的音频文件。

    media.stop() ；
    

### 快速的示例

    // Play audio
    //
    function playAudio(url) {
        // Play the audio file at url
        var my_media = new Media(url,
            // success callback
            function() {
                console.log("playAudio():Audio Success");
            },
            // error callback
            function(err) {
                console.log("playAudio():Audio Error: "+err);
            }
        );
    
        // Play audio
        my_media.play();
    
        // Pause after 10 seconds
        setTimeout(function() {
            my_media.stop();
        }, 10000);
    }
    

## media.stopRecord

停止正在录制的音频文件。

    media.stopRecord() ；
    

### 支持的平台

*   Android 系统
*   iOS
*   Windows Phone 7 和 8
*   Windows 8

### 快速的示例

    // Record audio
    //
    function recordAudio() {
        var src = "myrecording.mp3";
        var mediaRec = new Media(src,
            // success callback
            function() {
                console.log("recordAudio():Audio Success");
            },
    
            // error callback
            function(err) {
                console.log("recordAudio():Audio Error: "+ err.code);
            }
        );
    
        // Record audio
        mediaRec.startRecord();
    
        // Stop recording after 10 seconds
        setTimeout(function() {
            mediaRec.stopRecord();
        }, 10000);
    }
    

### Tizen 怪癖

*   Tizen 设备上不支持。

## MediaError

当出现一个错误的时候，一个`MediaError` 对象被返回到 `mediaError` 回调函数。

### 属性

*   **代码**： 下面列出的预定义的错误代码之一。

*   **message**：错误码对应的错误描述信息。

### 常量

*   `MediaError.MEDIA_ERR_ABORTED`
*   `MediaError.MEDIA_ERR_NETWORK`
*   `MediaError.MEDIA_ERR_DECODE`
*   `MediaError.MEDIA_ERR_NONE_SUPPORTED`