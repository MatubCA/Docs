# CSS



## 选择器

### 1.四种基本选择器

#### 标签选择器

作用：选中**同一类**标签。

格式：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    
    <style type="text/css">
        p{
            color: aqua;
            font-size: 30px;
            font-family: 微软雅黑;
        }
    </style>

</head>
<body>
    <p>山河无恙，故人相逢</p>
</body>
</html>
```

#### id 选择器

作用：选择具有特定 id 值的**一个**标签。

格式：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    
    <style type="text/css">
        #div1{
            width: 0px;
            height: 0px;
            border-top: 30px solid red;
            border-left: 20px solid transparent;
            border-right: 20px solid transparent;
        }
    </style>

</head>
<body>
    <div id="div1"></div>
</body>
</html>
```

什么标签能添加 id 属性呢？

所有的html标签都能添加 id 属性，id 属性值可以任意取，但有以下限制：

1. 只能有字母、数字、下划线。
2. 必须以字母开头。
3. 不能和标签同名。比如 id 不能叫做 body、img、a。

注意：id 属性值不能重复，即使两个标签是不同的类型。

#### 类选择器

作用：选择所有具有相同 class 属性值的标签。

格式：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    
    <style type="text/css">
        .class1{
            color: brown;
        }
        .class2{
            font-size: 20px;
        }
    </style>

</head>
<body>
    <p class="class1" class="class2">我走过你走过的路</p>
    <div class="class1">吹过你吹过地方晚风</div>
</body>
</html>
```

什么标签能添加 class 属性呢？

所有标签都可以添加 class 属性。

class 属性和 id 属性的区别：

1. 多个标签可以设置相同的 class 属性。
2. 同一个标签可以设置多个 class 属性。

在实际开发中，使用 id 还是 class？

在CSS中尽量使用 class，因为 id 会被JS所使用的，容易引起混乱。

**对于 class属性的理解，应该将其看做一个接口，实现了该接口就会有相应样式的实现。**

#### 通用选择器

作用：选择所有标签。`*`代表了通配符。

格式：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    
    <style type="text/css">
        *{
            font-style: italic;
        }
    </style>

</head>
<body>
    <p>山河无恙，故人相逢</p>

    <div id="div1"></div>

    <p class="class1" class="class2">我走过你走过的路</p>
    <div class="class1">吹过你吹过地方晚风</div>
</body>
</html>
```

不建议使用，IE的一些版本并不支持，效率也不高，标签越多，效率越底。



### 2.四种高级选择器

#### 后代选择器

作用：利用标签的嵌套，能更加精准的匹配某些标签，并且描述了一种祖先结构。

格式：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    
    <style type="text/css">
        /*空格相隔*/
        h3 b i{
            color: aqua;
        }
        h3 i{
            font-style: normal;
        }  
    </style>

</head>
<body>
    <h3>
        <b>
            <i>你最好有事！</i>
        </b>
    </h3>
</body>
</html>
```

理解：利用多个标签的嵌套，更精准、更高效的找到某些符合条件的标签。

为什么说是描述了一种祖先的结构呢？

后代选择器中相邻的标签，不一定具有直接的嵌套相邻关系，所以对于`h3 b i`我们唯一确定的是`<h3>`中嵌套`<b>`，`<b>`中嵌套 `<i>`，但它们三者之间可能还隔着多层嵌套关系。

#### 交集选择器

作用：在 id 选择器和类选择器之前添加标签名，使更精准、更高效的查找到目标。

格式：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    
    <style type="text/css">
        h3.class1{
            font-size: 20px;
            color: red;
            font-style: italic;
        }

        h3#id1{
            font-size: 30px;
            color: green;
            font-style: italic;
        } 
    </style>

</head>
<body>
    <h3 class="class1">今天风和日丽</h3>
    <h3 id="id1">吃了两大碗饭</h3>
    <h3>美滋滋</h3>
</body>
</html>
```

#### 并集选择器

作用：将标签选择器、id选择器和类选择器进行组合，对于内容的选择更加灵活。

格式：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    
    <style type="text/css">
        /*逗号相隔*/
        p,
        #div1,
        .class1,
        .class2{
            color: aqua;
            font-family: weiruanya;
        }
    </style>

</head>
<body>
    <p>山河无恙，故人相逢</p>

    <div id="div1"></div>

    <p class="class1" class="class2">我走过你走过的路</p>
    <div class="class1">吹过你吹过地方晚风</div>
</body>
</html>
```

#### 伪类选择器

作用：对CSS选择器的补充，找出哪些普通CSS选择器无法定位的元素。

什么是伪类？

**同一个标签根据其不同的状态，有不同的样式**。比如：某一个类型的第一个元素，超链接的点击前、鼠标悬浮、按住不松手、点击后等状态。这些都是普通的CSS选择器无法选择到的。

伪类分为两种：

1. 静态伪类：作用于超链接（不包括锚点）。
   - `:link`： 超链接点击之前
   - `:visited`：超连接访问之后
2. 动态伪类：作用于所有标签。
   - `:hover`：悬停，鼠标放在标签之上
   - `:active`：激活，鼠标点击标签但不松手
   - `:focus`：某个标签获得焦点时（比如输入框获得焦点）

静态伪类：

````html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    
    <style type="text/css">
        a:link{
            color:aqua;
        }
        a:hover{
            color: black;
        }
        
        a:active{
            color: blue;
        }
        a:visited{
            color: brown;
            
        }
    </style>

</head>
<body>
    <a href="baidu.com">百度一下</a>
</body>
</html>
````

示例：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>

    <style type="text/css">
        *{
            padding: 0px;
            margin: 0px;
        }
        .nav{
            width: 600px;
            height: 60px;
            border: 1px solid rgb(104, 12, 158);
        }
        .nav ul{
            list-style: none;
        }
        .nav ul li{
            float: left;
            width: 100px;
            height: 60px;
            /* 垂直居中 */
            line-height: 60px;
            /* 水平居中 */
            text-align: center;
        }
        .nav ul li a{
            /* 填充块级元素 */
            display: block;
            text-decoration: none;
            font-size: 30px;
            font-family: 微软雅黑;
            width: 100px;
            height: 60px;
           
        }
        /* 两个伪类之间使用逗号隔开 */
        .nav ul li a:link,.nav ul li a:visited{
            color:black;
            background-color: rgb(107, 15, 168);
        }
        .nav ul li a:hover{
            background-color:rgb(194, 168, 23) ;
        }

    </style>
</head>
<body>
    <div class="nav">
        <ul>
            <li><a href="#">博客园</a></li>
            <li><a href="#">首页</a></li>
            <li><a href="#">开源</a></li>
            <li><a href="#">归档</a></li>
            <li><a href="#">新随笔</a></li>
            <li><a href="#">管理</a></li>
        </ul>
    </div>
</body>
</html>
```

动态伪类：

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>

    <style type="text/css">
        table{
            border: 1px solid red;
            width: 900px;
            height: 900px;
            border-collapse: collapse;
        }
        table td{
            border: 1px solid red;
        }
        table td:hover{
            background-color: black;
        }
    </style>
</head>

<body>
    <table>
        <tr>
            <td></td>
            <td></td>
            <td></td>
        </tr>
        <tr>
            <td></td>
            <td></td>
            <td></td>
        </tr>
        <tr>
            <td></td>
            <td></td>
            <td></td>
        </tr>
    </table>
</body>

</html>
```

### 3.CSS3选择器





## 盒模型









## 动画

### 1.过渡

作用：实现同一标签不同状态间的平滑过渡。

根据中间状态的不同，分为两种：

1. 补间动画：自动完成起始状态到终止状态的转变，不用管中间状态。
2. 帧动画：通过一帧一帧的画面按照一定顺序和速度播放。（胶卷电影）

属性：

- `transition-property`：希望发生过渡的属性，如果所有属性都发生过渡，则使用 all。
- `transition-duration`：过渡持续的时间
- `transition-timing-function`：运动曲线，属性值为：
  - `linear`：线性
  - `ease`：匀速
  - `ease-in`：加速
  - `ease-out`：减速
  - `ease-in-out`：先加速后减速
- `transition-delay`：过渡延迟，表示间隔时间。

综合属性：

多个属性使用逗号隔开。

```css
// 执行过渡的属性 过渡持续时间 运动曲线 延迟时间 
transition: width 2s ease-in  ,height 2s ease-in-out;
```

示例：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>

    <style type="text/css">
        .main{
            width: 200px;
            height: 200px;
            background-color: green;

            transition: width 2s ease-in  ,height 2s ease-in-out;
        }
        .main:hover{
            width: 500px;
            height: 500px;
        }
    </style>
</head>
<body>
    <div class="main"></div>
</body>
</html>
```

案例：

```html
```



### 2.2D转换

### 3.3D转换

### 4.动画