## Q & A

### sam --> bam 问题
命令：
`samtools view -bS test.sam > test.bam`

报错：

    [E::sam_parse1] missing SAM header
    [W::sam_read1] parse error at line 1
    [main_samview] truncated file.

解决：
添加-t参数，提供参考基因组长度信息

`samtools view -t /gss1/home/jiawen/data/rice/tigr7.fa.fai -bS test.sam > test.bam`

### bsub在命令行提交存在管道和重定向的命令

通过-I参数解决

`bsub -n 1 -e test-e.log -I cut -f 2 test.sam | awk '$1==16' | wc -l 1> test.txt
`
### bsub指定节点
`bsub -m "hostA hostD hostB" myjob`

### Python virtualenv 和 Jupyter notebook一起使用

[快速配置](https://anbasile.github.io/programming/2017/06/25/jupyter-venv/)

具体如下：

![mark](http://ol8t44w0x.bkt.clouddn.com/blog/180701/2i4LC8BF8j.png?imageslim)

[更全面的配置过程](https://github.com/bhrutledge/jupyter-venv)

### python 交互式模式下Tab自动不全

创建~/.pythonstartup
```python3
# python startup file
#!/usr/bin/env python3
import sys
import readline
import rlcompleter
import atexit
import os
# tab completion
readline.parse_and_bind('tab: complete')
# history file
histfile = os.path.join(os.environ['HOME'], '.pythonhistory')
try:
readline.read_history_file(histfile)
except IOError:
pass
atexit.register(readline.write_history_file, histfile)
del os, histfile, readline, rlcompleter
```
配置环境变量

`echo 'export PYTHONSTARTUP=~/.pythonstartup' >> ~/.bashrc`


### Ubuntu16.4 vim卸载以及安装vim8.0

#### vim8
```bash
#查看vim相关软件
dpkg -l | grep vim
#卸载出现的vim相关软件
sudo apt-get remove xxx
#下载源码包
git clone https://github.com/vim/vim.git
# 安装
cd vim/src
#
./configure --with-features=huge \
--enable-multibyte \
--enable-rubyinterp \
--enable-python3interp \
--enable-perlinterp \
--enable-luainterp \
--enable-gui=gtk2 \
--enable-cscope \
--with-python3-config-dir=/usr/lib/python3.5/config-3.5m-x86_64-linux-gnu \
--prefix=/usr
#
sudo make VIMRUNTIMEDIR=/usr/share/vim/vim81 #这里的要根据下载的版本确定
sudo make
sudo make install
```
#### vim插件管理器Vundle
```bash
ls
```

### blastn output format 6

参考:[blastn output format 6](http://www.metagenomics.wiki/tools/blast/blastn-output-format-6)

![mark](http://ol8t44w0x.bkt.clouddn.com/blog/180719/AddA0ek7b3.png?imageslim)

![mark](http://ol8t44w0x.bkt.clouddn.com/blog/180719/DCl3LKKemf.png?imageslim)

```
qseqid    Query Seq-id
qgi       Query GI
qacc      Query accesion
qaccver   Query accesion.version
qlen      Query sequence length
sseqid    Subject Seq-id
sallseqid All subject Seq-id(s), separated by a ';'
sgi       Subject GI
sallgi    All subject GIs
sacc      Subject accession
saccver   Subject accession.version
sallacc   All subject accessions
slen      Subject sequence length
qstart    Start of alignment in query
qend      End of alignment in query
sstart    Start of alignment in subject
send      End of alignment in subject
qseq      Aligned part of query sequence
sseq      Aligned part of subject sequence
evalue    Expect value
bitscore  Bit score
score     Raw score
length    Alignment length
pident    Percentage of identical matches
nident    Number of identical matches
mismatch  Number of mismatches
positive  Number of positive-scoring matches
gapopen   Number of gap openings
gaps      Total number of gaps
ppos      Percentage of positive-scoring matches
frames    Query and subject frames separated by a '/'
qframe    Query frame
sframe    Subject frame
btop      Blast traceback operations (BTOP)
staxids   Subject Taxonomy ID(s), separated by a ';'
sscinames Subject Scientific Name(s), separated by a ';'
scomnames Subject Common Name(s), separated by a ';'
sblastnames Subject Blast Name(s), separated by a ';'   (in alphabetical order)
sskingdoms  Subject Super Kingdom(s), separated by a ';'     (in alphabetical order)
stitle      Subject Title
salltitles  All Subject Title(s), separated by a '<>'
sstrand   Subject Strand
qcovs     Query Coverage Per Subject
qcovhsp   Query Coverage Per HSP

```

```
default values are:
-outfmt "6 qseqid sseqid pident length mismatch gapopen qstart qend sstart send evalue bitscore"
```

### blastn short reads序列比对

blastn在进行short reads序列比对的时候，即使存在
可以比对上的序列也不会输出，此时需要添加 `-task blastn-short` 参数，如下：

![mark](http://ol8t44w0x.bkt.clouddn.com/blog/180719/12iej5dDh3.png?imageslim)

### Python输出带颜色的文字

格式: `\033[显示方式;前景色;背景色m Your STRING \033[0m`

注:显示方式,前景色,背景色至少存在一个，最后的紧跟其后的m不可少,通过`\033[0m 采用终端默认设置，即取消颜色设置`

| 前景色 | 背景色 | 颜色   |
| ------ | ------ | ------ |
| 30     | 40     | 黑色   |
| 31     | 41     | 红色   |
| 32     | 42     | 绿色   |
| 33     | 43     | 黃色   |
| 34     | 44     | 蓝色   |
| 35     | 45     | 紫红色 |
| 36     | 46     | 青蓝色 |
| 37     | 47     | 白色   |

| 显示方式 | 意义         |
| -------- | ------------ |
| 0        | 终端默认设置 |
| 1        | 高亮显示     |
| 4        | 使用下划线   |
| 5        | 闪烁         |
| 7        | 反白显示     |
| 8        | 不可见       |

### Python 使用自定义模块

假设我们有一个`hellojiawen.py`的脚本在`~/xxx`目录下，
我们想在其他脚本中使用其中的hello函数
```python3
#加载模块
import sys
sys.path.append(r'~/xxx')
import hellojiawen
#模块函数的使用
hellojiawen.hello()

```

### matplotlib Tight Layout guide

[Tight Layout guide](https://matplotlib.org/users/tight_layout_guide.html)
