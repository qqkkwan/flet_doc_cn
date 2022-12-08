---
title: 介绍
keywords: Flet介绍，中文文档
desc: Flet中文文档介绍
---

## 什么是Flet

Flet是一个框架，它允许用你喜欢的语言构建交互式多用户网页、桌面和移动应用程序，而不需要事先有前端开发的经验。

你用Flet控件为你的程序建立一个UI，它是基于谷歌的Flutter。Flet不只是 "包装 "Flutter小部件，而是通过组合较小的小部件、隐藏复杂性、实施UI最佳实践、应用合理的默认值来添加自己的 "观点"--所有这些都是为了确保你的应用程序看起来很酷、很专业，而不需要额外的努力。

## Flet应用案例

目前，你可以用Python编写Flet应用程序，其他语言也将很快加入。

这里有一个 "计数器 "应用的例子:

```python
import flet as ft

def main(page: ft.Page):
    page.title = "Flet counter example"
    page.vertical_alignment = ft.MainAxisAlignment.CENTER

    txt_number = ft.TextField(value="0", text_align=ft.TextAlign.RIGHT, width=100)

    def minus_click(e):
        txt_number.value = str(int(txt_number.value) - 1)
        page.update()

    def plus_click(e):
        txt_number.value = str(int(txt_number.value) + 1)
        page.update()

    page.add(
        ft.Row(
            [
                ft.IconButton(ft.icons.REMOVE, on_click=minus_click),
                txt_number,
                ft.IconButton(ft.icons.ADD, on_click=plus_click),
            ],
            alignment=ft.MainAxisAlignment.CENTER,
        )
    )

ft.app(target=main)
```

要运行该应用程序，请安装```Flet```模块:

```bash
pip install flet
```

并且运行程序:

```bash
python counter.py
```

该应用程序将在一个原生的操作系统窗口中启动--这对Electron来说是一个多么好的替代方案啊!

以下分别为在macOS和Windows上的展示效果：

<center class="half">
    <img src="/static/image/flet-counter-macos.png" width="350"/>
    <img src="/static/image/flet-counter-windows.png" width="350"/>
</center

现在，如果你想作为一个网页应用程序运行，只需将最后一行替换为:

```python
ft.app(target=main, view=ft.WEB_BROWSER)
```

再次运行，现在你马上就能得到一个网络应用:

<img src="/static/image/flet-counter-safari.png" style="zoom:50%;" />

## 教程

想学习如何建立一个真正的应用程序吗？跳转到你的语言的入门指南:

- [Python](./python_guide/python_guide.html)