# Ways-to-assign-variables-in-Python

Within minutes of starting to learn Python, everyone learns how to define a variable. You can say:
``` python
x = 100
```
and voila!  Your You have created a variable “x”, and assigned the integer value 100 to it.  It couldn’t be simpler than that.

But guess what?  This isn’t the only way to define variables, let alone assign to them, in Python. And by understanding the other ways that we can define variables, and the nuances of the “simple” variable assignment that I described above, you can get a better appreciation for Python’s consistency, along with the idea that “everything is an object.”

Method 1: Plain ol’ assignment
The first way to define a variable is with the assignment operator, =.  Once again, I can say
``` python
x = 100
```
and I have assigned to x. If x didn’t exist before, then it does now. If it did exist before, then x now points to a new and different object.

What if the new, different object is also of a new and different type? We don’t really care; the nature of a dynamic language is such that any variable can point to an object of any type. When we say “type(x)” in our code, we’re not really asking what type of object the variable “x” can hold; rather, we’re asking what type of variable “x” is pointing to right now.

One of the major misconceptions that I come across in my Python courses and newsletter is just how Python allocates and stores data.  Many of my students come from a C or C++ background, and are thus used to the “box model” of variable assignment: When we declare the variable “x”, we have to declare it with a type.  The language then allocates enough memory to store that type, and gives that memory storage an alias, “x”.  When I say “x=100”, the language puts the value 100 inside of the box named “x”.  If the box isn’t big enough, bad news!

In Python, though, we don’t use the box model of variable assignment. Rather, we have the dictionary model of variable assignment: When we assign “x=100”, we’re creating (or updating) a key-value pair in a dictionary. The key’s name is “x”, and the value is 100, or anything else at all. Just as we don’t care what type of data is stored in a dictionary’s values, we also don’t care what type of value is stored inside of a Python variable’s value.

(I know, it’s a bit mind-bending to say that Python variables are stored in a dictionary, when dictionaries are themselves Python values, and are stored in Python variables. It’s turtles all the way down, as they say.)

Don’t believe me?  Just run the “globals” function in Python. You’ll get a dictionary back, in which the keys are all of the global variables you’ve defined, and the values are all of the values of those variables.  For example:

``` python
x = 100
y = [10, 20, 30]
globals()
```
``` html
{'__name__': '__main__', '__doc__': None, '__package__': None, '__loader__': <class '_frozen_importlib.BuiltinImporter'>, '__spec__': None, '__annotations__': {}, '__builtins__': <module 'builtins' (built-in)>, 'x': 100, 'y': [10, 20, 30]}
```

``` python
x = 200
y = {100, 200, 300}
globals()
```

``` html
{'__name__': '__main__', '__doc__': None, '__package__': None, '__loader__': <class '_frozen_importlib.BuiltinImporter'>, '__spec__': None, '__annotations__': {}, '__builtins__': <module 'builtins' (built-in)>, 'x': 200, 'y': {200, 100, 300}}
```

But wait, we can do even better:

``` python
globals()['x'] = 300
x
```
300

``` python
globals()['y'] = {'a':1, 'b':2}
y
```

{'a': 1, 'b': 2}

That’s right; the result of invoking “globals” is not only a dictionary showing us all of the global variables, but is something we can modify — and whose modifications not only reflect, but also affect, our global variables.

Now, you might be thinking, “But not all Python variables are globals.” That’s true; Python’s scoping rules describe four levels: Local, Enclosing, Global, and Builtins.  Normally, assignment creates/updates either a global variable or a local one.  How does Python know which one to use?

The answer is pretty simple, actually: When you assign inside of a function, you’re working with a local variable. When you assign outside of a function, you’re working with a global variable.  It’s pretty much as simple as that.  Sure, there are some exceptions, such as when you use the “global” or “nonlocal” keyword to force Python’s hand, and create/update a variable in a non-local scope when you’re within a function.   But the overwhelming majority of the time, you can assume that assignment within a function creates or updates a local variable, and outside of a function creates or updates a global variable.

Note that this means assignment inside of a “for” loop or “if” statement doesn’t create a local variable; it creates a global one.  It’s common for my students to believe that because they are inside of an indented block, the variable they are creating is not global.  Not true!

If you’re in a function and want to see the variables that have been created, you can use the “locals” function, which operates just like the “globals” function we looked at earlier.  Even better, you can use the “vars” function, which invokes “locals” inside of a function and “globals” outside of a function.

So the first type of variable assignment in Python is also the most common, and can be subdivided into two basic categories, local and global.

I should add that I’m only talking here about variable assignment, not attribute assignment. Attributes (i.e., anything that comes after a “.” character, as “b” in the expression “a.b”) are a totally different kettle of fish, and have their own rules and quirks.

Method 2: def
If you want to create a function in Python, you use the “def” keyword, as in:
``` python
def hello(name):
...     return f"Hello, {name}"    # I love f-strings!
```
While we often think that “def” simply defines a function, I think it’s easier to think of “def” as actually doing two things:

Creating a new function object
Assigning that function object to the name immediately following the “def”
Thinking about “def” this way, as object creation + assignment, makes it easier to understand a variety of different situations that Python developers encounter.

First of all, “def” defines variables in the same scope as a simple variable assignment would. So if you’re in the global scope, you’re creating a global variable. Remember that Python doesn’t have separate namespaces for data and functions. This means that if you’re not careful, you can accidentally destroy your function or variable definition:

``` python
x = 100
def x():
...     return "Hello!"
print(x*2)
```
Traceback (most recent call last):
 File "<stdin>", line 1, in <module>
TypeError: unsupported operand type(s) for *: 'function' and 'int'
```
Also:
``` python
def x():
... return "Hello!"
x = 100
print(x())
Traceback (most recent call last):
 File "<stdin>", line 1, in <module>
TypeError: 'int' object is not callable
```
In both of these cases, the programmer assumed that setting a variable and defining a function wouldn’t interfere with one another, with clearly disastrous results.

The fact that “def” assigns to a variable also explains why you cannot define a function multiple times in Python, each with its own function signature (i.e., argument count and types). Some people, for example, expect to be able to do the following:
``` python
def hello(name):
...     return f"Hello, {name}"

def hello(first, last):
...     return f"Hello, {first} {last}"

```
They then assume that we can invoke “hello” with either one argument (and invoke the first version) or two arguments (and invoke the second).  However, this isn’t true; the fact that “def” defines a variable means that the second “def” obliterates whatever you did with the first.  Consider this code, in which I define “x” twice in a row with two different values:

``` python
x = 100
x = 200
```

You cannot realistically think that Python will know which “x” to choose according to context, right?  In the same way, Python cannot choose a function definition for you.  It looks up the function’s name in “globals()”, gets the function object from that dictionary, and then invokes it (thanks to the parentheses).

A final aspect of seeing “def” as variable assignment has to do with inner functions — that is, functions defined within other functions.  For example:
``` python
def foo():
...    def bar():
...        print("I'm in bar!")
...    return bar
```

What’s happening here?  Well, let’s assume that we execute function “foo”.    Now we’re inside of a function, which means that every time we define a variable, it’ll be a local variable, rather than a global one. The next thing we hit is “def”, which defines a variable.  Well, that means “bar” is a local variable inside of the “foo” function.

Then, in the next line of “foo”, after defining “bar”, we return a local variable to whoever called “foo”.  We can return any kind of local variable we like from a function; in this particular case, though, we’re returning a function object.

This just scratches the surface of inner functions, but the logic — we’re defining and then returning a local variable in “foo” — is consistent, and should make more sense if you think of “def” as just assigning variables.

Method 3: import
One of the most powerful aspects of Python is its very mature standard library. When you download and install Python, you have hundreds (thousands?) of modules available to you and your programs, just by using the “import” statement.

Well, guess what? “import” is defining a variable, too. Indeed, it often helps to think about “import” as defining a single variable, namely the one whose name you give when you invoke it.  For example:

``` python
import os
```
When I invoke the above, I am doing three things:

1. Python finds the module file os.py (or some variation thereof) and executes it
2. Python defines the variable “os” in the current scope
3. All of the global variables defined in the module are turned into attributes on the “os” variable.
It’s pretty easy to see that “import” defines a variable:

``` python
type(os)
```
<class 'module'>
What’s a bit harder to understand is the idea that the global variables defined inside of our module all become attributes on the module object. For example, in “os.py”, there is a top-level definition of the “walk” function. Inside of the module, it’s a global variable (function).  But to whoever imports “os”, “walk” isn’t a variable.  Rather, it’s an attribute, “os.walk”.  That’s why if we want to invoke it, we need to use the complete name, “os.walk”.

That’s fine, and works pretty well overall. But if you’re going to be using “os.walk” a lot, then you might not want the overhead of saying “os.walk” each and every time. Instead, you might want to create a global variable whose value is the same as “os.walk”.  You might even say something like this:
``` python
walk = os.walk
```
Since we’re assigning a variable, and since we’re not inside of a function, we’re creating a global variable here.  And since “os.walk” is already defined, we’re simply adding a new reference (name) to “os.walk”.

A faster, easier, and more Pythonic way to do this is with:
``` python
from os import walk
```
Although to be honest, this isn’t quite the same as what I did before. That’s because “from os import walk” does find the “os.py” module file, and does execute its contents — but it doesn’t define “os” as a variable in our global namespace.  Rather, it only creates “walk” as a variable, pointing to the value of what the module knows as “os.walk”.

Does that mean that “from … import …” doesn’t actually load the module? That would be pretty silly, in that future imports of “os” would then be less effective and efficient.

Python is actually pretty clever in this case: When it executes the module file, it creates the module object in sys.modules — a dictionary whose keys are the names of the modules we have loaded.  That’s how “import” knows to import a file only once; it can run “in” on sys.modules, check to see if a module has already been loaded, and only actually import it if the module is missing from that dict.

What if the module has been loaded?  Then “import” still defines the variable in the current namespace.  Thus, if your project has “import os” in 10 different files, then only the first invocation of “import os” actually loads “os.py”.  The nine following times, you just get a variable definition that points to the module object.

In the case of “from-import”, the same thing happens, except that instead of assigning the module’s name as a variable in the current namespace, you get the name(s) you explicitly asked for.

In theory, you can use “import” and “from-import” inside of a function, in which case you’ll define a local variable.  I’m sure that there is some use for doing so, but I can’t think of what it would be.  However, here Python is also remarkably consistent, allowing you to do things that wouldn’t necessarily be useful.

In Python 2.7, there were two different implementations of the “pickle” module, one written in Python (“pickle”) and one written in C (“cPickle”).  The latter executed much faster, but wasn’t available on all platforms.  It was often recommended that programmers do this, to get the best possible version:

``` python
try:
    import cPickle as pickle
except ImportError:
    import pickle

pickle
```
<module 'cPickle' from '/usr/local/Cellar/python@2/2.7.14_3/Frameworks/Python.framework/Versions/2.7/lib/python2.7/lib-dynload/cPickle.so'>
This technique (which isn’t necessary with Python 3) only worked because “import” is executed at runtime, along with other Python code, and because “import” defines a variable.  What the above code basically says is, “Try to load cPickle but if you succeed, use the name pickle.   If you can’t do that, then just use the regular ol’ pickle library.”

Method 4: class
The fourth and final way to define a variable in Python is with the “class” statement, which we use to create (big surprise) classes.  We can create a class as follows:

``` python
class Foo(object):
    def __init__(self, x):
        self.x = x
        
```

Newcomers to Python often think of classes as blueprints, or plans, or descriptions, of the objects that will be created. But they aren’t that at all — classes in Python are objects, just like anything else.  And since they’re objects, they have both a type (which we can see with the “type” function) and attributes (which we can see with the “dir” function):

``` python
type(Foo)
<class 'type'>
```

``` python
dir(Foo)
```

['__class__', '__delattr__', '__dict__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__', '__getattribute__', '__gt__', '__hash__', '__init__', '__init_subclass__', '__le__', '__lt__', '__module__', '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__setattr__', '__sizeof__', '__str__', '__subclasshook__', '__weakref__']
The “class” keyword defines a new variable — Foo, in this case — which is assigned to a class object. A class object is just like everything else, except that it is callable, meaning that we can execute it (with parentheses) and get a new object back:

``` python
Foo(10)
```
<__main__.Foo object at 0x10da5fc18>
Defining a class means that we’re defining a variable — which means that if we have a previous class of the same name, we’re going to overwrite it, just as was the case with functions.

Class objects, like nearly all objects in Python, can have attributes added to them at runtime.  We can thus say:
``` python
Foo.abc = 100
```
And now I’ve added the “abc” attribute to the “Foo” object, which is a class.  I can also do that inside of a class definition:
``` python
    class Foo(object):
...     abc = 100
...     def __init__(self, x):
...         self.x = x

Foo.abc
```

100
How is this possible?  Haven’t we, in our class definition, created a variable “abc”?  Nope — it looks like a variable, but it’s actually an attribute on the “Foo” class.  And it must be an attribute, not only because we can retrieve it with “Foo.abc” later, but because all assignments inside of a class definition aren’t creating variables, but rather attributes.  Inside of “class Foo”, we’re thus creating two attributes on the class — not only “abc”, but also “__init__”, the method.

Does this seem familiar, the idea global variables we define in one context are seen as attributes in another context?  Yes, that’s right — we saw the same thing with modules.  Global variables defined in a module are seen, once the module is imported, as attributes on that module.

You can thus think of classes, in some ways, as modules that don’t require a separate file. There are other issues as well, such as the difference between methods and functions — but in general, understanding that whatever you do inside of a class definition is treated as a  module can help to improve your understanding.

I don’t do this very often, but what does it mean, then, if I define a class within another class?  In some languages, such “inner classes” are private, and only available for use within the outer class. Not so in Python; since “class” defines a variable and any variable assignments within a class actually create attributes, an inner class is available to us via the outer class’s attribute:

``` python
    class Foo(object):
...     def __init__(self, x):
...         self.x = x
...     class Bar(object):
...         def __init__(self, y):
...             self.y = y
...
b = Foo.Bar(20)
b.y
```
20
Variables in Python aren’t just for storing data; they store our functions, modules, and classes, as well.  Understanding how various keywords define and update these variables can really help to understand what Python code is doing — and also provides for some cool tricks you can use in your own code. 
#### Credit:  Reuven Lerner
