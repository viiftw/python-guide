# Python style (VN)

## Mục lục

- [Code](#code)
  - [Nguyên tắc cơ bản](#nguyên-tắc-cơ-bản)
  - [Tính nguyên tử](#tính-nguyên-tử)
  - [Khối logic](#khối-logic)
  - [Methods, functions, and modules](#methods-functions-and-modules)
  - [Imports](#imports)
  - [Files `__init__.py`](#files-__init__py)
  - [Tài liệu hóa](#tài-liệu-hóa)
  - [Đặt tên](#đặt-tên)
- [Pull Requests](#pull-requests)
- [Tools](#tools)
  - [Testing (pytest)](#testing-pytest)
  - [Package manager (pip)](#package-manager-pip)
  - [Code formatting (Black)](#code-formatting-black)
  - [Virtual environments (venv)](#virtual-environments-venv)
  - [Linter (flake8)](#linter-flake8)
- [Vấn đề khác](#vấn-đề-khác)

## Code

### Nguyên tắc cơ bản

- **Duy trì được** (có hiểu mình viết gì sau 1 vài năm không?)
- **Đơn giản** (giữa 2 cách làm đơn giản và phức tạp nên chọn cách đơn giản)
- **Rõ ràng** (người khác có dễ hiểu code đang làm gì không?)

### Tính nguyên tử

**1 action ~ 1 line**:

Nên có chính xác **một** hành động trên mỗi dòng, điều này sẽ khiến code tường minh hơn

Bad ❌:

```python
# 1. 3 function calls
foo_result = foo(bar(spam(x)))

# 2. 4 action
result = calculate_weather(foo.bar.spam.hello)
```

Good ✅:

```python
# 1. thực hiên gọi đến từng function lần lượt
spam_result = spam(x)
bar_result = bar(spam_result)
foo_result = foo(bar_result)

# 2. đọc tuần tự các thuộc tính bar, spam, hello và gọi function calculate_weather
bar = foo.bar
spam = bar.spam
hello = spam.hello
result = calculate_weather(hello)
```

### Khối logic

Cố gắng chia code thành các khối hợp lý — theo cách này sẽ dễ dàng đọc và hiểu bản chất hơn nhiều.

Bad ❌:

```python
def register_model(self, app_label, model):
    model_name = model._meta.model_name
    app_models = self.all_models[app_label]
    if model_name in app_models:
        if (model.__name__ == app_models[model_name].__name__ and
                model.__module__ == app_models[model_name].__module__):
            warnings.warn(
                "Model '%s.%s' was already registered. "
                "Reloading models is not advised as it can lead to inconsistencies, "
                "most notably with related models." % (app_label, model_name),
                RuntimeWarning, stacklevel=2)
        else:
            raise RuntimeError(
                "Conflicting '%s' models in application '%s': %s and %s." %
                (model_name, app_label, app_models[model_name], model))
    app_models[model_name] = model
    self.do_pending_operations(model)
    self.clear_cache()
```

Good ✅:

```python
def register_model(self, app_label, model):
    model_name = model._meta.model_name
    app_models = self.all_models[app_label]
    
    if model_name in app_models:
        if (
            model.__name__ == app_models[model_name].__name__ and
            model.__module__ == app_models[model_name].__module__
        ):
            warnings.warn(
                "Model '%s.%s' was already registered. "
                "Reloading models is not advised as it can lead to inconsistencies, "
                "most notably with related models." % (app_label, model_name),
                RuntimeWarning, stacklevel=2)
            
        else:
            raise RuntimeError(
                "Conflicting '%s' models in application '%s': %s and %s." %
                (model_name, app_label, app_models[model_name], model))
        
    app_models[model_name] = model
    
    self.do_pending_operations(model)
    self.clear_cache()
```

### Methods, functions, and modules

Giới hạn kích thước cho một method/function là 50 dòng. Việc đạt đến giới hạn kích thước cho biết method/function đang thực hiện quá nhiều — vì vậy hãy phân tách các hành động bên trong.

Giới hạn kích thước module là 300 dòng. Việc đạt đến giới hạn kích thước cho biết rằng module đã có quá nhiều logic — vì vậy hãy phân tách module thành nhiều modules.

Độ dài 1 dòng là 100 ký tự.

### Imports

Import trên các dòng riêng biệt, đường dẫn import nên là tuyệt đối (absolute)

Bad ❌:

```python
# spam.py
import os, sys
from . import foo, bar
```

Good ✅:

```python
# spam.py
import os
import sys
from some.absolute.path import foo, bar
```

### Files `__init__.py`

Chỉ viết imports trong các file `__init__.py`.

Bởi thông thường lập trình viên sẽ đọc file này cuối cùng hoặc bỏ qua

### Tài liệu hóa

Khuyến nghị viết comments cho functions, methods, classes.

"Code is read much more than it is written"

### Đặt tên

Đặt tên biến **có ý nghĩa** và tránh viết tắt

```bash
module_name, package_name, ClassName, method_name, ExceptionName, function_name, GLOBAL_CONSTANT_NAME, global_var_name, instance_var_name, function_parameter_name, local_var_name
```

## Pull Requests

1 Pull Request = 1 issue

Refactoring = 1 PR

1 PR không nên vượt quá +/- 500 dòng thay đổi.

## Tools

### Testing (pytest)

[pytest](https://pytest.org) - code testing framework

Cấu hình đề xuất `pytest.ini`:

```ini
[pytest]
python_files = tests.py test_*.py *_tests.py
```

### Package manager (pip)

### Virtual environments (venv)

### Code formatting (Black)

Black - PEP8 tự động định dạng code (VSCode extension)

Cấu hình đề xuất `pyproject.toml`:

```toml
[tool.black]
line-length = 100
target-version = ['py38']
exclude = '''
(
  \.eggs
  |\.git
  |\.hg
  |\.mypy_cache
  |\.nox
  |\.tox
  |\.venv
  |_build
  |buck-out
  |build
  |dist
)
'''
```

### Linter (flake8)

[flake8](https://flake8.pycqa.org/en/latest/) - PEP8

Cấu hình đề xuất `.flake8`:

```ini
[flake8]
max-line-length = 100
max-complexity = 5
exclude = .venv,venv,**/migrations/*,snapshots
per-file-ignores =
    tests/**: S101
    **/tests/**: S101
```

## Vấn đề khác

- IDE: Visual Studio Code
- Hạn chế sử dụng magic methods

**Tài liệu này tham khảo Evrone & Google style guidelines đã tóm lược.**

## Ref

[google python style](https://google.github.io/styleguide/pyguide.html)  
[evrone style](https://github.com/evrone/evrone-python-guidelines)
