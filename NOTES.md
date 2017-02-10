# TODO

gclient 不能正常使用
gserver for multiple users, not load db for each connection, but get the db pointer
assign the pointer to the user who require this db, and ensure that each db is just kept once in memory

支持返回?p结果的查询
辛雨非的插入时vstree bug和彭鹏的build时vstree bug
vstree部分在build和insert时候的错误
统计74上的8亿测试结果
控制台命令如果使用方式不当，比如直接gbuild，应该输出提示信息示意该怎么操作
数据库的多版本备份，动态删除
保持连接而不是每次都用socket
模仿海量图大作业，基于gStore开发一个社交应用，要求可以批量导入且实时查询
陈佳棋的任务：s和p对应同一个实体，应该先重命名，再过滤。还有一种情况是两者只是名字相同，实则并无关系

各版本对比的表格中应加一列几何平均数，现实中大多数查询是简单查询，最好还有一个平均数，对应着把数据做归一化后求和
dbpedia q6现在应该统计并对比结果了，包含在测试结果中

最好用json格式来输出结果，或者在JSON和字符串之间相互转换
另外在改善kvstore后，build过程明显加快了很多，现在vstree的建立时间成了瓶颈

preFilter中不仅要看谓词，也要看表大小以及度数，有些节点最好过滤！！！

在0.4.0版本后统一使用git管理，为各位开发者维护一个分支
更新github上的测试报告和help文档
5亿+bug，最好单机能支持到10亿
---
备份原来的代码后，合并王的代码并测试，再在原来代码上加join缓存测试
以后统一用git进行版本管理，以当前版本为基础
bookug, hulin, wlb, cjq, pp, gpu, wuda, chenyuyan各一个分支

张雨的任务：单起点单终点的正则表达式路径问题，如果是多起点多终点？
下学期的任务：提取相关联的几个方向写论文
将IRC聊天放到gstore文档上，freenode #gStore

从下往上建立B+树和vstree树
加快更新操作，如insert和delete等，是否考虑增加修改的源操作
删除时数据全部删光会出大问题，保留空树？或者插入时考虑为空树的情况？
---
更新量太多可直接重建索引，少量更新可写到overflow page中，在系统闲置时合并
比如维护一个insert和delete记录索引，每次需要查多个，判断关系
同一triple在同一索引中最多出现一次：原来已有/没有，先删后插，先插后删时等价的？
若原来已有，插入应该被放弃？若原来没有，删除应该被放弃？

常量triple怎么办，有些点比较重要必须精确过滤，不仅仅看谓词还要看度数等等
---
评估函数用机器学习的方式？学习参数？学习模型？
但机器学习对新问题没有一个基本的界，不像数学化的评估函数，对任何情况都能保证一个上界
(目前数据库里面很少有人利用机器学习来估价)

WARN：B+树删除时，向旁边兄弟借或者合并，要注意兄弟的定义，应该是同一父节点才对！
输出还是组织成JSON格式比较好，用JSON的C++库进行压缩解压，客户端也无需对输出格式有太多了解，减轻后者负担

URI是唯一的，RDF图中不存在相同标签的节点，无法合并相似节点，也没法利用自同构的预处理加快查询
DBpedia最新的数据集，原来的相对太小了
count函数的优化：深搜计数即可，不必生成中间表
B+树每个节点内部添加索引，对keys分段？

考虑使用 sigmod2016 那篇图同构的论文方法，实现一套join
但那个是基于路径的，BFS和索引连接的思想值得借用，可作为另外一套join方法
@hulin
---
实现其他的join思路，比如基于过滤效果

pthread写多线程
可以尝试开-O6优化，如果结果保证正确，应该速度可以提升很多
@lijing
验证-O6的正确性和效率

如何在preFilter和join的开销之间做平衡
preFilter中的限制条件是否过于严格

寻找查询图的特征，分类做查询计划：
先对于每个查询，确定各部分的开销比例

fix the full_test:how to sum the db size right?
for virtuoso, better to reset each time and the given configure file(need to reserver the temp logs)

load过程先导入满足内存的内容，或者先来几轮搜索但不输出结果，避免开头的查询要读磁盘。vstree直接全导入内存？
先完成合并测试，再测lubm500M和bsbm500M  -- 90 server

两表拼接时多线程分块join
jemalloc??
查询级别的缓存（测试时可将查询复制后再随机打乱）
vstree并行分块


@lijing
考虑出入度数，编码为1的个数？应该不用，在编码的邻居信息中能够得到体现。
第二步编码应该更长，点和边对应着放在一起。按出入边分区，一步点，二步边分区和二步点。
对一个节点保留其最长链信息没啥用，因为数据图基本是连通的，最长链就是图的最长链。
多步编码不应分开，而应在编码逐一扩展，第二步可以依旧保留详细信息，最好用更长编码，因为信息更多。
可能有相同的谓词对应多个邻居，同样的谓词只要保留一个即可，不同邻居可以重合。
第三步可以只记谓词，第四步可以只记边的出入向。

是否可以考虑把literal也作为vstree中的节点，加快过滤？
join过程中生成literal可能开销大，且可以考虑用周围边对literal进行过滤。。。
但sp2o生成的literal链应该不会很长？

join buffer:use array instead of map, an ID can be in several columns, id2string maybe error(different pre, different list)!
use Bstr[2^16], only for entity, also as buffers(after intersecting with candidates)
if in id range, then use, otherwise generate(not update?) (when merging with hulin, discuss if exists)
Method:scan all linking point from left to right to find a node without literals(if not?), and buffer for this column
scan the column and get the maxID and minID, set minID as offset and only need to build a maxID-minID array
(the interval smaller, the better, how about using hash-conflict-list here)

set different buffer size for different trees in build/query, for example, sp2o and op2s with 16G, p2so with 16G
set string2id and id2string as 1-2G

测试时最好也统计出最长的string，和string的总大小

---

方正智汇：调用gStore做后台数据库，需要定义范式，比如领域、模型等等
递归删除的效率问题：比如删除一整个模型(没有关系数据库删除整个表快)
可以考虑加一个域的约束，根据这种边来全部删除
domain可用前缀表表达，但x可属于多个domain

批量插入批量修改的规模很大
需要支持修改谓词，不会出现p p o的情况
加谓词比较好加
删类的谓词，那么实例有该谓词的都得删

模型/领域 每个entity一个领域id？ 只给class分配id？只给最上层class分配id？(这样查询太复杂，效率低)
划分多个数据库，同样考虑模式，如果领域和领域之间没有交集
如果领域之间不完全独立，不能直接划分为多个数据库！如果划分需要考虑连接，划分后可直接用于分布式数据库应用

---

bsbm300M q8.sql - new gstore worser
it is because this query is linear, which means the dfs join order not optimized, and the time spent in preFilter maybe not change
maybe using non-dfs order for join?
we need to compare the time of each part in q8.sql

用vf2来对比？借鉴它的思路？不过vf2做的是标准的子图同构，而非子图包含，需要修改
vf2与gStore相互借鉴，另一种过滤方式？
客观来说，多边时vstree还是比较好的

对B+树加锁，可以直接更新节点的rank值，使其rank值最大（最高位或次高位），解锁时清除相应位
(不过这样会引入比较大的开销，因为内存堆的修改过程是比较耗时的，需要线性的查找时间)
实际上B+树应侧重于读的性能而非读写的平衡，比如不用块结构，直接把一个节点存成一片。
也可以使得每个节点固定大小，顺序存储，所有value存成记录文件，只增不减。
query过程几乎没有修改，需要free的block几乎没有，可以直接存free块号和当前最大块号，
而无需存所有块的使用情况。也是由于这个原因，树的查询中几乎不对堆做修改，避开了耗时的堆指针查询操作。

修复：需要返回pre结果的谓词查询，首先要知道哪些谓词位于select中，这需要扫描查询

change to BestJoin using index lists and super edges:
the memory cost of join will be cut down, so we can cache more for querying!
define different cache size for build and query in makefile with -D 
?how about BFS instead of DFS?

需要一个整体的并行模块：
并行的线程总数要根据cpu的线程总数来设置，不能超过太多
三种粒度，多个sparql query的并行，一个sparql内部多个basic query的并行，一个basic query内部的join流水线，两表拼接时的分块并行
并行时对kvstore部分的影响（vstree因为基本是全内存，所以没啥影响），换入换出问题，需要加锁（禁止换出）
树协议加锁？
多query并行时join的内存开销可能超标，需要在vector申请失败时抛出异常，其他地方捕捉异常处理（比如等待内存空闲）

关注jena的更新，以及其是否修复bug
每个模块的时间和提升
gStore性能表格，前后对比

to support 10 billion in a single machine, the memory should > 100G
change Bstr length to long long type
(also update anywhere related to length or strlen, such as Signature::encodeStr2Entity)
change entity/literal ID type to unsigned
+++++++++++++++++++++
新建一个ID管理模块，要求高效稳定，放在Util中
在storage, Database, VStree多个地方用到
better move StringIndex to KVstore
In unit test, insert/delete should be tested to improve coverage!!!
too many entities for lubm500M, so it is very slow


应统计并降低IO开销（主要是kvstore），尽量顺序连续、预读取
比如加载时除了索引结构外，可以先读入一批节点
另外如果可以把bstr和节点分离，确保每个节点都是相同大小（预先分配定量数组）
所有bstr串单独放到一个文件中，不删除不修改，只能附加
大小超过一个阈值后，进行整理，移除无效的字段
节点中只保留Bstr的文件偏移，用long long类型
可以考虑将id2string直接使用倒排表实现，不用B+树（因为插删是很少的）
但string2id很麻烦，不能直接用倒排表计算

get 不能全部返回去重的，因为插删也需要调用get
除了join模块，其他只和查询相关的模块，最好也改为去重的

watdiv5000 exit unexpectedly -- p2so index
541740149   549240150
entity 26060745
literal 23964574
pre 86

调试成组插删，提升效率
目前存在的问题在于small_aa small_ab q3.sql
toStartJoin to add literals p2olist the olist is not right

精简索引，如p2s和p2o可借用p2so来完成，同样得比如s2po和o2ps
而且sp2o普遍比较大，也可以用s2po加二分查找来完成
最终只需要完成6+3棵kv树
(很多问题，实现s2o要排序，代价可能非常高)
（可以考虑共用s2索引，同时存下o和po作为value，一个key对应两个Bstr）

implement so2p with s2p and o2p may cause error
s p o1
s2 p o

test on real data of applications is needed
when case is ?s p1 o       ?o p2 o
this will be viewed as two basic queries and answered separately
is this good? or if considered as one BGP, how can we do 2-hop encoding on it?

join(preFilter根据p的num数量)和stream中的问题(判断失误或内存泄露)
将b树返回num改为直接用已有的实现(应该在底层实现么，如何保证效率)
在Query中建立p2num和sp2num等对应，避免反复搜索。
(o2p o2ps o2s是否应该划分为entity/literal来加速, id2string是否用倒排表)

考虑用内联汇编在大循环里做优化(测试对比效率提升)
最好把entity和literal彻底分开

Join::preFilter -- 某些时候先过滤，某些时候后过滤，视结果数目和边的过滤性能而定
不用似乎也不会出错
when using allFilterByPres, not always good, sometimes too costly!(dbpedia2014, self6.sql)
use "well-designed" in GeneralEvaluation to enable not all selected query; 
三个瓶颈：getFinalResult copyToResult allPreFilter
join_basic的效率非常关键，是核心

另一种过滤：用sp2num预估数量，或者也预估vstree的结果数量，set triple dealed(以后避免重复处理)
可以考虑只在拼接时add literal(需要考虑常量约束，entity与literal分开考虑)，最多提前加一个保证join启动
在idlist中寻找entity和literal的临界点时，注意左小右大，不要单纯扫描
是否最后再考虑卫星边的约束更好？（卫星边一定是单度的）
entity和literal没必要放在一起作交，另外交的顺序或许也很重要
（每次应选最小的两个，或者多路merge的形式）

+++++++++++++++++++++
带标签的路径，而不是过滤加验证的方式 |图仿真避开无用的候选集和join
分析cas的结构和查询模式
ask转换为常量三元组的判断

统计数据库大小时使用du -h还是ls -lR是个问题，但一般也不会相差太多
gStore中的内存空洞问题，一方面是可能某个块没有写满，另一方面是可能预申请的块太多。

- - -

# TEST

signature.binary in db file are not removed in program, but it should be delete to save space

单元测试：
http://www.cnblogs.com/linux-sir/archive/2012/08/25/2654557.html
http://www.ibm.com/developerworks/cn/linux/l-cn-cppunittest/
https://my.oschina.net/vaero/blog/214893

watdiv5000在建立p2?索引时出错(p2so索引太大？)
jena在测试lubm5000的查询时，似乎答案有问题，如q0.sql

若将signature编码长度由800扩大到1200+1000，build时间大幅上升，查询时间有些不变甚至加长，有些减小一半。
for q6.txt in DBpedia, the latter signature length returns the time of 16366841ms, while teh size is the same as original, 457393467
(but the size of q6.sql in jena is 17852675, which one is right?)

建立日志系统方便调试？
出错时如何回滚，直接进行版本管理？
(每次插删前，将原来的索引做一个备份？)

测试时每个查询除了时间外，还要记录一下结果数，备用
a whole test on dbpedia must be done before commited!!!(just check query answer size)
dbpedia q0.sql gstore比virtuoso慢，因为过滤花了很久，而且候选集很大，之后又要反复判断
bsbm100000 self3.sql   pre_filter too costly
3204145 for ?v0    1 for ?v1  total 3204145
maybe we should start from ?v1 or using p2s or p2o(maybe p2so first, then pre_filter, then generate)
self5.sql 中也是preFilter时间太长
可以考虑视情况进行preFilter，或者研究下开销为何有时那么大
或许可以不用，或者仍然全用s2p或改判断条件，或者只过滤单度顶点的边约束

virtuoso: dbpedia2014 这个测试很慢，容易出问题，最好单列
100441525 ms to load
and the size is 17672699904/1000-39846 KB
50ms for q0.sql
217ms for q1.sql
210ms for q2.sql
23797ms for q3.sql
5536ms for q4.sql
2736ms for q5.sql
9515231ms for q9.sql

virtuoso: bsbm_10000
40137ms to load ,the size is 635437056/1000-39846 KB

#### using multi-join and stream, compared with jena
gstore performs worser than jena in these cases:
bsbm series: self1.sql, sellf3.sql, self8.sql (self4,5,6)
dbpedia series: q3.sql, q4.sql, q5.sql (q9.sql)
lubm series: q0.sql, q2.sql, q13.sql, q16.sql
watdiv series: C1.sql, F3.sql 

#### performance of vstree have great impact on join process, due to the candidate size
the build time is 3 orders...it seems not directly related with the length of signature
(so we can extend to better length!)

#### BSBM: when query contains "^^"(self0.sql), gstore output nothing. and when the results contain "^^"(self3.sql, self8.sql), gstore will omit the thing linked by "^^".
(this causes miss match because the other three dbms support this symbol. Virtuoso, however, can deal with queries containing "^^"
 but will not output "^^..." in results)

#### sesame does not support lubm(invalid IRI),  and unable to deal with too large datasets like dbpedia2014, watdiv_300M, bsbm_100000...

- - -

# DEBUG

error when use query "...." > ans.txt in gconsole

lubm5000 q1 q2 delete/insert/query
1003243 none after vstree

dbpedia q6.sql

build db error if triple num > 500M

- - -

# BETTER

#### 在BasicQuery.cpp中的encodeBasicQuery函数中发现有pre_id==-1时就可以直接中止查询，返回空值！

#### 将KVstore模块中在堆中寻找Node*的操作改为用treap实现(或多存指针避开搜索？)

#### 无法查询谓词，因为VSTREE中只能过滤得到点的候选解，如果有对边的查询是否可以分离另加考虑(hard to join)。(或集成到vstree中, add 01/10 at the beginning to divide s/o and p. however, result is 11 after OR, predicates are not so many, so if jump into s/o branch, too costly. and ho wabout the information encoding?)

- - -

# DOCS:

#### how about STL:
http://www.zhihu.com/question/38225973?sort=created
http://www.zhihu.com/question/20201972
http://www.oschina.net/question/188977_58777

- - -

# WARN

重定义问题绝对不能忍受，现已全部解决（否则会影响其他库的使用,vim的quickfix也会一直显示）
(因为和QueryTree冲突，最终搁置)
类型不匹配问题也要注意，尽量不要有（SparqlLexer.c的多字节常量问题）
变量定义但未使用，对antlr3生成的解析部分可以不考虑（文件太大，自动生成，影响不大）
以后最好使用antlr最新版（支持C++的）来重新生成，基于面向对象，防止与Linux库中的定义冲突！
（目前是在重定义项前加前缀）

- - -

# KEEP

大量循环内的语句必须尽可能地优化!!!

- gStore also use subgraph homomorphism!

- always use valgrind to test and improve

- 此版本用于开发，最终需要将整个项目转到gStore仓库中用于发布。转移时先删除gStore中除.git和LICENSE外的所有文件，然后复制即可（不要复制LICENSE，因为版本不同；也不用复制NOTES.md，因为仅用于记录开发事项）

- build git-page for gStore

- 测试时应都在热启动的情况下比较才有意义！！！gStore应该开-O2优化，且注释掉-g以及代码中所有debug宏

- 新算法在冷启动时时间不理想，即便只是0轮join开销也很大，对比时采用热启动

- 不能支持非连通图查询(应该分割为多个连通图)

- - -

# ADVICE

#### 数值型查询 实数域 [-bound, bound] 类型很难匹配，有必要单独编码么？    数据集中不应有范围    Query中编码过滤后还需验证
x>a, x<b, >=, <=, a<x<b, x=c
vstree中遇到"1237"^^<...integer>时不直接取字符串，而是转换为数值并编码
难点：约束条件转换为析取范式， 同一变量的约束，不同变量间的约束

#### 如何用RDF表示社交网络，以及社交应用的并发问题

#### construct a paper, revisit gStore: join method, encoding way, query plan

#### 阅读分析PG源代码

#### 单元测试保证正确性和效率，valgrind分析性能瓶颈与内存泄露，重复越多的部分越应该深入优化(甚至是汇编级别)

#### we can try on watdiv_1000/watdiv_2000/watdiv_1000M/freebase/bio2rdf

#### limited depth recursive encoding strategy: build using topological ordering, how about updates? (append neighbor vertices encodings) (efficience and correctness?)

#### 目前的vstree按B+树形式实现，其实没有必要将一个节点的标签存两次。可以父节点放child子节点放entry，也可以在父节点存entry数组，根节点的entry单独提出。
(这样需要从上到下考虑)

#### 论文中的vs*tree结构其实更复杂，节点之间有很多边相连，每层做一次子图匹配，过滤效果应该会更好，之后join代价很可能也会更小。目前的vstree仿照B+树实现更简单，每次单独过滤出一个节点的候选集，之后再统一join，但效果差强人意。

#### 在index_join中考虑所有判断情况、一次多边、交集/过滤等等，multi_join不动

#### 在join时两个表时有太多策略和条件，对大的需要频繁使用的数据列可考虑建立BloomFilter进行过滤

#### not operate on the same db when connect to local server and native!

#### VStree部分的内存和时间开销都很大，测试gstore时应打印/分析各部分的时间。打印编码实例用于分析和测试，如何划分或运算空间(异或最大或夹角最大，立方体，只取决于方向而非长度)

#### full_test中捕捉stderr信息（重要信息如时间结果应该确保是标准输出？），结果第一行是变量名（有select *时应该和jena分列比）

#### Can not operate on the same db when connect to local server and native!

#### auto关键字和智能指针？

#### 实现内存池来管理内存？

#### join可以不按照树序，考虑评估每两个表的连接代价
1. 用机器学习（对查询分类寻找最优，梯度下降，调参）评估深搜顺序的好坏
2. 压缩字符串：整体控制是否启动（比如安装时），同时/不同时用于内存和硬盘。对单个string根据结构判断是否压缩？（一个标志位）关键词映射？string相关操作主要是比较，相关压缩算法必须有效且不能太复杂！
3. 实现对谓词的查询（再看论文）
4. 将查询里的常量加入变量集，否则可能不连通而无法查询，也可能影响join效率。如A->c, B->c，本来应该是通过c相连的子图才对，但目前的gstore无法识别。 

#### 考虑使用并行优化：load过程能否并行？vstree过滤时可多个点并行进行，另外join时可用pipeline策略，每得到一个就传给后续去操作。

#### 写清楚每个人的贡献


## SSD
how to set /home/ssd r/w/x for everybody
mkfs.ext4 -E stride=128,stripe-width=128 /dev/sdb
tune2fs -O ^has_journal /dev/sdb
modify /etc/fstab:
/dev/sdb /home/ssd ext4 noatime,commit=600,errors=remount-ro 0 1
to check if valid:   mount /home/ssd
ensure that IO scheduler is noop(just ssd) or deadline(ssd+disk), otherwise:
add in rc.local: echo deadline(noop,cfq) >(>>) /sys/block/sdb/queue/scheduler
(open trim if ssd and system permitted)

## GPU

## RAID

## 学习Orient DB的方式，可同时支持无模式、全模式和混合模式
ACID? neo4j GraphDB

## 单个文件的gStore？嵌入式，轻便，类似sqlite，方便移植，做成库的方式给python等调用

## 联邦数据库，避免数据重导入，上层查询分块

## 没必要关闭IO缓冲同步，因为用的基本都是C语言的输入输出操作

---

## DataSet

http://www.hprd.org/download/
