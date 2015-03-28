在正态分布的假设下，比较两组计量资料数据采用t检验；同时比较多组数据则要采用方差分析。所有的统计软件，如[SPSS](http://jingyan.baidu.com/article/c35dbcb02e15018917fcbc62.html)、[Stata](http://wenku.baidu.com/link?url=80qnowgwIrWkjzdQp82yqhbrfQGKJm8YcKVJuG0x1C8miSeuCpgX5GvpC7yn5BlMj-Lp4Mrlje_8qG29EFtO2yGbvxLq747yZnkxOehSP0q)、[SAS](http://wenku.baidu.com/link?url=NIIhFEinPLKbvuDANEf6TAP5ZCfvUvaCBgkg5arPkWgFc-dDD49-GewhRT9NaA8b3qi0vZhNelIz56GuW0zyEDmAOXVf8KYNeTbxx_DenyO)，或带有部分统计功能的软件，如[Origin](http://wenku.baidu.com/link?url=YYAKjTsMpi0iSNIJN3F0JQdzsOxyhi6DfhP13SXmKbNGBylsOLb8CzzUznb5GjMGuz654W0UgAcPs7BEGPpNRP8mJaeCLA2hYyxCEnYZbmS)、[Excel](http://jingyan.baidu.com/article/48b558e3547bcf7f38c09af1.html)等都可以做单因素方差分析。[R语言](http://www.r-project.org/)是专业的统计工具，并可以免费[下载安装](http://ftp.ctex.org/mirrors/CRAN/)并易于使用。本文介绍在R中进行方差分析。

有如下数据<sup>[1]</sup>在Excel中，每列为1组，共4组。我们要比较各组差异。

<img src="README_files/figure-markdown_github/hello-1.png" title="" alt="" style="display: block; margin: auto;" />

首先，我们要把数据另存为csv格式，本例中我们存为“1.csv”。这样就方便在R中打开。

其次，我们启动R程序，在**文件**菜单中设置“工作目录”为数据文件所在的目录。然后在命令行中输入命令读入文件：

``` r
mydat=read.csv("1.csv", header=FALSE); # header参数决定是否把文件第一行作为标题名。
```

这样，数据就保存在变量mydat中了。另外，设置header参数是为了防止第一行数据变成标题名。我们可以在命令窗口中输入mydat来查看其中的内容。 你可以在命令窗口看到，数据中有一些NA值（Not a Number, NA），这些是由于我们的原始数据每组长度不一样，有空白所致。

接下来，我们要做的就是重新整理数据的格式，把所有的数值放在一列，把分组信息放在另外一列；简单地，我们使用R中的reshape2包来做这件事情。

``` r
library(reshape2); #载入包, 如果以前没装过，需要install.packages("reshape2")命令安装。
mynew=melt(mydat); #重新整理数据，把数值放在一列(value)，分组信息在另外一列（variable）。
```

通过查看，可以知道，数值和分组信息分别放在变量mynew的value列和variable列中了。接下来，就可以进行方差分析了：

``` r
ret=aov(value~variable, data=mynew); #方差分析，分组信息是自变量，数值是因变量
summary(ret); #汇总输出结果
```

    ##             Df Sum Sq Mean Sq F value Pr(>F)
    ## variable     3  49212   16404   2.166  0.121
    ## Residuals   22 166622    7574               
    ## 6 observations deleted due to missingness

可以看到p值（Pr）很大，方差分析没有统计学意义。显示数据不存在组间差异。

其实，通过简单地画一下数据图，也可以看出来大致的趋势。我们有必要通过图的直观性反过来验证一下统计计算的可靠性，避免出现一些低级错误。

<img src="README_files/figure-markdown_github/unnamed-chunk-5-1.png" title="" alt="" style="display: block; margin: auto;" />

当方差分析有意义时，可以进一步在各组间多重比较：

``` r
pairwise.t.test(mynew$value, mynew$variable)  #各组多重比较
```

    ## 
    ##  Pairwise comparisons using t tests with pooled SD 
    ## 
    ## data:  mynew$value and mynew$variable 
    ## 
    ##    V1   V2   V3  
    ## V2 0.59 -    -   
    ## V3 1.00 0.95 -   
    ## V4 0.18 1.00 0.39
    ## 
    ## P value adjustment method: holm

可以看到各组比较的结果，pairwise默认采用Holm方法，这个是改进的Bonferroni法。比较合适于多数情况。

当各组数据数目大致相等时，也可以选用国内用的比较多的Tukey法，可以在命令行中输入TukeyHSD(ret)来计算p值，异其中ret是方差分析的返回值，计算的结果与以上方法比较是相近的。

R语言计算方差分析，看似繁琐，但区区只有几行语句，就可实现。并且可以写成脚本，便于批量处理数据。R语言中，稍微改变参数就可以进行多因素方差分析等计算，非常方便。

关于R的入门语法，有一份翻译成中文的官方文档可以[下载](http://cran.r-project.org/doc/contrib/Ding-R-intro_cn.pdf)<sup>[2]</sup>。关于在R中进行各种统计分析，可以参考薛毅编写的《统计建模与R软件》<sup>[1]</sup>，此书非常全面。

在学习R语言的过程中，如有疑问可以在[**统计之都**(cos.name)](http://cos.name)上搜索或发帖求助。这个网站的常驻人群主要是统计学专业的研究生或博士。（该网站在我们这里有时会打不开，需要反复刷新，但在电信网却正常。）

参考文献
--------

1.  薛毅，陈立萍，《统计建模与R软件》，清华大学出版社。
2.  R Development Core Team, <http://cran.r-project.org/doc/contrib/Ding-R-intro_cn.pdf>
