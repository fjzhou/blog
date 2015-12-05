---
layout: post
title: HTML5下使用JS访问设备姿态
category: JavaScript
---

手机和平板上拥有陀螺仪和加速计等很好玩的传感器。因此可以获得设备当前的运动加速度和姿态。

<!--more-->

首先为手机定义三维空间的参考轴：

![XYZ轴定义](http://www.w3.org/TR/orientation-event/start.png)

JS可以通过[标准事件](http://www.w3.org/TR/orientation-event/)获得以下数据：

## 加速度数据
当设备运动时，会有加速度数据，单位是米/秒^2。在X，Y，Z三个轴上x，y，z三个加速度分量。

当屏幕朝上，水平静止放置时，acceleration接口中x=0，y=0，z=0，而accelerationIncludingGravity接口中
x=0，y=0，z=9.81。即设备只受到垂直方向上的重力加速度。  
PS: 在iOS下，此时z=-9.81。

加速度数据目前在手机上的一个小应用就是摇一摇。判断一个时间间隔内的设备位移，从而判定是否被摇动。代码：

```js
window.addEventListener("devicemotion", deviceMotionHandler, false);

function deviceMotionHandler(eventData) {
    var SHAKE_THRESHOLD = 3000;
    var acceleration = eventData.accelerationIncludingGravity;
    x = acceleration.x;
    y = acceleration.y;
    z = acceleration.z;
    var curTime = new Date().getTime();
    var diffTime = curTime - last_update;

    if(diffTime > 100) {
        last_update = curTime;
        var speed = Math.abs(x + y + z - last_x - last_y - last_z) / diffTime * 10000;
        if(speed > SHAKE_THRESHOLD) {
            // 摇一摇
        } else {
            // 摇的不够
        }
        last_x = x;
        last_y = y;
        last_z = z;
    }
}

```

## 旋转数据
设备在空间中的姿态数据根据陀螺仪传感器获得。设备以X，Y，Z三个轴为轴旋转，分别获得alpha，beta，gamma三个角度数据。

alpha是围绕Z轴旋转的角度。数字范围[0, 360)。顺时针旋转时，数字变小，逆时针时数字变大。  
beta是围绕X轴旋转的角度。数字范围[-180，180)。水平放置时是0，头部抬起时，数字变大，垂直时是90度。继续旋转时继续变大，直到屏幕朝下，接近180度。
而iOS中beta范围是[-90，90]，垂直时同样90度。区别在继续旋转时，数字变小了。即iOS中beta始终是锐角。

gamma是围绕Y轴旋转的角度。数字范围[-90，90)。机身左侧抬起时数字变大，屏幕朝右的垂直放置时为90度。
而iOS是[-180，180]。


