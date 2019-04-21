# shell-script-notes

# 概念

1. `Shell` 是一种介于用户和系统之间的应用程序，用户通过 `Shell` 程序访问操作系统内核服务。
2. `Shell` 脚本是一种运行在 `Shell` 上的脚本程序。
3. 常见的 Shell 程序：
    1. [Bourne Shell](https://www.wikipedia.org/wiki/Bourne_shell)（/usr/bin/sh或/bin/sh）
    2. [Bourne Again Shell](https://en.wikipedia.org/wiki/Bash)（/bin/bash）
    3. [C Shell](https://en.wikipedia.org/wiki/C_shell)（/usr/bin/csh）
    4. [K Shell](https://en.wikipedia.org/wiki/KornShell)（/usr/bin/ksh）

本文使用 `Bourne Again Shell` 作为环境，因此脚本文件开头都会冠以 `#!/bin/bash` 声明，`#!` 其后的路径指定的程序，就是解释脚本文件所用的 `Shell` 程序。

# 基础语法

1. `Shell` 脚本通常使用 `sh` 作为文件后缀，但这不是强制的。执行一个 `Shell` 脚本通常有两种方式：
    ```bash
    # 1. 作为可执行文件执行：
    chmod +x ./demo.sh
    ./demo.sh

    # 作为解释器参数执行：脚本文件中第一行的 #! 声明将被忽略
    /bin/bash demo.sh
    ```

2. `Shell` 变量的声明不同于 `PHP`，无需使用 `$` 符号，也和大多数语言不同，等号两边不能有空格，变量可以重新被定义。使用变量时，在变量名前边加上 `$` 符号并用大括号将其包括即可，大括号不是必须的，但是一个好习惯：
    ```bash
    #!/bin/bash

    my_name="xxxpangbo"
    echo ${my_name}

    for file in `ls /`; do
	    echo ${file}
    done
    ```

    通过 `readonly` 标记一个只读变量，通过 `unset` 删除一个非只读变量。
    ```bash
    #!/bin/sh

    myUrl="http://www.google.com"
    readonly myUrl

    myUrl="http://www.runoob.com"
    unset myUrl
    echo $myUrl
    // 没有输出
    ```

3. `Shell` 中字符串可以用单引号、双引号甚至不用引号：
    ```bash
    #!/bin/bash

    # 单引号中的所有字符都会原样输出，所以单引号字符串中的变量是无效的，虽然不能出现单个引号，用转移字符也不行，但是可以成对出现单引号：
    str='this is 'a’ string.'

    # 双引号字符串中则可以包含转义字符，也可以使用变量
    # 单引号和双引号都可以用于拼接字符串，相当于在一个字符串中插入另一个字符串
    
    your_name="runoob"
    # 使用双引号拼接
    greeting="hello, "$your_name" !"
    greeting_1="hello, ${your_name} !"
    echo $greeting  $greeting_1

    # 使用单引号拼接
    greeting_2='hello, '$your_name' !'
    greeting_3='hello, ${your_name} !'
    echo $greeting_2  $greeting_3

    # output
    # hello, runoob ! hello, runoob !
    # hello, runoob ! hello, ${your_name} !

    # 获取字符串长度
    echo ${#greeting_2}
    # output: 15

    # 提取子字符串
    string="runoob is a great site"
    echo ${string:1:4} # 输出 unoo

    # 查找子字符串，i 和 o 谁先出现就统计谁
    string="runoob is a great site"
    echo `expr index "$string" io`  
    # output: 4
    ```

4. `Shell` 数组和大多数语言中的数组类似，支持下标访问等特性，但仅支持一维数组，数组中的元素直接用空格分隔：
    ```bash
    #!/bin/bash

    array_name=(value0 value1 value2 value3)

    # 读取数组元素
    valuen=${array_name[n]}
    # 读取所有元素
    echo ${array_name[@]}
    
    # 取得数组元素的个数
    length=${#array_name[@]}
    # 或者
    length=${#array_name[*]}
    # 取得数组单个元素的长度
    lengthn=${#array_name[n]}
    ```

5. `Shell` 中使用注释：
    ```bash
    #!/bin/bash

    # 这是一个单行注释

    :<<！
    这是一个多行注释
    开头可结尾的 x 可以替换成其他字符，但需要成对出现
    ！
    ```

6. 在执行脚本时，可以向其传递一个或者多个参数，在脚本程序中通过 `$n` 的形式访问这些参数：
    ```bash
    #!/bin/bash

    echo "Shell 传递参数实例！";
    echo "执行的文件名：$0";
    echo "第一个参数为：$1";
    echo "第二个参数为：$2";
    echo "第三个参数为：$3";

    # 调用：./demo.sh 1 2 3
    # output
    # 第一个参数为：1
    # 第二个参数为：2
    # 第三个参数为：3

    # 还有一些预定义的参数
    # $#: 传递到脚本的参数个数
    # $$: 脚本运行的当前进程ID号
    # $!: 后台运行的最后一个进程的ID号
    # #?" 显示最后命令的退出状态。0表示没有错误，其他任何值表明有错误
    ```

7. `Shell` 支持常见的运算符，而 `bash` 则不支持数学运算符，不过可以通过 `expr` 来间接实现，注意运算符两边必须有空格：
    ```bash
    #!/bin/bash

    #!/bin/bash

    val=`expr 2 + 2`
    echo "两数之和为 : ${val}"

    # 乘法运算符需要转义
    `expr $a \* $b`

    # 条件表达式需要用中括号括起来，并且必须有空格
    [ $a == $b ]

    # 在 MAC 中 shell 的 expr 语法是：$((表达式))，此处表达式中的 "*" 不需要转义符号 "\" 
    echo $((5 * 6))
    # output: 30

    # 关系运算符只支持数字或者数字内容的字符串
    # -eq 检测两个数是否相等
    # -ne 检测两个数是否不相等
    # -gt 检测左边的数是否大于右边的
    # -lt 检测左边的数是否小于右边的
    # -ge 检测左边的数是否大于等于右边的
    # -le 检测左边的数是否小于等于右边的

    # 布尔运算符
    # ! 逻辑非
    # -o 逻辑或
    # -a 逻辑与

    # 逻辑运算符
    # && 逻辑 and
    # || 逻辑 or

    # 字符串运算符
    # = != 检测字符串是否相等
    # -z -n 检测字符串长度是否为 0
    # $ 检测字符串是否为空

    # 文件测试运算符
    # -b 检测文件是否是块设备文件
    # -c 检测文件是否是字符设备文件
    # -d 检测文件是否是目录
    # -f 检测文件是否是普通文件（既不是目录，也不是设备文件）
    # -g 检测文件是否设置了 SGID 位
    # -k 检测文件是否设置了粘着位(Sticky Bit)
    # -p 检测文件是否是有名管道
    # -u 检测文件是否设置了 SUID 位
    # -r 检测文件是否可读
    # -w 检测文件是否可写
    # -x 检测文件是否可执行
    # -s 检测文件是否为空（文件大小是否大于0）
    # -e 检测文件（包括目录）是否存在
    ```

