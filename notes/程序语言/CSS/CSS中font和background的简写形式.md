# CSS中font和background的简写形式

经常使用到CSS， 其中的 font 和 background 属性可以有多个更详细的关联样式属性，但也可以用这两个进行简写， 很多时候我都忘了他们的简写的格式，所以网上找了一些，写下来，方便日后直接参考...

## 1 字体属性主要包括下面几个

`font-family`，`font-style`，`font-variant`，`font-weight`，`font-size`，`font`

```
font-family（字体族）: “Arial”、“Times New Roman”、“宋体”、“黑体”等;
font-style（字体样式）: normal（正常）、italic（斜体）或oblique（倾斜）;
font-variant (字体变化): normal（正常）或small-caps（小体大写字母）;
font-weight (字体浓淡): 是normal（正常）或bold（加粗）。有些浏览器甚至支持采用100到900之间的数字（以百为单位）;
font-size（字体大小）: 可通过多种不同单位（比如像素或百分比等）来设置, 如：12xp，12pt，120%，1em
```

如果用 font 属性的话，就可以把几个样式进行简化，减少书的情况；font 属性的值应按以下次序书写(各个属性之间用空格隔开)：

    font-style | font-variant | font-weight | font-size | font-family

例如：

```css
.fontStyle01{
        font-style: italic;
        font-variant: normal
        font-weight: bold;
        font-size: 30px;
        font-family: arial, sans-serif;
}
```

上面的样式简写为：

    .fontStyle01{font:italic normal bold 12px arial,verdana;}

## 2 背景

```css
background-color: #999999; //元素的背景色
background-image: url("path/bgFile.gif"); //设置背景图像
background-repeat: repeat-x | repeat-y | repeat | no-repeat; //设置重复方式
background-attachment: fixed | scroll; //设置背景图片的固定方式
background-position: X轴坐标,Y轴坐标[top,bottom,center,left,right,20px,10%]; //设置背景的左上角位置,坐标可以是以百分比或固定单位
```

background 可以用这个属性把前面几个综合起来进行简写,

background 各个值的次序依次如下：

    background-color | background-image | background-repeat | background-attachment | background-position

如果省略某个属性不写出来，那么将自动为它取缺省值。比如，如果去掉background-attachment和background-position的话：

    background: #FFCC66 url("butterfly.gif") no-repeat;

例如：

```css
.bg01{
   background-color: #FFCC66;
   background-image: url("path/bgFile.gif");
   background-repeat: no-repeat;
   background-attachment: fixed;
   background-position: left top;
}
```

上面可以简写为:

    .bg01{background:#FFCC66 url("path/bgFile.gif") no-repeat fixed left top; }

---
via: https://blog.csdn.net/shenzhenNBA/article/details/7356095
