一、 概述
-----

最近有一些数据需要绘图分析，由于本人对excel不熟悉，查阅资料发现`pandas + pyecharts`对数据进行可视化分析非常方便，所以开始尝试使用。我这里通过[anaconda](https://links.jianshu.com/go?to=https%3A%2F%2Fwww.anaconda.com%2F)自带的jupyterlab进行测试，但是使用过程中发现pyecharts的图表无法在jupyterlab上面显示，经过查阅资料解决了此问题，在这里做一个记录方便以后查阅。

> 这里使用的 pyecharts 版本：`1.7.0`

二、使用pyecharts
-------------

查看pyecharts的版本号：

```swift
import pyecharts

pyecharts.__version__
```

代码运行后并不能绘制出图像来，我是使用jupyter lab运行代码的，用notebook就可以。如下图所示：

```jsx
from pyecharts.charts import Bar

x_value = ['A', 'B', 'C']
y1 = [123, 52, 214]
y2 = [45, 63, 161]

bar = (
    Bar()
    .add_xaxis(x_value)
    .add_yaxis(series_name='公司甲', yaxis_data=y1)
    .add_yaxis(series_name='公司乙', yaxis_data=y2)
)

bar.render_notebook()
```

![](//upload-images.jianshu.io/upload_images/8103938-3dcd75703dcc16eb.png?imageMogr2/auto-orient/strip|imageView2/2/w/1169/format/webp)

image.png

三、原因及解决方法
---------

*   **原因**  
    不同的 `notebook` 环境有自己不同的渲染要求，`pyecharts` 在底层做了适配处理，但因为我们无法在`import pyecharts`的时候知道用户具体使用的是哪种 `notebook` 环境，所以需要用户在使用时在顶部声明环境类型。
*   **解决方法**  
    Jupyter Lab 渲染的时候有两点需要注意：

`Jupyter Notebook` 直接调用`render_notebook`随时随地渲染图表，默认为`Jupter-Notebook`。

1.  在`Jupyter Lab`中运行下面的两行代码即可：

```jsx
from pyecharts.globals import CurrentConfig, NotebookType  
CurrentConfig.NOTEBOOK_TYPE = NotebookType.JUPYTER_LAB
```

2.  在第一次渲染的时候调用 `load_javascript()` 会预先加载基本 `JavaScript` 文件到 `Notebook` 中。如若后面其他图形渲染不出来，则请开发者尝试再次调用，因为 `load_javascript` 只会预先加载最基本的 js 引用。而主题、地图等 js 文件需要再次按需加载。
3.  `load_javascript()` 和 `render_notebook()` 方法需要在不同的 cell 中调用，这是 Notebook 的内联机制，其实本质上我们是返回了带有 _html_, _javascript_ 对象的 class。notebook 会自动去调用这些方法。

修改后的代码如下：

```python

from pyecharts.globals import CurrentConfig, NotebookType  
CurrentConfig.NOTEBOOK_TYPE = NotebookType.JUPYTER_LAB
from pyecharts.charts import Bar
from pyecharts import options as opts
# 内置主题类型可查看 pyecharts.globals.ThemeType
from pyecharts.globals import ThemeType

bar = (
    Bar(init_opts=opts.InitOpts(theme=ThemeType.LIGHT))
    .add_xaxis(["衬衫", "羊毛衫", "雪纺衫", "裤子", "高跟鞋", "袜子"])
    .add_yaxis("商家A", [5, 20, 36, 10, 75, 90])
    .add_yaxis("商家B", [15, 6, 45, 20, 35, 66])
    .set_global_opts(title_opts=opts.TitleOpts(title="主标题", subtitle="副标题"))
)
bar.load_javascript()
```

```python
bar.render_notebook()
```

运行结果如下图：

  

image.png

如果你按照上面的方式还是无法显示图形，可以按照下面的参考文档进行操作。

参考文章：  
[pyecharts无法在jupyterlab中显示问题](https://links.jianshu.com/go?to=https%3A%2F%2Fwww.pinggu.com%2Fpost%2Fdetails%2F5e772a3a079cdd2d7673d200)

[https://pyecharts.org/#/zh-cn/notebook](https://links.jianshu.com/go?to=https%3A%2F%2Fpyecharts.org%2F%23%2Fzh-cn%2Fnotebook)

本文转自 <https://www.jianshu.com/p/c464550e5d4c>，如有侵权，请联系删除。