---
id: custom-config
title: 自定义配置
slug: custom-config
order: 1
---

## 使用自定义配置

在 OpenSumi 中，常见的配置项一般分为如下两种：

1. 集成配置
2. 运行时配置

`集成配置` 一般为静态配置，随用户 IDE 服务的启动生效，即便修改后也需要 “刷新/重启服务” 后才能生效，常用的配置可以查看下面的 [集成配置](#集成配置) 查看。

`运行时配置` 一般为动态配置，支持在用户使用过程中通过一些界面操作，或用户自定义的方式去动态改变，常用于一些具有 “开关属性” 或 “多场景配置” 的场景，用户可以通过框架中的配置界面进行动态修改。
![settings](https://img.alicdn.com/imgextra/i4/O1CN015YKbLY1xUmFjFnVNs_!!6000000006447-2-tps-2354-1118.png)

与 `集成配置` 不同的是，`运行时配置` 在框架内可以通过具体的配置文件去进行自定义，如 `launch.json`、`task.json`、`settings.json` 等文件。

## 运行时配置

### 如何使用

在模块内，我们可以通过 `PreferenceService` 服务直接获取相关配置的值，如下面我们可以通过该服务去获取我们注册的 `debugModel.enable` 配置项：

```ts
@Autowired(PreferenceService)
protected readonly preferenceService: PreferenceService;

...
this.preferenceService.get('debugModel.enable')
```

而在插件中，我们兼容了 VS Code 中的 Configuration API，可以跟 VS Code 插件内采用相同的方式获取对应配置，如下面展示如何获取 `launch.json` 内的配置值:

```ts
const config = workspace.getConfiguration(
  'launch',
  vscode.workspace.workspaceFolders[0].uri
);

// retrieve values
const values = config.get('configurations');
```

同时，在 `集成配置` 中，也可以对框架一些默认的 `运行时配置` 进行二次配置，下面的代码配置了 “默认情况下隐藏文件树下的 index.js 文件” 配置：

```ts
appconfig = {
   ...
  defaultPreferences: {
    'files.exclude': {
      // 默认值 glob 表达式
      '**/.git': true,
      '**/.svn': true,
      '**/.hg': true,
      '**/CVS': true,
      '**/.DS_Store': true,
      // 追加, 这里屏蔽了 index.js 文件的展示
      '**/index.js'
    }
  }
   ...
}
```

需要注意的是，这些配置将影响 IDE 初次访问情况的表现，当用户通过设置面板或文件修改配置后，后续访问将会采用用户的自定义配置。

### 如何注册新的配置

常用的注册运行时配置主要有如下两种方式：

1. 模块 —— 通过 `PreferenceContribution` 贡献点注册
2. 插件 —— 通过 `Configurations` 贡献点注册

OpenSumi 提供了自定义配置能力，基于 [Contribution Point](../../develop/basic-design/contribution-point) 机制，只需要实现 `PreferenceContribution` 即可进行配置注册。

举个例子，我们通过创建 `DemoPreferenceContribution` 可以在项目中注册运行时配置，伪代码如下：

```ts
import { PreferenceContribution } from '@opensumi/ide-core-browser';
import { Domain, PreferenceSchema } from '@opensumi/ide-core-common';

export const DemoPreferenceSchema: PreferenceSchema = {
  type: 'object',
  properties: {
    'debugModel.enable': {
      type: 'string',
      default: 'test',
      description: 'test'
    }
  }
};

@Domain(PreferenceContribution)
export class DemoPreferenceContribution implements PreferenceContribution {
  public schema: PreferenceSchema = DemoPreferenceSchema;
}
```

通过将 `DemoPreferenceContribution` 引入到模块中的 Providers 声明后，便可以在其他模块通过下面方式使用

```ts
@Autowired(PreferenceService)
protected readonly preferenceService: PreferenceService;

...
this.preferenceService.get('debugModel.enable')
```

另一种注册方式则是通过插件的 [Configuration 贡献点](https://code.visualstudio.com/api/references/contribution-points#contributes.configuration) 在插件中进行注册。

## 集成配置

在集成 OpenSumi 框架的时候，我们往往需要进行独立的配置，下面列举了一些可在集成阶段通过传入配置项进行配置的参数：

在 [本地客户端示例](https://github.com/opensumi/ide-electron) 项目中，找到 [src/browser/index.ts#L113](https://github.com/opensumi/ide-electron/blob/main/src/browser/index.ts#L113) 的 `renderApp` 初始化方法添加如下：

```ts
renderApp({
  // 追加配置
  appName: 'OpenSumi',
  // 原有内容
  ...
})
```

而在 [WebIDE 示例](https://github.com/opensumi/ide-startup) 项目中, 你也可以找到类似配置，见 [src/browser/index.ts#L12](https://github.com/opensumi/ide-startup/blob/main/src/browser/index.ts#L12)。

完整配置文件可以参考实时代码：[packages/core-browser/src/react-providers/config-provider.tsx#L14~#L245](https://github.com/opensumi/core/blob/main/packages/core-browser/src/react-providers/config-provider.tsx#L14~#L245)。

![appconfig](https://img.alicdn.com/imgextra/i1/O1CN01kT4yVO1Ul9YeeMGWE_!!6000000002557-2-tps-1722-1490.png)

### 浏览器端配置

定义可见 `@opensumi/ide-core-browser` 中的 [AppConfig](https://github.com/opensumi/core/blob/main/packages/core-browser/src/react-providers/config-provider.tsx#L14~#L245) 定义。

示例项目中修改配置的参考位置如下：

- [WebIDE 示例](https://github.com/opensumi/ide-startup) - [src/browser/index.ts#L12](https://github.com/opensumi/ide-startup/blob/main/src/browser/index.ts#L12)
- [纯前端示例](https://github.com/opensumi/ide-startup-lite) - [src/browser/index.ts#L52](https://github.com/opensumi/ide-startup-lite/blob/main/src/app.tsx#L52)
- [本地客户端示例](https://github.com/opensumi/ide-startup) - [src/browser/index.ts#L113](https://github.com/opensumi/ide-electron/blob/main/src/browser/index.ts#L113)

部分配置如下：

| 参数                                    | 参数说明                                                                                                                                                                                                                                                  | 默认值                                                                                                                                                                  |
| --------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| appName                                 | 客户端的唯一名称，一般在客户端启动时的标题展示使用，同时也作为插件进程输出的客户端名称                                                                                                                                                                    | OpenSumi                                                                                                                                                                |
| uriScheme                               | 主要用于注册 Electron 版本中的客户端协议，便于实现协议唤起                                                                                                                                                                                                | sumi                                                                                                                                                                    |
| workspaceDir                            | 工作区路径，一般为一个文件夹或是 `*.sumi-workspace` 的工作区文件，当传入值为空时，编辑器左侧文件树将会展示打开文件夹的按钮。                                                                                                                              | 无                                                                                                                                                                      |
| didRendered                             | 当 DOM 首次渲染完成后调用，此时表示 IDE 界面已经完成渲染并可以操作，可以在此处传入函数处理界面 Loading，参考：[DidRendered](https://yuque.antfin.com/ide-framework/integration/features#Zs82P)                                                            | 无                                                                                                                                                                      |
| extensionDir                            | 插件目录路径                                                                                                                                                                                                                                              | 无                                                                                                                                                                      |
| extensionCandidate                      | 额外指定的插件路径，一般用于内置插件                                                                                                                                                                                                                      | 无                                                                                                                                                                      |
| storageDirName                          | 设置全局存储的文件夹名称，主要针对使用了 `Storage` 模块相关存储服务时进行文件夹名称配置                                                                                                                                                                   | .sumi                                                                                                                                                                   |
| preferenceDirName                       | 设置工作区配置文件的文件夹名称，对于集成环境，我们更推荐使用 `workspacePreferenceDirName` 和 `userPreferenceDirName` 进行更加精细的配置存储文件夹名称配置                                                                                                 | .sumi                                                                                                                                                                   |
| workspacePreferenceDirName              | 更精细的项目工作区配置存储位置，即当 preferenceDirName = '.sumi' ， workspacePreferenceDirName = '.kt'时，对应全局配置为 ~/.sumi/settings.json , 工作区配置为 {workspaceDir}/.kt/settings.json                                                            | .sumi                                                                                                                                                                   |
| userPreferenceDirName                   | 更精细的项目用户配置存储位置，即当 preferenceDirName = '.sumi' ， userPreferenceDirName = '.kt'时，对应全局配置为 ~/.sumi/settings.json , 工作区配置为 {userDir}/.kt/settings.json                                                                        | .sumi                                                                                                                                                                   |
| extensionStorageDirName                 | 全局插件数据存储目录名称，默认 .sumi，存储数据位置在 {userDir}/.sumi                                                                                                                                                                                      | .sumi                                                                                                                                                                   |
| defaultPreferences                      | 对客户端的整体配置进行初始化定义，常见的自定义参数如：颜色主题：`general.theme`、图标主题：`general.icon`、语言：`general.language`、排除文件选项：`filesExclude`、排除文件监听选项：`watchExclude` 等等，理论上可针对所有 IDE 中定义的配置进行默认值设置 | 无                                                                                                                                                                      |
| injector                                | 初始化的 DI 实例，一般可在外部进行 DI 初始化之后传入，便于提前进行一些依赖的初始化                                                                                                                                                                        | 无                                                                                                                                                                      |
| wsPath                                  | 默认 WebScoket 通信路径                                                                                                                                                                                                                                   | 无                                                                                                                                                                      |
| layoutConfig                            | 定义 IDE 各个布局区块默认加载的模块，可针对性对模块进行增删改。                                                                                                                                                                                           | 见：[default-config.tsx](https://github.com/opensumi/core/blob/58b998d9e1f721928f576579f16ded46b7505e84/packages/main-layout/src/browser/default-config.ts)             |
| layoutConponent                         | 定义 IDE 的整体布局，可以通过传入自定义布局的方式定义各个区块的默认大小及缩放选项等                                                                                                                                                                       | 见：[default-layout.tsx](https://github.com/opensumi/core/blob/58b998d9e1f721928f576579f16ded46b7505e84/packages/core-browser/src/components/layout/default-layout.tsx) |
| panelSizes                              | 可基于 `layoutComponent` 配置的基础上，定义面板大小，宽度/高度                                                                                                                                                                                            | 无                                                                                                                                                                      |
| defaultPanels                           | 定义各个区块的默认面板如 `defaultPanels: { [SlotLocation.bottom]: '@opensumi/ide-terminal-next' }`                                                                                                                                                        | 无                                                                                                                                                                      |
| webviewEndpoint                         | 用于挂载 webview 的 iframe 地址                                                                                                                                                                                                                           | `http://${deviceIp}:${port}/webview`                                                                                                                                    |
| extWorkerHost                           | Worker 插件的默认启动路径                                                                                                                                                                                                                                 | https://dev.g.alicdn.com/tao-ide/ide-lite/${version}/worker-host.js                                                                                                     |
| staticServicePath                       | 定义静态资源路径，框架内默认加载路径为 `http://127.0.0.1:8000/assets/${path}`                                                                                                                                                                             | http://127.0.0.1:8000/assets/${path}                                                                                                                                    |
| extensionDevelopmentHost                | 定义是否以插件开发模式启动                                                                                                                                                                                                                                | false                                                                                                                                                                   |
| editorBackgroundImage                   | 定义编辑器界面的背景图片                                                                                                                                                                                                                                  |                                                                                                                                                                         |
| useExperimentalShadowDom                | 定义是否在插件环境中启用 ShadowDom 模式，建议打开，打开后，视图插件的样式将会与全局环境隔离                                                                                                                                                               | false                                                                                                                                                                   |
| useIframeWrapWorkerHost                 | 加载 workerHost 时使用 iframe 包装，对于跨域的场景，加载 workerHost 时会使用 base64 编码后通过 importScripts 引入(importScripts 不受跨域限制)，但这会导致 workerHost 的 origin 为 null，使某些请求失败                                                    | false                                                                                                                                                                   |
| clientId                                | 自定义客户端 id，是 websocket 服务的唯一标识，也是传给声明了 backServices 的后端 Service 的唯一标识，注意保持这个 id 的唯一性                                                                                                                             | 无                                                                                                                                                                      |
| noExtHost                               | 是否禁用插件进程                                                                                                                                                                                                                                          | false                                                                                                                                                                   |
| extraContextProvider                    | 额外的 ConfigProvider，可以让 OpenSumi 内部的 ReactDOM.render 调用时，都被其包裹一层，以达到额外的 Context 传递效果                                                                                                                                       | 无                                                                                                                                                                      |
| allowSetDocumentTitleFollowWorkspaceDir | 允许按照工作区路径去动态设置 document#title,                                                                                                                                                                                                              | true                                                                                                                                                                    |
| remoteHostname                          | 远程访问地址，可以通过该地址访问当容器服务                                                                                                                                                                                                                | window.location.hostname                                                                                                                                                |
| enableDebugExtensionHost                | 开启插件进程的调试能力                                                                                                                                                                                                                                    | false                                                                                                                                                                   |
| inspectExtensionHost                    | 调试插件进程时的 ip 地址                                                                                                                                                                                                                                  | 无                                                                                                                                                                      |
| extensionFetchCredentials               | 加载插件前端资源时的 fetch credentials 选项，可选项为 "include"                                                                                                                                                                                           | "omit"                                                                                                                                                                  | "same-origin" | 无 |
| extensionConnectOption                  | 参考：[ExtensionConnectOption](https://github.com/opensumi/core/blob/58b998d9e1f721928f576579f16ded46b7505e84/packages/core-common/src/types/extension.ts#L18)                                                                                            | 无                                                                                                                                                                      |

### 服务端配置

定义可见 `@opensumi/ide-core-node` 中的 [AppConfig](https://github.com/opensumi/core/blob/a3e8741b86ba2dc7974d914da35e74200aabe1e2/packages/core-node/src/types.ts#L43~L115) 定义。

示例项目中修改配置的参考位置如下：

- [WebIDE 示例](https://github.com/opensumi/ide-startup) - [src/browser/index.ts#L50](https://github.com/opensumi/ide-startup/blob/main/src/node/start-server.ts#L50)
- [本地客户端示例](https://github.com/opensumi/ide-startup) - [src/browser/index.ts#L65](https://github.com/opensumi/ide-electron/blob/main/src/node/server.ts#L65)

部分配置如下：

| 参数                      | 参数说明                                                                                                                                              | 默认值        |
| ------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- | ------------- |
| injector                  | 初始化的 DI 实例，一般可在外部进行 DI 初始化之后传入，便于提前进行一些依赖的初始化                                                                    |               |
| marketplace               | 参考：[MarketplaceConfig](https://github.com/opensumi/core/blob/58b998d9e1f721928f576579f16ded46b7505e84/packages/core-node/src/bootstrap/app.ts#L28) |               |
| logLevel                  | 设置落盘日志级别，默认为 Info 级别的 log 落盘                                                                                                         | LogLevel.Info |
| logDir                    | 定义日志落盘路径                                                                                                                                      | ~/.sumi/logs  |
| LogServiceClass           | 待废弃，外部设置的 ILogService，替换默认的 logService，可通过在传入的 `injector` 初始化 `ILogService` 进行实现替换                                    | 无            |
| maxExtProcessCount        | 定义启用插件进程的最大个数                                                                                                                            | 无            |
| extLogServiceClassPath    | 定义插件日志自定义实现路径                                                                                                                            | 无            |
| processCloseExitThreshold | 定义插件进程关闭时间                                                                                                                                  | 无            |
| terminalPtyCloseThreshold | 定义终端 pty 进程退出时间                                                                                                                             | 无            |
| staticAllowOrigin         | 访问静态资源允许的 origin                                                                                                                             | 无            |
| staticAllowPath           | 访问静态资源允许的路径，用于配置静态资源的白名单规则                                                                                                  | 无            |
| blockPatterns             | 文件服务禁止访问的路径，使用 glob 匹配                                                                                                                | 无            |
| extHost                   | 插件 Node 进程入口文件                                                                                                                                | 无            |
| extHostIPCSockPath        | 插件进程存放用于通信的 sock 地址                                                                                                                      | /tmp          |
| extHostForkOptions        | 插件进程 fork 配置                                                                                                                                    | 无            |

更多配置，可查看 OpenSumi 源码 [opensumi/core](https://github.com/opensumi/core)。
