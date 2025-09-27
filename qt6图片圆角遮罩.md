---

### 🌙 代码结构

```qml
Image {
    id: sourceItem
    source: ""
    anchors.centerIn: parent
    width: 300
    height: 300
    visible: false
}

MultiEffect {
    source: sourceItem
    anchors.fill: sourceItem
    maskEnabled: true
    maskSource: mask
}

Item {
    id: mask
    width: sourceItem.width
    height: sourceItem.height
    layer.enabled: true
    visible: false

    Rectangle {
        width: sourceItem.width
        height: sourceItem.height
        radius: width/2
        color: "black"
    }
}
```

---

### 📒 笔记讲解

1. **Image { … }**

   * `id: sourceItem` → 原始图像对象。
   * `visible: false` → 不直接显示这张图，而是交给 `MultiEffect` 去处理。
   * 设置大小 `300x300`，居中显示。

2. **MultiEffect { … }**

   * `source: sourceItem` → 作用对象是上面的图像。
   * `maskEnabled: true` → 启用遮罩功能。
   * `maskSource: mask` → 遮罩来源是 `mask` 这个 Item。
   * `anchors.fill: sourceItem` → 遮罩后的效果覆盖原图区域。

3. **Item { id: mask … }**

   * 遮罩层，大小和图像一致。

   * `layer.enabled: true` → 启用独立图层渲染，否则 `maskSource` 无法正确获取。

   * `visible: false` → 遮罩本身不显示，只作为 `maskSource`。

   * 内部有一个 **Rectangle**：

     * 宽高和图片相同。
     * `radius: width/2` → 让矩形四角变成半径为宽度一半的圆角，即绘制一个 **圆形蒙版**。
     * `color: "black"` → 遮罩图层使用黑色，黑色部分表示保留内容，透明部分表示裁剪掉。

---

### 🎯 效果总结

这段代码实现了 **给图片加上圆形遮罩** 的功能：

* 原始图像不会直接显示。
* `MultiEffect` 根据 `mask` 的黑色区域显示内容，把图片裁剪成一个圆。
* 最终看到的是 **圆形的图像**，而不是方形。


