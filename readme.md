# PyTemplatesForPython Quick Start (beta)
## Install module
```bash
pip install pytemplatesforpython
```
## Create simple template
Write some text:
```
Plain text
```
Add a variable:
```
Plain text written by {{ insert(name) }}
```
And an object:
```
Plain text is written by {{ insert(author.name) }}
Some imformation about him:
He`s {{ insert(author.age )}} years old, he lives in {{ insert(author.city) }}
```
You ask what else can you do?  
I answer — all you can in the simple python!
#### Several nuances:
 - Don`t allow symbols of a new line inside of the double braces ({{}})
 - Do you want to use for, while, if, etc? I am just going to show how to do it, now just remember: {{+}} to increase Tabs number, {{-}} to decrease it.
 - All of the expressions in double braces are connected, you can write {{a = 3}} first, and then {{insert(a)}}
```
Plain text is written by {{ insert(author.name) }}
Several facts about him:
{{ for i, fact in enumerate(author.facts): }}{{+}}
Fact №{insert(i)}: {{ insert(fact) }}
{{-}}
```
Can you see it now? Expressions those are between {{+}} and {{-}} will have a tab before them, just like:
```python
for fact in author.facts:
  insert(i)
  insert(fact)
```
But this still won't work: all templates basically have just one parameter they can use in expressions - "context". Let's divide it into several parameters:
```
{{ man: Creature = context["man"] }}
{{ monkey: Creature = context["monkey"] }}
Man loves {{ insert(man.loves) }} and monkey loves {{ insert(monkey.loves) }}.
Man lives in {{ insert(man.lives_in) }}, monkey lives {{ insert(monkey.lives_in) }}.
```
For now we used templates only for the plain text but the most common usage is for HTML code:
```html
<!DOCTYPE html>
{{ author = context["author"] }}
<html>
<body>
  <p>Plain text is written by {{ insert(author.name) }}</p>
  <p>Several facts about him:</p>
  <ul>
    {{ for i, fact in enumerate(author.facts): }}{{+}}
      <li>
        Fact №{{ insert(i) }}: {{ insert(fact) }}.
      </li>
    {{-}}
  </ul>
</body>
</html>
```
## Work with templates
### Okay, we've created some templates, let's work with them in Python.
Save one template into template.html and import FileTemplate from the module:
```python
from pytemplatesforpython import FileTemplate
```
Create a FileTemplate object:
```python
template: FileTemplate = FileTemplate("template.html")
```
Create an Author class that we used in the template:
```python
class Author:
  def __init__(self, name, facts):
    self.name = name
    self.facts = facts

author = Author("Alexey", ["lives in Israel", "knows Python, Java and JavaScript", "can`t think of more facts"])
```
Render the template:
```python
result: str = template.render({"author":author})
```
Congrutilations! Now you can check what did we get,
```python
print(result)
```
write the result to the some another file,
```python
with open("result.html", "w+") as file:
  file.write(result)
```
or, if you use django, you can make an HttpResponce that every view has to return:
```python
from django.http import HttpResponse
def view(req):
  return HttpResponce(template.render({"author":author}))
```
## TemplatesLoader
In real project you usually need to use many templates, as example for different website pages. For that you can use TemplatesLoader. Import it:
```python
from pytemplatesforpython import TemplatesLoader
```
Create a TemplatesLoader object. You can specify a directory, where are all your templates stored or it will be current working directory:
```python
loader: TemplatesLoader = TemplatesLoader("templates/")
```
When you create a template, it compiles, in that step some syntax errors can be found. You can compile all your templates at start or compile ones that are being needed. To compile some of templates at start use:
```python
loader.load_template(filename)
```
Or you can load all the templates from the folder and its subfolders:
```python
loader.recursively_load_folder(filename)
```
To get one of the templates, never mind, if it was loaded or no use:
```python
loader.get_template(filename)
```
Please note that all of paths should be satisfied as relative to the path, that has been satisfied when the loader was created.
## Good Luck!