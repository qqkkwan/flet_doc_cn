---
title: 通过Python创建Flet应用程序
keywords: Flet安装, Flet入门
desc: 通过Python创建Flet应用程序
---

要写一个Flet应用程序，你不需要成为前端大师，但建议你有Python和面向对象编程的基本知识。

在本指南中，我们将研究Flet应用程序的结构，学习如何使用Flet控件输出数据，向用户请求数据，并建立基本的页面布局。我们还将介绍一些打包和部署选项，以便向你的用户提供一个就绪的应用程序。

## 安装```Flet```模块

Flet需要Python 3.7或以上版本。要开始使用Flet，你需要先安装flet模块:

```bash
pip install flet
```

> 升级```Flet```模块，请执行:
> ```bash
> pip install flet --upgrade
> ```

要安装Flet预发布版（针对高级用户），请运行：

```bash
pip install flet --pre
```

>! 我们建议在虚拟环境中安装预发布版本。

### Linux

在Linux和WSL上运行Flet应用程序需要安装[GStreamer](https://gstreamer.freedesktop.org/)库。很可能你的系统中已经有了这些库，但是如果你在加载共享库时遇到```error while loading shared libraries: libgstapp-1.0.so.0: cannot open shared object file: No such file or directory```，那么你需要安装GStreamer。

要在Ubuntu/Debian上安装GStreamer，运行以下命令:

```bash
sudo apt-get update
sudo apt-get install libgstreamer1.0-dev libgstreamer-plugins-base1.0-dev libgstreamer-plugins-bad1.0-dev gstreamer1.0-plugins-base gstreamer1.0-plugins-good gstreamer1.0-plugins-bad gstreamer1.0-plugins-ugly gstreamer1.0-libav gstreamer1.0-doc gstreamer1.0-tools gstreamer1.0-x gstreamer1.0-alsa gstreamer1.0-gl gstreamer1.0-gtk3 gstreamer1.0-qt5 gstreamer1.0-pulseaudio
```

关于在其他Linux发行版上的安装，请参见本[指南](https://gstreamer.freedesktop.org/documentation/installing/on-linux.html?gi-language=c)。

### WSL

Flet应用程序可以在WSL2上运行。如果你遇到无法打开显示的错误，请按照本[指南](https://github.com/microsoft/wslg/wiki/Diagnosing-%22cannot-open-display%22-type-issues-with-WSLg)进行故障排除。

## 基础的应用程序结构

一个非常小的Flet应用程序有以下结构:

```python
import flet as ft

def main(page: ft.Page):
    # add/update controls on Page
    pass

ft.app(target=main)
```

<img src="/static/image/basic-app-structure.png" style="zoom:50%;" />

> 本节特意称为 "基础"，因为在本指南的后面，我们将研究更多真实世界的应用程序结构与可重用控件的方法。

一个典型的Flet程序以调用```flet.app()```结束，至此应用程序开始等待新的用户会话。函数```main()```是Flet应用程序的一个入口点。它在一个新的线程中为每个用户会话调用，并在其中传递一个```Page(页面)```实例。当在浏览器中运行Flet应用程序时，每个打开的标签或页面都会启动一个新的用户会话。当作为一个桌面应用程序运行时，只创建一个会话。

```Page(页面)```就像一个特定于用户的 "画布"，是一个用户会话的视觉状态。为了建立一个应用程序的用户界面，你可以在一个页面上添加和删除控件，更新它们的属性。上面的代码样本将向每个用户显示一个空白页面。

默认情况下，Flet应用程序在本地操作系统窗口中启动，这对开发来说非常方便。然而，你可以通过修改对```flet.app```的调用在一个新的浏览器窗口中打开它，如下所示:

```python
ft.app(target=main, view=ft.WEB_BROWSER)
```

> 在内部，每个Flet应用都是一个网络应用，即使它是在一个本地操作系统窗口中打开的，一个内置的网络服务器仍然在后台启动。Flet的网络服务器被称为 "Fletd"，默认情况下，它在一个随机的TCP端口上监听。你可以指定一个自定义的TCP端口，然后在浏览器中与桌面视图一起打开该应用。
> ```python
> flet.app(port=8550, target=main)
> ```
> 在浏览器中打开```http://localhost:<port>```，查看Flet应用程序的网页版。

## 控件

用户界面是由**控件**（又称小部件）组成的。为了让用户看到控件，它们必须被添加到一个```Page(页面)```或其他控件中。页面是最顶层的控件。控件之间的嵌套可以表示为一棵以页面为根的树。

控件只是普通的Python类。通过构造函数创建控件实例，参数与它们的属性相匹配，例如:

```python
t = ft.Text(value="Hello, world!", color="green")
```

为了在页面上显示控件，将其添加到页面的```controls(控件)```列表中，并调用```page.update()```将页面变化发送到浏览器或桌面客户端:

```python
import flet as ft

def main(page: ft.Page):
    t = ft.Text(value="Hello, world!", color="green")
    page.controls.append(t)
    page.update()

ft.app(target=main)
```

<img src="/static/image/controls-text.png" style="zoom:50%;" />

> 在下面的例子中，我们将只展示```main```函数的内容。

你可以修改控件属性，用户界面将在下个```page.update()```更新:

```python
t = ft.Text()
page.add(t) # 这是page.controls.append(t)和page.update()的一个快捷方式。

for i in range(10):
    t.value = f"Step {i}"
    page.update()
    sleep(1)
```

有些控件是 "容器 "控件（如Page），可以包含其他控件。例如，```Row(列)```控件允许在一排中逐一排列其他控件。

```python
page.add(
    ft.Row(controls=[
        ft.Text("A"),
        ft.Text("B"),
        ft.Text("C")
    ])
)
```

或```TextField(文本域)```和旁边的```ElevatedButton(升降按钮)```:

```python
page.add(
    ft.Row(controls=[
        ft.TextField(label="Your name"),
        ft.ElevatedButton(text="Say my name!")
    ])
)
```

```page.update()```很智能，它只发送自上次调用后所做的修改，所以你可以在页面上添加几个新的控件，删除其中一些，改变其他控件的属性，然后调用page.update()来进行分批更新，比如说:

```python
for i in range(10):
    page.controls.append(ft.Text(f"Line {i}"))
    if i > 4:
        page.controls.pop(0)
    page.update()
    sleep(0.3)
```

一些控件，如按钮，可以有事件处理程序对用户输入作出反应，例如```ElevatedButton.on_click```:

```python
def button_clicked(e):
    page.add(ft.Text("Clicked!"))

page.add(ft.ElevatedButton(text="Click me", on_click=button_clicked))
```

更高级的例子，如一个简单的To-Do:

```python
import flet as ft

def main(page):
    def add_clicked(e):
        page.add(ft.Checkbox(label=new_task.value))

    new_task = ft.TextField(hint_text="Whats needs to be done?", width=300)
    page.add(ft.Row([new_task, ft.ElevatedButton("Add", on_click=add_clicked)]))

ft.app(target=main)
```

<img src="/static/image/simple-ToDo.png" style="zoom:50%;" />

> Flet实现了命令式的UI模型，即用有状态的控件 "手动 "构建应用程序的UI，然后通过更新控件的属性对其进行变化。Flutter实现了声明式模型，其中用户界面在应用程序数据变化时自动重新构建。在现代前端应用程序中管理应用程序的状态本身就是一项复杂的任务，Flet的 "老派 "方法对没有前端经验的程序员更有吸引力。

### ```visible(可见)```属性

每个控件都有```visible```属性，默认情况下为true - 控件在页面上被渲染。将```visible```设置为```false```可以完全阻止控件（以及它的所有子控件，如果有的话）在页面画布上呈现。隐藏的控件不能被聚焦或用键盘或鼠标选择，它们也不会产生任何事件。

### ```disabled(禁用)```属性

每个控件都有```disabled```属性，默认情况下是```false``的--控件及其所有子控件都是启用的。```disabled```属性主要用于数据输入控件，如```TextField(文本域)```、```Dropdown(下拉框)```、```Checkbox(复选框)```、按钮。然而，disabled可以被设置为一个父控件，它的值将递归地传播到所有子控件。

例如，如果你有一个带有多个输入控件的表单，你可以为每个控件单独设置禁用属性:

```python
first_name = ft.TextField()
last_name = ft.TextField()
first_name.disabled = True
last_name.disabled = True
page.add(first_name, last_name)
```

或者你可以把表单控件放到容器中，比如说```Column(列)```，然后为该列设置```disabled(禁用)```:

```python
first_name = ft.TextField()
last_name = ft.TextField()
c = ft.Column(controls=[
    first_name,
    last_name
])
c.disabled = True
page.add(c)
```

## 控制引用

Flet控件是对象，为了访问它们的属性，我们需要保持对这些对象的引用（变量）。

请看下面的例子:

```python
import flet as ft

def main(page):

    first_name = ft.TextField(label="First name", autofocus=True)
    last_name = ft.TextField(label="Last name")
    greetings = ft.Column()

    def btn_click(e):
        greetings.controls.append(ft.Text(f"Hello, {first_name.value} {last_name.value}!"))
        first_name.value = ""
        last_name.value = ""
        page.update()
        first_name.focus()

    page.add(
        first_name,
        last_name,
        ft.ElevatedButton("Say hello!", on_click=btn_click),
        greetings,
    )

ft.app(target=main)
```

<img src="/static/image/control-refs.png" style="zoom:50%;" />

在```main()```方法的最开始，我们创建了三个控件，我们将在按钮的```on_click```处理程序中使用它们：两个```TextField```用于显示名字和姓氏，一个```Column```用于显示问候信息。我们在创建控件时设置了它们的所有属性，在```main()```方法的最后，在```page.add()```调用中，我们使用它们的引用（变量）。

当越来越多的控件和事件处理程序被添加进来的时候，把所有的控件定义放在一个地方就变得很有挑战性，所以它们就散落在```main()```正文中。瞥一眼```page.add()```的参数，很难想象（如果不在IDE中不断跳转到变量定义）最终的表单会是什么样子:

```python
    page.add(
        first_name,
        last_name,
        ft.ElevatedButton("Say hello!", on_click=btn_click),
        greetings,
    )
```

```first_name```是一个TextField，它是否设置了自动聚焦？问候语是一个```行```还是一个```列```？

Flet提供了```Ref```实用工具类，它允许定义一个对控件的引用，在事件处理程序中使用该引用，并在以后建立树时将该引用设置为一个真正的控件。这个想法来自于React。

要定义一个新的类型化的控件引用：

```python
first_name = ft.Ref[ft.TextField]()
```

要访问被引用的控制（控制解除引用），使用Ref.current属性:

```python
# 初始化，清空
first_name.current.value = ""
```

要将控件分配给一个引用，请将Control.ref属性设置为一个引用:

```python
page.add(
    ft.TextField(ref=first_name, label="First name", autofocus=True)
)
```

所有的Flet控件都有```ref```属性。

我们可以重写我们的程序以使用引用：

```python
import flet as ft


def main(page):

    first_name = ft.Ref[ft.TextField]()
    last_name = ft.Ref[ft.TextField]()
    greetings = ft.Ref[ft.Column]()

    def btn_click(e):
        greetings.current.controls.append(
            ft.Text(f"Hello, {first_name.current.value} {last_name.current.value}!")
        )
        first_name.current.value = ""
        last_name.current.value = ""
        page.update()
        first_name.current.focus()

    page.add(
        ft.TextField(ref=first_name, label="First name", autofocus=True),
        ft.TextField(ref=last_name, label="Last name"),
        ft.ElevatedButton("Say hello!", on_click=btn_click),
        ft.Column(ref=greetings),
    )

ft.app(target=main)
```

<img src="/static/image/control-refs.png" style="zoom:50%;" />

现在我们可以在```page.add()```中清楚地看到页面的结构和它所建立的所有控件。

是的，逻辑变得有点冗长，因为你需要添加```.current.```来访问ref的控件，但这是个人喜好的问题 :)