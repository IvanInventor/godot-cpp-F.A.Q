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

## Where are my GDScript functions?
### Singletons
Singleton methods in C++ are called like this
```gdscript
var path = OS.globalize_path('res://resource.tscn')
```
```cpp
#include <godot_cpp/classes/project_settings.hpp>

godot::String path = godot::ProjectSettings::get_singleton()->globalize_path("res://resource.tscn");
```
### Global functions
You can find *almost* all global functions (@GlobalScope) in UtilityFunctions class
```gdscript
print("Hello world!")
push_error("My error!")
print(sqrt(16))
print(floor(42.56))

# Exceptions
var res = load('res://scene.tscn')
```
```cpp
#include <godot_cpp/variant/utility_functions.hpp>

godot::UtilityFunctions::print("Hello world!");
godot::UtilityFunctions::push_error("My error!");
godot::UtilityFunctions::print(sqrt(16));
godot::UtilityFunctions::print(floor(42.56));

// Exceptions
#include <godot_cpp/classes/resource_loader.hpp>

godot::Ref<godot::PackedScene> res = godot::ResourceLoader::get_singleton()->load("res://scene.tscn");
```
