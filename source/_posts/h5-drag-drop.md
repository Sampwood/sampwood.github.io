---
title: h5之拖放
categories:
  - coding
  - html
tags:
  - html5
date: 2017-12-09 13:23:20
update: 2017-12-09 13:23:20
---

本文主要介绍下h5中元素的拖放。

### 拖放
拖放（Drag和Drop）是HMTL5标准的组成部分。

拖放，拖放，分为拖和放。

#### 拖

##### draggable

首先，设置某个元素可拖动，把 `draggable` 属性设置为 true ：
```
<img draggable="true" />
```
<!-- more -->

##### ondragstart

然后，设置元素被拖动时的回调函数，给 `ondragstart` 属性赋值回调函数：

```
<img id="drag_id" draggable="true" ondragstart="drag(event)"/>

<script>
    function drag(event) {
        event.dataTransfer.setData("Text",event.target.id);
    }
</script>
```

`event.dataTransfer` 对象可以用来在`拖`和`放`之间传递数据。

`event.dataTransfer.setData` 用来塞入键值对。`event.dataTransfer.getData` 用来获取键值对。

#### 放

##### ondragover

首先，确定何处可以被放置。因为默认无法把元素放置到其他元素中，因此我们需要阻止对元素的默认处理方式。

`ondragover` 事件规定对元素放置的处理方式。

所以，通过调用 `ondragover` 事件的 `event.preventDefault()` 方法，可以让当前元素可以放置其他元素：

```
<div id="drop_id" ondragover="allowDrop(event)"></div>

<script>
    function allowDrop(event) {
        event.preventDefault();
    }
</script>
```

##### ondrop

然后，设置元素被放置时的回调函数，给 `ondrop` 属性赋值回调函数：

```
<div id="drop_id" ondragover="allowDrop(event)" ondrop="drop(event)"></div>

<script>
    function allowDrop(event) {
        event.preventDefault();
    }

    function drop(event) {
        event.preventDefault();
        var data = event.dataTransfer.getData("Text");
        event.target.appendChild(document.getElementById(data));
    }
</script>
```

**Note**: 至于在 `drop` 函数中调用 `preventDefault` 方法是避免浏览器对数据的默认处理（ `drop` 事件的默认行为是以链接形式打开）。
