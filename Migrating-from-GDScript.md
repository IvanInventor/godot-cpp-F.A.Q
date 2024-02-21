Things you need to know to quickly start in C++ with previous GDScript knowledge.
## How C++ scripting works?
Similar to creating classes in GDScript, that show up in classes list
```gdscript
extends Node
class_name CustomNode
```
you need to create new class in C++ and derive needed class
```cpp
class CustomNode : public godot::Node {
	GDCLASS(CustomNode, godot::Node);
};
```
Notice 2 things
- Public base class
- `GDCLASS` macro that takes class name and base class name

Finally, to be able to see your custom class in editor and be able to use it, you need to register it in your `register_types.cpp`
```cpp
void initialize_example_module(ModuleInitializationLevel p_level) {
	if (p_level != MODULE_INITIALIZATION_LEVEL_SCENE) {
		return;
	}

	ClassDB::register_class<CustomNode>();
}
```
And here it is!

![image](https://github.com/IvanInventor/godot-cpp-F.A.Q/assets/43908280/874fd69a-4eb7-4980-91ee-92ec2eef31d6)

For node types, creating custom class and using it in a scene is similar to attaching script to existing node. And then, you can still extend custom classes with GDScript! (with some caveats, of course)
