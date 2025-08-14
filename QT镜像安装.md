在国内安装 Qt 时，由于官方源在国外，下载速度可能较慢甚至失败。可以使用国内的镜像源进行加速，以下是几种常见方法，适用于 **在线安装器（Qt Online Installer）** 方式：

---

## ✅ 方法一：使用清华大学的 Qt 镜像（推荐）

### 步骤：

1. **下载 Qt 官方在线安装器**  
    前往：[https://www.qt.io/download](https://www.qt.io/download)  
    下载 Qt Online Installer（适用于 Windows、Linux、macOS）
    
下载的“qt-unified-windows-x64-4.5.2-online.exe”目录进入CMD，然后运行下面的命令：  ./qt-unified-windows-x64-X.X.X-online.exe --mirror https://mirror.nju.edu.cn/qt
## ✅ 方法二：手动下载离线安装包（如 Qt 5.15）

清华镜像也提供 Qt 离线安装包，适用于没有在线安装器或不想联网安装的情况：

- 浏览地址（清华镜像站）：
    
    ```
    https://mirrors.tuna.tsinghua.edu.cn/qt/
    ```
    
    示例：
    
    - Qt 5.15.2 安装器：
        
        ```
        https://mirrors.tuna.tsinghua.edu.cn/qt/archive/qt/5.15/5.15.2/
        ```
        
- 下载 `.exe`（Windows）或 `.run`（Linux）安装器即可离线安装。
    

---

## ✅ 方法三：使用 `qt-installer-framework` + 自建镜像（高级）

你也可以用 Qt Installer Framework 制作本地安装器，通过 `repository` 本地化安装，自行维护镜像。

---

## 附：常见国内镜像源

|镜像站|地址|
|---|---|
|清华大学|[https://mirrors.tuna.tsinghua.edu.cn/qt/](https://mirrors.tuna.tsinghua.edu.cn/qt/)|
|阿里云（旧）|[https://mirrors.aliyun.com/qt/](https://mirrors.aliyun.com/qt/)|
|中科大|[https://mirrors.ustc.edu.cn/qtproject/](https://mirrors.ustc.edu.cn/qtproject/)|

---

如果你是在 Linux（如 Ubuntu / Mint）使用 APT 安装的 Qt，也可以设置 apt 源为清华等镜像源。需要的话我可以给你详细步骤。

需要我帮你选择合适的版本、组件或配置 `.bashrc` 环境变量也可以继续告诉我。