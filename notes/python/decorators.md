## Python学习之装饰器（Decorators）
### 1.sample
```python
from functools import wraps

def decorator_name(func):
    @wraps(func)
    def decorated(*args, **kwargs):
        if not can_run:
            return "Function will not run"
        return func(*args, **kwargs)
    return decorated

@decorator_name
def func():
    return "Function is running"

can_run = True
print(func())

can_run = False
print(func())
```
*`wraps`接受一个函数来进行装饰，并加入了复制函数名称、注释文档、参数列表等功能*
```python
from functools import wraps

class logit(object):
    def __init__(self, logfile='out.log'):
        self.logfile = logfile

    def __call__(self, func):
        @wraps(func)
        def wrapped_function(*args, **kwargs):
            log_string = func.__name__ + " was called"
            print(log_string)
            # 打开logfile并写入
            with open(self.logfile, 'a') as opened_file:
                # 现在将日志打到指定的文件
                opened_file.write(log_string + '\n')
            # 现在，发送一个通知
            self.notify()
            return func(*args, **kwargs)
        return wrapped_function

    def notify(self):
        # logit只打日志，不做别的
        pass

@logit()
def myfunc1()
    pass
```