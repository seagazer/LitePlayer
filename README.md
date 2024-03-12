# LitePlayer [![](https://www.jitpack.io/v/seagazer/liteplayer.svg)](https://www.jitpack.io/#seagazer/liteplayer)

<font color=#4A90CE size=5>A lite player for android by Kotlin language</font>
<font color=#4A90CE size=5>一个完全使用 Kotlin 开发的简洁高效，轻量级的播放器框架</font>

#### 特点：

- 100% Kotlin 编写，完美与目前主流项目搭配
- 高效的列表性能，创新式的极简的列表接入方式，完美支持 ListView 和 RecyclerView
- 组件化设计，提供灵活自由的界面装配与展示
- 对核心模块提供顶层接口，渲染，播放等核心功能可以单个或者完全自定义，并无缝接入
- 集成 Lifecycle，资源自动释放，无需再手动处理资源泄露问题
- 框架极简，不提供冗余功能，更轻量，无需担心应用包大小敏感

#### 目前版本已支持功能：

- <font color=#4A90CE>支持 ExoPlayer 解码</font>
- <font color=#4A90CE>支持 MediaPlayer 解码</font>
- <font color=#4A90CE>支持 IjkPlayer 解码</font>
- <font color=#4A90CE>支持 SurfaceView 和 TextureView 渲染</font>
- <font color=#4A90CE>支持 AspectRatio 画面比例模式</font>
- <font color=#4A90CE>支持 FullScreen 模式</font>
- <font color=#4A90CE>支持 Window 悬浮窗模式</font>
- <font color=#4A90CE>支持 RecyclerView 列表播放</font>
- <font color=#4A90CE>支持 ListView 列表播放</font>
- <font color=#4A90CE>支持 RTMP,DASH 和其他数据流播放</font>
- <font color=#4A90CE>支持手势 seek, setBrightness, setVolume 操作</font>
- <font color=#4A90CE>支持预置 Controller 控制面板</font>
- <font color=#4A90CE>支持预置 Topbar 信息面板</font>
- <font color=#4A90CE>支持自定义 Overlay 面板</font>

#### TODO 计划：

- 支持系统 PictureInPicture 模式
- 提供预置弹幕控制面板(使用 Bilibili 弹幕库实现，本库不再集成)
- 支持 Cache 本地缓存(可使用 VideoCache 库进行视频地址的本地代理实现，本库不再集成)

#### v1 版本模块设计：

<img src="./image/liteplayer.png" width="820" height="500"/>

最新版本：[![](https://www.jitpack.io/v/seagazer/liteplayer.svg)](https://www.jitpack.io/#seagazer/liteplayer)

#### 基本使用方法：

1. 添加依赖和相关配置：

```java
    // 1.根目录的build.gradle配置maven地址:
    allprojects {
	    repositories {
		    ...
		    maven { url 'https://www.jitpack.io' }
	    }
    }

    // 2.注意：使用exoplayer时需要添加java8支持:
    android {
        // 添加java8支持
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
    }

    // 3.项目中添加远程依赖:
    dependencies {
            // 依赖LitePlayer库
            implementation 'com.github.seagazer:liteplayer:latestVersion'
            // 项目自动播放暂停及资源释放功能，需要依赖livedata
            implementation 'androidx.lifecycle:lifecycle-livedata:latestVersion'
            implementation 'androidx.lifecycle:lifecycle-extensions:latestVersion'
	}

    // 4.存在全屏切换场景时，manifest中对应的Activity需要配置`configChanges`属性，并且使用NoActionBar主题:
    <activity
        android:name=".XXActivity"
        android:configChanges="keyboard|keyboardHidden|orientation|screenSize|screenLayout|smallestScreenSize|uiMode"
        android:theme="@style/FullScreenTheme" />
```

2. `LitePlayerView`为基础视频播放控件，继承至`FrameLayout`，下面列举一些基础`API`：

```kotlin
<LitePlayerView.kt>
    // xml解析或者代码构建实例
    val litePlayerView = findViewById(R.id.lite_player_view)
    // 设置内置progress颜色
    litePlayerView.setProgressColor(progressColor, secondProgressColor)
    // 设置是否显示内置progress
    litePlayerView.displayProgress(true)
    // 设置是否开启重力感应自动全屏
    litePlayerView.setAutoSensorEnable(true)
    // 设置播放器内核：模式定义在PlayerType中
    litePlayerView.setPlayerType(PlayerType.TYPE_EXO_PLAYER)
    // 设置自定义的播放器内核(Type自动设置为TYPE_CUSTOM_PLAYER)
    litePlayerView.setCustomPlayer(CustomPlayer())
    // 设置软解(仅Ijk内核支持,Ijk默认软解)
    litePlayerView.supportSoftwareDecode(true)
    // 设置渲染方式：模式定义在RenderType中
    litePlayerView.setRenderType(RenderType.TYPE_SURFACE_VIEW)
    // 设置自定义渲染视图(必须是SurfaceView或者TextureView子类)
    litePlayerView.setCustomRender(CustomRender(context))
    // 设置是否全屏模式
    litePlayerView.setFullScreenMode(true)
    // 判断当前是否全屏模式
    litePlayerView.isFullScreen()
    // 设置是否悬浮窗模式
    litePlayerView.setFloatWindowMode(true)
    // 设置悬浮窗大小(FloatSize.NORMAL, FloatSize.LARGE)
    litePlayerView.setFloatSizeMode(FloatSize.LARGE)
    // 判断当前是否悬浮窗模式
    litePlayerView.isFloatWindow()
    // 设置比例模式：模式定义在AspectRatio中
    litePlayerView.setAspectRatio(AspectRatio.W_21_9)
    // 添加内置媒体控制面板
    litePlayerView.attachMediaController(LiteMediaController(context))
    // 添加内置媒体顶部面板
    litePlayerView.attachMediaTopbar(LiteMediaTopbar(context))
    // 添加内置手势控制面板(可单独开关某个手势支持)
    litePlayerView.attachGestureController(LiteGestureController(this).apply {
        supportSeek = true
        supportBrightness = true
        supportVolume = true
    })
    // 添加LiteFloatWindow处理悬浮窗播放(可继承IFloatWindow自定义悬浮窗)
    litePlayerView.attachFloatWindow(LiteFloatWindow(context, litePlayerView))
    // 添加自定义面板(Loading等)
    litePlayerView.attachOverlay(LoadingOverlay(context))
    // 添加一个监听面板(非View，可用于监听悬浮窗模式，全屏模式，自动感应模式状态变更)
    litePlayerView.attachOverlay(object : SimpleOverlayObserver() {
            override fun floatWindowModeChanged(isFloatWindow: Boolean) {
                super.floatWindowModeChanged(isFloatWindow)
            }

            override fun autoSensorModeChanged(isAutoSensor: Boolean) {
                super.autoSensorModeChanged(isAutoSensor)
            }

            override fun displayModeChanged(isFullScreen: Boolean) {
                super.displayModeChanged(isFullScreen)
            }
    })
    // 设置是否自动隐藏ITopbar和IController面板(IGesture面板会在操作后自动隐藏，自定义overlay需自己根据player或render状态控制显示隐藏时机)
    litePlayerView.setAutoHideOverlay(false)
    // 设置媒体播放资源
    litePlayerView.setDataSource(DataSource(url))
    // 设置播放速率
    litePlayerView.setPlaySpeed(1.5f)
    // 设置音量(音量范围自己通过AudioManager获取，每个设备和系统不一样)
    litePlayerView.setVolume(5)
    // 设置播放结束后是否自动重放
    litePlayerView.setRepeatMode(true)
    // 设置监听播放器状态
    litePlayerView.addPlayerStateChangedListener(PlayerStateChangedListener())
    // 设置监听Surface状态
    litePlayerView.addRenderStateChangedListener(RenderStateChangedListener())
    // 设置任何场景都接收player和render状态变更事件(默认false，页面处于可见的生命周期时才接收事件，悬浮窗播放需设置true否则后台播放接收不到事件)
    litePlayerView.setEventObserveForever(true)
    // 设置当音频焦点被抢占时是否自动停止播放(默认true)
    litePlayerView.setAutoPausedWhenAudioFocusLoss(false)
    // 开始播放
    litePlayerView.start()
    // 开始播放(从position开始，相当于start + seek操作)
    litePlayerView.start(position)
    // 跳至position位置
    litePlayerView.seekTo(position)
    // 暂停(是否用户主动暂停)[例如Activity移入后台，框架内部会标记为非用户暂停，页面状态恢复时会自动开启播放]
    litePlayerView.pause(true)
    // 恢复播放(当框架监听生命周期状态自动暂停时会在合适时机自动恢复播放)
    litePlayerView.resume()
    // 停止播放：在支持Lifecycle的Activity环境下无需主动调用，框架内部会自动调用
    litePlayerView.stop()
    // 销毁释放播放器：在支持Lifecycle的Activity环境下无需主动调用，框架内部会自动调用
    litePlayerView.destroy()
    // 获取IPlayer实例(ExoPlayerImpl, IjkPlayerImpl, MediaPlayerImpl, 可能为null)
    litePlayerView.getPlayer()
    // 获取IRender实例(RednerSurfaceView, RenderTextureView, 可能为null)
    litePlayerView.getRender()

<MediaLogger.kt>
    // 强制打开logcat(默认debug打开，release关闭)
    MediaLogger.openLogger()
    // 设置logcat级别为info(默认debug)
    MediaLogger.setLevel(MediaLogger.Level.INFO)
```

3. `ListPlayer`支持以极简的方式接入`RecyclerView`列表播放，框架设计使用代理模式，因此上述`LitePlayerView`的所有`API`都适用于`ListPlayer`

- 注意：`IPlayer`是在`attachToRecyclerView`中初始化，涉及 Player 的方法，例如`pause，setPlaySpeed`必须在`attachToRecyclerView`之后调用：

```kotlin
<ListPlayer.kt>
    // 默认构造需传入一个LitePlayerView实例
    val listPlayer = ListPlayer(LitePlayerView(context))
    listPlayer.isPlayableWhenScrollIdle = false// 设置是否滑动停止状态播放(默认true)
    // 定义列表滑动回调
    val videoScrollListener = object : ListPlayer.VideoListScrollListener {
            override fun getVideoContainer(position: Int): ViewGroup? {
                // 返回当前索引itemView中装载LitePlayerView的容器，建议itemView布局中定义一个空的FrameLayout作为载体
            }

            override fun getVideoDataSource(position: Int): DataSource? {
                // 返回当前索引的媒体资源
            }
        }
    // 关联RecyclerView，并且设置是否滑动过程中自动播放
    listPlayer.attachToRecyclerView(recyclerView, true, videoScrollListener)
    // 设置是否滑动过程中自动播放，建议一般在attach中设置后无特殊需求不进行模式变更(动态切换会造成数据重置)
    listPlayer.setAutoPlayMode(true)
    // 非自动播放时，点击item播放调用方法
    listPlayer.onItemClick(adapterPosition)
    // 设置自动缓存每个position对应视频的播放进度(默认false)
    listPlayer.supportHistory = true
```

4. `ListPlayer2`支持以极简的方式接入`ListView`列表播放，框架设计使用代理模式，因此上述`LitePlayerView`的所有`API`都适用于`ListPlayer2`

- 注意：`IPlayer`是在`attachToListView`中初始化，涉及 Player 的方法，例如`pause，setPlaySpeed`必须在`attachToListView`之后调用：

```kotlin
<ListPlayer2.kt>
    // 默认构造需传入一个LitePlayerView实例
    val listPlayer2 = ListPlayer2(LitePlayerView(context))
    // 定义列表滑动回调
    val videoScrollListener = object : ListPlayer2.VideoListScrollListener {
            override fun getVideoContainer(childIndex: Int, position: Int): ViewGroup? {
                // 此处需要区别于ListPlayer.VideoListScrollListener：
                // 1.childIndex是当前需要播放的itemView在ListView可见子View的index，通过该index获取当前容器：list_view.getChildAt(childIndex)
                // 2.position是当前数据adapter的索引
            }

            override fun getVideoDataSource(position: Int): DataSource? {
                // 返回当前索引的媒体资源
            }
        }
    // 关联ListView，并且设置是否滑动过程中自动播放
    listPlayer2.attachToListView(list_view, true, videoScrollListener)
    // 设置是否滑动过程中自动播放，建议一般在attach中设置后无特殊需求不进行模式变更(动态切换会造成数据重置)
    listPlayer2.setAutoPlayMode(true)
    // 非自动播放时，点击item播放调用方法
    listPlayer2.onItemClick(adapterPosition)
    // 设置自动缓存每个position对应视频的播放进度(默认false)
    listPlayer.supportHistory = true
```

##### 更多具体使用方式和场景可参考项目中的`sample`工程

#### Demo 展示：

<table>
<tr>
<td><center><img src="https://upload-images.jianshu.io/upload_images/4420407-b35972ffa16450c8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240" width="240" height="450"/></center></td>
<td><center><img src="https://upload-images.jianshu.io/upload_images/4420407-4d38e9e4c65602ec.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240" width="240" height="450"/></center></td>
<td><center><img src="https://upload-images.jianshu.io/upload_images/4420407-4bc41208d8b0607b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240" width="240" height="450"/></center></td>
</tr>
</table>
<table>
<tr>
<td><center><img src="https://upload-images.jianshu.io/upload_images/4420407-dbe0d9ed7df090ee.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240" width="240" height="450"/></center></td>
<td><center><img src="https://upload-images.jianshu.io/upload_images/4420407-62e648076c6900c1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240" width="240" height="450"/></center></td>
<td><center><img src="https://upload-images.jianshu.io/upload_images/4420407-9ad7d1efc4f3dc1c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240" width="240" height="450"/></center></td>
</tr>
</table>
<table>
<tr>
<td><center><img src="https://upload-images.jianshu.io/upload_images/4420407-b890137a1c2dae98.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240" width="240" height="450"/></center></td>
<td><center><img src="https://upload-images.jianshu.io/upload_images/4420407-2d9cc61bc9e58908.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240" width="240" height="450"/></center></td>
<td><center><img src="https://upload-images.jianshu.io/upload_images/4420407-abd3917cb9aba55e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240" width="240" height="450"/></center></td>
</tr>
</table>

### <font color=#ff0000>注意：Demo 展示中的视频链接均来源于网络，仅供学习交流使用，严禁用于商业用途，若有侵权请联系作者删除(seagazer@qq.com) </color>
