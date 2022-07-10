---
id: core-modules
title: Core Modules
slug: core-modules
order: 6
---

OpenSumi warehouse code is organized through [Monorepo] (https://www.perforce.com/blog/vcs/what-monorepo), and those under `packages` directory are all OpenSumi built-in modules as of version 2.8.0. There are 53 modules in the OpenSumi codebase. Some of them are fully pluggable while others provide core functionality. Some early designs are unsuitable for the current architecture and may be removed or incorporated into other modules in future.  

## Introduction to Core Modules

OpenSumi contains dozens of modules, while you don't need to reach every module in development. In general, you should have some basic understanding on following modules.

### Core Browser

Core-browser is meant to manage part of the OpenSumi frontend at runtime, including the frontend `ClientApp` and `Contribution` mechanism. In addition, core-Browser is responsible for initiating the RPC connection with the back end, and **cannot be hot-plugged**.  

### Core Node

Similar to core-browser, core-node features the runtime management of the backend part of OpenSumi, including the `ServerApp` and the backend `Contribution` mechanism, which also **cannot be hot-plugged**.

### Monaco

The main purpose of the monaco module is to reorganize and export the APIs of the Monaco Editor, because some of the OpenSumi editor functions depend on the private APIs of the Monaco Editor, so some monaco modules' APIs will be reorganized and exported for use by other modules. In addition, the monaco module also provides some `Contribution Points` that other modules can register and implement, overriding some built-in services. monaco module **cannot be hot-plugged**.

### File Service

File-service is a built-in file service implementation. Many cases are based on native file systems, while file-service is the default implementation. In addition, OpenSumi also helps to implement other file systems by means of customizing file-service. Examples include MemoryFS and BrowserFS. 

### File Tree Next

It is the default implementation of the file tree and relies on file-service to read the file tree, display the list and perform other functions. In addition, the File Tree reads "decorations" registered by decorations module to show the status of the file, for example, whether it has been modified in Git.  

### Terminal Next

Implementing terminal functions requires frontend rendering and backend Shell process management. Terminal module provides an independent contribution point of `TerminalNetworkContribution`, encouraging connections to backend Shell process by using customized external network channels(WebSocket/Socket). At the same time, it provides VS Code Terminal related APIs.

### OpenSumi Extension

Extension is the core OpenSumi extension system implementation, including the frontend view extension, the Node/VS Code extension process environment, the WebWorker extension environment. It also contains all OpenSumi and compatible VS Code extension API implementations. Extension relies on most of the functional modules, and it can be removed directly (but also loses all extension functionalities).

### Extension Manager

The main role of Extension-manager is to install, manage, enable or disable extensions. Also it supports customized sources. What makes it special is that extension-manager is the only module that can directly depend on extension module, because the extension needs to be synchronized and activated after installing and enabling it.

## Module Layering

Although OpenSumi employs Monorepo to organize the code structure, and the code between each module looks flat, the logical relation between modules is actually layered. For example, `core-browser`, `core-node`, `file-service`, `connection` and ` monaco` are essential in most cases and can be considered as `underlying modules`, which cannot be safely removed or `hot-plugged`. Modules including `opened-editor`, `markdown`and `search` have a single role and just provide partial views and functionalities, so removing them will not have a damaging effect on OpenSumi itself, and we call them `functional modules`.

One of the most important criteria to determine a module belongs to an `underlying module` or a `core module` is whether it can be safely removed. If you take in 'no extension functionality', then in fact `extension` can also be a functional module, so that most of its dependencies can be safely removed.

## Module List and Overview

```bash
.
├── addons                        # Used to rely on some modules to implement some functions that may have side effects, such as monitoring file-tree drag-and-drop issues and calling file-service APIs to write files  
├── comments                      # Used to implement the built-in Code Review comments, offering standard VS Code Comments API  
├── components                    # A set of basic components shared by various OpenSumi modules  
├── connection                    # Used to implement the bottom RPC framework at the Web and Electron ends and the corresponding connection management  
├── core-browser                  # The core framework of Web side functions, including the whole OpenSumi ClientApp and life cycle implementation  
├── core-common                   # Incorporating some base types, such as built-in Contributions that may be relied upon by many modules 
├── core-electron-main            # Incorporating the basic related functions of Electron-end main process
├── core-node                     # The core framework of Node-end functions, including OpenSumi ServerApp and life cycle implementation
├── debug                         # Debugger functional module, which implements the standard Debug Adapter Protocol interface and provides the corresponding VS Code API implementation
├── decoration                    # Used to manage and register decorations except the editor, such as decorations of the Git state in the file tree view
├── editor                        # Providing editor and group editor operations and relevant management functionalities for Upper-level packaging module of Monaco Editor
├── electron-basic                # The custom feature in the Electron base，including the basic implementation of the Welcome interface 
├── explorer                      # Providing a basic view container for FileTree, to be discarded. 
├── express-file-server           # OpenSumi built-in static resource server，mainly providing functions including extension resource reading  
├── extension-manager             # Built-in extension installation, management and other functions  
├── extension-storage             # Providing storage-related VS Code APIs to manage extension-related storage 
├── file-scheme                   # Providing file:// protocol file processing operations, such as, what kind of view is used to display the file in the front end and how to save the file in the back end  
├── file-search                   # File search service, based on VScode-ripgrep implementation of file search (non-content)  
├── file-service                  # The abstraction of File service that can customize file processing services during integration, with built-in file services based on native FS  
├── file-tree-next                # File tree implementation
├── i18n                          # Built-in i18n function and language packs
├── extension             #  Extension system implementation of OpenSumi, including extension runtime and extension API implementation 
├── keymaps                       # implementing keybinding function
├── logs-core                     # Built-in Logger implementation
├── main-layout                   # OpenSumi main interface framework implementation which can be highly free-customized based on Layout  
├── markdown                      # Built-in Markdown file preview function
├── markers                       # Issue panel implementation, based on the diagnostic information powered by the LSP and applied to the editor, offering a standard VS Code Diagnostic API implementation
├── menu-bar                      # The built-in menu bar implementation, using the DOM based menu on the Web side and the native menu on the Electron side  
├── monaco                        # Providing Contributions that allow other modules (Editor) to register or override some of the built-in services, for Monaco Editor references and packaging  
├── monaco-enhance                # Providing some basic widgets such as ZoneWidget and OverlayWidget, based on Monaco's top layer packaging
├── opened-editor                 # The "Open Editor" functionality implementation
├── outline                       # Outline function, lSP-based symbol interface, providing Treeview-based symbol jump and other functions
├── output                        # The output functionality implementation, providing standard VS Code Output APIs
├── overlay                       # OpenSumi built-in overlayer functionality implementation, such as message, notification and modal
├── preferences                   # Setting panel and functionality implementations
├── process                       # An Utils module that provides convenient subprocess management functions
├── quick-open                    # Quick open feature implementation, featuring a separate implementation and similar to the Monaco Editor's built-in feature  
├── scm                           # The abstract interface of Source Control functionality, offering a standard VS Code SCM API, such as Git extensions that are implemented based on the SCM interface  
├── search                        # Cross-file text search functionality
├── startup                       # OpenSumi Example module that can be used to start OpenSumi based on the Startup module in development state 
├── static-resource               # Static resource service management module
├── status-bar                    # Status bar functionality implementation, offering standard VS Code StatusBar APIs
├── storage                       # Storage functionality implementation, mainly maintaining reading and writing of various cache in OpenSumi  
├── task                          # Task functionality implementation, offering standard VS Code Task APIs
├── terminal-next                 # Terminal functionality implementation，offering standard VS Code Terminal APIs
├── theme                         # Color theme and icon theme functionality implementation, compatible with VS Code icons, color themes, providing standard VS Code Theme related ContributionPoint
├── toolbar                       # Toolbar functionality implementation, providing the OpenSumi-extended Toolbar-related APIs 
├── types                         # OpenSumi extension type declaration module, containing all VS Code API type declarations compatible with OpenSumi 
├── userstorage                   # User cache functionality implementation
├── variable                      # Providing management and implementation of various "magic variables" at runtime. For example, in the Task configuration file tasks.json, you can use ${WorkspaceFolder} to get the current working directory  
├── webview                       # Webview feature implementation, including various Webview components and capabilities, offering standard VS Code Webview APIs
├── workspace                     # Workspace functionality implementation, used to manage the current OpenSumi open workspace, and supporting multi-workspace functions
└── workspace-edit                # Packaging workspace-level editing, for example, to revoke a renaming needs to call WorkspaceEdit relative functions at the same time, so as to perform a file-level revocation.
```
