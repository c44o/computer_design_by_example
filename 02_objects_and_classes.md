1. Итоговый вариант программы на Питоне:
```python
def shape_density(thing, weight, named_argument_value="this is a plain named argument value"):
    print(named_argument_value)
    return weight / call(thing, "area")

Shape = {
    "density": shape_density,
    "_classname": "Shape", 
    "_parent": None,
#cache exercise - неверно, в класс добавлять нельзя
    "_cache":{}
}

Color = {
    "_classname": "Color", 
    "_parent": None,
#cache exercise - неверно, в класс добавлять нельзя
    "_cache":{}
}

Red = {
    "_classname": "Red",
    "_parent": Color,
#cache exercise - неверно, в класс добавлять нельзя
    "_cache":{}
}

Violet = {
    "_classname": "Violet", 
    "_parent": Color,
#cache exercise - неверно, в класс добавлять нельзя
    "_cache":{}
}


#----------------------------------
def square_perimeter(thing):
    return 4 * thing["side"]

def square_area(thing):
    return thing["side"] ** 2

Square = {
    "perimeter": square_perimeter,
    "area": square_area,
    "_classname": "Square",
    "_parent": Shape,
        #cache exercise - неверно, в класс добавлять нельзя
    "_cache":{}
}

def square_new(name, side_size, color=Red):
    return {
        "name": name,
        "side": side_size,
        "_class": [Square, color],
        #cache method - right
        "_cache": {}
    }

def circle_perimeter(thing):
    return 3.14 * thing["radius"]

def circle_area(thing):
    return 3.14 * thing["radius"] ** 2

Circle = {
    "perimeter": circle_perimeter,
    "area": circle_area,
    "_classname": "Circle",
    "_parent": Shape,
#cache exercise - неверно, в класс добавлять нельзя
    "_cache":{}
}

def circle_new(name, radius, color=Red):
    return {
        "name": name,
        "radius": radius,
        "_class": [Circle, color],
        #cache method - right
        "_cache": {}
    }

#----------------------------------
def find(classes, method_name):
    for cls in classes:
        while cls is not None:
            if method_name in cls:
                return cls[method_name]
            cls = cls["_parent"]
    raise NotImplementedError(method_name)

# def find_recursive(classes, method_name):
#     for cls in classes:
#         if method_name in cls:
#             return cls[method_name]
#         cls = cls["_parent"]
#         if cls is not None:
#             find_recursive(cls, method_name)
#         else: return

def find_recursive(cls, method_name):
    if cls is None:
        return None
    if method_name in cls:
        return cls[method_name]
    return find_recursive(cls["_parent"], method_name)

def find_recursive_wrapper(classes, method_name):
    for cls in classes:
        found = find_recursive(cls, method_name)
        if found is not None:
            return found
    raise NotImplementedError(method_name)

# def call(thing, method_name):
#     return thing[method_name](thing)

# def call(thing, method_name, *args):
#     method = find(thing["_class"], method_name)
#     return method(thing, *args)

def call(thing, method_name, *args, **kwargs):
    # cache method
    if method_name in thing["_cache"]:
        return thing["_cache"][method_name](thing, *args, **kwargs)
    method = find(thing["_class"], method_name)
    # thing["_cache"][method] = method(thing, *args, **kwargs)
    thing["_cache"][method_name] = method
    return method(thing, *args, **kwargs)


# def type_of(thing):
#     return call(thing, "_classname")

def type_of(thing):
    return thing["_class"]

# def is_instance(thing, general_thing):
#     return find(general_thing) == general_thing

def is_instance(thing, general_thing):
    for cls in thing["_class"]:
        while cls is not None:
            if cls is general_thing:
                return True
            cls = cls["_parent"]
    return False        

#----------------------------------
examples = [square_new("sq", 3), circle_new("ci", 2)]
for ex in examples:
    n = ex["name"] 
    d = call(ex, "density", 5, named_argument_value = "hi from kwargs")
    is_instance_variable = is_instance(ex, Shape)
    print(f"the {ex["_class"][0]['_classname']} is instance of Shape")
    print(f"{n}: {d:.2f}")
```
2.Вывод этого кода в консоль:
```powershell
hi from kwargs
the [{'perimeter': <function square_perimeter at 0x0000000001563F60>, 'area': <function square_area at 0x000000000156CCC0>, '_classname': 'Square', '_parent': {'density': <function shape_density at 0x0000000001341440>, '_classname': 'Shape', '_parent': None}}, {'_classname': 'Red', '_parent': {'_classname': 'Color', '_parent': None}}] is instance of Shape
sq: 0.56
hi from kwargs
the [{'perimeter': <function circle_perimeter at 0x000000000156D940>, 'area': <function circle_area at 0x000000000156D9E0>, '_classname': 'Circle', '_parent': {'density': <function shape_density at 0x0000000001341440>, '_classname': 'Shape', '_parent': None}}, {'_classname': 'Red', '_parent': {'_classname': 'Color', '_parent': None}}] is instance of Shape
ci: 0.40
(base) PS C:\Users\Admin\Desktop\books_contents\Machine Learning\Python\code> python .\objects_and_classes.py
hi from kwargs
the Square is instance of Shape
sq: 0.56
hi from kwargs
the Circle is instance of Shape
```
или, тот же фрагмент, но картинкой:
<img width="1099" height="257" alt="image" src="https://github.com/user-attachments/assets/c0c0b37e-55a1-470f-9af7-fd10d7e07f36" />

