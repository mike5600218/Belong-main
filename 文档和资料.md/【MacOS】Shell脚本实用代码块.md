# Shell实用代码块

[toc]
- [Shell实用代码块](#shell实用代码块)
  - [1、第一行固定写法](#1第一行固定写法)
  - [2、加权限(当前用户的执行权限)](#2加权限当前用户的执行权限)
  - [3、关于路径](#3关于路径)
  - [4、键盘监听](#4键盘监听)
  - [5、if/else举例](#5ifelse举例)
  - [6、字符串](#6字符串)
    - [6.1、字符串比较](#61字符串比较)
    - [6.2、字符串截取](#62字符串截取)
      - [6.2.1、shell脚本截取两个关键字之间的字符❤️](#621shell脚本截取两个关键字之间的字符️)
      - [6.2.2、字符串取数字](#622字符串取数字)
  - [7、for/in 和 do/done举例](#7forin-和-dodone举例)
  - [8、多行注释](#8多行注释)
  - [9、添加默认值（字符串）](#9添加默认值字符串)
  - [10、清空文件（localGoEnvInfo.md）](#10清空文件localgoenvinfomd)
  - [11、写文件](#11写文件)
    - [11.1、接受键盘输入，向文件写一段内容](#111接受键盘输入向文件写一段内容)
    - [11.2、向文件写一行内容](#112向文件写一行内容)
  - [12、在列表里面（gem list --local）搜寻（grep）文本（cocoapods）](#12在列表里面gem-list---local搜寻grep文本cocoapods)
  - [13、获取当前用户信息](#13获取当前用户信息)
    - [13.1、终端获取 `${HOME}`](#131终端获取-home)
    - [13.2、shell 脚本获取](#132shell-脚本获取)
      - [13.2.1、打印出当前的用户名](#1321打印出当前的用户名)
      - [13.2.2、打印出当前的用户进程ID](#1322打印出当前的用户进程id)
      - [13.2.3、打印出当前用户的详细信息](#1323打印出当前用户的详细信息)
      - [13.2.4、打印当前终端所指向的路径](#1324打印当前终端所指向的路径)
      - [13.2.5、打印当前执行的脚本文件的父目录](#1325打印当前执行的脚本文件的父目录)
      - [13.2.6、参数的获取](#1326参数的获取)
        - [13.2.6.1、获取参数的值](#13261获取参数的值)
        - [13.2.6.2、举例说明 `"$*"` 和 `"$@"` 的差异](#13262举例说明--和--的差异)
        - [13.2.6.3、获取位置参数的个数](#13263获取位置参数的个数)
  - [14、获取文件的路径、文件名、后缀、父级文件夹名等](#14获取文件的路径文件名后缀父级文件夹名等)
    - [14.1、提取文件全名](#141提取文件全名)
    - [14.2、提取后缀](#142提取后缀)
    - [14.3、提取出我们需要的文件所在的目录路径](#143提取出我们需要的文件所在的目录路径)
    - [14.4、【活学活用】提取我们需要的文件所在路径的父文件夹名字](#144活学活用提取我们需要的文件所在路径的父文件夹名字)
    - [14.5、同级建立同名目录](#145同级建立同名目录)
    - [14.6、总结](#146总结)
  - [15、获取目录下所有文件夹的名称并输出](#15获取目录下所有文件夹的名称并输出)
  - [16、获取桌面路径](#16获取桌面路径)
  - [17、awk](#17awk)
  - [18、其他](#18其他)


*用 Visual Studio Code 作为IDE编辑代码*

```shell
code .
```

## 1、第一行固定写法

```shell
#!/bin/bash
```

## 2、加权限(当前用户的执行权限)

*资料来源*

[mac查看、修改文件权限的命令](https://www.jianshu.com/p/d11f2c49921b)

*用户组*

```
u 代表用户;
g 代表用户组;
o 代表其他;
a 代表所有;
```

*权限*

```
-: 代表无权限;
r: 代表可读权限;
w: 代表可写权限;
x: 代表可执行权限;
```

*示例*

```shell
# 如果没有执行权限，在这个sh文件的目录下，执行chmod u+x *.sh
filePath=$(dirname $0)/$(basename $0)
echo "我在这里：$filePath" 

# 定位📌于该文件的垂直文件夹路径
folderPath=$(dirname $0)
cd folderPath

# 加权限（给当前用户加执行权限）
fileFullName=$(basename $0)
chmod u+x $fileFullName
```

## 3、关于路径

*资料来源*

[CSDN/shell 中获取文件真实路径（绝对路径）](https://blog.csdn.net/LGD_2008/article/details/45913957)

*获取 MacOS 系统桌面路径*

```bash
echo "Users/"${USER}"/Desktop"
```

*获取该 sh 文件的绝对路径*

```shell
echo "$(dirname $0)/$(basename $0)" 
```

*获取该 sh 文件所在目录的绝对路径*

```shell
filepath=$(cd "$(dirname "$0")"; pwd)
```

## 4、键盘监听(read)❤️

### 4.1、键盘输入 => 变量

*键盘输入值到变量`defaultInput`*

```shell
read -p "请输入项目文件夹名字,以回车结束:" folder_name
defaultInput $folder_name
# echo "The value of var is ${defaultInput}"
```

*把键盘输入放入变量`sure`*

```shell
read sure
```

### 4.2、高级用法示例

```shell
#!/bin/bash

while true;do
    # 提示用户输入路径
    read -p "请输入文件路径: " path
    # 检查路径是否存在
    if [ -e "$path" ]
    then
        echo "路径存在"
        # 提取不带后缀的文件名
        filename=$(basename "$path" .$(echo "$path" | awk -F . '{print $NF}'))
        echo "文件名为: $filename"
        break
    else
        echo "路径不存在，请重新输入"
    fi
done
```

## 5、if/else举例

*读取判定键盘输入的字符是否为回车*

```shell
read -p "请输入项目文件夹名字,以回车结束:" folder_name
defaultInput $folder_name
if [[ $folder_name = "" ]];then

else
  
fi
```

*判定命令行对外输出字符*

```shell
if brew -v;then
    echo "The package is installed"
    brew update
    brew doctor
    brew -v
else
    echo "The package is not installed"
    open https://brew.sh/
    /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
    ### brew环境变量设置
    echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> /Users/$(whoami)/.zprofile
    eval "$(/opt/homebrew/bin/brew shellenv)"
fi
```

*参数说明*

```shell
<<'COMMENT'
大于 -gt (greater than)
小于 -lt (less than)
大于或等于 -ge (greater than or equal)
小于或等于 -le (less than or equal)
不相等 -ne （not equal）
相等 -eq （equal）
COMMENT
```

## 6、字符串

### 6.1、字符串比较

```shell
## 判定路径不允许是桌面
if [ "$folderPath" = "/Users/"${USER}"/Desktop" ]; then
    # echo "Paths are equal."
    echo "桌面不允许执行此操作！"
else
    # echo "Paths are different."
    cp $filePath $"/Users/"${USER}"/Desktop"
fi
```

### 6.2、字符串截取

*资料来源*

[CSDN/shell脚本截取两个关键字之间的字符](https://blog.csdn.net/you_123/article/details/125409826)

#### 6.2.1、shell脚本截取两个关键字之间的字符❤️

```shell
#!/bin/sh

mysqlVersion=`mysql --version` # 获取命令输出到变量：用`命令`
# mysql  Ver 8.0.33 for macos13.3 on arm64 (Homebrew)
echo $mysqlVersion | awk -F 'Ver ' '{ print $2 }' | awk -F ' for' '{ print $1 }'
```

#### 6.2.2、字符串取数字

```bash
➜  ~ echo "hdsad1.02.2.22" | sed -r "s/.*([0-9]+\.[0-9]+\.[0-9]+)(\.[0-9]+).*/\1/g"
1.02.2
echo "hdsad1.02.2.22" | sed -r "s/.*([0-9]+\.[0-9]+\.[0-9]+)(\.[0-9]+).*/\2/g"

➜  ~ echo "hdsad1.02.2.22" | sed -r "s/.*([0-9]+\.[0-9]+\.[0-9]+)(\.[0-9]+).*/\2/g"
.22
```

```shell
version=$(mysql --version | awk -F 'Ver ' '{ print $2 }' | awk -F ' for' '{ print $1 }')
echo $version
```

## 7、for/in 和 do/done举例

```shell
for element in `gem list --local | grep cocoapods`
    do
        echo $"正在卸载CocoaPods子模块："$element$"......"
        # 使用命令逐个删除
        sudo gem uninstall $element
    done
else
    echo "取消卸载CocoaPods"
fi
```

## 8、多行注释

*添加定义*

```shell
<<'COMMENT'
Hi
COMMENT
```

## 9、添加默认值（字符串）

```shell
function defaultInput() {
	result=${1}
	if [${1} = $""]
	then
		# 什么都不输入，即默认值
		result=$'Test'
	fi
}
```

## 10、清空文件（localGoEnvInfo.md）

```shell
echo "" > localGoEnvInfo.md
```

## 11、写文件

### 11.1、接受键盘输入，向文件写一段内容

```shell
#【覆盖写入 和 追加写入 共用的前置条件】
## 文件全名（包含后缀名）
fileFullName=${filePath##*/}
fileCopy_fullname=$"/Users/"${USER}"/Desktop/"$fileFullName
echo $fileCopy_fullname
## 键盘输入
read -p "请输入需要追加写入的内容，以回车结束:" file_content
```

```shell
# 覆盖写入
cat>$fileCopy_fullname<<EOF
// 接受键盘输入的覆盖写入的内容
$file_content
EOF
```

```shell
# 追加写入
cat>>$fileCopy_fullname<<EOF
// 接受键盘输入的追加写入的内容
$file_content
EOF
```

### 11.2、向文件写一行内容

*配置 **GOPATH** 的环境变量*

```
1、全局搜索文件（ ~/.bash_profile）;
2、进行文件内查找字符串（GOPATH）;
3、并对其进行写入（export PATH="$GOPATH/bin:$PATH"），并刷新配置;
4、唯一性：目标文件里只允许写入一次
```

```shell
grep GOPATH ~/.bash_profile
if [ $? -ne 0 ] ;then
	echo 'export PATH="$GOPATH/bin:$PATH"' >> ~/.bash_profile
	source ~/.bash_profile
fi
```

*配置 **Homebrew** 的环境变量*

```
1、将 Homebrew 添加到终端会话的环境变量中，以便确保所有 Homebrew 安装的软件包都可以在终端中使用。
echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> /Users/$(whoami)/.zprofile
2、将 Homebrew 加载到当前 Shell 会话中，以便立即生效。这意味着您现在可以使用 Homebrew 来安装、更新和删除软件包。
eval "$(/opt/homebrew/bin/brew shellenv)"
```

## 12、在列表里面（gem list --local）搜寻（grep）文本（cocoapods）

```shell
echo "查看本地安装过的cocopods相关东西"
gem list --local | grep cocoapods
```

### 12.1、grep 返回的退出状态

```
<<'COMMENT'
grep 返回的退出状态为0，表示成功;
退出状态为1，表示没有找到;
如果找不到指定的文件，退出状态为2;
COMMENT
```

## 13、获取当前用户信息

### 13.1、终端获取 `${HOME}`

```bash
➜  Shell 学习 ${HOME}                   
➜  ~ 
➜  ~ echo ${HOME}              
/Users/jobs
➜  ~ 
```

### 13.2、shell 脚本获取

#### 13.2.1、打印出当前的用户名

**注意：这里是双引号**

```
echo "$USER"
```

或者

```
echo `whoami`
```

#### 13.2.2、打印出当前的用户进程ID

```
echo "$UID"
```

或者

```
echo `id -u`
```

#### 13.2.3、打印出当前用户的详细信息

**排列板式不一**

```
echo `who am i`
```

或者

```
echo `who` 
```

或者

```
who
```

#### 13.2.4、打印当前终端所指向的路径

```
CRTDIR=$(pwd)
echo $CRTDIR
```

#### 13.2.5、打印当前执行的脚本文件的父目录

```
workdir=$(cd $(dirname $0); pwd)
echo $workdir
```

或者

```
PRG="$0"
while [ -h "$PRG" ] ; do
  ls=`ls -ld "$PRG"`
  link=`expr "$ls" : '.*-> \(.*\)$'`
  if expr "$link" : '/.*' > /dev/null; then
    PRG="$link"
  else
    PRG=`dirname "$PRG"`/"$link"
  fi
done
PRGDIR=$(cd $(dirname $PRG); pwd)
echo $PRGDIR
```

#### 13.2.5、获取当前执行脚本的时间，并将其存储到变量中

```
cur_time=$(date +"%Y-%m-%d %H:%M:%S")
echo "当前执行的脚本时间是：${cur_time}"
```

其中，**$(date +"%Y-%m-%d %H:%M:%S")**用于获取当前时间，并将其格式化为年-月-日 时:分:秒的形式。
该时间将存储在**cur_time**变量中，并使用echo命令将其输出到屏幕上。

如果想要在脚本执行期间不断更新当前时间，可以将上述命令放在一个无限循环中，并添加一些睡眠时间，如下所示:

```
while true
do
    cur_time=$(date +"%Y-%m-%d %H:%M:%S")
    echo "当前执行的脚本时间是：${cur_time}"
    sleep 1
done
```

这将每秒钟更新一次当前时间，并将其输出到屏幕上。

#### 13.2.6、参数的获取

*资料来源*

[[segmentfault/Bash技巧:介绍 $0、$1、$2、$#、$@、$*、$? 的含义](https://segmentfault.com/a/1190000021435389)]

##### 13.2.6.1、获取参数的值

*假设执行 `./test.sh a b c` 这样一个命令，则可以使用下面的参数来获取一些值：*

```
$0
对应 *./test.sh* 这个值。如果执行的是 ./work/test.sh， 则对应 *./work/test.sh* 这个值，而不是只返回文件名本身的部分
```

```
$1
会获取到 a，即 "$1" 对应传给脚本的第一个参数
```

```
$2
会获取到 b，即 "$2" 对应传给脚本的第二个参数
```

```
$3
会获取到 c，即 "$3" 对应传给脚本的第三个参数。"$4"、"$5" 等参数的含义依此类推
```

```
$#
会获取到 3，对应传入脚本的参数个数，统计的参数不包括 "$0"
```

```
$@
会获取到 "a" "b" "c"，也就是所有参数的列表，不包括 "$0"
```

```
$*
也会获取到 "a" "b" "c"， 其值和 "$@" 相同。但 "$*" 和 "$@" 有所不同
"$*" 把所有参数合并成一个字符串，而 "$@" 会得到一个字符串参数数组
```

```
$?
可以获取到执行 ./test.sh a b c 命令后的返回值
在执行一个前台命令后，可以立即用 "$?" 获取到该命令的返回值
该命令可以是系统自身的命令，可以是 shell 脚本，也可以是自定义的 bash 函数
当执行系统自身的命令时，"$?" 对应这个命令的返回值
当执行 shell 脚本时，"$?" 对应该脚本调用 exit 命令返回的值。如果没有主动调用 exit 命令，默认返回为 0
当执行自定义的 bash 函数时，"$?" 对应该函数调用 return 命令返回的值。如果没有主动调用 return 命令，默认返回为 0
```

```
$$
shell脚本的进程号
```

##### 13.2.6.2、举例说明 `"$*"` 和 `"$@"` 的差异

*假设有一个 `testparams.sh` 脚本，内容如下：*

```bash
#!/bin/bash

for arg in "$*"; do
    echo "****:" $arg
done
echo --------------
for arg in "$@"; do
    echo "@@@@:" $arg
done
```

**这个脚本分别遍历 `"$*"` 和 `"$@"` 扩展后的内容，并打印出来。**

*执行 `./testparams.sh` 脚本，结果如下：*

```bash
$ ./testparams.sh This is a test
****: This is a test
--------------
@@@@: This
@@@@: is
@@@@: a
@@@@: test
```

可以看到，`"$*"` 只产生一个字符串，for 循环只遍历一次。
而 `"$@"` 产生了多个字符串，for 循环遍历多次，是一个字符串参数数组。

**注意：如果传入的参数多于 9 个，则不能使用 `$10` 来引用第 10 个参数，而是要用 `${10}` 来引用。**

即，需要用大括号 `{}`把大于 9 的数字括起来。
例如，`${10}` 表示获取第 10 个参数的值，写为 `$10` 获取不到第 10 个参数的值。
实际上，`$10` 相当于 `${1}0`，也就是先获取 `$1` 的值

##### 13.2.6.3、获取位置参数的个数

*在 bash 中，可以使用 `$#` 来获取传入的命令行或者传入函数的参数个数。*
**要注意的是，`$#` 统计的参数个数不包括脚本自身名称或者函数名称。**
例如，执行 `./a.sh a b`，则 `$#` 是 2，而不是 3。

可以看到，`$#` 实际上是扩展为位置参数（positional parameters）的个数，统计的参数不包括 `$0`。

## 14、获取文件的路径、文件名、后缀、父级文件夹名等

*资料来源*

[CSDN/shell截取文件名和文件目录](https://blog.csdn.net/u010670689/article/details/53425111)

*以 `/dir1/dir2/file.txt `为例*

使用 `${}`

### 14.1、提取文件全名

```bash
${var##*/}
该命令的作用是去掉变量var从左边算起的最后一个'/'字符及其左边的内容，返回从左边算起的最后一个'/'（不含该字符）的右边的内容。
使用该命令，可以提取出我们需要的文件名 file.txt
```

### 14.2、提取后缀

```bash
${var##*.}
该命令的作用是去掉变量var从左边算起的最后一个'.'字符及其左边的内容，返回从左边算起的最后一个'.'（不含该字符）的右边的内容。
使用该命令，可以提取出我们需要的文件后缀。

如果文件的后缀不仅有一个，例如，file.tar.gz，命令${var##*.}仅能提取最后一个后缀，而我想提取tar.gz时该怎么办？那么就要用下面所说的${var#*.}命令了。

${var#*.}
该命令的作用是去掉变量var从左边算起的第一个'.'字符及其左边的内容，返回从左边算起第一个'.'（不含该字符）的右边部分的内容。使使用该命令，可以提取出文件的多个后缀。
```

### 14.3、提取出我们需要的文件所在的目录路径

```bash
${var%/*}
该命令的使用是去掉变量var从右边算起的第一个'/'字符及其右边的内容，返回从右边算起的第一个'/'（不含该字符）的左边的内容。使用使用该命令，可以提取出我们需要的文件所在的目录
```

### 14.4、【活学活用】提取我们需要的文件所在路径的父文件夹名字

```bash
文件所在目录路径
folderPath=${filePath%/*}
文件所在文件夹名
folderName=${folderPath##*/}
```

### 14.5、同级建立同名目录

```bash
${var%%.*}
该命令的使用是去掉变量var从右边算起的最后一个'.'字符及其右边的内容，返回从右边算起的最后一个'.'（不含该字符）的左边的内容。当我们需要建立一个与文件名相同名字（没有后缀）的目录与对应的文件相对应时，就可以使用该命令来进行操作。
例如，解压文件的情况就与此类似，我们压缩文件file.zip时，会在与file.zip同级目录下建立一个名为file的目录。
```

### 14.6、总结

```bash
其实${}并不是专门为提取文件名或目录名的，它的使用是变量的提取和替换等等操作，它可以提取非常多的内容，并不一定是上面五个例子中的'/'或'.'。也就是说，上面的使用方法只是它使用的一个特例。

看到上面的这些命令，可能会让人感到非常难以理解和记忆，其实不然，它们都是有规律的。
#：表示从左边算起第一个
%：表示从右边算起第一个
##：表示从左边算起最后一个
%%：表示从右边算起最后一个
换句话来说，＃总是表示左边算起，％总是表示右边算起。

＊：表示要删除的内容，对于#和##的情况，它位于指定的字符（例子中的'/'和'.'）的左边，表于删除指定字符及其左边的内容；对于%和%%的情况，它位于指定的字符（例子中的'/'和'.'）的右边，表示删除指定字符及其右边的内容。这里的'*'的位置不能互换，即不能把*号放在#或##的右边，反之亦然。

例如：${var%%x*}表示找出从右边算起最后一个字符x，并删除字符x及其右边的字符。
```

```bash
# 读取键盘输入，回车结束监听记录
read -p "拖入文件:" filePath
## 文件全名（包含后缀名）
fileFullName=${filePath##*/}
## 文件的后缀名（只针对最右边的一个后缀名有效）
fileSuffixName=${filePath##*.}
## 文件的后缀名（多后缀名有效）
fileSuffixName2=${filePath#*.}
## 文件所在目录路径
folderPath=${filePath%/*}
## 文件所在文件夹名
folderName=${folderPath##*/}
```

## 15、获取目录下所有文件夹的名称并输出

```bash
#!/bin/bash

# 方法一 
dir=$(ls -l /usr/ |awk '/^d/ {print $NF}')
for i in $dir
    do
     echo $i
    done   

# 方法二
for dir in $(ls /usr/)
    do
        [ -d $dir ] && echo $dir
    done
# 方法三
ls -l /usr/ |awk '/^d/ {print $NF}' ## 其实同方法一，直接就可以显示不用for循环  
```

## 16、获取桌面路径

```shell
echo "Users/"${USER}"/Desktop"
```

## 17、awk

**直接打印awk的结果**

```shell
echo `mysql --version`| awk -F 'Ver ' '{ print $2 }' | awk -F ' for' '{ print $1 }'
```

**用shell变量进行承接awk的结果**

```shell
version=$(mysql --version | awk -F 'Ver ' '{ print $2 }' | awk -F ' for' '{ print $1 }')
echo $version
```

## 18、检测本机MacOS 是否安装了brew

*如果没有安装则下载安装，已经安装则进行跳过处理*

```shell
#!/bin/bash

# 检测是否已经安装了brew
if ! command -v brew &> /dev/null
then
    echo "brew 未安装，开始安装..."
    open https://brew.sh/
    /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
    ## brew环境变量设置
    echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> /Users/$(whoami)/.zprofile
    open /Users/$(whoami)/.zprofile
    eval "$(/opt/homebrew/bin/brew shellenv)"
else
    echo "brew 已经安装，跳过安装步骤。"
    ## brew 升级
    brew update
    brew doctor
    brew -v
fi
```

*卸载 brew* 

```shell
echo "======== 不管系统有没有安装brew 首先全部归零 ========"
echo "执行brew垃圾清除..."
brew cleanup
echo "准备卸载Homebrew..."
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/uninstall)"  
echo "准备手动清除Homebrew安装残留（可能需要输入管理员密码）"
sudo rm -rf /usr/local/Caskroom/
sudo rm -rf /usr/local/Frameworks/
sudo rm -rf /usr/local/Homebrew/
sudo rm -rf /usr/local/bin/
sudo rm -rf /usr/local/etc/
sudo rm -rf /usr/local/include/
sudo rm -rf /usr/local/lib/
sudo rm -rf /usr/local/opt/
sudo rm -rf /usr/local/sbin/
sudo rm -rf /usr/local/share/
sudo rm -rf /usr/local/var/
# 解决 Running `brew update --preinstall`... fatal: Could not resolve HEAD to a revision
sudo rm -rf $(brew --repo homebrew/core)
echo "======== 执行完毕brew归零操作 ========"
```

## 19、检测本机MacOS 上的 brew，是否已经安装某个由 brew 管理的软件

### 19.1、单一软件的判定

*如果没有安装则下载安装，已经安装则进行跳过处理*

例：`pandoc`

```shell
#!/bin/bash

# 检测是否已经安装了pandoc
if ! command -v pandoc &> /dev/null
then
    echo "pandoc 未安装，开始安装..."
    brew install pandoc
else
    echo "pandoc 已经安装，跳过安装步骤。"
fi
```

*或者*

```shell
if [[ $(command -v pandoc) ]]; then
  echo "pandoc is already installed."
else
  echo "pandoc is not installed. Installing..."
  brew install pandoc
fi
```

### 19.2、多软件的判定，例：`curl` + `wget`

```shell
if [[ -z “$(command -v curl)” || -z “$(command -v wget)” ]]; then
echo “未检测到curl或wget命令，现在开始安装curl命令…”
echo “”
brew install curl
echo “”
fi
```
