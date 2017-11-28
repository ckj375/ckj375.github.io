---
title: Android自定义View之转盘式倒计时
date: 2017-11-20 14:45:28
tags: [自定义View,倒计时]
categories: Android
---

### 需求分析
分享一个很久之前开发的Android倒计时特效.初始需求是这样的:以转盘形式设置倒计时时间,开始计时后,转盘箭头每秒转动一定角度,直至倒计时结束.Demo效果图如下,拖动转盘箭头设置倒计时时间(一圈对应60秒),彩色部分对应当前倒计时剩余时间.由于渐变色的那段圆弧是随着倒计时时间不断缩小的,所以考虑先绘制一个渐变色圆环,再在渐变色圆环上覆盖灰色圆弧,再在整个圆环视图顶层覆盖一个白色刻度环(其中灰色圆弧根据倒计时时间进度不断绘制弧度大小).
<!-- more -->
<center>
![img](http://o6xqhzzif.bkt.clouddn.com/hexo/customtimer/demo.png)
</center>
### 控件测量
不管是绘制圆弧还是圆环,都需要用到一个核心API,即drawArc,源码如下.
```
/**
 * <p>Draw the specified arc, which will be scaled to fit inside the
 * specified oval.</p>
 *
 * <p>If the start angle is negative or >= 360, the start angle is treated
 * as start angle modulo 360.</p>
 *
 * <p>If the sweep angle is >= 360, then the oval is drawn
 * completely. Note that this differs slightly from SkPath::arcTo, which
 * treats the sweep angle modulo 360. If the sweep angle is negative,
 * the sweep angle is treated as sweep angle modulo 360</p>
 *
 * <p>The arc is drawn clockwise. An angle of 0 degrees correspond to the
 * geometric angle of 0 degrees (3 o'clock on a watch.)</p>
 *
 * @param oval       The bounds of oval used to define the shape and size
 *                   of the arc
 * @param startAngle Starting angle (in degrees) where the arc begins
 * @param sweepAngle Sweep angle (in degrees) measured clockwise
 * @param useCenter If true, include the center of the oval in the arc, and
                        close it if it is being stroked. This will draw a wedge
 * @param paint      The paint used to draw the arc
 */
public void drawArc(@NonNull RectF oval, float startAngle, float sweepAngle, boolean useCenter,
        @NonNull Paint paint) {
    drawArc(oval.left, oval.top, oval.right, oval.bottom, startAngle, sweepAngle, useCenter,
            paint);
}
```
RectF这个类包含一个矩形的四个单精度浮点坐标.矩形通过上下左右4个边的坐标来表示一个矩形.这些坐标值属性可以被直接访问，用width()和height()方法可以获取矩形的宽和高.示意图如下:
<center>
![img](http://o6xqhzzif.bkt.clouddn.com/hexo/customtimer/rectf.png)
</center>

设置圆环属性,包括圆心坐标,半径,圆环颜色,粗细等.
```
mFirstPaint.setShader(mGradientColor);// 圆环颜色
mFirstPaint.setStrokeWidth(mStrokeSize);// 圆环粗细

mSecondPaint.setColor(bgColor);
mSecondPaint.setStrokeWidth(mStrokeSize);

mThirdPaint.setColor(mWhiteColor);
mThirdPaint.setStrokeWidth(mStrokeSize);

RectF mArcRect = new RectF();
mArcRect.top = yCenter - radius;
mArcRect.bottom = yCenter + radius;
mArcRect.left = xCenter - radius;
mArcRect.right = xCenter + radius;
```
<center>
![img](http://o6xqhzzif.bkt.clouddn.com/hexo/customtimer/coordinate.png)
</center>

### 控件绘制
开始绘制圆环(由于API默认是从3点方向顺时针绘制的,跟时钟的起始点不一致,所以将画布逆时针旋转90度).圆环一圈360度等分成60格,每格是6度;一圈60秒,每格是1秒.angle是指从0点方向顺时针转过的角度,倒计时开始后,灰色圆弧每秒绘制一小段弧度,直至全部覆盖渐变色圆环.
```
// 渐变色圆环
canvas.save();
canvas.rotate(-90, xCenter, yCenter);
canvas.drawArc(mArcRect, 0, 360, false, mFirstPaint);

// 灰色圆弧
if (angle % 6 == 0) {
    canvas.drawArc(mArcRect, angle, 360 - angle, false, mSecondPaint);
}

// 白色刻度
for (float i = 5.7f; i < 360; i += 6f) {
    canvas.drawArc(mArcRect, i, mStep, false, mThirdPaint);
}
canvas.restore();

canvas.save();
canvas.rotate(angle, canvas.getWidth() / 2, canvas.getHeight() / 2);
// 转盘箭头
canvas.drawBitmap(progressMark, canvas.getWidth() / 2 - progressMark.getWidth() / 2,canvas.getHeight() / 2 - radius - progressMark.getHeight() / 2, null);
canvas.restore();
```
### Touch事件
通过拖动转盘箭头来设置倒计时时间,根据触摸点位置计算夹角,换算成时间.
```
private void moved(float x, float y) {
float distance = (float) Math.sqrt(Math.pow((x - xCenter), 2)
        + Math.pow((y - yCenter), 2));
float degrees = (float) ((float) ((Math.toDegrees(Math.atan2(
        x - xCenter, yCenter - y)) + 360.0)) % 360.0);
// and to make it count 0-360
if (degrees < 0) {
    degrees += 2 * Math.PI;
}

int mAngle = Math.round(degrees);
int step = mAngle / 6;
if (mAngle - 6 * step > 0) {
    step++;
}
mAngle = 6 * step;

setAngle(mAngle);
mScrollListener.onScroll(mAngle);
invalidate();
}
```
项目地址:<https://github.com/ckj375/CustomTimer>
