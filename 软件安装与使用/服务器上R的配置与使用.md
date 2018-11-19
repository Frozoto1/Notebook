## 服务器(172.30.2.3) R的配置与使用
### 1. 环境变量的配置
```bash
添加以下环境变量到~/.bashrc中
# gcc 编译器与库环境变量配置(注意低版本的gcc编译器在R包安装的时候会报错误)
# gcc -v 查看当前gcc版本;这里使用gcc-6.4.0是没有问题的
export LD_LIBRARY_PATH="/gss1/biosoft/gcc-6.4.0/lib:$LD_LIBRARY_PATH"
export LD_LIBRARY_PATH="/gss1/biosoft/gcc-6.4.0/lib64:$LD_LIBRARY_PATH"
export PATH="/gss1/biosoft/gcc-6.4.0/bin:$PATH"

# R包安装所依赖的一些库环境变量配置
export LD_LIBRARY_PATH=/gss1/biosoft/xz-5.2.3/lib:$LD_LIBRARY_PATH
export LD_LIBRARY_PATH=/gss1/biosoft/pcre-8.38/lib:$LD_LIBRARY_PATH
export LD_LIBRARY_PATH=/gss1/biosoft/curl-7.54.1/lib:$LD_LIBRARY_PATH
export LD_LIBRARY_PATH=/gss1/biosoft/zlib-1.2.8/lib:$LD_LIBRARY_PATH
export LD_LIBRARY_PATH=/gss1/biosoft/bzip2-1.0.6/lib:$LD_LIBRARY_PATH
export PATH=$PATH:$JAVA_HOME/bin:/gss1/biosoft/curl-7.54.1/bin
```
### 2. R包镜像源配置
正常R包的安装比较慢，需要使用国内的镜像源
```bash
touch ~/.Rprofile
vim ~/.Rprofile
写入以下镜像源,这里使用的是中科大的速度还可以
options("repos" = c(CRAN="https://mirrors.ustc.edu.cn/CRAN/"))
options(BioC_mirror="http://mirrors.ustc.edu.cn/bioc/"
```
### 3. 其他说明

安装R包的时候需要`ssh login`到登录节点
