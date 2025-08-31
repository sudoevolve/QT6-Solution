

---

#### **一、 问题描述**

一个使用 CMake 构建的 Qt/QML 应用程序，在 **Debug** 模式下可以正常运行，并且能成功加载通过 Qt 资源系统（`.qrc` 文件）指定的图片、字体等资源。

然而，当将构建模式切换到 Release 后，应用程序在启动时会立即报错或闪退，日志中出现类似以下的错误信息：

QML Image: Cannot open: qrc:/new/prefix1/res/ico.jpg

#### **二、 根本原因**

此问题的根源在于 `CMakeLists.txt` 文件中对 Qt 资源文件 (`.qrc`) 的**处理方式不当**。

常见的错误写法是将资源文件的定义放在了 `qt_add_qml_module` 命令的 `RESOURCES` 参数中。

**错误示例:**

CMake

```
qt_add_qml_module(my_app
    URI MyApp
    VERSION 1.0
    QML_FILES
        Main.qml
        # ... 其他 qml 文件
    RESOURCES my_resources.qrc  # <--- 问题所在
)
```

这种写法意图是将资源作为 QML 模块的一部分。虽然在 Debug 模式下可能有效，但在 Release 模式下，由于链接和编译优化，会导致主可执行文件在启动的初始阶段无法找到并加载这个“模块私有”的资源，从而引发路径找不到的错误。

#### **三、 解决方案**

**核心原则**：将所有应用程序级别的资源，通过 `qt_add_resources` 命令，直接编译并链接到最终的可执行文件中，以确保资源在程序启动时就立即可用。

**具体步骤：**

1. 使用 qt_add_resources 声明资源
    
    在 qt_add_executable 命令之前，添加 qt_add_resources 命令，它会处理 .qrc 文件并生成一个包含 C++ 源码的变量。
    
    CMake
    
    ```
    # 步骤 1: 将 .qrc 文件编译成一个变量 (例如 APP_RESOURCES)
    qt_add_resources(APP_RESOURCES
        my_resources.qrc
    )
    ```
    
2. 将资源链接到可执行文件
    
    在 qt_add_executable 命令中，将上一步生成的变量添加到源文件列表中。
    
    CMake
    
    ```
    # 步骤 2: 将生成的资源变量链接到你的可执行文件
    qt_add_executable(my_app
        main.cpp
        ${APP_RESOURCES}
    )
    ```
    
3. 清理 qt_add_qml_module
    
    从 qt_add_qml_module 命令中移除 RESOURCES 参数。
    
    CMake
    
    ```
    qt_add_qml_module(my_app
        URI MyApp
        VERSION 1.0
        QML_FILES
            Main.qml
            # ... 其他 qml 文件
        # 此处不再需要 RESOURCES 参数
    )
    ```
    
4. 【关键】清理并重新构建
    
    在修改并保存 CMakeLists.txt 文件后，必须执行以下操作以确保更改生效：
    
    - 在 Qt Creator 中，切换到 **Release** 构建套件。
        
    - 右键单击项目，选择 **"Run CMake"** (执行 CMake)。
        
    - 然后 **"Clean Project"** (清理项目)。
        
    - 最后 **"Rebuild Project"** (重新构建项目)。
        
    - 如果是手动操作，请删除旧的构建目录后重新运行 CMake 和构建命令。
        

#### **四、 附加建议**

在检查 `CMakeLists.txt` 时，也请留意 `qt_add_qml_module` 中 `QML_FILES` 的语法是否正确。`QML_FILES` 关键字只应出现一次，其后跟随所有相关的 QML 文件。

**错误语法:**

CMake

```
QML_FILES Main.qml
QML_FILES Page1.qml # 错误
```

**正确语法:**

CMake

```
QML_FILES
    Main.qml
    Page1.qml
```

遵循以上步骤，即可彻底解决 Debug 正常而 Release 失败的资源加载问题，让您的应用程序在所有构建模式下都能稳定运行。



---

## 1️⃣ 通用原则

1. **qt不能访问 QML 模块内部 RESOURCES**
   qt自动生成的qt_add_qml_module(... RESOURCES ...)无法读取
   
   * Qt6 中 `qt_add_qml_module(... RESOURCES ...)` 注册的资源，仅供 QML 内部组件访问。
   * MediaPlayer/其他非 QML 模块组件无法直接读取。

3. **解决方案**

   * 使用 `qt_add_resources` 全局注册 qrc 资源
   * 将生成的资源文件加入可执行文件 `target_sources`
   * QML 内部即可直接使用 `qrc:/path/to/file`

---

## 2️⃣ 通用 CMake 模板

```cmake
cmake_minimum_required(VERSION 3.16)

project(MyApp VERSION 0.1 LANGUAGES CXX)

set(CMAKE_CXX_STANDARD_REQUIRED ON)

# -------------------------
# 查找 Qt6 模块
# -------------------------
find_package(Qt6 REQUIRED COMPONENTS Quick Core)

qt_standard_project_setup(REQUIRES 6.8)

# -------------------------
# 可执行文件
# -------------------------
qt_add_executable(appMyApp
    main.cpp
)

# -------------------------
# 全局注册 qrc 资源（通用方案）
# -------------------------
# qrc 文件可包含视频、图片、音频等
qt_add_resources(APP_RESOURCES
    resources.qrc   # 例如包含 bg1.mp4, bg2.mp4, icon.png 等
)

# 将资源加入可执行文件
target_sources(appMyApp PRIVATE ${APP_RESOURCES})

# -------------------------
# QML 模块（只管理 QML 文件，不注册资源）
# -------------------------
qt_add_qml_module(appMyApp
    URI MyApp
    VERSION 1.0
    QML_FILES
        Main.qml
        components/Button.qml
        components/Input.qml
)

# -------------------------
# 平台特定设置
# -------------------------
set_target_properties(appMyApp PROPERTIES
    MACOSX_BUNDLE_BUNDLE_VERSION ${PROJECT_VERSION}
    MACOSX_BUNDLE_SHORT_VERSION_STRING ${PROJECT_VERSION_MAJOR}.${PROJECT_VERSION_MINOR}
    MACOSX_BUNDLE TRUE
    WIN32_EXECUTABLE TRUE
)

# -------------------------
# 链接 Qt 库
# -------------------------
target_link_libraries(appMyApp
    PRIVATE Qt6::Quick Qt6::Core
)

# -------------------------
# 安装规则
# -------------------------
include(GNUInstallDirs)
install(TARGETS appMyApp
    BUNDLE DESTINATION .
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
)
```

---

## 3️⃣ QML 使用示例

```qml
import QtQuick
import QtQuick.Controls
import QtQuick.Window
import QtMultimedia

Window {
    id: root
    width: 1080
    height: 720
    visible: true

    MediaPlayer {
        id: mediaplayer
        source: "qrc:/videos/bg1.mp4"    // 全局注册 qrc
        audioOutput: AudioOutput {}
        autoPlay: true
        loops: MediaPlayer.Infinite
    }

    VideoOutput {
        anchors.fill: parent
        source: mediaplayer
        fillMode: VideoOutput.PreserveAspectCrop
    }

    Image {
        source: "qrc:/images/icon.png"   // 图片也可以使用
        anchors.centerIn: parent
        width: 100
        height: 100
    }
}
```

---

## 4️⃣ 特点与优势

* **通用**：任何资源类型（视频、图片、音频）都适用
* **跨平台**：Windows / Linux / macOS 都可用
* **稳定**：MediaPlayer、Image、Text 等都能读取 `qrc:/`
* **易维护**：qrc 文件集中管理，CMake 简洁

---

✅ 总结

* **不要用** `qt_add_qml_module(... RESOURCES ...)` 让 MediaPlayer 访问资源
* **使用** `qt_add_resources + target_sources` 全局注册 qrc
* QML 中直接用 `qrc:/` 路径即可

---

