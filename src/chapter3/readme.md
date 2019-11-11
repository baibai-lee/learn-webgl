# 第3章 绘制和变换三角形

##### webgl绘制三角形顶点的过程

```javascript
function initVertexBuffers(gl) {
  var vertices = new Float32Array([0.0, 0.5, -0.5, -0.5, 0.5, -0.5]);
  var n = 3; // 点的个数

  // 创建缓冲区对象
  var vertexBuffer = gl.createBuffer();
  if (!vertexBuffer) {
    console.log('Failed to create the buffer object');
    return -1;
  }

  // 将缓冲区对象绑定到目标
  gl.bindBuffer(gl.ARRAY_BUFFER, vertexBuffer);
  // 向缓冲区对象中写入数据
  gl.bufferData(gl.ARRAY_BUFFER, vertices, gl.STATIC_DRAW);

  var a_Position = gl.getAttribLocation(gl.program, 'a_Position');
  if (a_Position < 0) {
    console.log('Failed to get the storage location of a_Position');
    return -1;
  }

  // 将缓冲区对象分配给 a_Position 变量
  gl.vertexAttribPointer(a_Position, 2, gl.FLOAT, false, 0, 0);

  // 连接 a_Position 变量与分配给他的缓冲区对象
  gl.enableVertexAttribArray(a_Position);

  return n;
}
```

这个函数接收一个webgl的上下文对象作为参数，返回将要绘制的点的个数，出错时返回-1

- 首先创建存储坐标点的数组
- 再根据上一步的数组创建缓冲区对象
- 将缓冲区绑定在传入的上下文对象上
- 向缓冲区中写入数据
- 获取顶点着色器中声明的变量的地址，并将缓冲区对象分配给这个变量
- 连接上一步的变量与缓冲区对象，使着色器能够读取缓冲区中的数据

**NOTE:**这个函数违背了单一职责原则，容易造成误解，学习时务必仔细理解其中的过程

##### webgl可绘制的基本图形

| 基本图形 | 参数 mode      |
| -------- | -------------- |
| 点       | gl. POINTS     |
| 线段     | gl. LINES      |
| 线条     | gl.LINE_STRIP |
| 回路     | gl.LINE_LOOP |
| 三角形   | gl.TRIANGLES  |
| 三角带 | gl .TRIANGLE_STRIP |
| 三角扇 | gl .TRIANGLE_FAN |

**绘制策略：** 

- **点**。一系列点，绘制在v0, v1,v2 .....处
- **线段**。一系列连接的线段,被绘制在(v0,v1), (v2,v3),(v4,v5)......处，如果点的个数是奇数，最后一个点将被忽略
- **线条**。一系列连接的线段,被绘制在(v0,v1), (v1,v2), (v2,v3).......处，第1个点是第1条线段的起点，第2个点是第1条线段的终点和第2条线段的起点....第`1(i>1)`个点是第`i-1`条线段的终点和第1条线段的起点，以此类推。最后一个点是最后一条线段的终点
- **回路**。一系列连接的线段。与gI.LINE_STRIP 绘制的线条相比，增加了一条从最后一个点到第1个点的线段。因此，线段被绘制在(v0,v1), (v1,v2)......(vn,v0) 处，其中vn是最后一个点
- **三角形**。一系列单独的三角形，绘制在(v0,v1,v2). (v3,v4,v5) .....处。如果点的个数不是3的整数倍，最后剩下的一或两个点将被忽略
- **三角带**。一系列条带状的三角形，前三个点构成了第1个三角形，从第2个点开始的三个点构成了第2个三角形(该三角形与前一个三角形共享一条边)，以此类推。这些三角形被绘制在(v0,vI,v2)、(v2,v1,v3)、 (v2,v3,v4).....处 (注意点的顺序)
- **三角扇**。一系列三角形组成的类似于扇形的图形。前三个点构成了第1个三角形，接下来的一个点和前一个三角形的最后一条边組成接下来的一个三角形。这些三角形被绘制在(v0,v1,v2), (v0,v2,v3)、(v0,v3,v4)......处

如图：

![3.13](/Users/lee/Desktop/Development/webgl-learn/assets/img/3.13.png)

#### 移动、旋转和缩放

// TODO 认真学习线性代数

