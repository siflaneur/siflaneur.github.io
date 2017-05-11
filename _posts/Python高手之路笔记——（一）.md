---
title: Python高手之路
date: 2017-04-22 20:31:40
categories: Python


---

趁着这几天京东满150-100，买了几本Python的书，更新几期博客。

## Python 高手之路

### 项目布局

---

关于Python的项目布局应该保持简单，审慎地使用包和层次结构：层次过深/过平都不好。

标准的目录结构如下

```
../foobar/
|- docs #文档
|	|- conf.py
|	|- quickstart.rst
|	|- index.rst
|- foobar
|	|- __init__.py
|	|- cli.py
|	|-storage.py
|	|- test 测试用例
|		|- __init__.py
|		|- test_storage.py
|		|- test_cli.py
|- README.rst
|- requirements.txt
|- test_requirements.txt # 测试所需要的包依赖
```

#### 注意事项

一个常犯的错误时将单元测试放在包目录的外面，这些测试实际上应该放在软件的子包中，以便：

- 不会偶尔被`setuptools`作为 tests 顶层模块自动安装
- 能够被安装，且被其他包用于构建自己的单元测试

其他的一些额外数据，如图片，shell脚本放到觉得合适的地方即可。

除此之外，下面这些顶层目录也经常出现：

- `etc` 存放配置文件的样例
- `tools` 存放工具相关的 shell 脚本
- `bin` 存放将被 setup.py 安装的二进制脚本
- `data` 存放其他类型的文件，如媒体文件

还应注意一点，避免创建那种只有一个`__init__.py` 文件的目录。