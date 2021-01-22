---
ddtitle:  ESP学习笔记（一）使用VS Code快捷键开发
date: 2020-10-22 11:50:07
tags:
   - ESP
categories:
   - ESP学习笔记
---

# ESP学习笔记（一）使用VS Code快捷键开发

根据该教程，用户可在VS Code里快速地对ESP32开发板进行编译烧录等操作。

### Step1：VS Code任务配置

- 按下 `Crtl+Shift+P` 并键入 `Tasks:Configure Task`
- 选择使用模版新建 `tasks.json` 文件，选择 `others`
- 复制如下ESP32任务配置模版（包含编辑、下载、清除编译/Flash、打开monitor、打开menuconfig等）

```json
{
    // See https://go.microsoft.com/fwlink/?LinkId=733558
    // for the documentation about the tasks.json format
    "version": "2.0.0",
    "tasks": [
        {
            "label": "build app", // f5
            "type": "shell",
            "command": "cd ${fileDirname} && cd ../ && make -j8",
            "group": {
                "kind": "build",
                "isDefault": true
            }
        },
        {
            "label": "flash app", // f6
            "type": "shell",
            "command": "cd ${fileDirname} && cd ../ && make -j8 flash"
        },
        {
            "label": "monitor", // f7
            "type": "shell",
            "command": "cd ${fileDirname} && cd ../ && make monitor"
        },
        {
            "label": "clean app", // f8
            "type": "shell",
            "command": "cd ${fileDirname} && cd ../ && make clean",
        },
        {
            "label": "erase flash", // f9
            "type": "shell",
            "command": "cd ${fileDirname} && cd ../ && make erase_flash",
        },
        {
            "label": "menuconfig", // f10
            "type": "shell",
            "command": "cd ${fileDirname} && cd ../ && make menuconfig"
        },
    ]
}
```



**任务配置步骤如下所示**：

<img src="/home/fangsiyuan/Videos/Peek/vscode-esp32-1.gif" alt="vscode-esp32-1" style="zoom:100%;" />



### Step2：增%加快捷键

- 按下：`Ctrl+Shift+P` 

- 键入 `Preferences:Open Keyboard Shortcuts(JSON)`

- 填充如下参数

  ```json
  // Override key bindings by placing them into your key bindings file.
  [
      {
          "key": "f5",
          "command": "workbench.action.tasks.runTask",
          "args": "build app"
      },
      {
          "key": "f6",
          "command": "workbench.action.tasks.runTask",
          "args": "flash app"
      },
      {
          "key": "f7",
          "command": "workbench.action.tasks.runTask",
          "args": "monitor"
      },
      {
          "key": "f8",
          "command": "workbench.action.tasks.runTask",
          "args": "clean app"
      },
      {
          "key": "f9",
          "command": "workbench.action.tasks.runTask",
          "args": "erase flash"
      },
      {
          "key": "f10",
          "command": "workbench.action.tasks.runTask",
          "args": "menuconfig"
      }
  ]
  ```

  于是，我们就可以对所选工程进行快捷键开发：

| 快捷键 |     执行的命令     |      功能       |
| :----: | :----------------: | :-------------: |
|   F5   |     `make -j8`     |      编译       |
|   F6   |  `make -j8 flash`  |   编译、下载    |
|   F7   |   `make monitor`   |     监视器      |
|   F8   |    `make clean`    |    清除编译     |
|   F9   | `make erase_flash` |   擦除 flash    |
|  F10   | `make menuconfig`  | 打开 menuconfig |

> NOTE: 这些命令都应该在工程的 `main` 目录下的文件中执行，例如： 在 VS Code 中打开了 hello_world 工程中 main 目录下的 `hello_world_main.c` 文件，可以按快捷键 `F6` 进行编译、下载。暂不支持在其他目录下进行。

详细配置过程：

<img src="/home/fangsiyuan/Videos/Peek/vscode-esp32-2.gif" alt="vscode-esp32-2" style="zoom:100%;" />