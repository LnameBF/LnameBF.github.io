---
title: "CSharp关键字-Out"
date: 2026-01-08T16:37:49+08:00
lastmod: 2026-01-08T16:37:49+08:00
author: ["wait"]
keywords: 
- 
categories:  # 没有分类界面可以不填写
- 
tags: # 标签
- 文章
description: "CSharp关键字-Out"
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

~~~c#
static void Main(string[] args)
        {
            int[] nums = { 0, 1, 2, 3, 4, 5, 6, 7, 8, 9 };
            int[] result = GetMaxMinSumAvg(nums);
            Console.WriteLine("Max-{0}，Min-{1}，Sum-{2}，Avg-{3}", result[0], result[1], result[2], result[3]);
            Console.ReadKey();
        }
 
        //返回数组中最大值，最小值，总和，平均值，此时4个返回值所以声明一个数组
        public static int[] GetMaxMinSumAvg(int[] nums)
        {
            //假设res[0]最大值，res[1]最小值，res[2]总和，res[3]平均值
            int[] res = new int[4];
            res[0] = nums[0];//max
            res[1] = nums[0];//min
            res[2] = 0;//sum
            for (int i = 0; i < nums.Length; i++)
            {
                if (nums[i] > res[0])
                {
                    res[0] = nums[i];
                }
                if (nums[i] < res[1])
                {
                    res[1] = nums[i];
                }
                res[2] += nums[i];
            }
            res[3] = res[2] / nums.Length;
            return res;
        }
~~~
## 2.Out示例1

返回值类型不同时怎么办？

out参数：返回值多个，不限类型

注意事项：

 * 调用方法之前，对out参数传递的变量只需声明，可以赋值也可以不赋值，赋值也会在方法中被覆盖掉
 * 使用out参数传递变量时，必须在方法内为其赋值，否则return的返回值没有意义
 * 方法的参数使用out修饰时，调用该方法时也要加上out关键字
 * 使用out修饰的变量不需要return     

~~~c#
static void Main(string[] args)
        {
            int[] nums = { 0, 1, 2, 3, 4, 5, 6, 7, 8, 9 };
            int max;
            int min;
            int sum;
            int avg;
            string s;
            Test(nums, out max, out min, out sum, out avg, out s);
            Console.WriteLine(max);
            Console.WriteLine(min);
            Console.WriteLine(sum);
            Console.WriteLine(avg);
            Console.WriteLine(s);
            Console.ReadKey();
        }
 
        public static void Test(int[] nums, out int max, out int min, out int sum, out int avg, out string s)//5个out参数修饰的是多余的返回值
        {
            //out参数必须在方法内部为其赋值，否则返回去没有意义
            max = nums[0];
            min = nums[0];
            sum = 0;
            for (int i = 0; i < nums.Length; i++)
            {
                if (nums[i] > max)
                {
                    max = nums[i];
                }
                if (nums[i] < min)
                {
                    min = nums[i];
                }
                sum += nums[i];
            }
            avg = sum / nums.Length;
            //此方法void无返回值，无需写return
            s = "Test_Result";
        }
~~~ 

~~~
static void Main(string[] args)
        {
            int n;
            bool b = MyTryParse("123", out n);
            Console.WriteLine(b);
            Console.WriteLine(n);
            Console.ReadKey();
        }
 
        public static bool MyTryParse(string s, out int result)
        {
            result = 0;
            try
            {
                result = Convert.ToInt32(s);
                return true;
            }
            catch
            {
                return false;
            }
        }
~~~
