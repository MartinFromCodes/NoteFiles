# python 2.7 第三方模块安装

**BeautifulSoup 库需要单独安装**

## 方法一  单文件模块

###   单文件模块
直接把文件拷贝到 $python_dir/Lib

 
## 方法二 多文件模块，带setup.py
下载模块包，进行解压，进入模块文件夹，执行：
python setup.py install

### 1

 
## 方法三 easy_install 方式
 先下载ez_setup.py,运行python ez_setup 进行easy_install工具的安装，之后就可以使用easy_install进行安装package了。
  easy_install  packageName
  easy_install  package.egg

## 方法四 pip 方式
先进行pip工具的安裝：easy_install pip（pip 可以通过easy_install 安裝，而且也会装到 Scripts 文件夹下。）
安裝：pip install PackageName

更新：pip install -U PackageName
 

##总结
在Python中，安装第三方模块，是通过setuptools这个工具完成的。Python有两个封装了setuptools的包管理工具：easy_install和pip。目前官方推荐使用pip

在命令提示符窗口下尝试运行pip，如果Windows提示未找到命令，可以重新运行安装程序添加pip。


* 第一
* 第二
* 第三



