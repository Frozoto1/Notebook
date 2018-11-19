# **Linux Shell command line**
[THE awk language](https://www.gnu.org/software/gawk/manual/html_node/Getting-Started.html#Getting-Started)
### awk数值计算
```bash
- 求某一列的和
awk 'BEGIN{m=0}{m+=$1}END{print m}' test.txt
- 增加条件判断求列和
awk 'BEGIN{m=0}{if($8>=5)m+=$8}END{print m}' test.txt
awk 'BEGIN{m=0;t=0}{if($3>=1551 && $3<1650 && $8>5){m+=$7;t+=$8}}END{print m/t}' test.CGmap
- 求两列和之比
awk 'BEGIN{m=0;all=0}{m+=$7;all+=$8}END{printf "%.2f\n",m/all}' test.txt
- 求某列均值
awk 'BEGIN{m=0}{m+=$6}END{printf "%.4f\n",m/NR}' test.txt
- 条件判断求均值
awk '{if($1==0 && $2 ==0){lev=0}else{lev=$1/($1+$2)}}{print lev}' test.txt
- 某行第二列到最后一列的和大于某值输出本行
awk '{for(i=2;i<=NF;i++) t+=$i; {if(t>20){print $0}}; t=0}' FS="," new_chr11_DEG_FPKM.csv

- 某行至少有一个值的大小满足指定的要求输出该行
awk '{for(i=1;i<=NF;i++){if($i>=5){print $0;next}}}' OFS="\t" test.txt
```
### awk中引入外部变量
```bash
i="CHH"
awk -v i=${i} 'BEGIN{m=0}{if($8>=5 && $4==i)m+=$7}END{print m}' test.txt
```
### bash shell 字符串截取

[blog link](http://www.cnblogs.com/liuweijian/archive/2009/12/27/1633661.html)
```bash
1. basename
    test="hello-world"
    echo `basename ${test} -world`
    输出为:hello
2.
    ${varible##*string}从左向右截取最后一个string后的字符串
    ${varible#*string}从左向右截取第一个string后的字符串
    ${varible%%string*}从右向左截取最后一个string后的字符串
    ${varible%string*}从右向左截取第一个string后的字符串
e.g.:
```
![mark](http://ol8t44w0x.bkt.clouddn.com/blog/180109/1Dk9I8gG0A.png?imageslim)
```bash
3. ${varible:n1:n2} n1为起始截取位置；第一个字符串索引为0；n2为截取字符串的个数
test="love-bq"
echo ${test:0:4}
输出为:love
4. cut
获取文件名前缀
ls | cut -d "." -f1
获取文件名后缀
ls | cut -d "." -f2
```
### shell中的数学运算

主要介绍let,expr,bc,awk数值计算

- let 运算量与运算符之间**不允许空格**;**不支持浮点**
- expr 运算量与运算符之间**需要空格**；**不支持浮点**
- bc **支持浮点数**运算;**空格不敏感**
- awk **支持浮点数**运算；**空格不敏感**

#### bc的使用实例
代码：
```bash
a=2
b=3
echo "scale=4;$a/$b"|bc
```
运行结果：
```bash
.6666
```
#### awk的使用实例
代码:
```bash
a=2
b=3
c=5
d=6
awk 'BEGIN{printf "%.4f\n",('$a'/'$b')}'
echo '==分割=='
awk 'BEGIN{printf "%.4f\n%.4f\n",('$a'/'$b'),('$c'/'$d')}'
awk 'BEGIN{print '$a'/'$b'}'
```
运行结果：
```bash
0.6667
==分割==
0.6667
0.8333
0.6667
```
### awk排除某列输出

```bash
#排除第一列输出其他所有列
awk '{$1=""; print $0}' filename
#排除第一，第三列输出其他所有
awk '{$1=$3=""; print $0}' filename
```

### awk指定列所有行数值运算(文件中所有数值共同与固定值进行算数运算)

```bash
<jiawen@mgt bam>$head -n 5 a.txt
Chr10	1	1	2
Chr10	2	3	1
Chr10	5	2	2
Chr10	8
Chr10	10
<jiawen@mgt bam>$map_reads=4
<jiawen@mgt bam>$head -n 5 a.txt | awk '{for(i=2;i<=NF;i++){$i=$i*2/'${map_reads}'}{print $0}}' OFS="\t"
Chr10	0.5	0.5	1
Chr10	1	1.5	0.5
Chr10	2.5	1	1
Chr10	4
Chr10	5
```

### shell script 哈希 awk 对数运算
```bash  
#!/bin/bash

name=(mil_sRNA_21_reads_pos_lf-1.txt mil_sRNA_21_reads_pos_lf-2.txt mil_sRNA_21_reads_pos_wt.txt mil_sRNA_22_reads_pos_lf-1.txt mil_sRNA_22_reads_pos_lf-2.txt mil_sRNA_22_reads_pos_wt.txt mil_sRNA_24_reads_pos_lf-1.txt mil_sRNA_24_reads_pos_lf-2.txt mil_sRNA_24_reads_pos_wt.txt)
#
declare -A map_reads_dict
#
map_reads_dict=([mil_sRNA_21_reads_pos_lf-1.txt]=11232292 [mil_sRNA_21_reads_pos_lf-2.txt]=10112485 [mil_sRNA_21_reads_pos_wt.txt]=10430005 [mil_sRNA_22_reads_pos_lf-1.txt]=11232292 [mil_sRNA_22_reads_pos_lf-2.txt]=10112485 [mil_sRNA_22_reads_pos_wt.txt]=10430005 [mil_sRNA_24_reads_pos_lf-1.txt]=11232292 [mil_sRNA_24_reads_pos_lf-2.txt]=10112485 [mil_sRNA_24_reads_pos_wt.txt]=10430005)
#
for i in `eval echo {0..$((${#name[*]}-1))}`
do
    oname=NOR_${name[i]}
    awk -v map_reads=${map_reads_dict["${name[i]}"]} '{for(j=1;j<=NF-1;j++){$j=log($j*1000000/map_reads)}{print $0}}' FS="," OFS="\t" ${name[i]} > ${oname}
done
```
### awk for if 嵌套
```bash
awk '{for(i=1;i<=NF;i++)if($i>10)print $0}' FS="," OFS="," test.txt
```
### awk 标注满足条件的行
```bash
cat  food_list.txt

No      Item_Name               Price           Quantity
1       Mangoes                 $3.45              5
2       Apples                  $2.45              25
3       Pineapples              $4.45              55
4       Tomatoes                $3.45              25
5       Onions                  $1.45              15
6       Bananas                 $3.45              30

#星号标记Quantity<=20的行
awk '$4 <= 20 { printf "%s\t%s\n", $0,"*" ; } $4 > 20 { print $0 ;} ' food_list.txt

No    Item_Name     Price      Quantity
1     Mangoes       $3.45       5    *
2     Apples        $2.45      25
3     Pineapples    $4.45      55
4     Tomatoes      $3.45      25
5     Onions        $1.45      15   *
6     Bananas       $3.45      30
```
### 30个使用awk的例子
[30 Examples for Awk Command in Text Processing](https://likegeeks.com/awk-command/)

### awk个人日志
[朱双印的个人日志](http://www.zsythink.net/archives/tag/awk)
### shell 关联数组(associative array)

```bash
示例 1
test.sh 文件的内容如下
name=(jim tom lucy)

declare -A phone
phone=([jim]=135 [tom]=136 [lucy]=158)

for i in `eval echo {0..$((${#name[*]}-1))}`
do
    echo ${name[i]} phone number is ${phone["${name[i]}"]}
done

在命令提示符下输入 ./test.sh，执行结果如下：
jim phone number is 135
tom phone number is 136
lucy phone number is 158
```
### sed 指定行替换
    sed -i '1s/\./_/g' all_FPKM.csv

### awk子字符串
    awk '{print substr($9,4,14) }' OFS='\t' test.txt

### uniq -c 输出结果格式化

uniq -c 的输出在统计数字之前存在一些空格，将这些空格去除掉，并以Tab作为分隔符

    cut -f 4 kind_NG_IU_DMR.txt | cut -d ";" -f 2 | sort | uniq -c | perl -pe 's/\s+(\d+) (.*)/\1\t\2/'

### shell 数组

1. 定义数组

    myarray=(6 9 10 11)

2. 获取数组内所有的元素

    ${myarray[*]}

    或

    ${myarray[@]}

3. 数组长度

    ${#myarray[*]}

4. 数组值的索引

    i=2

    ${myarray[${2}]}

5. 数组切片

    ${myarray[*]:0:2}

6. 数组元素删除

    unset myarray[1] #删除第二个元素

7. 数组删除

    unset myarray

8. 实例
```bash
        #!/bin/bash
        myarray=(6 9 10 11)
        len=${#myarray[*]}
        # 直接索引数组内的值
        for i in "${myarray[@]}"
        do
            echo ${i}
        done
        # 通过下标索引数组元素的值
        for ((i=0;i<=${len};i++))
        do
            echo ${myarray[${i}]}
        done
```
### shell中判断文件是否为空

使用test命令

-s 文件存在且大小不为0，返回True

-e 文件存在，返回True

```bash
    #! /bin/sh
    if test -s file.txt; then
            echo "hi"
    else
            echo "empty"
    fi
```

### shell echo 和 printf输出

```
echo -e "hello\nOocarain" #-e开启转义
echo -e "hello\c" #\c不换行
#
a="number"
b=5
printf "%s\t%d" ${a} ${b}
```



### shell 中多行注释

1.
```
<< COMMENT1
注释内容
COMMENT1
```
2.
```
: '
注释内容
'
```

e.g.:
```bash
#!/bin/bash
echo "Say Something"
<<COMMENT1
    your comment 1
    comment 2
    blah
COMMENT1
echo "Do something else"

#---
#OR
#---
#!/bin/bash
foo=bar
: '
This is a test comment
Author foo bar
Released under GNU
'
echo "Init..."
# rest of script
# 注意上面的:和'之间有一个空格
```

### 截取文件指定行

1. `sed -n '3,10p' test.txt` #取3-10行
2. `awk 'NR>=1 && NR<=5' test.txt` #取1-5行

### awk匹配设置大小写不敏感
`awk 'BEGIN{IGNORECASE=1} /amit/' marks.txt`

![mark](http://ol8t44w0x.bkt.clouddn.com/blog/181025/DgIfbDmb4g.png?imageslim)

59Zm4U4u7xeW

144.34.173.118

29507
