---
title: "pytest_01"
date: 2026-01-08T16:37:49+08:00
lastmod: 2026-01-08T16:37:49+08:00
author: ["wait"]
keywords: 
- 
categories:  # 没有分类界面可以不填写
- 
tags: # 标签
- 文章
description: "pytest_01"
weight:
slug: ""
draft: false # 是否为草稿
comments: true # 本页面是否显示评论
reward: true # 打赏
mermaid: true #是否开启mermaid
showToc: true # 显示目录
TocOpen: true # 自动展开目录
hidemeta: false # 是否隐藏文章的元信息，如发布日期、作者等
disableShare: true # 底部不显示分享栏
showbreadcrumbs: true #顶部显示路径
cover:
    image: "" #图片路径例如：posts/tech/123/123.png
    zoom: # 图片大小，例如填写 50% 表示原图像的一半大小
    caption: "" #图片底部描述
    alt: ""
    relative: false
---

# fixture 是什么

fixture 是 pytest 测试框架中的一个核心概念，可以理解为测试的"固定装置"或"夹具"，主要用于为测试函数提供可复用的测试环境或数据。

## fixture 的主要功能：

1. **准备测试环境**：在测试前设置好需要的数据、资源或状态
2. **提供测试数据**：为测试函数提供输入数据
3. **共享测试资源**：让多个测试函数共享相同的测试资源
4. **处理清理工作**：测试完成后清理环境，释放资源

## fixture 的定义和使用：

```python
@pytest.fixture
def numbers():
    # 设置测试数据
    a = 10
    b = 5
    # 返回测试数据
    return (a, b)

# 使用 fixture
def test_add_with_fixture(numbers):
    a, b = numbers
    assert add(a, b) == 15
```

## 特点：

1. **自动注入**：只需要在测试函数的参数中指定 fixture 的名称，pytest 就会自动将 fixture 的返回值注入到测试函数中
2. **可配置作用域**：可以设置 fixture 的作用域（function、class、module、session）
3. **支持 setup/teardown**：使用 yield 语句可以实现测试前的设置和测试后的清理工作
4. **可以嵌套**：fixture 可以依赖其他 fixture

## 示例：设置和清理

```python
@pytest.fixture
def database_connection():
    print("连接到测试数据库...")  # setup 部分
    db = {"connected": True}
    
    yield db  # 提供资源给测试函数
    
    print("关闭数据库连接...")  # teardown 部分
```

## 在你的代码中的应用：

1. **数据加载 fixture**：`yaml_test_data` 加载 YAML 测试数据
2. **对象创建 fixture**：`todo_lists_from_yaml` 根据 YAML 数据创建测试对象
3. **参数化 fixture**：`divide_test_case` 和 `scenario_data` 提供多组测试数据
4. **共享资源 fixture**：`load_yaml_file` 提供可在多个测试间共享的功能

fixture 使测试代码更简洁、更模块化，降低了测试维护成本，是编写高质量测试的重要工具。 

# Pytest 中的标记(Markers)

pytest中的标记(markers)分为两类：内置标记和自定义标记。

## 标记类型

### 内置标记
pytest框架自带的一些常用内置标记：

1. **@pytest.mark.skip** - 跳过测试
2. **@pytest.mark.skipif** - 有条件地跳过测试
3. **@pytest.mark.xfail** - 标记预期失败的测试
4. **@pytest.mark.parametrize** - 参数化测试
5. **@pytest.mark.usefixtures** - 使用指定的fixtures
6. **@pytest.mark.filterwarnings** - 过滤警告信息

### 自定义标记
`math`和`slow`是自定义标记，它们是在代码中定义的，而不是pytest框架自带的。在项目中，这些标记通常在`pytest.ini`文件中注册：

```ini
[pytest]
markers =
    slow: 标记测试为慢速测试
    math: 标记测试为数学相关测试
    basic: 标记测试为基础测试
    advanced: 标记测试为高级测试
    math_class: 标记整个测试类为数学测试
```

你可以根据项目需要自由创建任意数量的自定义标记，但最好在`pytest.ini`文件中注册它们，以避免pytest警告。

## 使用示例

### 自定义标记的使用

```python
@pytest.mark.slow
def test_slow_operation():
    time.sleep(0.1)
    assert add(1, 1) == 2

@pytest.mark.math
def test_only_math():
    assert divide(10, 2) == 5
```

### 组合使用多个标记

```python
@pytest.mark.slow
@pytest.mark.math
def test_slow_and_math():
    time.sleep(0.1)
    assert multiply(2, 3) == 6
```

### 带参数的标记

```python
@pytest.mark.parametrize("a, b, expected", [
    pytest.param(1, 2, 3, marks=pytest.mark.basic),
    pytest.param(5, 7, 12, marks=pytest.mark.basic),
    pytest.param(-10, 10, 0, marks=pytest.mark.advanced),
])
def test_add_with_marks(a, b, expected):
    assert add(a, b) == expected
```

### 类级别的标记

```python
@pytest.mark.math_class
class TestMathClass:
    def test_add_in_class(self):
        assert add(3, 4) == 7
    
    def test_subtract_in_class(self):
        assert subtract(10, 7) == 3
```

## 运行带标记的测试

```bash
# 运行所有标记为'math'的测试
pytest -m math

# 运行所有标记为'slow'的测试
pytest -m slow

# 运行所有不是'slow'的测试
pytest -m "not slow"

# 运行同时具有'slow'和'math'标记的测试
pytest -m "slow and math"

# 运行带有'basic'或'advanced'标记的测试
pytest -m "basic or advanced"
```

## 总结

`@pytest.mark.math`和`@pytest.mark.slow`等是项目中自定义的标记，用于对测试进行分类和筛选，而不是pytest框架自带的标记。标记提供了一种灵活的方式来组织测试并选择性地运行特定类型的测试。 
