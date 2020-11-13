1. JS去操作修改DOM是极其消耗性能的，可认为ecmascript和dom之间有一座桥梁沟通，每次去修改，都要付“过桥费”
````
//反面示例
function innerHTMLLoop(){
    for (var i = 0;i< 10000; i++){
        document.getElementById('here').innerHTML += 'a'
    }
}
上面这个例子，dom元素每次循环都要被访问两次，一次是读取innerHTML属性值，另一次重写它

//用局部变量修改一下
function innerHTMLLoop2(){
    var content = '';
    for (var i = 0;i< 10000; i++){
      content +='a'
    }
    document.getElementById('here').innerHTML += content
}
````
2. 类似 document.getElementsByName()返回的类数组对象，是会**实时变化**的
````
//下面示例的代码其实是一个死循环
var alldivs = document.getElementsByTagName('div')
for(var i=0; i< alldivs.length; i++){
    document.body.appendChild(document.createElement('div'))
}

// 优化：将HTML集合映射到普通数组，另将集合长度赋值给一个len变量
function toArray(coll){
    for(var i=0, a=[], len = coll.length; i<len; i++){
        a[i] = coll[i]
    }
    return a
}
var coll = document.getElementsByTagName('div')
var ar   = toArray(coll)
````
3. 处理大量组合查询，使用querySelectorAll()更有效率，利用css选择器定位节点，且不会返回HTML集合，即不会实时变化
````
var errs = document.querySelectorAll('div.warning,div.notice')
````
querySelector()来获取第一个匹配的节点  
4. DOM树：表示页面结构  渲染树：表示DOM节点如何显示（DOM树结合CSS树）
DOM树中的每一个需要显示的节点在渲染树中至少存在一个对应的节点（隐藏的DOM元素在渲染树中没有对应的节点）
5. 考虑样式的频繁改变，会影响元素的集合结构，也导致频繁重绘和重排
````
// 合并所有的改变然后一次处理
var el = document.getElementById('mydiv')
el.style.cssText = 'border-left:1px; border-right:2px; padding:5px;'
````
6. 当要对DOM元素进行一系列操作时，可以通过以下步骤减少重绘和回流的次数
* 使元素脱离文档流
* 对其应用多重改变
* 把元素带回文档中
7. DOM脱离文档的三种方法
* 隐藏元素（改变display属性)，应用修改，重新显示
* 使用文档片段（document fragment),在当前DOM之外构建一个子树，在当前DOM之外构建一个子树，再把它拷贝回文档
* 将原始元素拷贝到一个脱离文档的节点中，修改副本，完成后再替换原始元素
8. 使用以下步骤可以避免页面中的大部分重排
* 使用绝对（absolute）位置定位页面上的动画元素，将其脱离文档流
* 让元素动起来，当它扩大时，会临时覆盖部分页面，但这只是页面的一个小区域的重绘过程，不会产生重排并重绘页面的大部分内容
* 当动画结束时恢复定位，从而只会下移一次文档的其他元素
9. 当页面中存在大量元素，而且每一个都要一次或多次绑定事件处理器（如：onclick）,会影响性能，考虑用**事件委托**进行优化
