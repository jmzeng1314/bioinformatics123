# 学习目的：(原则：恰到好处，进退自如)

- R语言入门，熟悉数据处理
- 入门ggplot绘图
- R包和bioconductor的使用

# 1.介绍R语言(10分钟)

**R语言的起源**：
R 是一个有着统计分析功能及强大作图功能的软件系统，是由新西兰奥克兰大学统计学系的 Ross Ihaka 和 Robert Gentleman 共同创立，可能是因为两人首字母都是R所以就叫R语言。R语言是一门统计语言，天生就不同于其他的编程语言。R语言封装了各种基础学科的计算函数，我们在R语言编程的过程中只需要调用这些**计算函数，就可以构建出面向不同领域、不同业务的、复杂的数学模型。**

掌握R语言的语法，仅仅是学习R语言的第一步，要学好R语言，需要你要具备基础学科能力(初等数学，高等数学，线性代数，离散数学，概率论，统计学) + 业务知识(金融，生物，互联网) + IT技术(R语法，R包，数据库，算法) 的结合。所以把眼光放长点，只有把自己的综合知识水平提升，你才真正地学好R语言。

换句话说，一旦你学成了R语言，你将是不可被替代的。

**R语言的用途**:

- 统计分析：包括统计分布，假设检验，统计建模
- 金融分析：量化策略，投资组合，风险控制，时间序列，波动率
- 数据挖掘：数据挖掘算法，数据建模，机器学习
- 互联网：推荐系统，消费预测，社交网络
- 生物信息学：DNA分析，物种分析
- 生物制药：生存分析，制药过程管理
- 全球地理科学：天气，气候，遥感数据
- 数据可视化：静态图，可交互的动态图，社交图，地图，热图，与各种Javascript库的集成核心是统计分析，分类是数据处理，机器学习，文本挖掘，量化投资, 高级绘图

**R语言初学者必备的技能**：
- Ｒ包安装
```R
install.packages(“package_name”): 安装
library(“package_name”): 加载使用
detach(“package_name”, unload = TRUE): 卸载
remove.packages(“package_name”): 从本地删除
update.packages(oldPkgs = “package_name”): 升级
```

详细内容参考[R包终极解决方案](https://mp.weixin.qq.com/s?__biz=MzAxMDkxODM1Ng==&mid=2247483988&idx=1&sn=c13789e00d253f79a9b497f7a15f341a&chksm=9b4842efac3fcbf929c85a769d446a2e1127d154833b74ac53c326cf94d698531e346c39deff&mpshare=1&scene=1&srcid=10237PCnKhjmv7xJGDlXUPUI#rd)

- 获取帮助：help(XXX) ？XXX。Jimmy的要求，这个函数要用过1000遍才算数。
- rm(list = ls())  清除R工作环境中的全部东西
- 数据结构查询：
  在Rstudio中可以方便地可视化查看，在R的console当中不停地使用这两个函数是十分便捷的方法，
  head() 查看数据的前6行，
  str()  查看数据的结构组成，对于了解数据很有帮助，我们在后面的练习中会不断使用

**Rstudio的使用**
Rstudio介绍

# 2.R 语言基础(30分钟)

## 2.1 基本运算：
**算术计算**
`：+ - * \ ^`
逻辑比较
- 函数计算：`log2(x) log10(x) exp(x) sin(x) tan(x) abs(x) sqrt(x)`
- 理解变量(变量就是储存器)：`a <- 3;a <- "b"` 赋值符号是<-,使用ls()可以显示当前已经创建的对象

## 2.2 数据结构

理解变量(变量就是储存器)

**向量**

向量是多个值的集合，根据变量类型的不同可以分为数值型向量，字符型向量和逻辑向量。构建向量使用函数c()。
使用names()函数可以为向量中的元素命名。使用length()函数可以返回向量长度。

- 通过位置（index）
  a[1],a[2,4],a[2:4]
- 通过名字
  a[b]
- 排除选择（不要某个元素）
  a[-2],a[-c(2:4)]
- 通过逻辑向量选择
  a[b == TURE],a[c >= 3],a[c !=3]

**矩阵**

矩阵可以理解为多行多列的表格，通常，列表示变量，行表示个体或者观测值。构造矩阵可以通过matrix()函数

举例说明：
- nrow() and ncol()可以返回矩阵的行数和列数，rownames()和colnames()可以返回或者设置矩阵的行名和列名。
- 从一个矩阵中提取子集的方法和向量类似,只不过分为行和列两个参数，行和列各自的提取方式和向量相同，[]内第一个参数行位置，第二个参数代表列位置
- rowSums() colSums()可以用来为矩阵的行列分别求和，rowMeans() 和 colMeans()则用来计算均值。
- 如果想使矩阵的行列互换，只需要使用t()函数即可。

**因子**

在R中，变量可以是连续型变量，名义型变量（无需:a,b,c）和有序变量(上，中，下)。名义型变量和有序变量也叫作因子，通过factor()函数可以设置因子，并以数组形式储存。

**数据框(重点)**

数据框和矩阵形式类似，但是可以接受列的类型不同。其中行代表观测值或者个体，列代表变量。构建数据框可以使用data.frame()函数。
在R中，使用subset(x, subset, select, drop = FALSE, ...)函数可以轻松帮我们实现提取子集的目的，通过subset筛选行，通过select来筛选列。

**列表**

list()，选取元素使用的是[[]]
补充材料[用R语言把数据玩出花样](http://blog.fens.me/r-transform/)
# 3.R语言进阶(30分钟)
## 3.1条件控制if else

讲解逻辑判断

```
if (condition) {
  expr
}

```

## 3.2循环 while for

- 介绍while循环

```
while (condition) {
  expr
}

```
介绍break

- 介绍for循环

```
primes <- c(2, 3, 5, 7, 11, 13)
# loop version 1
for (p in primes) {
  print(p)
}
# loop version 2
for (i in 1:length(primes)) {
  print(primes[i])
}
```
练习：写一个**汇报成绩自动写评语**的小脚本
要求：给出成绩数据，自动汇报最高成绩，并给出评语
```
Subject <- c("yuwen","math","English")
Tom <- c(98,60,34)
Jimmy <- c(32,100,76)
Sky <- c(86,79,92)
score <- data.frame(Tom,Jimmy,Sky,row.names=Subject)

for (name in list(Tom,Jimmy,Sky)){
  print(max(name))
  a <- which(score==max(name),arr.ind = TRUE)
  p <- paste(names(score)[a[2]]," is good at ",row.names(score)[a[1]],sep ="")
  print(p)
}
```
## 3.3函数(介绍apply函数）
首先我们引用一下[R常见问题解答](https://cran.r-project.org/doc/contrib/Liu-FAQ.pdf)中的第8题？
> R 需要编程么？
> 不！**大多数时候不需要**，因为 R 有很多函数和包，而且每天都在增加，你用的一般方法和
> 函数都可以在 R 自带包中找到。

写一个自己的fucntions

apply(为了解决for循环效率低下的缺陷)，下面介绍一下apply(),lapply(),sapply()

- apply(X, MARGIN, FUN, ...)
  其中X为一个数组；MARGIN为一个向量（表示要将函数FUN应用到X的行还是列），若为1表示取行，为2表示取列，为c(1,2)表示行、列都计算。
```
ma <- matrix(c(1:4, 1, 6:8), nrow = 2)
ma
apply(ma, c(1,2), sum)
apply(ma, 1, sum)
apply(ma, 2, sum)
```
注意行列都计算等同于每个元素都计算
比如：
apply(ma, c(1,2), sqrt)
等同于
sqrt(ma) 这是**向量化计算**(速度很快)

- lapply(X, FUN, ...)
  lapply返回的是列表，举例
```
# The vector pioneers has already been created for you
pioneers <- c("GAUSS:1777", "BAYES:1702", "PASCAL:1623", "PEARSON:1857")
# Split names from birth year
split_math <- strsplit(pioneers, split = ":")
# Take a look at the structure of split_math
str(split_math)
# Convert to lowercase strings: split_low
split_low <- lapply(split_math,tolower)
# Take a look at the structure of split_low
str(split_low)
```
- sapply(X, FUN, ...) 注意和lapply的区别,返回值不一样
```
# create temp
temp <- list(c(3,7,9,6,-1),
            c(4,8,3,-1,-3),
            c(1,4,6,2,-2),
            c(5,7,9,4,2),
            c(3,7,9,6,-1),
            c(-3,5,8,9,4),
            c(6,9,12,13,5))
# Use lapply() to find each day's minimum temperature
lapply(temp,min)
# Use sapply() to find each day's minimum temperature
sapply(temp,min)
# Use lapply() to find each day's maximum temperature
lapply(temp,max)
# Use sapply() to find each day's maximum temperature
sapply(temp,max)
```
更多细节参考[掌握R语言中的apply函数族](http://blog.fens.me/r-apply/)
进阶包lpyr()(课上不讲)

# 4.数据处理(主要是数据框)(30分钟)

- 4.1 R包的概念及基本操作：
- 4.2 R的文件读取和结果输出(readr)
- 4.3 Hadley Wickham的神包tidyverse(一包走天下)
  **包括六个他自己写的包，规范了数据处理的流程**
- 4.4 dplyr包介绍(筛选汇排变)
  filter(),select(), summarise(),arrange(),mutate() group_by()
## 课堂举例

```{r}
library(tidyverse)
head(iris)
```
### mutate()函数的使用

对已有列数据变换并添加为新的列，类似标准函数transform
添加一个新的列new，并且new是Sepal.Length和Sepal.Width的乘积
```{r}
df_mutate<- mutate(iris, new = Sepal.Length * Sepal.Width)
head(df_mutate)
```
### select()函数的使用：

按照列名筛选列，可结合starts_with，ends_with，contains，matches，one_of，num_range和everything等使用
- 变量名称以Petal开头的变量
```{r}
df_select_1 <- select(iris, starts_with("Petal"))
```
- 变量名称以Width结尾的变量
```{r}
df_select_2 <- select(iris, ends_with("Width"))
```
- 变量名称包含etal的变量
```{r}
df_select_3 <- select(iris, contains("etal"))
```
- 变量名称匹配t的变量

```{r}
df_select_4 <- select(iris, matches(".t."))
```

-只筛选Petal.Length,Petal.Width两个变量
```{r}
df_select_5 <- select(iris, Petal.Length, Petal.Width)
```
- 筛选所有的变量
```{r}
df_select_6 <- select(iris, everything())
```
- 把需要的变量赋值给vars，然后用one_of模式筛选
```{r}
vars <- c("Petal.Length", "Petal.Width")
df_select_7 <- select(iris, one_of(vars))
```
###filter()函数的使用
按照已定条件对行做过滤，类似标准函数subset,
- 筛选Species是setosa的数据
```{r}
df_filter_1 <- filter(iris, Species == "setosa")
```
- 筛选Sepal.Length大于5，并且Species是setosa的数据
```{r}
df_filter_2 <- filter(iris, Sepal.Length > 5 & Species == "setosa")
```
- 筛选Sepal.Length大于5，并且Species包含在c("setosa","versicolor")中的数据
```{r}
df_filter_3 <- filter(iris, Sepal.Length > 5 & Species %in% c("setosa","versicolor"))
```
###arrange()函数的使用
对数据进行排序，类似标准函数order：
按照Sepal.Length从小到大排序
```{r}
df_arrange_1 <- arrange(iris, Sepal.Length)
```
- 双变量排序，按照Sepal.Length从小到达，并且按照Sepal.Width从大到小排序
  desc()表示从大到小
```{r}
df_arrange_2 <- arrange(iris, Sepal.Length, desc(Sepal.Width))
```
###summarise()函数的使用
对数据进行汇总操作,可结合group_by使用
- 计算Sepal.Length的平均值和标准差：
```{r}
df_summarise_1 <- summarise(iris, mean(Sepal.Length), sd(Sepal.Length))
```
- 按照Species分组，计算Sepal.Length的平均值和标准差
```{r}
iris_groups<- group_by(iris, Species)
df_summarise_2 <- summarise(iris_groups, mean(Sepal.Length), sd(Sepal.Length))
```
- 我们尝试一下 %>% (快捷键是英文状态下，ctr + shift + M)
```{r}
df_summarise_3 <- iris %>%
                    group_by(Species) %>%
                    summarise(mean(Sepal.Length), sd(Sepal.Length))
```

- dplyr的计算是以tbl格式返回的，我们可以转成dataframe, 当然这步没必要，但是为了方便显示
```
df_summarise_3<- as.data.frame(df_summarise_2)
```

以下课堂不讲，自行练习

### 数据关连

我们知道，数据库中经常需要将多个表进行连接操作，如左连接、右连接、内连接等，这里dplyr包也提供了数据集的连接操作，具体如下：

创建两个数据集
```{r}
library(dplyr)
df1 <- data.frame(x = c('a','b','c','a','b','e','d','f'), y = c(1,2,3,4,5,6,7,8))
df1 <- tbl_df(df1)

df2 <- data.frame(x = c('a','b','c'), z = c('A','B','C'))
df2 <- tbl_df(df2)
```

- 內连inner_joinll
```{r}
inner <- inner_join(x = df1, y = df2, by = 'x')
```
- 左连left_join
```{r}
left <- left_join(x = df1, y = df2, by = 'x')
```
- 右连right_join
```{r}
right <- right_join(x = df1, y = df2, by = 'x')
```
- 全连full_join
```{r}
full <- full_join(x = df1, y = df2, by = 'x')
```
- 返回能够与y表匹配的x表所有记录semi_join
```{r}
semi <- semi_join(x = df1, y = df2, by = 'x')
```
- 返回无法与y表匹配的x表的所记录anti_join
```{r}
anti <- anti_join(x = df1, y = df2, by = 'x')
```
### 数据合并
在R基础包里有cbind()函数和rbind()函数实现按列的方向进行数据合并和按行的方向进行数据合并，而在dplyr包中也添加了类似功能的函数，它们是bind_cols()函数和bind_rows()函数。
例子：
```{r}
mydf1 <- data.frame(x = c(1,2,3,4), y = c(10,20,30,40))
mydf2 <- data.frame(x = c(5,6), y = c(50,60))
mydf3 <- data.frame(z = c(100,200,300,400))
bind_rows(mydf1, mydf2)
bind_cols(mydf1, mydf3)
```
需要说明的是，bind_rows()函数需要两个数据框或tbl对象有相同的列数，而bind_cols()函数则需要两个数据框或tbl对象有相同的行数

- 4.5 tidyr包（gather, spread, unite,separate)
- Tidy Data 介绍tidy data的重要性，为什么要这样做
- 生成iris.tidy
```{r}
library(tidyverse)
```
```{r}
iris.tidy <- iris %>%
  gather(pm,Value,-Species) %>%
  separate(pm,c("Part","Measure"),sep = "\\.")
```
如果不能理解 %>% 符号，分步讲解结果
- 生成iris.wide, head数据后发现有两列观察值，应该是spread的结果
- 我们尝试把iris.tidy spread一下(Spread)
```
iris.wide <- iris.tidy %>%
  spread(Measure, value)
```
会报错的原因是行重复
下面是解决方案：
- Add column with unique ids，在此head一下数据，
```{r}
iris$Flower <- 1:nrow(iris)
```
- 先在宽转长，gather，在分列，长转宽，spread，最终筛选出需要的数据
```{r}
iris.wide <- iris %>%
  gather(key, value, -Species, -Flower) %>%
  separate(key, c("Part","Measure"), "\\.") %>%
  spread(Measure, value) %>%
  select(-Flower)
```
- 将iris还原成原来的数据，可选
```{r}
iris <- select(iris,-Flower)
```
- 4.6 实例讲解R数据处理的一般流程(融合在ggplot中讲解)

# 5.R字符串操作(课上不讲,有补充材料)

- 基础字符串操作
- 实例讲解stringr包

# 6.Ｒ的可视化介绍(40分钟，课堂上会举大量例子)

## 6.1 ggplot2

- ggplot是有语法的<<ggplot2:数据分析与图形艺术>>
> 一张统计图形就是从**数据**（data）到**几何对象**（geometric object，缩写为geom，包括点，线，条形等）的**图形属性**(aesthetic attributes,缩写为aes，包括颜色，形状，大小等）的一个映射。

有了语法之后，图形不在被形状束缚，作图进入艺术创作的状态，限制我们的不是技术，而是想象力。
- 讲解图层的概念（layer）
  根据百度百科，图层的定义是：
> 图层就像是含有文字或图形等元素的胶片，一张张按顺序叠放在一起，组合起来形成页面的最终效果

做出基本的图形后，根据需要不断地添加图层，每一个图层的元素都可以更改，甚至我们可以把不同数据集的数据融合在一张图上
- gglot2极简入门：
  数据，图形属性，几何对象，主题，分面，统计变换
```{r}
ggplot(mtcars, aes(x = wt, y = mpg, color = disp)) +
  geom_point()
```
mtcars是数据
aes是图形属性，包括颜色，形状，大小等
eom_point()是点，线，条形等几何对象，没有散点图，直方图这种具体的概念。
有了这三个元素，我们就已经可以开始作图
如果需要添加元素，+号即可搞定，如下：
```{r}
ggplot(diamonds, aes(x = carat, y = price))+
  geom_point()+
  geom_smooth()
```
geom_point()散点图之上可以添加geom_smooth() 平滑曲线
- 实例演示(点，条形，线)
## 6.2 qplot(课上不讲)

在Hadley Wickham在他的书中一开始就**介绍qplot作为base plot和ggplot2之间的过渡**，但是鉴于大家没有学过base plot，防止qplot对大家作图思维产生影响
在学习完ggplot后介绍qplot作为点心使用。

# 7.结合芯片分析数据介绍Ｒ的分析流程(融汇贯通)(40分钟)

介绍jimmy的五行代码搞定芯片分析
差异分析：
热图，火山图，GO分析，KEGG分析

# 8.R语言资源汇总

www.guoshipeng.com  

