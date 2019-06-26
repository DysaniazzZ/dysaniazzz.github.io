---
layout:     post
title:      "ijkplayer 编译及问题"
date:       2019-06-14 12:00:00
author:     "DysaniazzZ"
header-img: "img/20190614/bg.jpg"
---

### Build

  * install homebrew, git, yasm

    ```
    ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
    brew install git
    brew install yasm
    ```

  * add these lines to your ~/.bash_profile and use source to refresh

    ```
    export ANDROID_SDK=<your sdk path>
    export ANDROID_NDK=<your ndk path>
    
    eg:
    open ~/.bash_profile
    
    # android sdk
    export ANDROID_SDK=/Users/dysania/Library/Android/sdk
    export PATH=${PATH}:${ANDROID_SDK}/tools
    export PATH=${PATH}:${ANDROID_SDK}/platform-tools
    export PATH=${PATH}:${ANDROID_SDK}/build-tools/24.0.3
    # android ndk
    export ANDROID_NDK=/Users/dysania/Library/Android/sdk/ndk-bundle/android-ndk-r14b
    export PATH=${PATH}:${ANDROID_NDK}

    source ~/.bash_profile
    ```

  * download the repo and checkout to a new branch

    ```
    git clone https://github.com/Bilibili/ijkplayer.git ijkplayer-android
    cd ijkplayer-android
    git checkout -B latest k0.8.8
    ```

  * optional: customize your codec/format

    ```
    cd config
    rm module.sh
    ln -s <module-yours.sh> module.sh
    cd ..
    ```

  * start to build

    ```
    ./init-android.sh

    # optional: init to support https
    ./init-android-openssl.sh

    cd android/contrib
    # optional: build to support https
    ./compile-openssl.sh clean
    ./compile-openssl.sh all

    ./compile-ffmpeg.sh clean
    ./compile-ffmpeg.sh all		// or use ./compile-ffmpeg.sh armv7a|armv5 to filter
    cd ..

    ./compile-ijk.sh all
    ```

### FAQ

  1. Support protocols: [FFmpeg Protocols Documentation](http://www.ffmpeg.org/ffmpeg-protocols.html)
     * https
       * [一步步带你编译ijkplayer so支持HTTPS](https://www.imooc.com/article/33930)
     * rtsp
       * [rtp protocol not found](https://github.com/bilibili/ijkplayer/issues/2199)
       * [RTSP protocol not found](https://github.com/bilibili/ijkplayer/issues/208)
       * [Android下用ijkPlayer播放rtsp视频流编译so文件](http://aduroidpc.top/2017/06/10/Android%E4%B8%8B%E7%94%A8ijkPlayer%E6%92%AD%E6%94%BErtsp%E8%A7%86%E9%A2%91%E6%B5%81%E7%BC%96%E8%AF%91so%E6%96%87%E4%BB%B6/)

      ```
      https:
      https://play.lijiangtv.com/live_zh/live_ff.m3u8

      rtsp:
      rtsp://184.72.239.149/vod/mp4:BigBuckBunny_175k.mov
      ```

  2. Support codecs: [FFmpeg Codecs Documentation](https://ffmpeg.org/ffmpeg-codecs.html)
     * mp2
       * [http://xxx有画面没声音](https://github.com/bilibili/ijkplayer/issues/3951)
     * wmv/wma
       * [How can I add wmv and mms support?](https://github.com/bilibili/ijkplayer/issues/82)
       * [Does this player supports WMA audio playback? ](https://github.com/bilibili/ijkplayer/issues/1327)

      ```
      mp2:
      http://117.139.20.20:6410/28000001/00000000000700000000000011176392

      wmv3/wmav2:
      rtsp://live.tsr.he.cn/tv1
      ```

  3. 播放体验问题
     * 硬解黑屏
       * [Mediacodec硬解黑屏问题](https://github.com/Bilibili/ijkplayer/issues/3181)
         * 解决办法：当使用硬解（mediacodec）时采用 TextureView，使用软解（avcodec）时采用 SurfaceView

### Extension

  * 显示视频文件封装信息及音视频信息：ffprobe -v error -show_format -show_streams -of json input.mp4
  * 精简显示视频文件封装信息及音视频信息：ffprobe -v error -show_entries stream=codec_name:format=format_name -of json input.mp4
  * 输出文件封装格式，音视频编码及宽高分辨率：ffprobe -v error -show_entries stream=codec_name,height,width:format=filename,format_name -of json input.mp4
  * 显示视频编码格式：ffprobe -v error -select_streams v:0 -show_entries stream=codec_name -of default=nw=1:nk=1 input.mp4
  * 显示音频编码格式：ffprobe -v error -select_streams a:0 -show_entries stream=codec_name -of default=nw=1:nk=1 input.mp4
  * 显示视音频编码格式：ffprobe -v error -show_entries stream=codec_name -of default=nw=1:nk=1 input.mp4