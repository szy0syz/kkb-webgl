# kkb-webgl

## 基础

### webgl 是什么

webgl 是在网页上绘制和渲染三维图形的技术，可以让用户与其交互。

之前学过的 `div+css`、`canvas 2d` 都是专注于二维图形的，他们虽然也能模拟一部分三维效果，但他们和 `webgl` 比起来，那就是玩具枪和 `ak47` 的区别。

![ch01-001](assets/screenshots/ch01-001.jpg)

### webgl 行业背景

随着 5G 时代的到来，3D 可视化需求大量涌现。 3D 游戏、炫酷的活动宣传页、三维数字城市、VR 全景展示、3D 产品展示等领域中，很多项目都是用 `WebGL` 实现的，也只能用 `WebGL` 来做，也就是说， `WebGL` 的时代就在眼前了。

### webgl 坐标系

`webgl` 画布的建立和获取，和 `canvas 2d` 是一样的。

一旦我们使用 `canvas.getContext()` 方法获取了 `webgl` 类型的上下文对象后，那么这张画布就不再是以前的 `canvas 2d` 画图了。

当然它也不会变成三维的，因为我们电脑屏幕依然是平的。

那这张画布有什么不一样了呢？它的坐标系变化。

`canvas 2d` 画布和 webgl 画布使用的坐标系都是二维直角坐标系，只不过他们坐标原点、y 轴的坐标方向，、坐标基底都不一样了。

### `canvas 2d`画布的坐标系

- `canvas 2d` 坐标系的原点在左上角
- `canvas 2d` 坐标系的 y 轴方向是朝下的
- `canvas 2d` 坐标系的坐标基底有两个分量，分别是一个像素的宽和一个像素的高，即一个单位宽度便是一个像素的宽度，一个单位的高便是一个像素的高

![ch01-002](assets/screenshots/ch01-002.png)

### `webgl`的坐标系

- `webgl` 坐标系的坐标原点在画布中心
- `webgl` 坐标系的 y 轴方向是朝上的
- `webgl` 坐标基底中的两个分量分别是半个 `canvas` 的宽和半个 `canvas` 的高，即 1 个单位的宽便是半个 `canvas` 的宽，1 个单位的高便是半个 `canvas` 的高

![ch01-003](assets/screenshots/ch01-003.png)

### 用`webgl`画一个点

#### 绘图的基本步骤

![ch01-004](assets/screenshots/ch01-004.jpg)

点事最简单的图像，是几何图形最基本的组成部分。大概步骤如下：

1. 找一张画布
2. 找一只笔
3. 开始画画

以上这三步是从现实生活中抽离出来的普遍规律，这在哪里都适用。`canvas 2d` 的绘图逻辑也是这样。

首先要有 canvas 画布，然后通过 canvas 画布的 getContext('2d') 方法获取一支二维画笔，然后直接在画布上画画。就像这样：

```javascript
// canvas 画布
const canvas = document.getElementById('canvas');
// 二维画笔
const ctx = canvas.getContext('2d');
// 沾点画笔的颜色
ctx.fillStyle = 'red';
// 用画笔画一个矩形
ctx.fillRect(20, 20, 300, 20);
```

`webgl` 的绘图逻辑亦是如此，只不过它更像电脑绘画，其绘画的步骤还多了一层介质。这层介质就是手绘板，就像这样：

1. 找一台电脑
2. 找一块手绘板
3. 找一只触控笔
4. 开始画画

![ch01-005](assets/screenshots/ch01-005.jpg)

#### `canvas 2d`和`webgl`的绘图区别

> 我们的 `三维画笔` 是没法直接在 `二维画布` 上画画的 —— 因为画布不认！

在 `webgl` 里绘图，或许你会觉得也可以像 `canvas 2d` 那样，就像下面这样写：

```javascript

```

然而，实际上，webgl 的绘图逻辑 和 canvas 2d 的绘图逻辑还有一个本质区别。

我们在学习 html 的时候就应该知道，浏览器有三大线程：js 引擎线程、GUI 渲染线程、浏览器事件触发线程。

其中 GUI 渲染线程 就是用于绘图的，在这个渲染线程里，有负责不同渲染工作的工人。比如有负责渲染 `html+css` 的工人、有负责渲染二维图形的工人、有负责渲染三维图形的工人。

渲染二维图形的工人和渲染三维图形的工人不是一个国家的，因为他们说的语言不一样。

渲染二维图形的工人说的是 `JavaScript语言`

渲染三维图形的工人说的是 `GLSL ES语言`

而我们在做 web 项目时，业务逻辑、交互操作都是用 js 写的。

我们在用 js 绘制 `canvas 2d` 图形的时候，渲染二维图形的工人只认识 `js语言`， 所以他们可以正常渲染图形。

但我们用 js 渲染 `webgl` 图形时， 渲染三维图形的工人就不认识这个 js 语言了，因为它们只认识 `GLSL ES语言`。

因此，这个时候我们就需要招人翻译翻译这个 `三维语言`

这个做翻译的人是谁，它就是我们之前提到过的`手绘板`，它在 webgl 里叫 “程序对象”

#### `webgl` 的绘图思路

1. 找一台电脑 - 浏览器里内置的 `webgl渲染引擎`，负责渲染 `webgl图形`，只认 `GLSL ES语言`
2. 找一块手绘板 - 程序对象，承载 `GLSL ES语言`，翻译 `GLSL ES语言` 和 `js语言`，使两者可以相互通信
3. 找一支触控笔 - 通过 canvas 获取的 webgl 类型的上下文对象，可以向手绘板传递绘图命令，并接受手绘板的状态信息
4. 开始画画 - 通过 webgl 类型的上下文对象，用 js 画画

#### `webgl` 的绘图步骤

1、在 html 中建立 canvas 画布

```html
<canvas id="canvas"></canvas>
```

2、在 js 中获取 canvas 画布

```js
const canvas = document.getElementById('canvas');
```

3、使用 canvas 获取 webgl 绘图上下文

```js
const gl = canvas.getContext('webgl');
```

4、在 script 中建立定点着色器和偏远着色器 `glsl es`

```html
<!-- 顶点着色器 -->
<script id="vertexShader" type="x-shader/x-vertex">
  void main() {
    // 点位
    gl_Position = vec4(0,0,0,1);
    //尺寸
    gl_PointSize = 50.0;

  }
</script>

<!-- 片元着色器 -->
<script id="fragmentShader" type="x-shader/x-fragment">
  void main() {
    gl_FragColor = vec4(1, 1, 0, 1);
  }
</script>
```

> 定点着色器和片元着色器的区别： -定点着色器是衣架，片元着色器是衣服 -定点着色器是一个架子，而片元着色器是负责装饰这个架子的 -在实际中，这个架子(定点着色器)就是三维模型，其实没有任何色彩的，只有点位信息。而片元着色器就是基于定点信息来去填充定点之间的片元

5、在 js 中获取定点着色器和片元着色器

```js
const vsSource = document.getElementById('vertexShader').innerText;
const fsSource = document.getElementById('fragmentShader').innerText;
```



## 3-js向attribute 变量传参的原理

### 着色器中的attribute变量

```js
attribute vec4 a_Position;
void main(){
    gl_Position = a_Position;
    gl_PointSize = 50.0;
}
```

- attribute 是存储限定符，是专用用于向外部导出与定位相关的对象，这类似与es6模板语法中的export
- vec4 是变量类型，vec4 是4维矢量对象
- a_Position 是变量名，之后在 js 中会根据这个变量名导入变量。这个变量名是一个指针，指向实际数据的存储位置，也就是说，我们如果在着色器外部改变了 a_Position 所指向的实际数据，那么在着色器中 a_Position 所对应的数据也会修改。

### 在js中获取attribute变量

我们再 js 里不能直接写 a_Position 来获取着色器变量
因为着色器和js是两个不同的语种，着色器无法通过 window.a_Position 属性向全局暴露变量
那我们就要在js 里获取着色器暴露的变量，就需要找人来翻译，这个人就是 `陈允许对象`

```js
const a_Position=gl.getAttribLocation(gl.program,'a_Position');
```

- gl 是 `webgl` 的上下文对象
- `gl.getAttribLocation()` 是获取着色器中的 attribute 变量的方法
- `getAttribLocation()` 方法的参数中：
  - `gl.program` 是初始化着色器时，在山西该文对象上挂载的程序对象
  - `a_Position` 是着色器暴露出的变量名

### 在 `js` 中修改 `attribute` 变量

`attribute` 变量即使在js中获取了，他也只是一个只会说 `GLSL ES` 语言的人，他不认识 `js` 语言，所以我们不能用 `js` 的语法来修改 `attribute` 变量的值：

```js
a_Position.a=1.0
```

我们得用特定的方法来改变 `a_Position` 的值：

```js
gl.vertexAttrib3f(a_Position,0.0,0.5,0.0);
```

- gl.vertexAttrib3f() 是改变变量值的方法。
- gl.vertexAttrib3f() 方法的参数中：

  - a_Position 就是咱们之前获取的着色器变量。

  - 后面的3个参数是顶点的x、y、z位置

`a_Position` 被修改后，我们就可以使用上下文对象绘制最新的点位了

```js
gl.clearColor(0.0, 0.0, 0.0, 1.0);
gl.clear(gl.COLOR_BUFFER_BIT);
gl.drawArrays(gl.POINTS, 0, 1);
```

### vertexAttrib3f()的同族函数

`gl.vertexAttrib3f(location,v0,v1,v2)` 方法是一系列修改着色器中的 `attribute` 变量的方法之一，它还有许多同族方法，如：

```js
gl.vertexAttrib1f(location,v0) 
gl.vertexAttrib2f(location,v0,v1)
gl.vertexAttrib3f(location,v0,v1,v2)
gl.vertexAttrib4f(location,v0,v1,v2,v3)
```

它们都可以改变attribute 变量的前n 个值。

比如 vertexAttrib1f() 方法自定一个矢量对象的v0值，v1、v2 则默认为0.0，v3默认为1.0，其数值类型为float 浮点型。

### webgl 函数的命名规律

GLSL ES里函数的命名结构是：<基础函数名><参数个数><参数类型>

以vertexAttrib3f(location,v0,v1,v2,v3) 为例：

- vertexAttrib：基础函数名
- 3：参数个数，这里的参数个数是要传给变量的参数个数，而不是当前函数的参数个数
- f：参数类型，f 代表float 浮点类型，除此之外还有i 代表整型，v代表数字……

关于用js 控制点位的方法咱们就说到这，接下咱们说一个用鼠标控制点位的例子。



## 用鼠标控制点位

> 要用鼠标控制一个点的位置，首先要知道鼠标点在webgl 坐标系中的位置，这样才能让一个点出现在我们鼠标点击的位置。

### 获取鼠标点在webgl 坐标系中的位置

对于鼠标点在webgl 坐标系中的位置，我们是无法直接获取的。所以我们得先获取鼠标在canvas 这个DOM元素中的位置。

#### 获取鼠标在canvas 画布中的css 位置

```js
canvas.addEventListener('click',function(event){
    const {clientX,clientY}=event;
    const {left,top}=canvas.getBoundingClientRect();
    const [cssX,cssY]=[
        clientX-left,
        clientY-top
    ];
})
```

对于cssX,cssY 的获取，大家应该都不陌生，这在canvas 2d 也会用到。

我们可以用向量减法来求解。

已知：向量a(clientX,clientY)，向量c(left,top)

求：向量c

解：

由向量的减法得：向量a减向量c，等于以向量c 的终点为起点，以向量a的终点为终点的向量c

所以：向量c=a-c=(clientX-left,clientY-top)

将向量c 视之为坐标点c，那点c 就是鼠标在canvas 画布中的css 位。

因为html 坐标系中的坐标原点和轴向与canvas 2d是一致的，所以在我们没有用css 改变画布大小，也没有对其坐标系做变换的情况下，鼠标点在canvas 画布中的css 位就是鼠标点在canvas 2d坐标系中的位置。









### `webgl`的同步绘图原理

`canvas2d` 可能会认为无法画出多点是 gl.clear(gl.COLOR_BUFFER_BIT)清理画布导致，因为我们在用 `canvas2d` 做动画时，其中就有一个 `ctx.clearRect()` 清理画布的方法。

`gl.drawArrays(gl.POINTS,0,1)` 方法和 `canvas 2d` 里的 `ctx.draw()` 方法是不一样的，`ctx.draw()` 是真的像画画一样，一层一层的覆盖图像。

`gl.drawArrays()` 方法只会同步绘图，走完了js主线程后，再次绘图时，就会从头再来。也就是说我们异步执行的`gl.drawArrays()` 方法会把画布上的图像都刷掉。











