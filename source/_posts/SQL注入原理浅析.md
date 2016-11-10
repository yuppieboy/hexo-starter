---
title: SQL注入原理浅析
date: 2016-11-10 12:39:05
categories : mysql
tags: sql
---
## 原理
SQL注入攻击指的是通过构建特殊的输入作为参数传入Web应用程序，而这些输入大都是SQL语法里的一些组合，通过执行SQL语句进而执行攻击者所要的操作，其主要原因是程序没有细致地过滤用户输入的数据，致使非法数据侵入系统。

根据相关技术原理，SQL注入可以分为平台层注入和代码层注入。前者由不安全的数据库配置或数据库平台的漏洞所致；后者主要是由于程序员对输入未进行细致地过滤，从而执行了非法的数据查询。基于此，SQL注入的产生原因通常表现在以下几方面：①不当的类型处理；②不安全的数据库配置；③不合理的查询集处理；④不当的错误处理；⑤转义字符处理不合适；⑥多个提交处理不当。

## 攻击
API在编写过程中没有过滤用户输入的非法字符串，很容易造成sql注入，以mysql-python为例：


    conn = MySQLdb.connect(
      host='localhost',
      port=3306,
      user='root',
      # passwd='',
      db='Test'
    )

    cur = conn.cursor()

    try:
      name = "1 or 1 = 1"
      password = "1 or 1 = 1"
      sql = 'select * from user where name = %s and password = %s'%(name,password)
      cur.execute(sql)
      print cur.fetchall()
      conn.commit()

    except Exception as e:
      print e
      conn.rollback()

    cur.close()
    conn.close()


result：

    (('Tom', '0000', '\xe5\xa5\xb3'), ('Alen', '7875', '\xe7\x94\xb7'), ('Jack', '7455', '\xe7\x94\xb7'))

很明显使用简单的``"1 or 1 = 1"``就欺骗了数据库获取了所以user表内容。

## 防护
归纳一下，主要有以下几点：

1.永远不要信任用户的输入。对用户的输入进行校验，可以通过正则表达式，或限制长度；对单引号和
双"-"进行转换等。

2.永远不要使用动态拼装sql，可以使用参数化的sql或者直接使用存储过程进行数据查询存取。

3.永远不要使用管理员权限的数据库连接，为每个应用使用单独的权限有限的数据库连接。

4.不要把机密信息直接存放，加密或者hash掉密码和敏感的信息。

5.应用的异常信息应该给出尽可能少的提示，最好使用自定义的错误信息对原始错误信息进行包装

6.sql注入的检测方法一般采取辅助软件或网站平台来检测，软件一般采用sql注入检测工具jsky，网站平台就有亿思网站安全平台检测工具。MDCSOFT SCAN等。采用MDCSOFT-IPS可以有效的防御SQL注入，XSS攻击等。

## 小结
SQL注入的手法相当灵活，在注入的时候会碰到很多意外的情况。能不能根据具体情况进行分析，构造巧妙的SQL语句，从而成功获取想要的数据，是高手与“菜鸟”的根本区别。😊
