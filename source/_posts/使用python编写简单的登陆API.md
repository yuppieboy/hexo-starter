---
title: 使用python编写简单的登陆API
date: 2016-11-14 15:26:13
categories: Python
tags: API
---

## 框架
`Flask`是一个使用 `Python` 编写的轻量级 Web 应用框架。其 `WSGI` 工具箱采用 `Werkzeug` ，模板引擎则使用 `Jinja2` 。

<div align="middle">
<img src="https://raw.githubusercontent.com/yuppieboy/yuppieboy.github.io/master/images/flask_logo.png" width = "50%" >
</div>

## 思路
利用Flask,通过和数据库校验用户名密码来判断是否可以登陆成功。

## 实现

    #!/usr/bin/python
    # -*- coding: utf-8 -*-

    from flask import Flask, request, render_template, Response
    import MySQLdb
    import json

    app = Flask(__name__)

    @app.route('/login', methods=['GET', 'POST'])

    def login():
        if request.method == 'POST':
            if valid_login(request.form['username'],
                           request.form['password']):
                return json.dumps({'status': 1, 'data': 'login success'})
            else:
                error = 'Invalid username/password'
                return error
        else:
            return 'Not Support Get'

    def valid_login(username,password):

        conn = MySQLdb.connect(
            host='localhost',
            port=3306,
            user='root',
            # passwd='',
            db='Test'
        )

        cur = conn.cursor()
        resp = []

        try:
            sql = "select * from user where name = '%s' and password = '%s'" % (username, password)
            cur.execute(sql)
            resp = cur.fetchall()
            conn.commit()

        except Exception as e:
            print e
            conn.rollback()

        cur.close()
        conn.close()

        return len(resp)

    if __name__ == '__main__':
        app.run(host="127.0.0.1", port=8080, debug=True)



## 部署
`pycharm`运行xxx.py即可

    * Running on http://127.0.0.1:8080/ (Press CTRL+C to quit)
    * Restarting with stat
    * Debugger is active!
    * Debugger pin code: 273-703-379

Mac默认开启apache服务占用了80端口，所以这边暂时使用8080作为测试端口在本地测试。

## 小结
用Python写Restful风格的webAPI其实蛮方便，也容易部署，小项目容易上手，本文所写API只为好玩，真实项目要考虑更多的东西，比如加密等等。
