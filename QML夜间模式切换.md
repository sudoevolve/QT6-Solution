# QML 夜间/日间模式切换实现指南

QML 中实现一个简单、灵活的夜间模式切换系统，支持主色、副色、字体颜色的自动切换，适用于无 C++ 注册的纯 QML 项目。

---

## 📁 项目结构建议

```
EvolveUI/
├── main.qml
└── components/
    └── Theme.qml
```

---

## 🌗 Step 1: 创建 `Theme.qml` 管理配色逻辑

路径：`components/Theme.qml`

```qml
// components/Theme.qml
import QtQuick 2.15

QtObject {
    id: theme

    property bool isDark: false

    property color primaryColor: isDark ? "#121212" : "#ffffff"
    property color secondaryColor: isDark ? "#1e1e1e" : "#f5f5f5"
    property color textColor: isDark ? "#ffffff" : "#000000"
    property color borderColor: isDark ? "#666666" : "#cccccc"

    function toggleTheme() {
        isDark = !isDark
    }
}
```

> ✅ 本方案不使用 `pragma Singleton`，直接实例化该对象，避免注册复杂性。

---

## 🖼 Step 2: 在 `main.qml` 中集成主题功能

```qml
import QtQuick 2.15
import QtQuick.Controls 2.15
import QtQuick.Layouts 1.15
import "components" as Components

ApplicationWindow {
    visible: true
    width: 600
    height: 450
    title: "流式组件排列示例"

    // 实例化主题对象
    Components.Theme {
        id: theme
    }

    // 背景层绑定主题主色
    Rectangle {
        anchors.fill: parent
        color: theme.primaryColor
        z: -1
    }

    Flow {
        anchors.fill: parent
        spacing: 12
        anchors.margins: 20

        // 切换模式按钮
        Components.Button {
            text: theme.isDark ? "切换为日间模式" : "切换为夜间模式"
            onClicked: theme.toggleTheme()
        }

        // 其他组件...（绑定 theme 色彩可选）
    }
}
```

---

## 🎨 Step 3: 在组件中响应主题色（推荐做法）

你可以在自定义组件（如 `Button.qml`, `Input.qml`）中添加对 `theme` 的颜色绑定：

```qml
// 组件内部（例如某个按钮）
Rectangle {
    color: theme.secondaryColor
    border.color: theme.borderColor

    Text {
        text: "按钮"
        color: theme.textColor
    }
}
```

> ⚠️ 记得确保 `Theme.qml` 在当前作用域中已被实例化或传入。

---

## ✅ 优点总结

- ☑ 纯 QML 实现，无需 C++ 代码或 QML 模块注册
- ☑ 主题切换即刻生效，数据驱动 UI
- ☑ 可扩展支持更多属性（透明度、阴影、字号等）

---

## 📌 建议扩展

- 保存 `isDark` 到 `QtSettings` 做持久化
- 封装 `ThemeManager.qml` 支持多套风格切换
- 添加渐变过渡动画效果提高体验

---

## 📚 参考用法

```qml
import "components" as Components

Components.Theme {
    id: theme
}

Rectangle {
    color: theme.primaryColor
    Text { color: theme.textColor }
}
```

---

