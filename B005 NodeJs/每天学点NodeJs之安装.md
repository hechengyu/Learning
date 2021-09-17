# Window 安装

**步骤1**：下载安装。下载路径：[https://nodejs.org/en/download/](https://nodejs.org/en/download/)，安装步骤：next…

**步骤2**：配置环境变量。

**步骤3**：查看版本。命令：`node -v`

**步骤4**：配置 nmp。

-   全局模块的存放路径及 cache 的路径。

```shell
# 全局模块的存放路径
npm config set prefix "{path}\nodejs\node_global"
# cache 的路径
npm config set cache "{path}\nodejs\node_cache"
```

-   npm 镜像源配置

```shell
# 查看使用的镜像源
npm config get registry
# 设置 淘宝镜像源
npm config set registry https://registry.npm.taobao.org
```

**步骤5**：配置全局变量

-   NODE_HOME：{NodeJs_Root}
-   NODE_PATH：`{path}\nodejs\node_global\node_modules`

**步骤6**：安装模块 express

```shell
npm install -g cnpm --registry=https://registry.npm.taobao.org
```

