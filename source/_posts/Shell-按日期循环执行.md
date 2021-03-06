title: Shell 按日期循环执行
date: 2015-11-03 20:41:17
tags: [Linux,Shell]
categories: Shell
---
服务器上有些脚本,执行的参数用到了日期,也就是每天执行一次,日期取当天时间作为参数,当有些时候需要把这些脚本过去一段时间的运行结果重新运行一次时,手动指定脚本日期没有办法大批量执行,需要在shell中写循环,让脚本批量执行.

### 方法一
以一个小的例子来说:把指定日期时间段内的日期都输出来,看看下面的代码:
```bash
#! /bin/bash

start_date=20151101
end_date=20151103
start_sec=`date -d "$start_date" "+%s"`
end_sec=`date -d "$end_date" "+%s"`
for((i=start_sec;i<=end_sec;i+=86400)); do
    day=$(date -d "@$i" "+%Y-%m-%d")
    echo $day
done
```
看看输出结果:
```
2015-11-01
2015-11-02
2015-11-03
```
执行结果是循环输出日期,把需要循环的脚本放在循环里调用,把`$day`当参数使用即可.如果脚本里面的变量是日期,那就把脚本代码拷贝到循环中间.日期的格式可以按需求自己调整.例如:
```bash
for((i=start_sec;i<=end_sec;i+=86400)); do
    day=$(date -d "@$i" "+%Y-%m-%d")
    echo $day
    sudo /usr/python/ /usr/dev/job.py $day
done
```
这样就可以把时间参数批量传入脚本.

### 方法二
但是上面的还是很麻烦，你要记住一天有多少秒，这样也不太容易记住，下面还有另一种方法,注意到时间格式为`20151101`,这种其实也是一个数字，我们可以让日期按天增加，然后比较日期大小关系:
```
#! /bin/bash

start=20160101
end=20160103

while [ ${start} -le ${end} ]
do
  echo ${start}
  start=`date -d "1 day ${start}" +%Y%m%d`	# 日期自增
done
```
**备注:**Shell里面的数字大小比较:
```
-ne	# 不相等
-gt	# 大于
-lt	# 小于
-ge	# 大于或等于
-le	# 小于或等于
```

### 方法三
虽然上面的方法二比方法一要好很多，但是好像有个问题，一般日期都是以字符串的形式传入，多半是`2016-01-01`这种带间隔的方式，这种情况我们是没办法比较的，当然把上面的改一改也能用,例如:
```
#! /bin/bash

# 先把日期处理成纯数字的格式
start=`date -d "2016-01-01" +%Y%m%d`
end=`date -d "2016-03-01" +%Y%m%d`

while [ ${start} -le ${end} ]
do
  echo ${start}
  # 假设下面有一个脚本需要2016-01-01格式的日期
  bash xxx.sh `date -d "${start}" +%Y-%m-%d`
  start=`date -d "1 day ${start}" +%Y%m%d`	# 日期自增
done

```
但是这样感觉好麻烦，为啥循环就只能是数字呢?有没有一种比较通用的循环方式,这个当然有，你可以像下面这样:
```
#! /bin/bash

start='2016-01-01'
end='2016-01-03'

while [ "${start}" != "${end}" ]
do
  echo ${start}
  start=`date -d "1 day ${start}" +%Y-%m-%d`	# 日期自增
done

```
这样就可以循环了，但是记住，`start`取不到`end`值，即最后输出的是`[start,end)`，好像Shell里面并没有`do...while`这种循环，所以最好在循环之前，先做一步处理:
```
end=`date -d "1 day ${end}" +%Y-%m-%d`	# 日期自增
```
然后再就可以取到两边的边界值了。
**注意:**这样比较的时候，一定要注意`while`里面的左右条件一定要用`""`引起来，`'`不行。
