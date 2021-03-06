
# Contributing new stage or lesson

Inside `stage` module there are two sub-modules named `one` and `two`.  
Each sub-module folder should contain `__init__.py`.  

## Creating a new stage
Inside `stage` folder, `mkdir` a new stage with name you want.  
Create a minimal `__init__.py` with following `dict`:  
```python
setup = {
  'path': 'backend/file/path/to/sub-module',   # This str will be use in os.path.join(), Do NOT starts with /
  'url': '/frontend/url/to/lesson',            # index url for stage page. Will be DEPRECATED soon.
  'package': 'package.to.sub-module'           # Package path to sub-module, use for __import__ function call.
}
```

## Creating a new lesson
Make sure there is a `__init__.py` inside the `stage/[stage_name]` folder (such as `stage/one`).

Create a new file with any name you like (although snake_case is preferred). 
**IMPORTANT**
```
If you wish your lessons to be sorted then they should be named with an index. For example:
  Say you have a lesson you want to be called i_love_haruna, and you want it to be the 35th lesson.
  You shoud create a file called s35_i_love_haruna.py with a async function like:
  async def i_love_haruna(request):
```
With following code:  
```python
from utils.form import blank_form
from utils import fields_generate

# YOU SHOULD PROPERLY EDIT FEW SECTION:
# route:       dict for good Sanic route setup and auto import.
# data:        dict for present at Frontend
# answer:      function is to judge stdout result is correct or wrong.
#              This must be manually write in order to judge.

route = {
    'type': blank_form, # Right now only provide *blank_form* one kind of form 181004.
    'url': '/stage/one/hello_python',
    'methods': [ 'GET', 'POST' ]
}
data = {
    'title': 'Hello Python',  # Any title would show in Frontend.
    # image is optional, suggest use https
    'image': 'https://community-cdn-digitalocean-com.global.ssl.fastly.net/assets/tutorials/images/large/EBOOK_PYTHON_no-name.png?1516826609',
    'description': [
        'Hello there!',
        'I see that you are a new face!',
        'Try to say Hello to me!'
    ],
    # code with a array, each blank show with five underline(_),
    # fronend will automatically translate to <input> tag.
    # remember each line should escape with special character.
    'code': [
        '_____(\'_____\')'
    ],
    'fields': []
}
data['fields'] = fields_generate(data)  # NEVER remove this line!!

async def sanic_request(request):
    try:
        return override(request)
    except NameError:
        global data, route
        return route['type'](data, request, answer)

# If you don't want to use any type of those.
# You can write yourself one, just properly handle Sanic request.
# **IMPORTANT** Unless you are sure to use customize one, or do not comment out this function.
# def override(request):
#     pass

def answer(stdout, stderr):
    try:
        if stderr != []:
            return False
        else:
            return stdout[0].decode() == 'Hello Python\n'
    except:
        return False

```

## Provide a test
After you create a new lesson it is important to make sure the code functions as intended.  
Inside the `tests/stage/[stage name]/` folder, create a new test.py file that starts with `test_`.
For example, `s1_hello_python.py` would have a test file called `test_s1_hello_python.py`

Add following code:
```python
from stage.[stage name].[lesson name] import route, data
from tests.utils import check_attributes, post


# This test function must declare on the top in order to test first.
# It checks the attribute is not missing inside the [lesson].py
# DO NOT REMOVE OR MOVE DOWN THIS FUNCTION !!!
def test_attributes():
    check_attributes(route, data)

# This is the main test function, everything you need just only req_data.
async def test_lesson(test_cli):
    req_data = {
        ...
        'your_filed_with_id': 'field_answer'
        ...
    }
    await post(cli=test_cli, url=route['url'], data=req_data)

# This function provide a inner function for further override.
# In case you want to customize any check.
# You can pick one to use.
# async def test_lesson(rest_cli):
#     
#     def override(res_data):
#         # Any other check you wan to do.
#
#     req_data = {
#         ...
#         'your_filed_with_id': 'field_answer'
#         ...
#     }
#     await post(cli=test_cli, url=route['url'], data=req_data, callback=override)
```

Of course you can add more than one test if you wish.
Remember that make sure every test should be in the same test file.
Also, every function should be `async`, have a `test_` prefix and only the `test_cli` argument.

For example:
```python
# I want a new test function named haruna_cute
async def test_haruna_cute(test_cli):
    ...
    req_data = {
        ...
        'desire_field_key': 'desire_data'
        ...
    }
    ...
```
