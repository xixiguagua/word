absolute    

生成绝对定位的元素，相对于 static 定位以外的第一个父元素进行定位。

元素的位置通过 "left", "top", "right" 以及 "bottom" 属性进行规定。


fixed   

生成绝对定位的元素，相对于浏览器窗口进行定位。

元素的位置通过 "left", "top", "right" 以及 "bottom" 属性进行规定。


relative    

生成相对定位的元素，相对于其正常位置进行定位。

因此，"left:20" 会向元素的 LEFT 位置添加 20 像素。


static  默认值。没有定位，元素出现在正常的流中（忽略 top, bottom, left, right 或者 z-index 声明）。


display:

none    此元素不会被显示。

block   此元素将显示为块级元素，此元素前后会带有换行符。

inline  默认。此元素会被显示为内联元素，元素前后没有换行符。

inline-block    行内块元素。（CSS2.1 新增的值）

list-item   此元素会作为列表显示。

run-in  此元素会根据上下文作为块级元素或内联元素显示。

compact CSS 中有值 compact，不过由于缺乏广泛支持，已经从 CSS2.1 中删除。

marker  CSS 中有值 marker，不过由于缺乏广泛支持，已经从 CSS2.1 中删除。

table   此元素会作为块级表格来显示（类似 <table>），表格前后带有换行符。

inline-table    此元素会作为内联表格来显示（类似 <table>），表格前后没有换行符。

table-row-group 此元素会作为一个或多个行的分组来显示（类似 <tbody>）。

table-header-group  此元素会作为一个或多个行的分组来显示（类似 <thead>）。

table-footer-group  此元素会作为一个或多个行的分组来显示（类似 <tfoot>）。

table-row   此元素会作为一个表格行显示（类似 <tr>）。

table-column-group  此元素会作为一个或多个列的分组来显示（类似 <colgroup>）。

table-column    此元素会作为一个单元格列显示（类似 <col>）

table-cell  此元素会作为一个表格单元格显示（类似 <td> 和 <th>）

table-caption   此元素会作为一个表格标题显示（类似 <caption>）

inherit 规定应该从父元素继承 display 属性的值。



获取元素实际样式信息时，应使用 getComputedStyle 或 currentStyle。

通过 style 只能获得内联定义或通过 JavaScript 直接设置的样式。通过 CSS class 设置的元素样式无法直接通过 style 获取。


样式设置

 尽可能通过为元素添加预定义的 className 来改变元素样式，避免直接操作 style 设置。

通过 style 对象设置元素样式时，对于带单位非 0 值的属性，不允许省略单位。

优先使用 addEventListener / attachEvent 绑定事件，避免直接在 HTML 属性中或 DOM 的 expando 属性绑定事件处理。

解释：

expando 属性绑定事件容易导致互相覆盖。

 使用 addEventListener 时第三个参数使用 false。

解释：

标准浏览器中的 addEventListener 可以通过第三个参数指定两种时间触发模型：冒泡和捕获。而 IE 的 attachEvent 仅支持冒泡的事件触发。所以为了保持一致性，通常 addEventListener 的第三个参数都为 false。

 在没有事件自动管理的框架支持下，应持有监听器函数的引用，在适当时候（元素释放、页面卸载等）移除添加的监听器。

word-spacing

定义 
word-spacing 属性增加或减少单词间的空白（即字间隔）。 

该属性定义元素中字之间插入多少空白符。针对这个属性，“字” 

定义为由空白符包围的一个字符串。如果指定为长度值，会调整字之间的通常间隔；

所以，normal 就等同于设置为 0。允许指定负长度值，这会让字之间挤得更紧。 

注释：允许使用负值。

white-space

定义和用法

white-space 属性设置如何处理元素内的空白。 

这个属性声明建立布局过程中如何处理元素中的空白符。值 pre-wrap 和 pre-line 是 CSS 2.1 中新增的。


可能的值
    值	       描述
    normal	默认。空白会被浏览器忽略。
    pre	空白会被浏览器保留。其行为方式类似 HTML 中的 <pre> 标签。
    nowrap	文本不会换行，文本会在在同一行上继续，直到遇到 <br> 标签为止。
    pre-wrap	保留空白符序列，但是正常地进行换行。
    pre-line	合并空白符序列，但是保留换行符。
    inherit	规定应该从父元素继承 white-space 属性的值。


左侧固定右侧自适应宽度的两列布局

用两种不同的方法来实现一个两列布局，其中左侧部分宽度固定、右侧部分宽度随浏览器宽度的变化而自适应变化 

我的方法一：

不使用浮动，使用绝对定位，将左上角的块放好位置，右边的块设置margin-left

html 文件：

    <!DOCTYPE html>
    <html lang="en">
        <head>
            <meta charset="UTF-8">
            <title>layout</title>
            <link rel="stylesheet" type="text/css" href="task0001-6-3.css">
        </head>
        <body>
            <div class="row">
                <div class="left">DIV-A</div>
                <div class="right">DIV-B</div>
            </div>
            <div class="bottom">DIV-C</div>
        </body>
    </html>


    .row {
        position: relative;
    }
    .left {
        width: 100px;
        height: 100px;
        background-color: red;
        position: absolute;
        top: 0;
        left: 0;
    }
    .right {
        height: 100px;
        background-color: blue;
        margin-left: 100px;
    }
    .bottom {
        height: 100px;
        background-color: yellow;
    }

方法二：

使用浮动，左边的块使用浮动，右边的块使用margin-left

    .left {
        width: 100px;
        height: 100px;
        background-color: red;
        float: left;
    }
    .right {
        height: 100px;
        background-color: blue;
        margin-left: 100px;
    }
    .bottom {
        height: 100px;
        background-color: yellow;
    }


补充：

 BFC 

    <!DOCTYPE html>
    <html lang="en">
        <head>
            <meta charset="UTF-8">
            <title>使用 BFC 进行两列布局</title>
            <link rel="stylesheet" href="two-col-layout-with-BFC.css">
        </head>
        <body>
            <div class="left">DIV-A</div>
            <div class="right">DIV-B</div>
            <div class="bottom">DIV-C</div>
        </body>
    </html>


    .left{
        width: 100px;
        height: 100px;
        background-color: blue;
        float: left;
    }
    .right{
        height: 100px;
        background-color: yellow;
        overflow: hidden;
    }
    .bottom{
        height: 100px;
        background-color: red;
    }


双飞翼布局

用两种不同的方式来实现一个三列布局，其中左侧和右侧的部分宽度固定，中间部分宽度随浏览器宽度的变化而自适应变化



    <!DOCTYPE html>
    <html lang="en">
        <head>
            <meta charset="UTF-8">
            <title>Flying Swing Layout</title>
            <link rel="stylesheet" type="text/css" href="task0001-6-4.css">
        </head>
        <body>
            <div class="bd">
                <div class="main">
                    <div class="main-wrap">
                        <p>Flying Swing Layout</p>
                    </div>
                </div>
                <div class="sub">
                    <p>Flying Swing Layout</p>
                    left
                </div>
                <div class="extra">
                    <p>Flying Swing Layout</p>
                    right
                </div>
            </div>
        </body>
    </html>

        .bd {

            /*padding: 0 190px;*/
        }
        .main {
            float: left;
            width: 100%;
            background-color: #aaa;
        }
        .main-wrap {
            margin: 0 190px;
        }
        .sub {
            float: left;
            width: 190px;
            margin-left: -100%;
            background-color: blue;        
            /*position: relative;
            left: -190px;*/
        }
        .extra {
            float: left;
            width: 190px;
            margin-left: -190px;
            background-color: yellow;        
            /*position: relative;
            right: -190px;*/
        }


补充：

使用 BFC 的另一种方法



            <div class="left">left</div>
            <div class="right">right</div>
            <div class="main">
                flying-Swing-BFC.htmlflying-Swing-BFC.htmlflying-Swing-BFC.htmlflying-Swing-BFC.htmlflying-Swing-BFC.htmlflying-Swing-BFC.htmlflying-Swing-BFC.htmlflying-Swing-BFC.htmlflying-Swing-BFC.htmlflying-Swing-BFC.htmlflying-Swing-BFC.html
            </div>
            <div class="footer">
                footerfooterfooterfooterfooterfooterfooterfooterfooter
            </div>


    .left{
        width: 100px;
        background-color: red;
        float: left;
    }
    .right{
        width: 200px;
        background-color: blue;
        float: right;
    }
    .main{
        background-color: #eee;
        overflow: hidden;
    }


浮动布局

实现一个浮动布局，红色容器中每一行的蓝色容器数量随着浏览器宽度的变化而变化 picpic
这个题我觉的直接将每一个块浮动起来就好了，不知我理解的对不对。

        <body>
            <div></div>
            <div></div>
            <div></div>
            <div></div>
            <div></div>
            <div></div>
            <div></div>
            <div></div>
            <div></div>
        </body>

    body {
        background-color: red;
    }
    div {
        width: 150px;
        height: 100px;
        margin: 10px;
        float: left;
        background-color: blue;
    }


清除浮动/闭合浮动

清除浮动：清除对应的单词是 clear，对应CSS中的属性是 clear：left | right | both | none；

闭合浮动：更确切的含义是使浮动元素闭合，从而减少浮动带来的影响。

我们想要达到的效果更确切地说是闭合浮动，而不是单纯的清除浮动，设置clear：both清除浮动并不能解决warp高度塌陷的问题。

正是因为浮动的这种特性，导致本属于普通流中的元素浮动之后，包含框内部由于不存在其他普通流元素了，也就表现出高度为0（高度塌陷）。
在实际布局中，往往这并不是我们所希望的，所以需要闭合浮动元素，使其包含框表现出正常的高度。

            <div class="row clearfix">
                <div class="left">
                    <h1>left</h1>
                    <div>Content or Something</div>
                </div>
                <div class="right">right</div>
            </div>
            <div class="row2">Row2</div>


            .row {
                border: 1px solid #f33;
            }
            .clearfix:after {
                content: "\200B";
                display: block;
                height: 0;
                clear: both;
            }
            .clearfix {
                *zoom: 1;
            }
            .left {
                width: 200px;
                float: left;
                background-color: #eee;
            }
            .right {
                width: 200px;
                float: right;
                background-color: #eee;
            }
            .row2 {
                width: 600px;
                height: 50px;
                background-color: #aaa;
            }

其中*zoom: 1是为了触发hasLayout

还有另一种


        .clearfix{
            overflow: auto;
            zoom: 1;
        }


一个坑！

如果使用了 over-flow，在后面如果有元素要绝对布局在父元素的外面的，意思就是出现 top, bottom, left, right 的值为负值时，就会出现看不到，或者滚动条的问题！


清除浮动的几种方法

box-sizing

当你设置一个元素为 box-sizing: border-box; 时，此元素的内边距和边框不再会增加它的宽度。

他们的内边距和边框都是向内的挤压的。支持IE8+，需要加浏览器内核。

        .simple {
            width: 500px;
            margin: 20px auto;
            -webkit-box-sizing: border-box;
            -moz-box-sizing: border-box;
            box-sizing: border-box;
        }

div 三列等高

纯CSS实现三列DIV等高布局

最关键的地方有3句：

最外层div设置一个溢出隐藏

    #wrap {
        overflow:hidden;
    }

每一个子块设置 padding 和 margin

    #left,#center,#right{
        margin-bottom:-10000px;
        padding-bottom:10000px;
    }

overflow:hidden; ‘隐藏溢出。如果内容溢出wrap层，则不显示。

    margin-bottom:-10000px; //底部边距-10000px。 
    padding-bottom:10000px; //底部填充10000px。

上面这两句能够实现的效果就是，产生10000px的填充，然后用负的边距把它给抵销掉。

去除inline-block元素间间距

去除inline-block元素间间距的N种方法

真正意义上的inline-block水平呈现的元素间，换行显示或空格分隔的情况下会有间距。 

移除空格
使用 margin 负值
取消闭合标签
使用 font-size: 0
使用 letter-spacing
使用 word-spacing
其他
 font-size: 0 比较好，对别的元素影响最小。
 代码如下：在 a 的外层将字体尺寸设为 0，载对内层的 a 重新设置字体大小，即可。

        nav {
            font-size: 0;
        }
        nav a {
            font-size: 16px;
        }

以下参考[https://github.com/ecomfe/spec]

 同一 rule set 下的属性在书写时，应按功能进行分组，并以 Formatting Model（布局方式、位置） > Box Model（尺寸） > Typographic（文本相关） > Visual（视觉效果） 的顺序书写，以提高代码的可读性。


Formatting Model 相关属性包括：position / top / right / bottom / left / float / display / overflow 等
Box Model 相关属性包括：border / margin / padding / width / height 等
Typographic 相关属性包括：font / line-height / text-align / word-wrap 等
Visual 相关属性包括：background / color / transition / list-style 等
另外，如果包含 content 属性，应放在最前面。

示例：

    .sidebar {
        /* formatting model: positioning schemes / offsets / z-indexes / display / ...  */
        position: absolute;
        top: 50px;
        left: 0;
        overflow-x: hidden;

        /* box model: sizes / margins / paddings / borders / ...  */
        width: 200px;
        padding: 5px;
        border: 1px solid #ddd;

        /* typographic: font / aligns / text styles / ... */
        font-size: 14px;
        line-height: 20px;

        /* visual: colors / shadows / gradients / ... */
        background: #f5f5f5;
        color: #333;
        -webkit-transition: color 1s;
           -moz-transition: color 1s;
                transition: color 1s;
    }


触发 BFC 的方式很多，常见的有：

float 非 none
position 非 static
overflow 非 visible


需注意，对已经触发 BFC 的元素不需要再进行 clearfix。

!important

 尽量不使用 !important 声明。

 当需要强制指定样式且不允许任何场景覆盖时，通过标签内联和 !important 定义样式。

必须注意的是，仅在设计上 确实不允许任何其它场景覆盖样式 时，才使用内联的 !important 样式。通常在第三方环境的应用中使用这种方案。下面的 z-index 章节是其中一个特殊场景的典型样例。

 将 z-index 进行分层，对文档流外绝对定位元素的视觉层级关系进行管理。

同层的多个元素，如多个由用户输入触发的 Dialog，在该层级内使用相同的 z-index 或递增 z-index。

建议每层包含100个 z-index 来容纳足够的元素，如果每层元素较多，可以调整这个数值。

 在可控环境下，期望显示在最上层的元素，z-index 指定为 999999。

可控环境分成两种，一种是自身产品线环境；还有一种是可能会被其他产品线引用，但是不会被外部第三方的产品引用。

不建议取值为 2147483647。以便于自身产品线被其他产品线引用时，当遇到层级覆盖冲突的情况，留出向上调整的空间。

 在第三方环境下，期望显示在最上层的元素，通过标签内联和 !important，将 z-index 指定为 2147483647。

第三方环境对于开发者来说完全不可控。在第三方环境下的元素，为了保证元素不被其页面其他样式定义覆盖，需要采用此做法。

使用 transition 时应指定 transition-property。

示例：

    /* good */
    .box {
        transition: color 1s, border-color 1s;
    }

    /* bad */
    .box {
        transition: all 1s;
    }
 尽可能在浏览器能高效实现的属性上添加过渡和动画。

在可能的情况下应选择这样四种变换：

    transform: translate(npx, npx);
    transform: scale(n);
    transform: rotate(ndeg);
    opacity: 0..1;

典型的，可以使用 translate 来代替 left 作为动画属性。

示例：

    /* good */
    .box {
        transition: transform 1s;
    }
    .box:hover {
        transform: translate(20px); /* move right for 20px */
    }

    /* bad */
    .box {
        left: 0;
        transition: left 1s;
    }
    .box:hover {
        left: 20px; /* move right for 20px */
    }

禁止 img 的 src 取值为空。延迟加载的图片也要增加默认的 src。

src 取值为空，会导致部分浏览器重新加载一次当前页面，

 避免为 img 添加不必要的 title 属性。

多余的 title 影响看图体验，并且增加了页面尺寸。

 为重要图片添加 alt 属性。

可以提高图片加载失败时的用户体验。

 添加 width 和 height 属性，以避免页面抖动。

 有下载需求的图片采用 img 标签实现，无下载需求的图片采用 CSS 背景图实现。

产品 logo、用户头像、用户产生的图片等有潜在下载需求的图片，以 img 形式实现，能方便用户下载。
无下载需求的图片，比如：icon、背景、代码使用的图片等，尽可能采用 CSS 背景图实现。