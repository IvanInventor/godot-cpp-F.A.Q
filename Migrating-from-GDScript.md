Things you need to know to quickly start in C++ with previous GDScript knowledge.
## How C++ scripting works?
### Extending classes
Similar to creating classes in GDScript, that show up in classes list
```gdscript
extends Node
class_name CustomNode
```
you need to create new class in C++ and derive needed class
```cpp
class CustomNode : public godot::Node {
protected:
	static void _bind_methods() {}

	GDCLASS(CustomNode, godot::Node);
};
```
Notice 3 things
- Public base class
- Protected `static void _bind_methods()` method we'll use later to register bunch of things
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

### Overriding virtual methods
Overriding virtual methods of nodes in GDScript works really similar in C++, see this example for method prototypes
```gdscript
extends Node

func _enter_tree():
	pass

func _exit_tree():
	pass

func _ready():
	pass

func _physics_process(delta: float):
	pass

func _process(delta: float):
	pass

func _input(event: InputEvent):
	pass

func _shortcut_input(event: InputEvent):
	pass

func _unhandled_input(event: InputEvent):
	pass

func _unhandled_key_input(event: InputEvent):
	pass
```
```cpp
#include <godot_cpp/classes/node.hpp>
#include <godot_cpp/classes/input_event.hpp>

class Example : public godot::Node {
protected:
	static void _bind_methods() {};


	void _enter_tree() override;
	
	void _exit_tree() override;
	
	void _ready() override;
	
	void _physics_process(double delta) override;
	
	void _process(double delta) override;
	
	void _input(const godot::Ref<godot::InputEvent>& event) override;
	
	void _shortcut_input(const godot::Ref<godot::InputEvent>& event) override;
	
	void _unhandled_input(const godot::Ref<godot::InputEvent>& event) override;
	
	void _unhandled_key_input(const godot::Ref<godot::InputEvent>& event) override;
};
```
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

## Type casting
Dynamic type casting can be done like this
```gdscript
func _input(event):
	if event is InputEventKey:
		print(event.keycode)
```
```cpp
#include <godot_cpp/classes/input_event_key.hpp>

void Example::_input(const godot::Ref<godot::InputEvent>& event) {
	if(godot::InputEventKey* keyevent = godot::Object::cast_to<godot::InputEventKey>(event.ptr())) {
		godot::UtilityFunctions::print(keyevent->get_keycode());
	}
}
```
```cpp
