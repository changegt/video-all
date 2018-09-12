## videoJS组件相关研究

### 初始化方法

```html
<!--加载资源-->
<link rel="stylesheet" href="https://vjs.zencdn.net/7.1.0/video-js.css">
<script src="https://vjs.zencdn.net/7.1.0/video.js"></script>
<!--dom-->
<video
    id="my-player"
    class="video-js vjs-big-play-centered"
    controls
    preload="metadata"
    poster="//vjs.zencdn.net/v/oceans.png"
    width="600"
    height="400">
  <source src="//vjs.zencdn.net/v/oceans.mp4" type="video/mp4"></source>
  <source src="//vjs.zencdn.net/v/oceans.webm" type="video/webm"></source>
  <source src="//vjs.zencdn.net/v/oceans.ogv" type="video/ogg"></source>
  <p class="vjs-no-js">
    当前浏览器不支持播放视频
  </p>
</video>
<!--js:初始化-->
<script type="text/javascript">
	videojs('my-player', {}, function(){
	    //ready
	});
</script>
```
**上面是通过js初始化的，还有一种就是dom设置data-setup来配置初始化配置，不需要js初始化，但是自定义行为就会弱化**

#### a、标签属性解析配置
```js
<video
    id="my-player"
    class="video-js vjs-big-play-centered"
    controls 								//控制栏
    muted 									//静音
    autoplay 								//自动播放
    preload="metadata" 						//【metadata(加载视频元信息) | auto(加载视频数据) | none(不加载任务数据)】
    poster="//vjs.zencdn.net/v/oceans.png" 	//封面
    width="600"
    loop 									//是否循环播放
    src="" 									//资源地址，或者使用source标签
    height="400">
</video>
```

#### b、常用调用方法
```js
//videoJS实例player 【player.__proto__ 查看实例的构造函数的圆形对象中的所有属性和方法】

//操作行为
play => player.play()
pause => player.pause()
reset => player.reset()                     //重置video标签，具体表现是讲video的资源引用去除
volume => player.volume(num)                //num 0 - 1 标识音量大小
dispose => player.dispose()                 //销毁实例
currentTime => player.currentTime(time)     //设置当前播放时间
load => player.load()                       //加载资源行为
muted => player.muted(true | false)         //静音或者取消静音操作

//获取状态行为
player.hasStarted_ == player.hasStarted()   //获取当前视频是否开始
player.currentTime()                        //获取当前播放时间
player.duration()                           //获取当前的视频的总时间
player.paused()                             //判断当前视频是否暂停状态
player.muted()                              //判断当前是否静音

//扩展
reload => {
    player.pause()
    document.getElementsByTagName('video')[0].src = ''//资源
    player.load()
    player.play()
}
```

#### c、监听事件
```js
//监听方法
player.on('event', function(){});

//可以监听的事件【常用】
loadstart               //是否开始加载数据
loadedmetadata          //是否加载完视频元信息
loadeddata              //是否加载完数据
play                    //播放
pause                   //暂停
ended                   //播放结束
error                   //出现错误
timeupdate              //播放时间更新
progress                //播放进度更新
```

#### d、相关样式方案
```css
<style>
    /*播放图标想显示圆形*/
    .video-js .vjs-big-play-button{
        font-size: 2.5em;
        line-height: 2.3em;
        height: 2.5em;
        width: 2.5em;
        -webkit-border-radius: 2.5em;
        -moz-border-radius: 2.5em;
        border-radius: 2.5em;
        background-color: #73859f;
        background-color: rgba(115,133,159,.5);
        border-width: 0.15em;
        margin-top: -1.25em;
        margin-left: -1.75em;
    }
    
    /* 中间的播放箭头 */
    .vjs-big-play-button .vjs-icon-placeholder {
        font-size: 1.63em;
    }
    
    /* 加载圆圈 */
    .vjs-loading-spinner {
        font-size: 2.5em;
        width: 2em;
        height: 2em;
        border-radius: 1em;
        margin-top: -1em;
        margin-left: -1.5em;
    }
    
    /*进度显示当前的播放时间*/
    .video-js .vjs-time-control{display:block;}
    .video-js .vjs-remaining-time{display: none;}
    
    /*暂停时候显示播放按钮*/
    .vjs-paused .vjs-big-play-button,
    .vjs-paused.vjs-has-started .vjs-big-play-button {
        display: block;
    }
</style>
```

### demo代码
[github地址](https://github.com/changegt/video-all/tree/master/videoJS-stu)
```html
<html>
<head>
	<meta charset="UTF-8">
	<title>videoJS demo</title>
</head>
<body>
	<script src="https://cdn.bootcss.com/jquery/3.3.1/jquery.min.js"></script>
	<!-- <script src="../src/video.js"></script>
	<link rel="stylesheet" href="../css/video-js.min.css"> -->
	<link rel="stylesheet" href="https://vjs.zencdn.net/7.1.0/video-js.css">
	<script src="https://vjs.zencdn.net/7.1.0/video.js"></script>
	
	<video
	    id="my-player"
	    class="video-js vjs-big-play-centered"
	    controls
	    muted
	    preload="metadata"
	    poster="//vjs.zencdn.net/v/oceans.png"
	    loop
	    width="600"
	    height="400">
	  <source src="//vjs.zencdn.net/v/oceans.mp4" type="video/mp4"></source>
	  <source src="//vjs.zencdn.net/v/oceans.webm" type="video/webm"></source>
	  <source src="//vjs.zencdn.net/v/oceans.ogv" type="video/ogg"></source>
	  <p class="vjs-no-js">
	    To view this video please enable JavaScript, and consider upgrading to a
	    web browser that
	    <a href="http://videojs.com/html5-video-support/" target="_blank">
	      supports HTML5 video
	    </a>
	  </p>
	</video>

	<div>
		<button class="play">play</button>
		<button class="pause">pause</button>
		<button class="reload">reload</button>
		<button class="reset">reset</button>
		<button class="volume">volume[0.5]</button>
		<button class="dispose">dispose</button>
		<button class="muted">muted[toggle]</button>
	</div>

	<div class="logtxt" style="width: 100%;height: 300px; overflow: auto;">

	</div>

	<script type="text/javascript">
		var player, listeners;
		
		$(document).ready(function(){
			player = videojs('my-player', {}, function(){

				listen();
				event();

				if(listeners.readyCall){
					listeners.readyCall();
				}
			});

			
		});

		function listen(){
			var arr = ['loadstart','loadedmetadata','loadeddata','play','pause','ended','error'];

			listeners = {
				readyCall: function(){
					log('readyCall');
				},
				loadedmetadataCall: function(){
					log('loadedmetadata');	
				},
				playCall: function(){
					log('play');
				},
				pauseCall: function(){
					log('pause');
				},
				endedCall: function(){
					log('ended');
				},
				errorCall: function(){
					log('error');
				},
				loadstartCall: function(){
					log('loadstart');	
				},
				loadeddataCall: function(){
					log('loadeddata');
				},
				timeupdateCall: function(){ //时间更新
					log('timeupdate:['+player.currentTime()+']');
				},
				progressCall: function(){ //播放过程中持续监听
					log('progress');
				},
			};

			arr.forEach(function(el){
				player.on(el, listeners[el+'Call']);
			});
		}

		function event(){
			//事件
			var $source = $('#my-player source');

			var arr = ['play','pause','reset','dispose'];
			//dispose videoJs实例销毁

			arr.forEach(function(el){
				$('.'+el).click(function(){
					player[el]();
				});
			});

			$('.reload').click(function(){
				player.pause();
				$source.attr('src', $('#my-player source').attr('src'));
				player.load();
				player.play();
			});

			$('.volume').click(function(){
				player.volume(.5);
			});

			$('.muted').click(function(){
				var val = player.muted();
				player.muted(!val);
			});
		}

		function log(str){			
			$('.logtxt').append('<p>'+str+'</p>');
		}

	</script>

	<style>
		/*播放图标想显示圆形*/
		.video-js .vjs-big-play-button{
		    font-size: 2.5em;
		    line-height: 2.3em;
		    height: 2.5em;
		    width: 2.5em;
		    -webkit-border-radius: 2.5em;
		    -moz-border-radius: 2.5em;
		    border-radius: 2.5em;
		    background-color: #73859f;
		    background-color: rgba(115,133,159,.5);
		    border-width: 0.15em;
		    margin-top: -1.25em;
		    margin-left: -1.75em;
		}
		/* 中间的播放箭头 */
		.vjs-big-play-button .vjs-icon-placeholder {
		    font-size: 1.63em;
		}
		/* 加载圆圈 */
		.vjs-loading-spinner {
		    font-size: 2.5em;
		    width: 2em;
		    height: 2em;
		    border-radius: 1em;
		    margin-top: -1em;
		    margin-left: -1.5em;
		}

		/*进度显示当前的播放时间*/
		.video-js .vjs-time-control{display:block;}
		.video-js .vjs-remaining-time{display: none;}

		/*暂停时候显示播放按钮*/
		.vjs-paused .vjs-big-play-button,
		.vjs-paused.vjs-has-started .vjs-big-play-button {
		    display: block;
		}
	</style>
</body>
</html>
```