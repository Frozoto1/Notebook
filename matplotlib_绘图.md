## Python 可视化

### 绘制箭头

[推荐官方用户指导Annotating Axes](https://matplotlib.org/users/annotations_guide.html)

方法一：
[axes.arrow()](https://matplotlib.org/api/_as_gen/matplotlib.axes.Axes.arrow.html)

方法二：
[axes.annotate()](https://matplotlib.org/api/_as_gen/matplotlib.axes.Axes.annotate.html#matplotlib.axes.Axes.annotate)
```python3
axes.flatten()[i].annotate("", xy=(380, -0.5), xytext=(0, -0.5),arrowprops={'arrowstyle':"->"})
#第一个参数为箭尾注释,xy为箭头坐标，xytext为箭尾坐标
#注意当使用arrowstyle属性的时候其他的arrowprops属性将被禁用
axes.flatten()[i].annotate("", xy=(380, -0.3), xytext=(0, -0.3),arrowprops={'width':1})

```
实例：
```python3
import glob
import pandas as pd
import matplotlib.pyplot as plt
import numpy as np
#
siRNA_files = glob.glob('sort_NOR*.txt')
#
merge = []
all_data = []
for k in range(9):
    temp = pd.read_table(siRNA_files[k],header=None,index_col=0,sep='\t')
    for i in range(12):
        for j in range(len(temp.iloc[i,:])):
            merge.append(temp.iloc[i,j])
    merge = [x for x in merge if not pd.isnull(x)]
    all_data.append(merge)
    merge = []
#
color_list = ['red','orange','blue']
label_list = ['$\itEpi-lf1$'+'_seedling_D1','$\itEpi-lf1$'+'_seedling_D2','WT_seedling']
title_list = ['21nt','22nt','24nt']
#
fig,axes = plt.subplots(nrows=3,ncols=1)
fig.set_size_inches(10,8)
for i in range(3):
    axes.flatten()[i].plot(all_data[0+3*i],color=color_list[0],label=label_list[0])
    axes.flatten()[i].plot(all_data[1+3*i],color=color_list[1],label=label_list[1])
    axes.flatten()[i].plot(all_data[2+3*i],color=color_list[2],label=label_list[2])
    axes.flatten()[i].set_title(title_list[i],fontsize=16)
    if i != 2:
        axes.flatten()[i].set_ylim(0,15)
        axes.flatten()[i].set_yticks([0,15])
        axes.flatten()[i].set_yticklabels([0,15],fontsize=16)
    else:
        axes.flatten()[i].set_ylim(-1,15)
        axes.flatten()[i].set_yticks([0,15])
        axes.flatten()[i].set_yticklabels([0,15],fontsize=16)
        #axes.flatten()[i].annotate("", xy=(380, -0.5), xytext=(0, -0.5),arrowprops={'arrowstyle':"->"})
        axes.flatten()[i].annotate("", xy=(380, -0.3), xytext=(0, -0.3),arrowprops={'width':1})
        #
        #axes.flatten()[i].arrow(0, 0, 380, 0, head_width=2, head_length=0.1,width=0.2, fc='k', ec='k')
    if i == 0:
        axes.flatten()[0].legend(prop={'size':10})
#     
plt.setp(axes.flatten()[0].get_xticklabels(), visible=False)
plt.setp(axes.flatten()[1].get_xticklabels(), visible=False)
plt.xticks([0,380],['Chr1','Chr12'],fontsize=16)
fig.text(0.06, 0.5, 'log(Normalized siRNA counts)', ha='center', va='center', rotation='vertical',fontsize=20)
plt.savefig('Genome_siRNA_condition.pdf',dpi=300)
#plt.show()
```
输出:

![mark](http://ol8t44w0x.bkt.clouddn.com/blog/180426/IadKdh9i79.png?imageslim)
补充输入文件格式：

![mark](http://ol8t44w0x.bkt.clouddn.com/blog/180426/JJfB0DlGCe.png?imageslim)

### 全局字体的设置

    import matplotlib
    matplotlib.rcParams['font.family'] = 'Arial'

### 调整图形在画布中的位置及子图间距调整

    [matplotlib.pyplot.subplots_adjust](https://matplotlib.org/api/_as_gen/matplotlib.pyplot.subplots_adjust.html)
