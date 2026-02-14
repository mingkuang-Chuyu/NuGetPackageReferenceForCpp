# 关于
微软后爹默认并没有为C++项目（vcxproj）打开NuGet PackageReference。

传统的NuGet（即Packages Config）模式，每个项目都会在项目目录下生成一个`packages`文件夹，里面存放着该项目所引用的NuGet包。这种方式会导致磁盘空间占用较大，并且在构建过程中需要频繁访问磁盘，可能会影响构建时间。

新的`PackageReference`模式下，下载的NuGet统一会缓存系统全局的.nuget文件夹中，所有项目共享引用，这可以显著降低磁盘空间占用和构建时间。

因此单纯磁盘占用以及速度考虑使用`PackageReference`是必要的。本项目就是为C++项目启用`PackageReference`而创建的。
* 温馨提示：更多`PackageReference`信息请参考[微软官方文档](https://learn.microsoft.com/zh-cn/nuget/consume-packages/package-references-in-project-files)

# 使用方法
## 先决条件
* Visual Studio 2019 16.8或者更高版本
   - 如果低于此版本，仅保证让MSBuild以及nuget.exe支持`PackageReference`包还原，但是无法在`Visual Studio`中使用NuGet包管理器安装和管理NuGet包。
    - MSBuild 还原方法：
        - 可以仅还原： `msbuild -t:Restore "你的sln文件路径"` 
        - 也可以还原同时进行构建：`msbuild -r -t:Build "你的sln文件路径"`。
    - nuget.exe 还原方法：可以使用 `nuget restore "你的sln文件路径"` 进行包还原。
* 工作负载中安装 `.NET桌面开发`，否则无法保证Visual Studio内部构建时`NuGet PackageReference`包可以正常还原，往往会报告还原失败。

## 为C++项目启用PackageReference
1. 将本项目里的 `Directory.Build.props`放在代码根目录下即可启用`PackageReference`。
   - sln文件不在根目录下也没关系，`Directory.Build.props`会向上查找直到找到sln文件为止。所以直接放在代码根目录下就可以了。
2. 重启VS

## 安装NuGet包
1. 右键点击项目，选择`管理NuGet程序包`
2. 在`浏览`标签页中搜索需要安装的包，点击安装即可
   - 安装NuGet包后，需要重启Visual Studio，否则智能感知无法正确识别新安装的包

# Q&A
## 为什么不直接在项目文件里启用`PackageReference`，而是通过`Directory.Build.props`来启用呢？
如果直接在项目文件里启用`PackageReference`，需要修改每个项目的文件，这对于大型项目来说是非常麻烦的。而通过`Directory.Build.props`，只需要在代码根目录下放置一个文件，就可以让所有项目都启用`PackageReference`，大大简化了配置过程。

## 我放了`Directory.Build.props`之后，为什么还是无法使用NuGet包管理器安装和管理NuGet包呢？
1. 检查Visual Studio版本是否满足要求，必须是2019 16.8或者更高版本。如果低于此版本请手工使用MSBuild/nuget.exe还原包，或者升级Visual Studio版本。

## 如果未来微软发布新的Visual Studio版本后，`PackageReference`的使用方式发生了变化，我该如何适配呢？
这个没有办法，可以先手工使用`MSBuild/nuget.exe`还原。等未来适配后再更新`Directory.Build.props`文件。

当然也欢迎大家提交PR来适配新的Visual Studio版本，或者如果你有更好的启用`PackageReference`的方式，也欢迎提交PR来改进这个项目。
