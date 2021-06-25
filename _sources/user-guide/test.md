---
jupytext:
  formats: ipynb,md:myst
  text_representation:
    extension: .md
    format_name: myst
    format_version: 0.13
    jupytext_version: 1.10.3
kernelspec:
  display_name: ai
  language: python
  name: ai
---

# 测试

```{code-cell} ipython3
import ipywidgets as widgets
from IPython.display import display

button = widgets.Button(description="Click Me!")
output = widgets.Output()

display(button, output)


@output.capture(wait=True)
def on_button_clicked(b):
    print(type(b))
    print("Button clicked.")
    b.icon = "warning"


button.on_click(on_button_clicked)
# button.click()
```

```{code-cell} ipython3
from ipywidgets import VBox, jsdlink, IntSlider, Button
s1, s2 = IntSlider(max=200, value=100), IntSlider(value=40)
b = Button(icon='legal')
jsdlink((s1, 'value'), (s2, 'max'))
VBox([s1, s2, b])
```

```{code-cell} ipython3
b = widgets.Button(description='Sample Button',
           layout=widgets.Layout(width='30%', height='50px', border='5px dashed blue'))
b
```
