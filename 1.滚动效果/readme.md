# 列表滚动效果
制作列表滚动效果的核心就在于滚动框的`scrollTop`属性，它代表着我们滚动了多少像素。
当然为了让容器不显示滚动条我们会设置`overflow:hidden;` 来隐藏滚动条。

## 无缝滚动效果

1. 准备HTML结构
```html
    <div class="container">
        <h1>诗词推荐</h1>
        <div class="list">
            <ul>
                <li>1.水调歌头·隐括杜牧之齐山诗</li>
                <li>2.初夏</li>
                <li>3.皇矣</li>
                <li>4.新雁过妆楼·中秋后一夕李方庵月庭延客命小妓过新水令坐间赋词</li>
                <li>5.临江仙引·上国</li>
            </ul>
        </div>
    </div>
```

2. 加上style样式

```css
    <style>
        .container {
            width: 600px;
            margin: 40px auto;
        }
        
        .list {
            overflow: hidden;
            height: 120px;
        }
        
        .list ul {
            margin: 0;
            padding: 0;
        }
        
        .list li {
            height: 30px;
            line-height: 30px;
        }
    </style>
```

3. 书写我们的 JavaScript 代码逻辑

```javascript
        const listBox = document.querySelector('.list');
        listBox.innerHTML += listBox.innerHTML;
        listBox.scrollTop = 0;
        const speed = 50;
        let counter = 0;
        
        const callback = () => {
                if(listBox.scrollTop >= listBox.scrollHeight / 2){
                    counter = 0;
                    listBox.scrollTop = counter;
                }else{
                    listBox.scrollTop = ++counter;
                }
        }

        let timer = setInterval(callback, speed);
        

        listBox.addEventListener('mouseover', () => {
            clearInterval(timer);
        });

        listBox.addEventListener('mouseout', () => {
            timer = setInterval(callback, speed);
        });
```

**拿到listBox容器之后，我们要把里面的内容拷贝一份。你可能会问我为什么？**   

当我们滚动到最底部的时候是不是要有一个新的替补呀？序号为5的内容后面应该跟着的是序号为1的内容，假如我们不拷贝一份的话，5后面是空白，当scrollTop为0的时候里面就跳到从1开始滚了，这样非常的突然，用户一定会认为这个网站出问题了。

大家可以注释拷贝内容这一行代码看看会是怎么个情况，学习最好的方法就是自己去体验，学会体验不一样的东西，做一个老司机，在控制台里面输入`listBox.scrollTop`, 你会发现`scrollTop`最大为`29.600000381469727`，所以说为了让我们有更多的内容可以滚动，拷贝一份即可。

然后我们先将 scrollTop 初始化为 0， 设置了一个滚动速度speed为50ms，以及一个存储定时器变量timer，还有counter计数器。

**你可能感到困惑为什么不直接让scrollTop自己加加不就好了？**

是因为我发现在我的Chrome下面测试，scrollTop自己加加会莫名变成浮点数，导致后面判断是否滚到相应的位置无法判断，导致出现问题。

通过定时器 setInterval 每 50ms 让 scrollTop 加 + ， 这样就形成了滚动，当用户把鼠标移动上去的时候，清除这个定时器，鼠标移动到容器框之外的时候又加上定时器让他继续滚动。


**`listBox.scrollTop >= listBox.scrollHeight / 2` 这个条件大家可能不太好理解？**

scrollTop 现在大家应该清楚是什么了，那 scrollHeight 呢？

遇见不懂的东西，我们首先要去猜测，之后再通过代码或者什么其他方法去验证我们的猜测，当实在找不到的时候我们再去查资料，这可以非常好的锻炼我们解决问题的能力，这是为了避免总会有一些问题是网络上找不到问题的。

首先我们通过 `scrollHeight` 单词来猜测，滚动高度，listBox 滚动的高度。 我们知道 `scrollTop` 是滚动上方，也就是说滚动距离容器顶部的距离，同时也是我们滚动了多少像素。此时还剩下什么高度呢？ listBox 可以滚动的高度？ listBox 里面内容的高度？ 那么究竟是哪一个呢？

我们在控制台里面输入 `listBox.scrollHeight` , 我们发现是 `300` ？ 不知道根据这个数值大家有没有猜到什么呢？ 一个 li 的高度是30，一共有 5 个，然后我们又拷贝了一份。也就是说`30 * 5 * 2`，刚好就是我们的 `300`。 也就是说 `listBox.scrollHeight` 是我们容器里面内容的高度。

**那么现在我问，想要知道可以滚动的高度是多少呢？**

`可以滚动的高度 = 容器内容高度 - 可以看得到的高度`， 也就是 `300 - 120`

## 间歇滚动效果

间歇滚动，说明就是过一段时间再滚动。那么滚动我们一定会用到 setInterval ， 那么过一会再滚呢？那就是用 setTimeout ， 所以我们需要准备 2 个回调函数，一个用于 seInterval 和 setTimeout。

setInterval 回调里面是滚动的逻辑，setTimeout里面的回调是创建 setInterval 的逻辑。 那么什么时候停止滚动呢？每一个的 li 高度是 30， 我们需要停的地方分别是 `30 60 90 120` 等等，这些都是可以被30整除的。

那么如何停止滚动呢？ 清除 setInterval 的返回值 timer 就行了，然后再定义一个 setTimeout 让其 2s 后再通过 setInterval 创建一个 timer 开始滚动。 

我们再来理清一下流程，首先 setTimeout 回调创建一个 setInterval 的 timer 开始滚动，滚动到 30 的时候，清除 timer ，然后再设置一个 setTimeout ，让其 2s 后再滚动。

* JavaScript 逻辑

HTML 和 CSS 与上面相同

```javascript
        const listBox = document.querySelector('.list');
        listBox.innerHTML += listBox.innerHTML;
        listBox.scrollTop = 0;
        const itemHeight = 30;  // li 高度
        const delay = 2000; // timeout 时长
        const speed = 50; // iterval 时长
        let timer; // 保存 iterval 引用
        let counter = 0;

        function init() {
            timer = setInterval(slide, speed);
            listBox.scrollTop = ++counter; // 先滚一点, 避免一直停在 counter % itemHeight == 0
        }

        function slide() {  // 滚动逻辑
            if (counter % itemHeight == 0) {
                clearInterval(timer);
                setTimeout(init, delay)
            } else {
                listBox.scrollTop = ++counter; 
                if (listBox.scrollTop >= listBox.scrollHeight / 2) {
                    counter = 0;
                    listBox.scrollTop = counter;
                }
            }
        }
        setTimeout(init, delay)
```


## 回到顶部效果

1.准备 HTML 与 CSS

**注意** ： 不要设置 `href="#"` 这样会让程序失效，空锚链接会默认直接跳到顶部。当然这也算是最简单的顶部效果，就是体验不太好。

```html
    <style>
        .box{
            min-height: 2000px;
        }
    </style>
    <div class="box"></div>
    <a href="javascript:void(0);" id="back-top">回到顶部</a>
```

2.书写 JavaScript 逻辑

首先我们准备一个判断滚动方向的代码片段，这个也是我搜索的一个代码片段。

用第二次滚动的数值减去第一次滚动的数值，因为只有滚动的时候才会触发回调，所以俩次的数值通常是不同的，假如 > 0 就说明向下的，< 0 就是说明向上。

```js
// 判断页面滚动的方向
function scroll( fn ) {  
    let beforeScrollTop = document.body.scrollTop,  
        fn = fn || function() {};  
        doc.onscroll = () => {  
            let afterScrollTop = document.body.scrollTop;
            let delta = afterScrollTop - beforeScrollTop;  
            if( delta === 0 ) return false;  
            fn( delta > 0 ? "down" : "up" );  
            beforeScrollTop = afterScrollTop;
        };  
}
```

然后我们准备一个渐进函数，这个函数的作用就是，返回一个a 趋近与 b 的值，而 rate 则是 每次趋近的比例。 

```
    function aToB( a , b , rate){
        return a + ( b - a ) / rate;
    }
```

**完整的代码**

当我们明白了前面几个例子之后，这个滚动到顶部非常简单，只不过我们这次不是恒定减多少，而是用了一个趋近函数。

而且当我们在往上滚动的过程中，用户往下滚动，我们立刻停止自动往上滚动。

```js
        let doc = document.body ;
        let timer;

        function aToB( a , b , rate){
            return a + ( b - a ) / rate;
        }

        // 判断页面滚动的方向
        function scroll( fn ) {  
            let beforeScrollTop = document.body.scrollTop,  
                fn = fn || function() {};  
                doc.onscroll = () => {  
                    let afterScrollTop = document.body.scrollTop;
                    let delta = afterScrollTop - beforeScrollTop;  
                    if( delta === 0 ) return false;  
                    fn( delta > 0 ? "down" : "up" );  
                    beforeScrollTop = afterScrollTop;
                };  
        }  

        scroll((dir) => {
            if(dir == "down"){
                // 当用户想停，往下面一滚就可以停住。
                clearInterval(timer);
            }
        });

        let btn = document.querySelector('#back-top');

        btn.onclick = () => {
            timer = setInterval(() => {
                doc.scrollTop = aToB(doc.scrollTop, 0 , 4);
                if(doc.scrollTop <= 1) clearInterval(timer);
            }, 50);
        }
```

## 轮播

轮播的原理其实有非常多，这里我们主要使用`float:left;`以及`scrollLeft`来控制轮播，原理跟前面的间歇性滚动一样。

对于 CSS 与 HTML 有一部分有点难，我们先来准备下 HTML

```html
   <div class="container" id="Carousel" >
       <ul>
           <li><img src="http://desk.fd.zol-img.com.cn/t_s960x600c5/g5/M00/0B/06/ChMkJ1jwQjyIXn8sAAH-7v517g8AAbpyQKMLWsAAf8G90.jpeg" alt=""></li>
           <li><img src="http://desk.fd.zol-img.com.cn/t_s960x600c5/g5/M00/0F/01/ChMkJ1jPUhqIcE05AAVLOLCv7H4AAa4nwF1zK4ABUtQ787.jpg" alt=""></li>
           <li><img src="http://desk.fd.zol-img.com.cn/t_s960x600c5/g5/M00/01/0E/ChMkJlbKwYuId4plAAS7yhw9BL0AALGZwKPTTYABLvi038.jpg" alt=""></li>
           <li><img src="http://desk.fd.zol-img.com.cn/t_s960x600c5/g5/M00/01/0E/ChMkJ1bKwYqIR2LdAAL1EEQXUccAALGZwIvzYkAAvUo497.jpg" alt=""></li>
       </div>
   </div>
```

```css
        *{
           padding: 0;
           margin: 0;
        }

        .container, .container li {
            width: 400px;
        }

        .container{
            overflow: hidden;
            margin: 20px auto;
        }

        ul{
           width: 3200px;
           list-style: none;
        }

        ul:after{
            content: '';
            display: block;
            clear: both;
        }

        .container li{
            float: left;  
            text-align: center;
        }

        .container li img {
            width: 100%;
        }
```

外层的`container`作为我们的可视容器，里面的`ul`作为我们的滚动容器。

```js
        let carousel = document.querySelector('#Carousel');
        carousel.children[0].innerHTML += carousel.children[0].innerHTML;
        carousel.scrollLeft = 0;
        const itemWidth = 400;
        const delay = 2000;
        const speed = 20;
        let timer;
        let counter = 0;

        function init() {
            timer = setInterval(slide, speed);
            counter += 10;
            carousel.scrollLeft = counter; // 先滚一点, 避免一直停在 counter % itemWidth == 0
        }

        function slide() {  // 滚动逻辑
            if (counter % itemWidth == 0) {
                clearInterval(timer);
                setTimeout(init, delay)
            } else {
                counter += 10;
                carousel.scrollLeft = counter; 
                if (counter >= carousel.scrollWidth / 2) {
                    counter = 0;
                    carousel.scrollLeft = counter;
                }
            }
        }
        setTimeout(init, delay)
```

只是改变了一下变量名称，与一些滚动速度。

