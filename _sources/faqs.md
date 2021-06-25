# 常见问题

常见问题（Frequently Asked Questions）

```{div} w3-card-4 w3-padding w3-pale-blue
Q: 当我显示一个小部件或交互时，我只看到一些文本，例如 `IntSlider(value=0)` 或者 `interactive(children=(IntSlider(value=0, description='x', max=1), Output()), _dom_classes=('widget-interact',))`，为什么？

A: 如果小部件控件不可用，则打印小部件的文本表示形式。这可能意味着小部件 JavaScript 仍在加载中。如果消息持续存在于 Jupyter Notebook 或 JupyterLab 中，这可能意味着没有安装或没有启用小部件 JavaScript 库。如果你在另一个前端看到这个消息（例如，GitHub 或 {term}`NBViewer` 上的静态渲染），这可能意味着你的前端目前不支持小部件。
```
