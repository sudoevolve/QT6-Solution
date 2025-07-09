在 Qt 中添加软件图标主要分为两个层面：

1. **窗口图标**：显示在应用程序窗口左上角和任务栏上的图标。这个设置是跨平台的，在 C++ 代码中完成。
    
2. **应用程序图标**：可执行文件（`.exe`）、macOS 的应用包（`.app`）以及在桌面、开始菜单中显示的图标。这个设置是**平台相关**的，需要在项目配置文件中完成。
    

---

### 第一步：设置窗口图标（跨平台通用）

这是最基础的一步，确保您的应用窗口有一个图标。

- **方法**：在 `main.cpp` 文件中，使用 `QGuiApplication::setWindowIcon()`。
    
- **依赖**：需要 `QIcon` 类，所以必须包含头文件 `#include <QIcon>`。
    
- **资源**：图标文件（如 `.png`）需要先添加到 Qt 资源系统（`.qrc` 文件）中。
    

**代码示例 (`main.cpp`)：**

C++

```
#include <QGuiApplication>
#include <QQmlApplicationEngine>
#include <QIcon> // 必须包含此头文件

int main(int argc, char *argv[])
{
    QGuiApplication app(argc, argv);

    // 从资源文件中加载并设置图标
    app.setWindowIcon(QIcon(":/icons/app_icon.png"));

    // ... 其他代码
    return app.exec();
}
```

---

### 第二步：设置应用程序图标（各平台不同）

这一步是为了让您的应用在操作系统中看起来更专业。

#### **Windows**

- **文件格式**：`.ico` (一个文件包含多种尺寸)。
    
- **qmake (`.pro` 文件)**：添加 `RC_ICONS = myapp.ico`。
    
- **CMake (`CMakeLists.txt`)**：创建一个 `.rc` 资源文件，指明图标路径，然后将 `.rc` 文件添加到可执行文件的源列表中。
    

#### **macOS**

- **文件格式**：`.icns`。
    
- **qmake (`.pro` 文件)**：添加 `ICON = myapp.icns`。
    
- **CMake (`CMakeLists.txt`)**：设置 `MACOSX_BUNDLE_ICON_FILE` 属性。
    

#### **Linux**

- **文件格式**：`.png` (需要提供多种尺寸，如 32x32, 64x64, 128x128 等)。
    
- **方法**：创建一个 `.desktop` 文件，在其中通过 `Icon` 字段指定图标名称（不含扩展名）。
    
- **部署**：在安装打包时，将图标文件安装到系统的图标路径下（如 `/usr/share/icons/hicolor/<尺寸>/apps/`），并将 `.desktop` 文件安装到 `/usr/share/applications/`。
    

#### **Android**

- **文件格式**：`.png` (需要为不同屏幕密度提供多种尺寸)。
    
- **方法**：在 Qt Creator 中，通过“项目” -> “构建” -> “构建 Android APK” -> “创建模板”来生成 Android 项目文件。
    
- **部署**：将不同尺寸的图标文件（通常命名为 `ic_launcher.png`）放入生成的 `android/res/mipmap-*` 目录下。`AndroidManifest.xml` 文件会自动引用它们。
    

#### **iOS**

- **文件格式**：`.png` (需要为 App Store、通知、设置等提供多种精确尺寸)。
    
- **方法**：使用 **Asset Catalog** (`.xcassets`) 来管理所有图标。
    
- **部署**：在 `.pro` 文件中用 `QMAKE_ASSET_CATALOGS` 指向你的 `.xcassets` 文件夹，或在 Xcode 项目中进行配置。
    

---

### 总结表

|平台|主要方法|文件格式|qmake 关键字|CMake 关键字/方法|
|---|---|---|---|---|
|**窗口图标**|在 `main.cpp` 中编码|`.png` (在 `.qrc` 中)|-|-|
|**Windows**|项目文件配置|`.ico`|`RC_ICONS`|添加 `.rc` 文件|
|**macOS**|项目文件配置|`.icns`|`ICON`|`MACOSX_BUNDLE_ICON_FILE`|
|**Linux**|创建 `.desktop` 文件|`.png` (多尺寸)|`INSTALLS`|`install()` 指令|
|**Android**|修改原生项目模板|`.png` (多尺寸)|-|-|
|**iOS**|使用 Asset Catalog|`.png` (多尺寸)|`QMAKE_ASSET_CATALOGS`|-|

简单来说，**先用 `setWindowIcon` 搞定窗口通用图标，再根据你的目标发布平台，配置对应的应用程序图标。**