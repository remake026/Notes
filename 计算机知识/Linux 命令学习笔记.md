# Linux 命令学习清单
> 适配语雀笔记，按章节整理，每一条附带**实操练习命令**，边敲边学。
>

## 一、核心概念
### 1.1 Linux终端
> 理解：终端 = 命令行交互入口，Shell解析命令
>

```plain
echo $SHELL
```

### 1.2 命令基础格式：`命令名 [选项] [参数]`
```plain
ls -lh /home
cp -r ~/test_src ~/test_dest
```

### 1.3 帮助系统
```plain
man ls                # 完整手册，按q退出
ls --help             # 简要帮助

# 练习：查看 cp 的帮助文档
man cp
cp --help
```

## 二、文件与目录管理
### 2.1 基础操作命令
| 命令 | 常用参数 | 练习指令 |
| --- | --- | --- |
| ls | -l 详细列表，-h人类可读大小，-a显示隐藏文件 | `ls -lh`     `ls -la` |
| cd | ~家目录，..上级目录，-回到上一次目录 | `cd ~`     `cd ..`     `cd -` |
| pwd | 无参数，打印当前路径 | `pwd` |
| mkdir | -p 递归创建多级目录 | `mkdir -p demo/a/b/c` |
| rm | -r递归，-f强制，-i交互确认（推荐） | `rm -i test.txt`     `rm -rf demo`（慎用） |
| cp | -r递归复制目录 | `cp -r demo demo_bak` |
| mv | 移动 / 重命名 | `mv demo_bak demo_new` |
| touch | 创建空文件、更新文件时间戳 | `touch test.txt` |


**整套实操练习**

```plain
mkdir -p linux_practice
cd linux_practice
touch note.txt
ls -lh
cp note.txt note2.txt
mv note2.txt note_bak.txt
rm -i note_bak.txt
cd ..
```

### 2.2 文件查看命令
| 命令 | 常用参数 | 练习指令 |
| --- | --- | --- |
| cat | 一次性输出文件内容 | `cat note.txt` |
| less | 分页浏览，/搜索，q退出 | `less /etc/passwd` |
| head | -n 看前N行 | `head -n 10 /etc/passwd` |
| tail | -n看末尾N行，-f实时跟踪日志 | `tail -n 10 /etc/passwd`     `tail -f app.log` |


```plain
head -n 15 /etc/group
tail -n 20 /var/log/syslog
```

### 2.3 文件搜索命令
#### find：按**文件名/属性**找文件
```plain
# 在当前目录查找txt文件
find . -name "*.txt"
# 查找目录
find /home -type d -name "linux_practice"
# 查找7天前的文件
find . -type f -mtime +7
```

#### grep：在**文件内容**搜索关键词
参数：`-i`忽略大小写，`-r`递归，`-n`显示行号，`-v`反向匹配

```plain
grep "root" /etc/passwd
grep -i "bash" /etc/passwd
grep -rn "linux" ./linux_practice
grep -v "#" /etc/profile   # 排除带#注释行
```

## 三、权限管理
### 3.1 权限基础
r=4 w=2 x=1；u所有者 g组 o其他人

### 3.2 chmod 修改权限
数字模式

```plain
chmod 644 test.txt    # rw-r--r-- 文件默认
chmod 755 run.sh      # rwxr-xr-x 脚本/目录
chmod 700 secret.txt  # 仅自己能访问
```

符号模式

```plain
chmod u+x run.sh      # 给所有者增加执行权限
chmod g-w test.txt    # 移除组的写权限
chmod -R 755 ./demo   # 递归修改目录下所有文件
```

> 练习
>

```plain
touch run.sh
chmod u+x run.sh
ls -l run.sh
```

### 3.3 chown / chgrp 修改属主属组
```plain
chown user:user test.txt       # 同时修改所有者+组
chown -R user:user ./demo      # 递归修改
chgrp user test.txt            # 只修改所属组
```

> ⚠️ chown 需要root权限，普通用户只能测试查看，不能修改别人文件
>

## 四、进程管理
### 4.1 查看进程
```plain
ps aux                     # 列出全部进程
ps aux | grep nginx        # 过滤指定进程
top                        # 实时资源监控
htop                       # 增强版top（需要apt/yum安装）
```

练习：查看当前终端所有进程

```plain
ps aux | grep bash
```

### 4.2 终止进程
```plain
kill 1234                  # 优雅终止 SIGTERM(15)
kill -9 1234               # 强制杀死 SIGKILL(9)，最后手段
pkill -f "python"          # 按进程名字批量杀
```

### 4.3 后台运行 & 前后台切换
```plain
nohup ./task.sh > task.log 2>&1 &   # 后台常驻，关闭终端继续跑
jobs                                 # 查看当前终端后台任务
# Ctrl+Z                            # 暂停前台程序放入后台
bg %1                                # 让任务1后台继续运行
fg %1                                # 任务1切回前台
```

## 五、网络管理
| 命令 | 参数 | 练习指令 |
| --- | --- | --- |
| ping | -c 指定发包数量 | `ping -c 4 baidu.com` |
| ip addr | 查看网卡IP | `ip addr` |
| ss | -t TCP，-u UDP，-l监听，-n数字端口，-p进程 | `ss -tuln`     `ss -tlnp` |
| curl | -I只看响应头，-O下载文件 | `curl -I [https://www.baidu.com](https://www.baidu.com)` |
| wget | 下载文件 | `wget https://xxx/file` |
| ssh | 远程登录 | `ssh username@192.168.1.100` |
| scp | 远程拷贝 | `scp test.txt user@ip:/tmp` |


```plain
ping -c 4 baidu.com
ip addr
ss -tuln
curl -I [https://www.baidu.com](https://www.baidu.com)
```

## 六、文本处理三剑客：grep、sed、awk
### grep（查找）
```plain
grep -rn "error" ./
grep -v "DEBUG" app.log
```

### sed 流编辑器，替换/截取
`-i`直接修改原文件；`s/old/new/g`全局替换

```plain
# 预览替换（不改原文件）
sed 's/root/admin/g' /etc/passwd
# 直接修改，先备份bak
sed -i.bak 's/root/admin/g' test.txt
# 打印5~10行
sed -n '5,10p' /etc/passwd
```

### awk 格式化、统计
`-F` 指定分隔符

```plain
# 打印第一列
awk '{print $1}' /etc/passwd
# 冒号分割，打印第1列用户名
awk -F':' '{print $1}' /etc/passwd
# 对第一列数字求和
awk '{sum+=$1} END{print sum}' data.txt
```

### 三剑客组合练习
```plain
# 统计包含root的行数
grep "root" /etc/passwd | wc -l
# 提取用户名并排序
awk -F':' '{print $1}' /etc/passwd | sort
```

## 七、管道 | 、重定向、通配符
### 管道 `|`
把前命令输出交给后面命令

```plain
ps aux | grep bash
history | tail -n 10
ls -lh | grep txt
```

### 重定向
| 符号 | 作用 | 示例 |
| --- | --- | --- |
| `>` | 覆盖写入文件 | `ls > list.txt` |
| `>>` | 追加写入 | `echo "new line" >> list.txt` |
| `2>` | 错误输出重定向 | `wrongcmd 2> err.log` |
| `&>` | 标准输出+错误全部重定向 | `cmd &> all.log` |


```plain
ls -lh > filelist.txt
echo "hello linux" >> filelist.txt
cat filelist.txt
```

### 通配符
`*`任意字符，`?`单个字符，`[]`范围，`{a,b}`枚举

```plain
ls *.txt
ls file?.txt
ls file[0-9].txt
echo {1..10}
ls {*.txt,*.md}
```

## 八、系统信息与监控
```plain
whoami                     # 当前用户
uname -a                   # 系统内核信息
uptime                     # 开机时间、负载
df -h                      # 磁盘挂载使用（h人类可读）
du -sh *                   # 当前目录各文件夹大小
du -sh * | sort -h         # 按大小排序，磁盘排查神器
free -h                    # 内存使用
journalctl -xe             # 系统日志
dmesg | tail -20           # 内核最新日志
history                    # 历史命令
```

```plain
whoami
uname -a
df -h
free -h
du -sh * | sort -h
```

## 九、高效操作技巧
### 终端快捷键
+ `Tab` 自动补全
+ `Ctrl+C` 终止当前命令
+ `Ctrl+L` 清屏
+ `Ctrl+A` 光标到行首；`Ctrl+E`行尾
+ `Ctrl+R` 反向搜索历史命令
+ `Ctrl+U` 删除光标前面整行；`Ctrl+K`删除光标后面

### 命令别名
```plain
# 临时生效（当前终端）
alias ll='ls -lh --color=auto'
# 永久写入配置
echo "alias ll='ls -lh --color=auto'" >> ~/.bashrc
source ~/.bashrc
```

### 高频组合命令练习
```plain
# 查找7天前日志并删除
find /tmp -name "*.log" -mtime +7 -delete
# 实时过滤日志错误
tail -f app.log | grep --line-buffered "ERROR"
# 统计各类文件数量
ls | awk -F. '{print $NF}' | sort | uniq -c | sort -rn
```

## 十、学习路线复习任务
1. 阶段1：`ls cd pwd touch`，练习目录跳转、文件创建
2. 阶段2：`cp mv rm mkdir find`，文件增删改查
3. 阶段3：`chmod chown`，练习修改文件权限
4. 阶段4：`ps top kill`，查看杀死进程
5. 阶段5：`grep sed awk | > >>` 文本处理组合
6. 阶段6：`ip ss curl ssh` 网络命令
7. 阶段7：别名、nohup，简单自动化

### 推荐实操任务包（一次性复制练习）
```plain
# 新建练习目录
mkdir linux_study && cd linux_study
touch log1.txt log2.txt run.sh
chmod u+x run.sh
ls -lh
echo "test content" > log1.txt
cat log1.txt
grep "test" log1.txt
find . -name "*.txt"
```

> 直接全选复制，粘贴到语雀新建文档即可。  
需要我额外给你做一份**配套的Linux练习题（填空+实操题+参考答案）** 吗？可以一起放进语雀。
>

