---
title: python
date: 2024-07-17 09:46:52
tags:
    - python
---
- 生成器返回文件流
```bash
def iterfile(filestream):
    yield from filestream

headers = {}
content_disposition = 'attachment; filename="{}"'.format(filename)
headers["Content-Disposition"] = content_disposition

z = zipfile.ZipFile(filestream, "r")
content = z.open(filename)

return StreamingResponse(iterfile(content),
                             status_code=200,
                             media_type="application/octet-stream",
                             headers=headers)

```
- 在 Python 中，__getitem__ 是一个特殊方法（也称为魔术方法），它允许类的实例像字典或列表一样使用方括号 [] 进行索引操作。这个方法通常用于实现自定义容器类，使得这些类的对象可以以更加直观的方式访问其内部数据。
```bash
class MyList:
    def __init__(self, elements):
        self.elements = elements
    
    def __getitem__(self, index):
        return self.elements[index]

# 创建 MyList 类的实例
my_list = MyList([1, 2, 3, 4, 5])

# 使用 [] 访问元素
print(my_list[0])  # 输出: 1
print(my_list[2])  # 输出: 3
```
