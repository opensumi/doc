---
id: custom-keybinding
title: Custom Keybinding
slug: custom-keybinding
order: 5
---

## Overview

As a rich-interactive IDE, a favourable keybinding design can largely free users from relying on interface operations and improve the work/operation efficiency. Apart from supporting extensions registration by extensions under the OpenSumi framework, it also supports to register expansion by modules. This section focuses on how to preset more keybindings for your application in the integration phase.

## Keybinding Registration

In the module, we usually employ `KeybindingContribution` approach to register，which are detailed in [Keybinding Registration](../../develop/basic-design/contribution-point#快捷键注册).

## Favorable keybinding Characters

The following modifiers are supported under specific platforms:

|  platform   | modifier                            |
| ------- | ---------------------------------- |
| macOS   | `Ctrl+`, `Shift+`, `Alt+`, `Cmd+`  |
| Windows | `Ctrl+`, `Shift+`, `Alt+`, `Win+`  |
| Linux   | `Ctrl+`, `Shift+`, `Alt+`, `Meta+` |

Meanwhile, you can use `ctrlcmd` as a modifier in keybinding registration. This modifier will be identified as `Cmd` in macOS and `Ctrl` in Linux and Windows.

Some of the remaining supported key values are as follows： 

- `f1-f19`, `a-z`, `0-9`
- `,`-`,`=`,`[`, `]`,`\`, `;`, `,`,`,`.`,`/`
- `left`, `up`, `right`, `down`, `pageup`, `pagedown`, `end`, `home`
- `tab`, `enter`, `escape`, `space`, `backspace`, `delete`
- `pausebreak`, `capslock`, `insert`
- `numpad0-numpad9`, `numpad_multiply`, `numpad_add`, `numpad_separator`
- `numpad_subtract`, `numpad_decimal`, `numpad_divide`

## Control the Effective Scope by "when"

In general, when we register a keybinding, we only expect the keybinding to take effect in a specific area. Generally we recommend using the `when` logic for control. There are partial `when` expressions defined in the OpenSumi framework, and in most cases you can use them directly, see [contextkey/index.ts](https://github.com/opensumi/core/blob/f3fd01381d6ee854102d491b14957e9e634941a3/packages/core-browser/src/contextkey/index.ts).

You only need to add the `when` field when registering a keybinding, so that the keybinding will only be responded when `when` is in effect, which can effectively avoid keybinding conflicts in your IDE, as follows:

```ts
keybindings.registerKeybinding({
  command: 'type',
  keybinding: 'enter',
  when: 'editorTextFocus'
});
```

You can also customize or register `when` expressions, as described in the simple example: [dialog.contextkey.ts](https://github.com/opensumi/core/blob/f3fd01381d6ee854102d491b14957e9e634941a3/packages/overlay/src/browser/dialog.contextkey.ts) .

From then on, you can customize the function keybindings by integrating customized modules.
