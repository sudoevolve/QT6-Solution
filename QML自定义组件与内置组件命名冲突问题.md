🚫 QML 自定义组件与 Qt 内置控件命名冲突问题

### ❗️问题描述

当你创建了一个自定义组件 `Button.qml` 并尝试这样使用：

```qml
import QtQuick 2.15
import QtQuick.Controls 2.15

Button {
    text: "你好"
}
```

会发现使用的并  **不是你自定义的按钮样式** ，而是 Qt 自带的 `QtQuick.Controls.Button`。

---

### 🔍 原因分析

QML 会优先解析已 `import` 的模块中的同名组件。

* `import QtQuick.Controls 2.15` 中已经定义了 `Button`。
* 所以你在代码中写 `Button {}`，它就默认是  **官方的按钮控件** ，而不是你的 `Button.qml` 文件。

---

### ✅ 解决方案

#### 方式一：使用命名空间导入

将你的组件放在一个文件夹里，比如 `components/`，使用时加上前缀：

```qml
import "components" as Components

Components.Button {
    text: "使用自定义按钮"
}
```

这样就明确告诉 QML 使用你定义的 `Button.qml`。

---

#### 方式二：重命名你的组件

将你的组件重命名为非冲突名称，例如：

```plaintext
Button.qml  →  EvolveButton.qml
```

然后正常使用：

```qml
EvolveButton {
    text: "这是我的按钮"
}
```

---

#### 方式三：避免导入 QtQuick.Controls（不推荐）

如果你不需要用 `TextField`、`Slider` 等 Qt 内置控件，可以避免导入：

```qml
// 只导入基础模块
import QtQuick 2.15
```

但这限制太大，不建议用于完整项目中。

---

### 🧠 建议的最佳实践

* 所有自定义组件统一放入 `components/` 目录，并使用命名空间导入。
* 为避免歧义，自定义组件尽量加前缀（如 `EvolveButton`, `MySwitch` 等）。
* 在团队协作中建立组件命名规范，避免误用官方组件名。
