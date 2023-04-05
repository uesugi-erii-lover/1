智能数据工程实验报告
====================

- 学院：信息学院
- 专业：计算机技术
- 姓名：许潇
- 学号：12020215271

> [作业要求](作业要求.md)

# 对Wikidata数据进行存储和查询
## 一、问题
1、使用JSON获取Wikidata数据。

2、使用关系数据库和开源的gStore数据库存储Wikidata数据。

3、对关系数据库和gStore的存储分别书写SQL语句和SPARQL语句回答问答查询。

## 二、Wikidata数据分析
Wikidata实体的顶级字段有:实体名(id)、实体类型(type)、实体多语言标签(labels)、实体多语言描述(descriptions)、实体多语言别名(aliases)、根据属性来分组的实体陈述(statement)或声明(claims)、与该实体相关的站点链接(sitelinks)、当前文件的版本(lastrevid)、当前文件的发布日期(modified)。其中，多语言标签、描述、别名被称作指纹，用来区分同名条目; 声明包含属性、属性值和修饰词; 描述是含有参考文献和排名的声明; 每个声明总是与一个属性相关联，并且在一个实体中可以有多条声明与同一属性相关联。

![](https://www.hualigs.cn/image/609a6adfd9cde.jpg)

JSON格式的Wikidata数据：

![](https://www.hualigs.cn/image/609a6b17abf43.jpg)

- type：标识实体的类型，有item和property两类。
- id：实体的ID。
- labels：不同的语言描述的实体标签（名称）。
- descriptions：不同的语言对实体的描述。
- aliases：不同的语言描述的实体的别名，可能包含多个别名。
- claims: 以属性分组的实体声明(claims)或者陈述(statements)，claim包含一条主体信息(mainSnak)以及一些修饰信息(qualifierSnaks)。statement是含有参考资料（reference）的claim。每个claim总是与一个属性(property)关联(claim是关于此property的)。并且在一个实体中可以有多条claim与同一property关联。
- sitelinks：描述item实体的网页的地址

## 三、关系数据库表的设计
对Wikidata数据进行分析，知道实体间的关系是通过属性来进行描述，这样实体之间相互形成一个网，对此，设计了四张表，分别为wiki_main，wiki_claim，wiki_qualifier,wiki_reference。

- wiki_main表

![](https://www.hualigs.cn/image/609a6b907135f.jpg)

wiki_claim表

![](https://www.hualigs.cn/image/609a6c0000adc.jpg)

wiki_qualifier表

![](https://www.hualigs.cn/image/609a6bffcea41.jpg)

wiki_reference表

![](https://www.hualigs.cn/image/609a6bffcfc0a.jpg)

## 四、MySQL存储Wikidata数据

首先下载latest-all.json.bz2文件，分析Wikidata数据结构，读取文件内容，在数据库建立mywikidata表，将文件数据存取到数据库mywikidata表中从而得到Wikidata数据。

![](https://www.hualigs.cn/image/609a6c9a813ee.jpg)

![](https://www.hualigs.cn/image/609a6cfc4de1b.jpg)


## 五、MySQL查询Wikidata数据

MySQL语句：SELECT entity_id,label,description from wiki_main WHERE label ='world population'

![](https://www.hualigs.cn/image/609a6dc162559.jpg)

## 六、gStore存储Wikidata数据

### 1、将.sql转为.nt格式

将MySQL得到的数据导出得到mywikidata.sql。由于gStore是从图数据库角度存储和检索RDF数据，因此我们要先将MySQL存储的数据转换成RDF三元组形式。借助d2rq-0.8.1工具，将.sql文件转化为.ttl文件，然后再将.ttl文件转化为.nt文件。步骤如下：

a.	从www.d2rq.org下载d2rq安装包，并且下载jdbc驱动包mysql-connector-java-5.1.47.jar文件放到d2rq解压后的lib文件夹中。

b.	用generate-mapping工具为数据库创建映射文件，用命令行执行如下指令：

![](https://www.hualigs.cn/image/609a7191a402a.jpg)

其中-u root 为数据库的登录名，-p 123456 是登录密码，-o mywikidata.ttl为映射文件的名称，jdbc:mysql:///mywikidata 指定数据库名称。

c.	运行dump-rdf工具，执行如下指令，将数据库转存为RDF文件。

![](https://www.hualigs.cn/image/609a7231bc39d.jpg)

d.  得到mywikidata.ttl和mywikidata.nt

![](https://www.hualigs.cn/image/609a725f12ac7.jpg)

e.  将mywikidata.nt压缩包从linux中下载并解压。

![](https://www.hualigs.cn/image/609a72c337961.jpg)

![](https://www.hualigs.cn/image/609a7404cbe85.jpg)

### 2、安装VMware和CentOS8
VMware安装步骤：https://www.cnblogs.com/fuzongle/p/12760193.html

CentOS8安装步骤：https://www.cnblogs.com/zkwarrior/p/12697806.html

VMware和CentOS8安装成功图如下：

![](https://www.hualigs.cn/image/609a73c020b03.jpg)

![](https://www.hualigs.cn/image/609a729f6855b.jpg)

### 3、CentOS8环境下安装docker
docker安装步骤：https://blog.csdn.net/A632189007/article/details/78662741

如图所示：

![](https://www.hualigs.cn/image/609a74ee1a712.jpg)

![](https://www.hualigs.cn/image/609a75370a4a1.jpg)

![](https://www.hualigs.cn/image/609a7560b724b.jpg)

### 4、用Docker部署gStore
步骤：http://www.gstore.cn/pcsite/index.html#/documentation

如图所示：

![](https://www.hualigs.cn/image/609a75fe2120f.jpg)

![](https://www.hualigs.cn/image/609a763b6cb06.jpg)

我创建了一个文件夹mywikidata1，里面打算是装zyy.sql和mywikidata.nt文件，zyy.sql是写语句的。

![](https://www.hualigs.cn/image/609a7739c2e4b.jpg)

### 5、gStore存储Wikidata数据
使用gbuild命令从RDF格式文件（mywikidata.nt）构建新数据库wikidatazyy,如图所示：

![](https://www.hualigs.cn/image/609a78d2bbea4.jpg)

创建了wikidatazyy数据库，将mywikidata1文件夹下的mywikidata.nt文件存储在wikidatazyy数据库中。

导入时间如下：

![](https://www.hualigs.cn/image/609a79293029a.jpg)

## 七、gStore查询Wikidata数据
1、首先，创建zyy.sql，zyy.sql是写语句的。

![](https://www.hualigs.cn/image/609a779f200ea.jpg)

2、打开zyy.sql。

![](https://www.hualigs.cn/image/609a7b0dd313f.jpg)

3、编写语句

![](https://www.hualigs.cn/image/609a7cb7f23db.jpg)

4、运行语句

![](https://www.hualigs.cn/image/609a7bc65a2d5.jpg)

## 八、性能分析
本文是提取1万条数据，这二者效率如下表所示：

![](https://www.hualigs.cn/image/609a7ff956903.jpg)

从表格可以看出：MySQL的存储效率优于gStore，但是gStore的查询时间优于MySQL，MySQL以牺牲查询效率为代价，提高了存储效率。gStore以牺牲空间为代价，提高了查询效率。

## 九、附录
1、使用JSON获取Wikidata数据

2、将Wikidata数据导入到MySQL中

3、MySQL查询

4、gStore查询

代码：
if __name__ == '__main__':
    starttime = datetime.datetime.now()
    # 连接数据库
    db = pymysql.connect(host = "localhost", user = "root", password = "123456", database = "mywikidata", charset = "utf8mb4")
    print('数据库连接成功')

    # cratetable(db)

    wiki_maindata, wiki_claimdata, wiki_qualifierdata, wiki_referencedata= [], [], [], []

    data = BZ2File("latest-all.json.bz2")
    i = 0
    for line in data:
        text = data.readline()
        context = json.loads(text[:len(text) - 2])
        print('第', i, '次插入数据')
        insertdata(context, wiki_maindata, wiki_claimdata, wiki_qualifierdata, wiki_referencedata)
        if i % 2000 == 0:
            insertwiki_main = "insert into wiki_main(entity_id,type,data_type,len,label,description,aliase) values(%s,%s,%s,%s,%s,%s,%s)"
            insertwiki_claim = "insert into wiki_claim(id,entity_id,property_id,datavalue_value,datavalue_type,type,qualifiers_order,snaks_order,snaktype) values(%s,%s,%s,%s,%s,%s,%s,%s,%s)"
            insertwiki_qualifier= "insert into wiki_qualifier(id,property_id,hash,snaktype,datavalue_value,datavalue_type,datatype) values(%s,%s,%s,%s,%s,%s,%s)"
            insertwiki_reference = "insert into wiki_reference(id,property_id,snaktype,datavalue_value,datavalue_type,datatype) values(%s,%s,%s,%s,%s,%s)"
            cursor = db.cursor()
            cursor.executemany(insertwiki_main, wiki_maindata)
            cursor.executemany(insertwiki_claim, wiki_claimdata)
            cursor.executemany(insertwiki_qualifier, wiki_qualifierdata)
            cursor.executemany(insertwiki_reference, wiki_referencedata)
            db.commit()
            wiki_maindata.clear()
            wiki_claimdata.clear()
            wiki_qualifierdata.clear()
            wiki_referencedata.clear()
        i += 1


        if i >= 1000000:
            cursor = db.cursor()
            cursor.close()
            endtime = datetime.datetime.now()
            print('运行时间：')
            print(endtime - starttime).seconds
            sys.exit()



