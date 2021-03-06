title: 'Hive 控制生成文件个数'
date: 2016-11-21 17:02:51
tags: [Hive,Hadoop]
categories: Hive笔记
---
在有些时候，想要控制hql执行的mapper,reducer个数,reducer设置过少，会导致每个reducer要处理的数据过多，这样可能会导致OOM异常，如果reducer设置过多，则会导致产生很多小文件，这样对任务的执行以及集群都不太好.通常情况下这两个参数都不需要手动设置，Hive会根据文件的大小和任务的情况自动计算,但是某些特殊情况下可能需要进行调优，下面列举两个常用的调优场景看看这两个参数在调优的时候都是怎么用的:

### 设置reducer个数
reducer个数最直接的影响是hql执行完之后生成的文件个数，假设你的任务有n个reducer,那么最后可能会生成的文件肯定至少有n个,前提是你没有设置合并小文件，这个有什么用处呢？最简单的一个用处是我们在hive里面经常会调第三方接口来获取数据，例如解密之内的，假设接口不限速，我们在udf里面调接口的时候会发现特别慢，感觉直接select很快，但是把查询结果insert到一个表保存就很慢，这个原因就在于数据请求线程太少了。
在hadoop里面，一个文件至少会起一个mapper,如果你的文件很小(默认1G起一个mapper),那就完了，整个任务就一直是一个个来请求接口的，所以非常的慢。那如果想加快接口的调用呢？其实也简单，把文件分成几个小文件，假设分成了10个小文件，那么再次调接口就会快很多了，10线程和单线程的差别还是非常大的。
具体的语句也就两句话，记住，划分小文件还得保证每个文件尽量大小一样。
```
set mapred.reduce.tasks=50;
insert into table xxx
select
  *
from
 xxx
distribute by rand();
```
**备注:**第一个set设置的就是最后你要生成的文件个数，后面的`distribute by rand()`保证了记录随机分配到50个文件，不管里数据量有多小，最后这50个文件的大小应该是一致的.

### 动态分区产生小文件
有些场景会产生大量的文件，比如动态分区插入，或者两个比较大的表做join，对于大表做join，我没有细测，但是我发现我用一个很小的表(大概70M),去join一个很大的表(大概400G),由于Hive在处理小表join大表的时候会做优化,左边的表会都加载到内存里面，然后分发到各个节点和大表做join,这样最后就会在大表所在的节点产生最终的结果，后果就是会原来大表的那些文件现在都变成小文件了,小文件太多其实对性能还是有影响的,这个其实可以最后用一个reducer来合并小文件。
主要说一下动态分区产生小文件问题,这是个很有意思的问题，动态分区好用，但是为啥会产生这么多小文件。原因就在于，假设动态分区初始有N个mapper,那么最后生成了m个分区，最终会有多少个文件生成呢？答案是`N*m`,是的，每一个mapper会生成m个文件，就是每个分区都会对应一个文件，这样的话你算一下。所以小文件就会成倍的产生。怎么解决这个问题，通常处理方式也是像上面那样，让数据尽量聚到一个reducer里面,因为有时候虽然动态分区不会产生reducer,但是也就意味着最后没有进行文件合并,我们也可以用`distribute by rand()`这句来保证数据聚类到相同的reducer。
