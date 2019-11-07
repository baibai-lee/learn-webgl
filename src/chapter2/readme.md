# 第2章 WebGL入门

##### 绘制2D图形的步骤

1. 获取`canvas`元素
2. 向该元素请求二维图形的 “绘图上下文”
3. 在绘图上下文中调用绘制函数 

##### canvas坐标系统

原点在左上角，水平向右为 X 轴正方向，垂直向下为 Y 轴正方向，如图

![2.5](/assets/img/2.5.png)

##### 着色器是什么？

顶点着色器 --- 描述顶点特征，如位置、颜色等。顶点是二维或三维空间中的一个点

片元着色器 --- 进行逐片元处理过程

![2.10](/assets/img/2.10.png)

##### WebGL坐标系统

通常，在WebGL中，当你面向计算机屏幕时，X轴是水平的(正方向为右), Y轴是垂直的(正方向为下)，而Z轴垂直于屏幕(正方向为外)。观察者的眼睛位于原点(0.0, 0.0, 0.0)处，视线则是沿着Z轴的负方向，从你指向屏幕(图2.16右)，这套坐标系又被称为右手坐标系，如图

![2.16](/assets/img/2.16.png)

##### WebGL坐标系统与canvas坐标系统如何映射？

![2.18](/assets/img/2.18.png)

##### 使用变量向shader程序中传值

attribute变量 --- 传输顶点相关的数据

uniform变量 --- 传输对所有顶点都相同的数据（或者叫与顶点无关数据）

##### 例2.7 鼠标点击绘制点中的坐标转换

```javascript
var g_points = []; // 鼠标点击位置数组
function click(ev, gl, canvas, a_Position) {
  var x = ev.clientX; // x 坐标
  var y = ev.clientY; // y 坐标
  // getBoundingClientRect返回 元素的大小及其相对于视口的位置
  var rect = ev.target.getBoundingClientRect();
  // console.log(rect)

  x = ((x - rect.left) - canvas.width / 2) / (canvas.width / 2);
  y = (canvas.height / 2 - (y - rect.top)) / (canvas.height / 2);
  // 暂存鼠标位置信息
  g_points.push(x);
  g_points.push(y);
  // console.log(g_points)

  // 清除canvas
  gl.clear(gl.COLOR_BUFFER_BIT);

  var len = g_points.length;
  for (var i = 0; i < len; i += 2) {
    // 将点的位置传给 a_Position
    gl.vertexAttrib3f(a_Position, g_points[i], g_points[i + 1], 0.0);

    // 绘制点
    gl.drawArrays(gl.POINTS, 0, 1);
  }
}
```

![2.26](/assets/img/2.26.png)

首先，获取`<canvas>`在浏览器客户区中的坐标, `rect.1eft` 和`rect.top` 
是`<canvas>`的原点在浏览器客户区中的坐标，如图所示，这样`(x-rect.left)`  和 
`(y-rect.top)`就可以将客户区坐标系下的坐标(x, y)转换为`<canvas>`坐标系下的坐标了

接下来，将`<canvas>`坐标系下的坐标转换到WebGL坐标系统中，如图所示，
为了进行这一步转换，你需要知道`<canvas`的中心点。我们通过`canvas.height` (这里
是400)和`canvas.width `(也是400)获取`<canvas>`的宽度和高度，而中心点的坐标是
`(canvas. height/2, canvas.width/2)`  

然后，你就可以使用`((x-rect .left)-canvas.width/2)` 和 `(canvas .height/2- (y-
rect.top))` 将 `<canvas>`的原点平移到中心点(WebGL 坐标系统的原点位于此处)

接着，如图所示，`<canvas>` 的x轴坐标区间为从0到`canvas.width (400)`， 而
其y轴区间为从0到`canvas .height(400)`。因为WebGL中轴的坐标区间为从-1.0到1.0,
所以最后一步我们将x坐标除以`canvas.width/2`,将y坐标除以`canvas.height/2`,将
`<canvas>`坐标映射到WebGL坐标