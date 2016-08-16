---
title: jQuery
date: 2016-03-18 22:23:12
categories: jQuery
tags: jQuery

---

### 选择器
- **id**  
`$("#id")`  
- **element**  
`$("tagName")`  
- **class**  
`$(".class")`
- **全选**  
`$("xxx *")`
- **多个**  
`$("xxx,yyy")`
- **父子(孙)**  
`$("xxx yyy")`
- **父子(无孙)**  
`$("xxx>yyy")`
- **最紧邻的下一个**  
`$("xxx+yyy")`
<!--more-->
- **相邻的所有**  
`$("xxx~yyy")`
- **第一/最后**  
`$("xxx:first") / $("xxx:last")`  
- **指定的某个**  
`$("xxx:eq(4)")第五个`
- **包含某个字符**  
`$("li:contains('文字')")`
- **包含元素名称**  
`$("li:has('p')")包含P元素的所有li标签`
- **获取不可见元素**  
`$("xxx:hidden")包括type="hidden"和style="display:none"`
- **获取所有可见元素**  
`$("xxx:visible")`
- **属性选择器**  
`$("li[attribute=value]")  如$("li[title='蔬菜']")`
- **属性反向选择器**  
`$("li[attribute!=value]")`
- **包含指定内容的全部元素**  
`$("li[attribute*=value]") 如$("li[title*='蔬']")`
- **子元素过滤选择器**  
`$("li:first-child") / $("li:last-child")`
- **表单input选择器**  
`$("form :input")获取表单全部元素,包括textarea,select,button等`
- **表单text文本选择器**  
`$("form :text") 获取全部单行的文本输入元素,包括textarea,input type="text"`
- **表单password文本选择器**  
`$("form :password") type="password"`
- **表单radio选择器**  
`$("form :radio") type="radio"`
- **表单checkbox选择器**  
`$("form :checkbox") type="checkbox"`
- **表单submit选择器**  
`$("form :submit") 包括input type="submit"和button`
- **表单img选择器**  
`$("form :img") 只针对input type="img"，而img不算`
- **表单button选择器**  
`$("form :button") input type="button"和button`
- **checked选中状态选择器**  
`$("form :checked")只针对checked="checked"`
- **selected选中状态选择器**  
`$("form :selected")只针对selected="selected"`
---
### 操作DOM
- **操作元素熟悉**  
`.attr(属性名，属性值)`
- **操作元素内容**  
`.html()可以解析标签，.text()只取文本`
- **操作元素样式**  
`.addClass(className)加样式名，.css({"background-color":"red","color":"white"})`
- **移除属性和样式**  
`.removeAttr(attrName)移除属性，.removeClass(className)移除样式`
- **用append向元素追加内容**  
`.append(content) 可以是字符、html元素标记或一个返回字符串内容的函数`
- **用appendTo向被选元素内插入内容**  
`$(content).appendTo(selector) $("hello").appendTo("span")`
- **用before()和after()在元素前后插入内容**  
`$(seletor).before(content)/$(selector).after(content)`
- **用clone()复制元素**  
`$(selector).clone()复制某个元素，包括他的节点，文本和属性。`
- **用replaceWith和replaceAll替换内容**  
`$(selector).replaceWith(content)和$(content).replaceAll(selector)`
- **用wrap()和wrapInner()包裹元素和内容**  
`$(selector).wrap(wrapper)和$(selector).wrapInner(wrapper) 但前者用于包裹元素本身，后者则用于包裹元素中的内容`
- **用each()遍历**  
`$(selector).each(function(index))`
- **删除元素**  
`$(selector1).remove(selector2)删除所选元素本身和子元素；.empty只删除所选元素的子元素。`
---
### 事件与应用
- **初始化**  
`$(document).ready(function(){}) == $(function(){})`
- **用.bind()绑定元素事件**  
`$(selector).bind(event,function})`
- **用.hover切换事件**  
`$(selector).hover(over,out) 当鼠标移到所选元素上时，执行方法中的第一个函数，鼠标移出时，执行方法中的第二个函数`
- **用.toggle()方法绑定多个事件**  
`$(selector).toggle(fun1(),fun2(),funN(),...),默认不加fun为切换显示/隐藏。支持主流稳定的版本1.8.2，而1.9.0之后的不再支持`
- **用.unbind()移除元素绑定**  
`$(selector).unbind(event,fun)`
- **用.one()版定一次性事件**  
`$(selector).one(event,[data],fun) 参数event为事件名称，data为触发事件时携带的数据，fun为触发该事件时执行的函数。`
- **用.trigger手动触发事件**  
`$(selector).trigger(event)`
- **文本框的focus和blur事件**  
`.focus(fun) .blur(fun)`
- **下拉列表框change事件**  
`.change(fun)`
- **用.live()绑定动态元素事件**  
`不支持版本1.9`
---
### 动画特效
- **用.show()和.hide()显示/隐藏元素**  
`$(selector).hide(speed,[callback])和$(selector).show(speed,[callback]) speed:slow、fast或毫秒数`
- **用.toggle()实现动画效果**  
`$(selector).toggle(speed,[callback])。1.9.0之后的不再支持`
- **用.slideUp()和.slideDown()实现滑动**  
`$(selector).slideUp(speed,[callback])和$(selector).slideDown(speed,[callback])。滑动改变元素的高度，淡入淡出改变元素的透明度。`
- **用.slideToggle()实现滑动切换效果**  
`$(selector).slideToggle(speed,[callback])`
- **用.fadeIn()和.fadeOut()实现淡入淡出效果**
`$(selector).fadeIn(speed,[callback])和$(selector).fadeOut(speed,[callback])`
- **用.fadeTo()设置淡入淡出效果的不透明度**  
`$(selector).fadeTo(speed,opacity,[callback]) opacity参数为指定的不透明值，它的取值范围是0.0~1.0`
- **用.animate()做简单的动画效果**  
`$(selector).animate({params},speed,[callback])`
- **用.stop()停止所有动画效果**  
`$(selector).stop([clearQueue],[goToEnd])两个可选项参数clearQueue和goToEnd都是布尔类型值，前者表示是否停止正在执行的动画，后者表示是否完成正在执行的动画，默认为false。`
- **用.delay()延迟执行**  
`$(selector).delay(duration)`
---
### Ajax
- **用.load()异步请求数据**  
`load(url,[data],[callback])`
- **用getJSON()异步加载json数据**  
`$.getJSON(url,[data],[callback])`
- **用getScript()异步加载并执行js文件**  
`$.getScript(url,[callback])`
- **用get()方法以get方式从服务器获取数据**  
`$.get(url,[callback])`
- **用post()方法以post方式从服务器获取数据**  
`$.post(url,[data],[callback])`
- **用.serialize()序列化表单元素**  
`$(selector).serialize() 将表单中有name属性的元素值进行序列化，生成标准URL编码文本字符串，直接可用于ajax请求`
- **.用.ajax加载服务器数据**  
`$.ajax({})/jQuery.ajax({})`
- **用.ajaxSetup()设置全局ajax默认选项**  
`jQuery.ajaxSetup([options])或$.ajaxSetup([options])`
- **用.ajaxStart()和ajaxStop()用于ajax请求发出前/完成后触发函数**  
`$(selector).ajaxStart(function())/$(selector).ajaxStop(function())`
---
### 常用插件
- **表单验证插件.validate()**  
`$(form).validate({options})`
- **表单插件 .ajaxForm()**  
`$(form). ajaxForm ({options})`
- **图片灯箱插件.lightBox()**  
`$(linkimage).lightBox({options}) 其中linkimage参数为包含图片的<a>元素名称，options为插件方法的配置对象。`
- **图片放大镜.jqzoom()**  
`$(linkimage).jqzoom({options})`
- **cookie插件**  
`保存：$.cookie(key，value)；读取：$.cookie(key)，删除：$.cookie(key，null)`
- **搜索插件 .autocomplete**  
`$(textbox).autocomplete(urlData,[options]);`
- **右键菜单插件.contextmenu**  
`$(selector).contextMenu(menuId,{options});`
- **自定义对象级插件.focusColor()**  
`$(Id).focusColor(color)`
- **自定义类级别插件**  
`$.addNum(p1,p2) 和 $.subNum(p1,p2) 加/减`
---
### UI型插件
- **拖拽插件 .draggable()**  
`$(selector).draggable({options})`
- **放置插件 .dropable()**  
`$(selector).droppable({options})`
- **拖拽排序插件.sortable()**  
`$(selector).sortable({options});`
- **面板折叠插件（手风琴）.accordion()**  
`$(selector).accordion({options});`
- **选项卡插件.tabs()**  
`$(selector).tabs({options});`
- **对话框插件.dialog()**  
`$(selector).dialog({options});`
- **菜单工具插件.menu()**  
`$(selector).menu({options});`
- **微调按钮插件.spinner()**  
`$(selector).spinner({options});`
- **工具提示插件.tooltip()**  
`$(selector).tooltip({options});`
---
### 工具栏函数
- **获取浏览器名称和版本信息**  
`$.browser/$.browser.chrome/$.browser.mozilla  $.browser.version`
- **检测浏览器是否属于W3C盒子模型**  
`$.support.boxModel	`
- **检测对象是否为空**  
`$.isEmptyObject(obj)`
- **检测对象是否为原始对象**  
`$.isPlainObject(obj) 能检测对象是否为通过{}或new Object()关键字创建的原始对象`
- **检测两个节点的包含关系**  
`检测两个节点的包含关系`
- **字符串操作函数**  
`$.trim(str) 能删除字符串中左右两边的空格符，但该函数不能删除字符串中间的空格`
- **序列化编码，向服务端发送URL请求**  
`$. param (obj);`
- **扩展工具函数**  
`$.extend({options}) 对原有的工具函数进行扩展，自定义类级别的jQuery插件`
- **使用$.extend()扩展Object对象**  
`$. extend (obj1,obj2,…objN);`