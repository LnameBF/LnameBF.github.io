---
title: "如何指定运行特定的pytest测试方法"
date: 2026-01-08T16:37:49+08:00
lastmod: 2026-01-08T16:37:49+08:00
author: ["wait"]
keywords: 
- 
categories:  # 没有分类界面可以不填写
- 
tags: # 标签
- 文章
description: "如何指定运行特定的pytest测试方法"
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

# 如何指定运行特定的pytest测试方法

要指定运行`test_divide_parametrized`这个特定的测试函数，你可以使用pytest的命令行选项。以下是几种方式：

## 1. 使用函数名直接指定

最简单的方法是直接指定测试文件和测试函数名：

```bash
pytest test_yaml_data.py::test_divide_parametrized -v
```

这个命令会:
- 运行`test_yaml_data.py`文件中的
- 名为`test_divide_parametrized`的测试函数
- `-v`参数表示详细输出，会显示每个测试用例的结果

## 2. 使用关键字过滤

如果测试函数名称很长或者不方便输入完整名称，可以使用关键字过滤：

```bash
pytest -k "divide_parametrized" -v
```

这个命令会运行所有名称中包含`divide_parametrized`的测试函数。

## 3. 如果你为测试添加了标记(marker)

如果这个测试函数有特定的标记，比如`@pytest.mark.divide`：

```bash
pytest -m divide -v
```

## 4. 运行失败的测试后重新运行单个测试

如果你在全量测试中发现这个测试失败了，可以先运行所有测试，然后只重新运行这个失败的测试：

```bash
# 第一次运行所有测试
pytest
# 只重新运行上次失败的测试
pytest --lf
# 或者只运行上次失败的第一个测试
pytest --ff
```

## 5. 结合其他选项

你还可以结合其他选项，比如:

```bash
# 运行特定测试并显示详细输出
pytest test_yaml_data.py::test_divide_parametrized -v

# 运行特定测试并显示捕获的输出（比如print语句）
pytest test_yaml_data.py::test_divide_parametrized -v --capture=no

# 在测试失败时立即停止
pytest test_yaml_data.py::test_divide_parametrized -v -x
```

## 注意事项

1. 如果你的测试函数实际上没有使用`@pytest.mark.parametrize`，而是通过fixture进行参数化（如你的代码中所示），使用`-v`选项会看到更详细的输出，显示每个参数化的测试用例。

2. 如果`test_divide_parametrized`在不同的文件中有多个，你需要指定文件名确保运行正确的那个。

3. 如果你的测试在特定的目录下，需要先进入该目录或者在命令中指定路径。

最常用和最简单的方法是第一种：直接指定文件名和函数名。 
