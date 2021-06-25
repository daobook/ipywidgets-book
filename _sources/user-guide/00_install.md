# 安装

用户可以使用 {term}`pip` 或 {term}`conda` 安装 {term}`ipywidgets` 的当前版本。在大多数情况下，安装 Python `ipywidgets` 包也会自动配置经典的 Jupyter Notebook 和 JupyterLab 3.0 来显示 `ipywidgets`。

````{tabbed} pip
```shell
pip install ipywidgets
```
````

````{tabbed} conda
```shell
conda install -c conda-forge ipywidgets
```
````

## 安装在经典的 Jupyter Notebook

大多数情况下，安装 `ipywidgets` 会自动配置 Jupyter Notebook 使用组件。`ipywidgets` 包通过 `widgetsnbeextension` 包实现了这一点，该包配置了经典的 Jupyter Notebook 来显示和使用小部件。如果你安装了旧版本的 Jupyter 笔记本，你可能需要手动启用 `ipywidgets` 笔记本扩展：

```shell
jupyter nbextension enable --py widgetsnbextension
```

如果你的 Jupyter Notebook 和 IPython 内核安装在不同的环境中（例如，不同的环境提供不同的 Python 内核），那么安装需要两个步骤：

1. 在包含 Jupyter 笔记本服务器的环境中安装 `widgetsnbeextension` 包。
2. 在将使用 `ipywidgets` 的每个内核环境中安装 `ipywidgets`。

例如，如果使用 conda 环境，将 JupyterLab 安装在基本环境中，并将内核安装在名为 `ai` 的环境中，则命令如下：

```shell
conda install -n base -c conda-forge widgetsnbextension
conda install -n ai -c conda-forge ipywidgets
```

## 在 JupyterLab 3.0 中安装

大多数情况下，安装 `ipywidgets` 会自动配置 JupyterLab 3.0 来使用组件。`ipywidgets` 包通过 `jupyterlab_widgets` 包（1.0 版）来实现这一点，该包将 JupyterLab 3 配置为显示和使用小部件。

如果你的 JupyterLab 和 IPython 内核安装在不同的环境中（例如，不同的环境提供不同的 Python 内核），那么安装需要两个步骤：

1. 在包含 JupyterLab 的环境中安装 `jupyterlab_widgets` 包（1.0 或更高版本）。
2. 在将使用 `ipywidgets` 的每个内核环境中安装 `ipywidgets`。

例如，如果使用 conda 环境，将 JupyterLab 安装在基本环境中，并将内核安装在名为 `ai` 的环境中，则命令如下：

```shell
conda install -n base -c conda-forge jupyterlab_widgets
conda install -n ai -c conda-forge ipywidgets
```
