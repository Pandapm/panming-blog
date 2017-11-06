---
title: H5设备方向及运动API介绍
date: 2017-03-01 10:35:36
tags: [H5,移动设备]
categories: Web前端 
---
H5提供了一些与设备相关的API，其中
有两种事件负责处理设备的物理方向和运动信息，它们常见的信息源有陀螺仪、罗盘及加速计。
第一种是处理方向的事件：`deviceorientation`，它会在传感器检测到设备在**方向上产生变化时**触发。通过处理该事件传来的数据信息，使针对由于用户移动设备引起旋转和角度变化的行为进行设计。
第二种是处理运动的事件：`devicemotion`，它会在**加速度发生改变时**触发。与`deviceorientation`不同，`devicemotion`监听的是加速度的变化。传感器通常都具有监听`devicemotion`的能力，包括笔记本中用于保护移动存储设备的传感器。而能监听`deviceorientation`事件的传感器更多出现在移动设备中。

<!-- more -->

先说这俩事件的浏览器支持情况：
![这里写图片描述](http://img.blog.csdn.net/20170226220838321?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUGFuZGFfbQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

## 设备方向deviceorientation事件和DeviceOrientationEvent对象
可以获取手机静止状态下的方向数据（手机所处的角度、方位和朝向等）。
### 设备坐标系
设备坐标系由 x、y 和 z 值表示，一般以设备的中心为基准
![设备坐标系](https://developers.google.cn/web/fundamentals/native-hardware/device-orientation/images/axes.png)

- x: 处于屏幕平面，正值表示向右。
- y: 处于屏幕平面，正值表示向上。
- z: 与屏幕或键盘垂直，正值表示向上。
### 返回的数据
数据以欧拉角的形式返回，表示设备坐标系与地球坐标系 之间的差异度数，所以，通过deviceoriention事件获取到的值可以理解为：
-  `alpha`(z): 手机在水平面上旋转的角度，当设备的顶部指向正北时其值为 0°。 当设备逆时针旋转时值增加。【0 -360】
- `beta`(x): 围绕x轴上下翻转的角度，当设备的顶部和底部与地球表面等距时其值为 0。【向上翻为负值，-180 - 180】
- `gamma`(y): 围绕y轴左右翻转的角度，当设备的左侧和右侧与地球表面等距时 其值为 0。【向左翻为负值， -180 - 180】


> 地球坐标系： 
X: 表示东西方向（东为正值）。 
Y: 表示南北方向（北为正值）。 
Z: 表示上下方向，与地面垂直 （向上为正值）。

### 尝试
要监听 `deviceorientation`，首先检查浏览器是否支持这个事件，然后将一个事件handler附加到 window 对象以监听 deviceorientation 事件。
```js
if (window.DeviceOrientationEvent) {
    window.addEventListener('deviceorientation', deviceOrientationHandler, false);
    document.getElementById("doeSupported").innerText = "";
}
```
事件一般返回三个常用的属性：alpha、 beta 和 gamma。
事件handler的一个例子：
```js
function handleOrientation(orientData) { 
    var alpha = orientData.alpha;
    var beta = orientData.beta;
    var gamma = orientData.gamma;
    xxxx....
}
```
特别小的一个demo： demo.varpm.com
 
 另外，这是返回的DeviceOrientationEvent对象的全部属性：
 ![这里写图片描述](http://img.blog.csdn.net/20170301104719411?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUGFuZGFfbQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
其中，如果浏览器可以提供相对某坐标系的值得花，absolute为true。
## 设备动作devicemotion事件和DeviceMotionEvent对象

设备动作事件的处理跟方向事件是一样的，
```js
window.addEventListener("devicemotion", handleMotion, true);
```

设备动作事件按定期间隔检测，并及时返回有关设备在该时刻角速度和加速度的数据，一些设备没有硬件来排除重力的影响。
当然两个代码的不同之处除了事件名称还有做为参数传递给handler的DeviceMotionEvent对象所包含的四个常见的属性：
```js
DeviceMotionEvent.acceleration //排除重力影响后的加速度
DeviceMotionEvent.accelerationIncludingGravity //不排除重力影响
DeviceMotionEvent.rotationRate //旋转速度
DeviceMotionEvent.interval //获取信息的时间间隔，单位是毫秒
```
`DeviceMotionEvent`对象提供设备在位置和方向上的改变速度的相关信息。这些变化信息也是通过三个轴来体现的:
acceleration 和 accelerationIncludingGravity，都包含下面三个轴:

- x: 西向东
- y: 南向北
- z: 垂直地面

rotationRate的情况有点不同：
- alpha: 设备沿着垂直屏幕的轴的旋转速率 (桌面设备相对于键盘)
- beta: 设备沿着屏幕左至右方向的轴的旋转速率(桌面设备相对于键盘)
- gamma: 设备沿着屏幕下至上方向的轴的旋转速率(桌面设备相对于键盘)  
如果手机在桌面上，屏幕向上：
![这里写图片描述](http://img.blog.csdn.net/20170226222436538?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUGFuZGFfbQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast) 
    
如果手机屏幕与地面垂直：
![这里写图片描述](http://img.blog.csdn.net/20170226222557219?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUGFuZGFfbQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

一个摇一摇的demo：
```js
var THRESHOLD = 1000;
var pre_x = pre_y = pre_z = cur_x = cur_y = cur_z = 0;
var preTime = 0;
var count = 0;
function shakeEventHandler(event) {
    var acceleration =event.accelerationIncludingGravity;
    cur_x = acceleration.x;
    cur_y = acceleration.y;
    cur_z = acceleration.z;
    console.log(cur_x,cur_y,cur_z);
    if(Math.abs(cur_x-pre_x) > 30 || Math.abs(cur_y-pre_y) > 30 || Math.abs(cur_z-pre_z) > 30) {
        //简单的摇一摇触发代码
        info_motion.innerHTML = ++count;
    }
    pre_x = cur_x;
    pre_y = cur_y;
    pre_z = cur_z;
}

```

附上一个完整可运行的demo：
[demo.varpm.com](demo.varpm.com)

```html

<!DOCTYPE html>
<html lang="en">
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
    <meta name="MobileOptimized" content="320">
    <meta name="viewport" content="initial-scale=1.0,user-scalable=no,minimum-scale=1.0, maximum-scale=1.0,width=device-width">
    <title>Document</title>
    <style>
    .orientation {
        width: 100px;
        margin: 20px;
        height: 100px;
        display: inline-block;
        background-color: #abcdef;
        position: relative;
     }
    </style>
</head>
<body>
    <h1>这是一个小demo</h1>
    <p>z:<span id="info-alpha"></span></p>
    <p>x:<span id="info-beta"></span></p>
    <p>y:<span id="info-gamma"></span></p>
    <div class="orientation orientation-x">
        <p>这是一个X</p>
    </div>
    <div class="orientation orientation-y">
        <p>这是一个Y</p>
    </div>
    <div class="orientation orientation-z">
        <p>这是一个Z</p>
    </div>
    <p id="info-motion"></p>
</body>
<script type="text/javascript" >
    var info_z = document.getElementById('info-alpha');
    var info_x = document.getElementById('info-beta');
    var info_y = document.getElementById('info-gamma');
    var info_motion = document.getElementById('info-motion');
    var orientation_x = document.querySelector('.orientation-x');
    var orientation_y = document.querySelector('.orientation-y');
    var orientation_z = document.querySelector('.orientation-z');
    if(window.DeviceOrientationEvent) {
        window.addEventListener('deviceorientation', handleOrientation);
    }
    else {
        orientation.innerHTML = '当前浏览器不支持DeviceOrientationEvent';
    }
    function handleOrientation(event) {
        console.log(event);
        var x = event.beta;
        var y = event.gamma;
        var z = event.alpha;
        info_z.innerHTML = parseInt(z);
        info_x.innerHTML = parseInt(x);
        info_y.innerHTML = parseInt(y);
        console.log('rotateY(' + y + 180 + 'deg)');
        orientation_x.style.transform  = 'rotateX(' + x + 180 + 'deg)';
        orientation_y.style.transform  = 'rotateY(' + y + 180 + 'deg)';
        orientation_z.style.transform  = 'rotateZ(' + z + 'deg)';
    }
    if (window.DeviceMotionEvent) {
        window.addEventListener('devicemotion', shakeEventHandler, false);
    } else {
        alert('当前浏览器不支持DeviceMotionEvent');
    }
    var THRESHOLD = 1000;
    var pre_x = pre_y = pre_z = cur_x = cur_y = cur_z = 0;
    var preTime = 0;
    var count = 0;
    function shakeEventHandler(event) {
        var acceleration =event.accelerationIncludingGravity;
        cur_x = acceleration.x;
        cur_y = acceleration.y;
        cur_z = acceleration.z;
        console.log(cur_x,cur_y,cur_z);
        if(Math.abs(cur_x-pre_x) > 30 || Math.abs(cur_y-pre_y) > 30 || Math.abs(cur_z-pre_z) > 30) {
            //简单的摇一摇触发代码
            info_motion.innerHTML = ++count;
        }
        pre_x = cur_x;
        pre_y = cur_y;
        pre_z = cur_z;
    }
</script>
</html>

```


\* 文中图片来自于Google Developer和MDN

