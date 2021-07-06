---
title: Python解决excel列的连接
date: 2021-07-06 14:16:36
tags:
---

正好看了一个能解决问题的视频教程，两个execl拼到一起，和sql里的join一样

````python
import pandas as pd
df_grade = pd.read_excel('学生成绩表.xlsx')
df_sinfo = pd.read_excel('学生信息表.xlsx')
df_sinfo = df_sinfo[['学号','姓名','性别']]
df_merge = pd.merge(left=df_grade,right=df_sinfo,left_on='学号',right_on='学号')
new_columns = df_merge.columns.to_list()
for name in ['姓名','性别'][::-1]:
    new_columns.remove(name)
    new_columns.insert(new_columns.index('学号')+1,name)

df_merge = df_merge.reindex(columns=new_columns)
df_merge.to_excel('data.xlsx',index=False)
````
