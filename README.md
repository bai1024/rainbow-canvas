### 知识点梳理
# `<canvas>`
一、基本用法
1. 可以理解为一个画布，其大小需要在标签中直接定义。由于canvas本身没有绘图能力，所以所有的绘图工作都是由js来实现的。
2. `<canvas>`元素有一个做`getContext()`的方法，这个方法是用来获得渲染上下文和它的绘画功能。getContext()只有一个参数，上下文的格式(目前只支持“2D”环境)。 
```javascript
const canvas = document.querySelector('#draw');//获取canvas元素
const ctx = canvas.getContext('2d');//获取该canvas的2D绘图环境对象
```

二、绘制形状/路径
- `stroke()`:通过线条绘制图形轮廓
- `beginPath()`:新建一条路径
- `moveTo()`:起始位置
- `lineTo()`:直线结束的点

三、添加样式和颜色
- `strokeStyle()`:设置图形轮廓的颜色
- `lineWidth()`:设置线的宽度
- `lineCap()`:线段端点显示的样子。有三种样式butt(平),round(圆)和square(方)。默认是butt
- `lineJoin()`:图形中两线段连接处所显示的样子。round(圆交),bevel(斜交)和miter(斜接)。默认是 miter

#HSL色彩模式
一、什么是HSL?
>HSL色彩模式使用HSL模型为图像中每一个像素的HSL分量分配一个0~255范围内的强度值。HSL图像只使用三种通道，就可以使它们按照不同的比例混合，在屏幕上重现16777216种颜色。

在线体验不同的hsl产生的不同效果：http://mothereffinghsl.com/

二、HSL的含义。
H：Hue(色调)。0(或360)表示红色，120表示绿色，240表示蓝色，也可取其他数值来指定颜色。取值为：0 - 360；
S：Saturation(饱和度)。取值为：0.0% - 100.0%；
L：Lightness(亮度)。取值为：0.0% - 100.0%；
这之中 H 值从 0 到 360 的变化代表了色相的角度的值域变化，利用这一点就可以实现绘制时线条颜色的渐变了，只需要在它的值超过 360 时恢复到 0 重新累加即可。
```javascript
let hue = 0;
ctx.strokeStyle = `hsl(${ hue }, 100%, 50%)`;    
if(hue >=360){
  hue = 0
}
hue++
```

### 项目实现中的关键点
一、点击鼠标后书写。
这一部分我们可以先分成两部分分析，第一部分就是事件的监听。
从点击准备，拖动鼠标开始书写到放开鼠标结束绘制。第二部分就是`canvas`绘制的部分。
1. 事件监听
从上面的分析中得知这一部分我们需要监听三个不同的事件，首先鼠标移动绘制样式这个事件是肯定的，因此我们可以直接写出这一部分的代码。
```javascript
canvas.addEventListener('mousemove',draw)
```
但是如果直接这样写的话会出现一个问题，就是只要鼠标滑过界面，就会绘制图案，这与我们的初衷相悖，因此我们还需要加一个判断，即，在鼠标按下的情况下拖动鼠标才可以绘制图形。
```javascript
let isDrawing = false;//标记变量
canvas.addEventListener('mousedown', isDrawing = true);
canvas.addEventListener('mousemove', draw);
canvas.addEventListener('mouseup', () => isDrawing = false);
canvas.addEventListener('mouseout', () => isDrawing = false); // 鼠标移出画布范围时
```
2.canvas绘制
鼠标点击拖动时触发的函数
```javascript
ctx.beginPath();
ctx.moveTo(lastX,lastY);
ctx.lineTo(e.offsetX,e.offsetY);
ctx.stroke();
lastX = e.offsetX;
lastY = e.offsetY;
```
二、线条衔接问题
在上面一点中，已经提到了鼠标拖动事触发的函数，但是在运行中却会出现下图中的情况。
![](http://ok7n02kz6.bkt.clouddn.com/FjnXtXNdkrTvnqa9UBmjMU34LeLk.gif)
出现这种情况的原因是因为`lastX`与`e.offsetX`的值是一样的。可以看一下console.log的打印结果。
![](http://ok7n02kz6.bkt.clouddn.com/FgQ9DlwBh8RnUF74rtSW2bfCwreu.gif)
所以只要在`mouseup`事件中更新一下`lastX`的值就可以了。现在再来看一下打印结果：
![](http://ok7n02kz6.bkt.clouddn.com/FgAdrWSKVrrLCGTe6t9yr6HZ5Zy8.png)