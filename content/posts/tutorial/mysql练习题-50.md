---
title: "Mysql练习题-50"
date: 2026-01-08T16:37:49+08:00
lastmod: 2026-01-08T16:37:49+08:00
author: ["wait"]
keywords: 
- 
categories:  # 没有分类界面可以不填写
- 
tags: # 标签
- 文章
description: "Mysql练习题-50"
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

1. 本文汇总了不同版本的SQL面试50题的题目，删去了重复内容，并根据涉及知识点进行分类，按照由易到难、相似题放在一起的思路进行排序并重新编号。适合sql新手按知识点学习/检测自己的

学习效果。

2. 我使用软件是MySQL8.0，使用其他数据库软件，代码写法可能略有不同。文中给出的是我的解答，算是最近学习的小结。数据分析新手，欢迎交流指正（特别是52、53题，有没有什么更好的思路呢）。

3. 原始表和汇总后的53道题目分类如下所示。创建原始表非本文重点，代码放在文章最后，有需要的小伙伴可以先跳到最后。

【**Like**】
- like与%的三种用法：'%字符'/'%字符%'/'字符%'


1. 查询「李」姓老师的数量 【Like】

```
-- 1. 查询「李」姓老师的数量 select count(*) from teacher where tname like '李%';
```

2. 查询名字中含有「风」字的学生信息【Like】

```
-- 2. 查询名字中含有「风」字的学生信息 select * from student where sname like '%风%';
```



【**聚合函数、group by和where的相爱相杀**】

1.聚合函数sum/avg/count/max/min经常与好基友group by搭配使用

2.在使用group by时，select后面只能放
- 常数（如数字/字符/时间）- 聚合函数- 聚合键（也就是group by后面的列名）；
因此，在使用group by时，千万不要在select后面放聚合键以外的列名！

3.where函数后面不能直接使用聚合函数！（考虑放在having后面/变成子查询放在where后面）



3. 查询男生、女生人数【聚合函数】

```
-- 3. 查询男生、女生人数 select ssex, count(*) from student group by ssex;
```

4. 查询课程编号为02的总成绩【聚合函数】

```
-- 4. 查询课程编号为02的总成绩 select cno, sum(score) from sc group by cno having cno='02';
```

 5. 查询每门课程的平均成绩，结果按平均成绩降序排列，平均成绩相同时，按课程编号升序排列【聚合函数】

```
-- 5. 查询每门课程的平均成绩，结果按平均成绩降序排列，平均成绩相同时，按课程编号升序排列 select cno, avg(score) as avg_score from sc group by cno order by avg_score desc, cno;
```

6. 求每门课程的学生人数 【聚合函数】

```
-- 6. 求每门课程的学生人数 select cno, count(sno) as student_number from sc group by cno;
```

7. 统计每门课程的学生选修人数（超过 5 人的课程才统计）【聚合函数】

```
-- 7. 统计每门课程的学生选修人数（超过 5 人的课程才统计） select cno, count(*) as student_number from sc group by cno having count(*)>5 order by student_number desc, cno;
```

8. 检索至少选修两门课程的学生学号 【聚合函数】

```
-- 8. 检索至少选修两门课程的学生学号 select sno from sc group by sno having count(cno)>=2;
```



【**子查询**】

1.select的结果列全部来自同一张表（而select的条件列来自不同表里），考虑子查询；

select的结果列来自多张表，考虑联结

2.子查询与in/not in是好基友

3.“所有/全部/都"类型的查询可以考虑not in求补集



9. 查询在 SC 表存在成绩的学生信息【子查询】

```
-- 9. 查询在 SC 表存在成绩的学生信息 select * from student where sno in (select sno from sc);
```

10. 查询不存在" 01 "课程但存在" 02 "课程的情况【子查询】

```
-- 10. 查询不存在" 01 "课程但存在" 02 "课程的情况 select * from sc where sno not in (select sno from sc where cno =01) and sno in (select sno from sc where cno=02);
```

11. 查询同时存在" 01 "课程和" 02 "课程的情况【子查询】

```
-- 11. 查询同时存在" 01 "课程和" 02 "课程的情况 select * from student where sno in(select sno from sc where cno='01') and sno in (select sno from sc where cno='02');
```

12. 查询出只选修两门课程的学生学号和姓名【子查询】

```
-- 12. 查询出只选修两门课程的学生学号和姓名 select sno, sname from student where sno in (select sno from sc group by sno having count(cno)=2);
```

13. 查询没有学全所有课程的同学的信息 【子查询】

```
-- 13. 查询没有学全所有课程的同学的信息 -- 注意：用子查询求所有课程数而不是直接写3；student表中有没选课的学生 select * from student where sno not in (select sno from sc group by sno having count(cno)=(select count(*) from course));
```

14. 查询选修了全部课程的学生信息【子查询】

```
-- 14. 查询选修了全部课程的学生信息 select * from student where sno in (select sno from sc group by sno having count(cno)=(select count(cno) from course));
```

15. 查询所有课程成绩均小于60分的学号、姓名【子查询】

```
-- 15. 查询所有课程成绩均小于60分的学号、姓名 /* 思路：先选出有课程成绩>=60分的学号，再用not in求补集得到所有成绩均<60的学号； 注意student表里有未选课的学生！*/ select sno, sname from student where sno not in (select distinct sno from sc where score>=60) and sno in (select distinct sno from sc);
```

16. 查询课程编号为 01 且课程成绩在 80 分以上的学生的学号和姓名【子查询】

```
-- 16. 查询课程编号为 01 且课程成绩在 80 分以上的学生的学号和姓名 select sno, sname from student where sno in (select sno from sc where cno='01' and score>80);
```

17. 查询学过「张三」老师授课的同学的信息 【子查询】

```
-- 17. 查询学过「张三」老师授课的同学的信息 -- 四表串联查询。法1： select * from student where sno in (select sno from sc, course, teacher where teacher.tname='张三' and course.tno=teacher.tno and sc.cno=course.cno); -- 经过试验，即使张三教了不止一门课，也可用使用等号的法1求出所有结果 -- 法2：直接套三个子查询也可以，但是过于冗长，当子查询串联的表比较多时，比较推荐法1用where和等号代替一部分子查询 select * from student where sno in (select sno from sc where cno in (select cno from course where tno= (select tno from teacher where tname='张三')));
```

18. 查询没学过"张三"老师讲授的任一门课程的学生姓名 【子查询】

```
-- 18. 查询没学过"张三"老师讲授的任一门课程的学生姓名 -- 思路：先求出学过张三老师课程的学生，再在student表里面求补集。注意有没选课任何课的学生。 select sname from student where sno not in (select sno from sc, course, teacher where teacher.tname='张三' and course.tno=teacher.tno and sc.cno=course.cno);
```

19. 查询至少有一门课与学号为" 01 "的同学所学相同的同学的信息 【子查询】

```
-- 19. 查询至少有一门课与学号为" 01 "的同学所学相同的同学的信息 -- 注意：记得排除学号为“01”的同学本人 select * from student where sno in (select distinct sno from sc where cno in (select cno from sc where sno='01')and sno <>'01');
```

20. 查询和" 01 "号的同学学习的课程完全相同的其他同学的信息【子查询】

```
-- 20. 查询和" 01 "号的同学学习的课程完全相同的其他同学的信息 -- 思路：条件一（表a）：在19题选出至少一门课相同的同学的基础上（in子查询），与01同学选课相同的科目数=01同学的总科目数 -- 条件二（表b）：选课总数与01同学的选课总数也相同 -- 接下来的步骤，表a与表b相交求学号，再结合student表求信息 select * from student where sno in (select a.sno from -- 条件一：在19题选出至少一门课相同的同学的基础上（in子查询），与01同学选课相同的科目数=01同学的总科目数 (select sno, count(cno) from sc where cno in (select cno from sc where sno=01) group by sno having count(cno)=(select count(cno) from sc where sno=01) and sno <>01) a inner join -- 条件二：选课总数与01同学的选课总数也相同 (select sno, count(sno) from sc group by sno having count(sno)=(select count(cno) from sc where sno=01) and sno <>01) b on a.sno=b.sno)
```

21. 查询不同课程成绩相同的学生的学生编号、课程编号、学生成绩 【子查询】

```
-- 21. 查询不同课程成绩相同的学生的学生编号、课程编号、学生成绩 /* 注意：题目本身有歧义，我的理解是找出选了不止一门课，且自己所有课程分数相同的学生。 思路：条件1：该学生总选课数>1 条件2：满足1，且该学生所有课程分数最大值=最小值，即可保证所有课程分数相等 */ select * from sc where sno in (select sno from sc group by sno having min(score)=max(score) and count(*)>1);
```



【**联结：inner join/outer join**】
- 下面一张图就足以说明联结join的各种情况了- SQL面试50题只用到了inner join、简单的outer join(即 left join/right join)、三表联结


22. 检索" 01 "课程分数小于 60，按分数降序排列的学生信息【inner join】

```
-- 22. 检索" 01 "课程分数小于 60，按分数降序排列的学生信息 /* 我的疑问：实际上下面两种方法都可以写联结，我的习惯是用法1，后面的联结题也都是这样写的。 但是有没有大神可以从效能或是什么其他角度讲解一下哪种联结的写法比较好？*/ -- 法1： select b.*, a.score from sc as a inner join student as b on a.sno=b.sno where a.cno='01' and a.score<60 order by a.score desc; -- 法2：先子查询再联结 select a.*, b.score from student as a inner join (select sno, score from sc where cno='01' and score<60) as b on a.sno=b.sno order by b.score desc;
```

23. 查询不及格的课程及学生名，学号，按课程号从大到小排列【inner join】

```
-- 23. 查询不及格的课程及学生名，学号，按课程号从大到小排列 select a.cno, b.sname, b.sno from sc as a inner join student as b on a.sno=b.sno where a.score<60 order by a.cno desc;
```

24. 查询课程名称为「数学」，且分数低于 60 的学生姓名和分数 【inner join】

```
-- 24. 查询课程名称为「数学」，且分数低于 60 的学生姓名和分数 select b.sname, a.score from sc as a inner join student as b on a.sno=b.sno where a.score<60 and a.cno =(select cno from course where cname='数学');
```

25. 查询平均成绩大于等于 85 的所有学生的学号、姓名和平均成绩 【inner join】

```
-- 25. 查询平均成绩大于等于 85 的所有学生的学号、姓名和平均成绩 select a.sno, b.sname, avg(a.score) from sc as a inner join student as b on a.sno=b.sno group by a.sno having avg(a.score)>=85;
```

26. 查询不同老师所教不同课程平均分从高到低显示【inner join】

```
-- 26. 查询不同老师所教不同课程平均分从高到低显示 select b.tno, a.cno, avg(a.score) from sc as a inner join course as b on a.cno=b.cno group by a.cno order by avg(a.score) desc;
```

27. 查询平均成绩大于等于 60 分的同学的学生编号和学生姓名和平均成绩【inner join】

```
-- 27. 查询平均成绩大于等于 60 分的同学的学生编号和学生姓名和平均成绩 select a.sno, b.sname, avg(a.score) from sc as a inner join student as b on a.sno=b.sno group by a.sno having avg(a.score)>=60;
```

28.查询两门及其以上不及格课程的同学的学号，姓名及其平均成绩【inner join】

```
-- 28.查询两门及其以上不及格课程的同学的学号，姓名及其平均成绩 select a.sno, b.sname, avg(a.score) from sc as a inner join student as b on a.sno=b.sno where a.score<60 group by a.sno having count(a.score)>=2;
```

29. 查询同名同性学生名单，并统计同名同性人数【inner join】

```
-- 29. 查询同名同性学生名单，并统计同名同性人数 /* 思路：同时将姓名和性别2个字段作为分组依据并计数，超过1就说明有重复 注意：原student表中无同名同性别学生，想检验答案的正确性，可以自己添加记录 */ select a.*, b.student_number from student as a inner join (select sname, ssex, count(*) as student_number from student group by sname, ssex having count(*)>1) as b on a.sname=b.sname and a.ssex=b.ssex;
```

30. 查询所有学生的课程及分数情况（存在学生没成绩，没选课的情况）【outer join】

```
-- 30. 查询所有学生的课程及分数情况（存在学生没成绩，没选课的情况） -- 思路：需要将student表中非两表交集的部分也显示出来，用outer join select a.sno, a.sname, b.cno, b.score from student as a left join sc as b on a.sno=b.sno;
```

31. 查询所有同学的学生编号、学生姓名、选课总数、所有课程的总成绩。(没成绩的显示为 null )【outer join】

```
-- 31. 查询所有同学的学生编号、学生姓名、选课总数、所有课程的总成绩(没成绩的显示为 null ) select b.sno, b.sname, count(a.score), sum(a.score) from sc as a right join student as b on a.sno=b.sno group by a.sno;
```

32. 查询存在" 01 "课程但可能不存在" 02 "课程的情况(不存在时显示为 null )【outer join】

```
-- 32. 查询存在" 01 "课程但可能不存在" 02 "课程的情况(不存在时显示为 null ) -- 思路：1.在sc表中选出临时表a（存在" 01 "课程），临时表b（存在" 02 "课程）； 2. a left join b select a.*, b.cno, b.score from (select * from sc where cno='01') as a left join (select * from sc where cno='02') as b on a.sno=b.sno;
```

33. 查询任何一门课程成绩在 70 分以上的姓名、课程名称和分数【三表联结】

```
-- 33. 查询任何一门课程成绩在 70 分以上的姓名、课程名称和分数 -- 法1：where 与等号 select sname, cname, score from student, course, sc where student.sno=sc.sno and course.cno=sc.cno and score>70; -- 法2 inner join*2 select a.sname, c.cname, b.score from student as a inner join (select * from sc where score>70) as b on a.sno=b.sno inner join course as c on b.cno=c.cno;
```

34. 查询" 01 "课程比" 02 "课程成绩高的学生的信息及课程分数【三表联结】

```
-- 34. 查询" 01 "课程比" 02 "课程成绩高的学生的信息及课程分数 /* 思路：1.在sc表中选出临时表b（" 01 "课程），临时表c（" 02 "课程）; 2.ab表相交条件：学生号相同且a表成绩>b表成绩 3.再与student表相交 */ select a.*, b.score as '01', c.score as '02' from student as a inner join (select * from sc where cno='01') as b on a.sno=b.sno inner join (select * from sc where cno='02') as c on b.sno=c.sno and b.score>c.score;
```



【limit易错点】
- limit m,n 表示从第m+1条记录开始，共选n条记录，如limit 1,2 选的是第2、3条记录（很容易误以为是选1、2条记录）- limit n 表示选择前n条记录，是limit 0,n 的省略


35. 成绩不重复，查询选修「张三」老师所授课程的学生中，成绩最高的学生信息及其成绩【limit】

```
-- 35. 成绩不重复，查询选修「张三」老师所授课程的学生中，成绩最高的学生信息及其成绩 /* 思路：1.表联结：可以用inner join；也可以用where+等号 2.成绩最高：成绩降序排列后limit 1；成绩不重复，也可以用max*/ -- 法1：where+等号+limit select student.*, sc.score from sc, course, teacher,student where teacher.tname='张三' and course.tno=teacher.tno and course.cno=sc.cno and student.sno=sc.sno order by sc.score desc limit 1; -- 法2：where+等号+max select student.*, max(sc.score) as max_score from sc, course, teacher,student where teacher.tname='张三' and course.tno=teacher.tno and course.cno=sc.cno and student.sno=sc.sno; -- 法3 inner join+limit select a.*, b.score from sc as b inner join student as a on a.sno=b.sno where b.cno=(select cno from course where tno=(select tno from teacher where tname='张三')) order by b.score desc limit 1; -- 法4 inner join+max select a.*, max(b.score) as max_score from sc as b inner join student as a on a.sno=b.sno where b.cno=(select cno from course where tno=(select tno from teacher where tname='张三'));
```

36. 成绩有重复的情况下，查询选修「张三」老师所授课程的学生中，成绩最高的学生信息及其成绩【limit】

```
-- 36. 成绩有重复的情况下，查询选修「张三」老师所授课程的学生中，成绩最高的学生信息及其成绩 /* 思路：1.student表与sc表联结为临时表a；2.用limit/max，求出张三老师所授课程的课程号与最高分为临时表b 3.求表a表b的交集，条件是课程号、成绩与b相等。 */ -- 法1：limit select a.* from (select student.*, sc.cno, sc.score from student, sc where student.sno=sc.sno) as a inner join (select cno, score from sc where cno=(select cno from course, teacher where teacher.tname='张三' and course.tno=teacher.tno )order by score desc limit 1) as b on a.cno=b.cno and a.score=b.score; -- 法2：max select a.* from (select student.*, sc.cno, sc.score from student, sc where student.sno=sc.sno) as a inner join (select cno, max(score) as score from sc where cno= (select cno from teacher, course where teacher.tname='张三' and teacher.tno=course.tno) group by cno) as b on a.cno=b.cno and a.score=b.score;
```



【窗口函数 rank/dense_rank/row_number】
- rank/dense_rank/row_number等窗口函数只能写在select后面，不能直接写在where后面，要筛选窗口函数的结果列时，先写子查询，再用where+列别名完成


37. 按各科成绩进行排序，并显示排名，Score 重复时保留名次空缺【窗口函数】

```
--37. 按各科成绩进行排序，并显示排名，Score 重复时保留名次空缺 select *, rank() over (partition by cno order by score desc) as ranking from sc;
```

38. 按各科成绩进行排序，并显示排名，Score 重复时合并名次【窗口函数】

```
-- 38. 按各科成绩进行排序，并显示排名，Score 重复时合并名次 select *, dense_rank() over (partition by cno order by score desc) as ranking from sc;
```

39. 查询学生的总成绩，并进行排名，总分重复时保留名次空缺【窗口函数】

```
-- 39. 查询学生的总成绩，并进行排名，总分重复时保留名次空缺 select sno, sum(score), rank() over (order by sum(score) desc) as ranking from sc group by sno;
```

40. 查询学生的总成绩，并进行排名，总分重复时不保留名次空缺【窗口函数】

```
-- 40. 查询学生的总成绩，并进行排名，总分重复时不保留名次空缺 select sno, sum(score), dense_rank() over (order by sum(score) desc) as ranking from sc group by sno;
```

41. 查询每门功成绩最好的前两名【窗口函数】

```
-- 41. 查询每门功成绩最好的前两名 /* 思路：“成绩最好的前两名”我的理解是在有成绩重复的时候，保留名次空缺，即用rank分科排名，名次<=2。 比如有2个人或者3个人并列第一，这种方法就能将这2个人/3个人都选出来。 3个人并列的情况中，我没有用limit2的原因是似乎没有恰当理由删去1个明明考了最高成绩的学生。 注意：rank函数只能用在select后面，不能用在where后面，所以要先写子查询，为子查询、rank函数结果分别起别名，再筛选名次<=2 */ select * from (select *, rank() over(partition by cno order by score desc) as ranking from sc) as a where ranking <=2;
```

42. 查询各科成绩前三名的记录【窗口函数】

```
-- 42. 查询各科成绩前三名的记录 /* 思路：“成绩前三” 我的理解是出现成绩并列，不保留名次空缺。因此用dense_rank分科排名，名次<=3。 注意：dense_rank函数只能用在select后面，不能用在where后面，所以要先写子查询，为子查询、dense_rank函数结果分别起别名，再筛选名次<=3 */ select * from (select *, dense_rank() over(partition by cno order by score desc) as ranking from sc) as a where ranking <=3;
```

43. 查询所有课程成绩第2名到第3名的学生信息及该课程成绩【窗口函数】

```
-- 43. 查询所有课程成绩第2名到第3名的学生信息及该课程成绩 -- 思路：同42，用dense_rank, 名次in (2,3) select a.*, b.cno, b.score, b.ranking from student a inner join (select *, dense_rank() over (partition by cno order by score desc) ranking from sc ) b on a.sno=b.sno where ranking in (2,3);
```



【case】case 结合聚合函数来旋转行列字段是一种常用技巧



44.按平均成绩从高到低显示所有学生的所有课程的成绩以及平均成绩【case】

```
-- 44.按平均成绩从高到低显示所有学生的所有课程的成绩以及平均成绩 /* 注意：1.case 结合聚合函数来旋转行列字段是一种常用技巧 2.else null不能写为else 0，因为0代表选了课但是得了0分 3.student表里面有没有选课的学生 */ select b.sno, a.avg_score, a.01, a.02, a.03 from (select sno, avg(score) as avg_score, max(case when cno='01' then score else null end) as '01', max(case when cno='02' then score else null end) as '02', max(case when cno='03' then score else null end) as '03' from sc group by sno) as a right join student as b on a.sno=b.sno;
```

45. 使用分段[100-85]，[85-70]，[70-60]，[<60]来统计各科成绩，分别统计各分数段人数：课程ID和课程名称【case】

```
-- 45. 使用分段[100-85]，[85-70]，[70-60]，[<60]来统计各科成绩，分别统计各分数段人数：课程ID和课程名称 -- 思路：要计数产生多个新的列，考虑用case+sum来完成 select a.cno, b.cname, sum(case when a.score>=85 then 1 else 0 end) as '[100-85]', sum(case when a.score>=70 and a.score<85 then 1 else 0 end) as '[85-70]', sum(case when a.score>=60 and a.score<70 then 1 else 0 end) as '[70-60]', sum(case when a.score<60 then 1 else 0 end) as '[<60]' from sc as a inner join course as b on a.cno=b.cno group by a.cno;
```

46. 查询各科成绩最高分、最低分和平均分：以如下形式显示：课程 ID ，课程 name ，最高分，最低分，平均分，及格率，中等率，优良率，优秀率。及格为>=60，中等为：70-80，优良为：80-90，优秀为：>=90 【case】

```
-- 46. 查询各科成绩最高分、最低分和平均分：以如下形式显示：课程 ID ，课程 name ，最高分，最低分，平均分，及格率，中等率，优良率，优秀率。及格为>=60，中等为：70-80，优良为：80-90，优秀为：>=90 -- 思路：1.用case+sum计数 2.分数比率=1.的结果/选课人数 select a.cno '课程ID', b.cname '课程name', max(a.score) '最高分', min(a.score) '最低分', avg(a.score) '平均分', sum(case when a.score>=60 then 1 else 0 end)/count(a.score) '及格率', sum(case when a.score>=70 and a.score<80 then 1 else 0 end)/count(a.score) '中等率', sum(case when a.score>=80 and a.score<90 then 1 else 0 end)/count(a.score) '优良率', sum(case when a.score>=90 then 1 else 0 end)/count(a.score) '优秀率' from sc a inner join course b on a.cno=b.cno group by a.cno;
```

47. 统计各科成绩各分数段人数：课程编号，课程名称，[100-85]，[85-70]，[70-60]，[60-0] 及所占百分比【case】

```
-- 47. 统计各科成绩各分数段人数：课程编号，课程名称，[100-85]，[85-70]，[70-60]，[60-0] 及所占百分比 -- 思路同46 select a.cno, b.cname, sum(case when a.score>=85 then 1 else 0 end) as '[100-85]人数', sum(case when a.score>=70 and a.score<85 then 1 else 0 end) as '[85-70]人数', sum(case when a.score>=60 and a.score<70 then 1 else 0 end) as '[70-60]人数', sum(case when a.score<60 then 1 else 0 end) as '[60-0]人数', sum(case when a.score>=85 then 1 else 0 end)/count(a.sno) as '[100-85]百分比', sum(case when a.score>=70 and a.score<85 then 1 else 0 end)/count(a.sno) as '[85-70]百分比', sum(case when a.score>=60 and a.score<70 then 1 else 0 end)/count(a.sno) as '[70-60]百分比', sum(case when a.score<60 then 1 else 0 end)/count(a.sno) as '[60-0]百分比' from sc as a inner join course as b on a.cno=b.cno group by a.cno;
```



【时间函数】

在mysql里计算时间差：
- 日期差：datediff(time终, time始)- 指定时间差：timestampdiff(timestamp, time始, time终)
令人崩溃的一点是，即便是如此相近的两个时间差函数，其后面时间参数的顺序居然是相反的，如果计算结果出现了负数，考虑参数写反的可能性。



48. 查询各学生的年龄，只按年份来算【时间函数】

```
-- 48. 查询各学生的年龄，只按年份来算 -- 思路：year函数分别求出current_date和sage的年，相减即可 select sno, sname, year(current_date())-year(sage) as age from student;
```

49. 按照出生日期来算，当前月日 < 出生年月的月日则，年龄减一【时间函数】

```
-- 49. 按照出生日期来算，当前月日 < 出生年月的月日则，年龄减一 -- 思路：使用timestampdiff()求差，将时间戳设置为年即可 Select sno, sname, timestampdiff(year, sage, current_date()) as age from student;
```

50. 查询本月过生日的学生【时间函数】

```
-- 50. 查询本月过生日的学生 -- 思路：month函数分别求出current_date和sage的月份，相同即可 select sno, sname, sage from student where month(sage)=month(current_date());
```

51. 查询下月过生日的学生【时间函数】

```
-- 51. 查询下月过生日的学生 -- 思路参见50 select sno, sname, sage from student where month(sage)=month(current_date())+1;
```

52. 查询本周过生日的学生【时间函数】*

```
-- 52. 查询本周过生日的学生 /* 思路：1.replace将生日中的年换为今天的年，计算同学的生日在本年是第几周； 2.求出今天是本年的第几周 3.若相等，则符合条件 注意：这种方法存在问题，1月第一周和12月最后一周可能跨年，倒置结果出错。欢迎提供更好的方案。*/ select sno, sname, sage from student where week(replace(sage, year(sage), year(current_date())), 1) = week(current_date(),1);
```

53. 查询下周过生日的学生【时间函数】*

```
-- 53. 查询下周过生日的学生 -- 思路和存在问题参见52 select sno, sname, sage from student where week(replace(sage, year(sage), year(current_date())), 1) = week(current_date(),1)+1;
```



【创建原始表】
- 插入数据 怕出错的话，可以先start transaction; 确认数据插入无误后再commit;
```
-- 创建数据库exercise create database exercise; -- 使用数据库exercise use exercise; -- 创建学生表student create table student (Sno varchar(10) not null, Sname varchar(10) , Sage date , Ssex varchar(10) , primary key (Sno)); start transaction; insert into student values ('01', '赵雷', '1990-01-01', '男'); insert into student values ('02', '钱电', '1990-12-21', '男'); insert into student values ('03', '孙风', '1990-05-20', '男'); insert into student values ('04', '李云', '1990-08-06', '男'); insert into student values ('05', '周梅', '1991-12-01', '女'); insert into student values ('06', '吴兰', '1992-03-01', '女'); insert into student values ('07', '郑竹', '1989-07-01', '女'); insert into student values ('08', '王菊', '1990-01-20', '女'); commit; -- 创建科目表course create table course (Cno varchar(10) not null, Cname varchar(10) , Tno varchar(10) , primary key (Cno)); start transaction; insert into course values ('01', '语文', '02'); insert into course values ('02', '数学', '01'); insert into course values ('03', '英语', '03'); commit; -- 创建教师表teacher create table teacher (Tno varchar(10) not null, Tname varchar(10) , primary key (Tno)); strat transaction; insert into teacher values ('01', '张三'); insert into teacher values ('02', '李四'); insert into teacher values ('03', '王五'); commit; -- 创建成绩表 sc create table sc (Sno varchar (10) , Cno varchar (10) , score decimal(18,1), primary key (Sno, Cno)); start transaction; insert into SC values('01' , '01' , 80); insert into SC values('01' , '02' , 90); insert into SC values('01' , '03' , 99); insert into SC values('02' , '01' , 70); insert into SC values('02' , '02' , 60); insert into SC values('02' , '03' , 80); insert into SC values('03' , '01' , 80); insert into SC values('03' , '02' , 80); insert into SC values('03' , '03' , 80); insert into SC values('04' , '01' , 50); insert into SC values('04' , '02' , 30); insert into SC values('04' , '03' , 20); insert into SC values('05' , '01' , 76); insert into SC values('05' , '02' , 87); insert into SC values('06' , '01' , 31); insert into SC values('06' , '03' , 34); insert into SC values('07' , '02' , 89); insert into SC values('07' , '03' , 98); commit;
```

