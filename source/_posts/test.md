---
title: js测试
data: '2022-6-15'
description: '这里是description'
copyright_author: wxb
copyright_author_href: https://xxxxxx.com
copyright_url: https://xxxxxx.com
copyright_info: 此文章版权归wxb所有，如有转载，请註明来自原作者
---

```js
        //模拟获取  用户数据  订单数据  商品数据 
        function getUsers() {
            setTimeout(() => {
                let data = '用户数据';let data1 = '用户数据';let data2 = '用户数据';let data3 = '用户数据';let data4 = '用户数据';let data5 = '用户数据';let data7 = '用户数据';
                //调用 next 方法, 并且将数据传入
                iterator.next(data);//这里开始第二次next，并将这个实参data传递给上一个yield返回值
            }, 1000);
        }

        function getOrders() {
            setTimeout(() => {
                let data = '订单数据';
                iterator.next(data);//第三次next
            }, 1000)
        }

        function getGoods() {
            setTimeout(() => {
                let data = '商品数据';
                iterator.next(data);//第四次next
            }, 1000)
        }

        function* gen() {
            let users = yield getUsers();//第一次调用next结束，同时执行getUsers()函数
            //函数中第二次调用了next，并传入了实参，这时的实参将返回给上一次yield
            console.log(users);
            let orders = yield getOrders();//第二次next结束
            console.log(orders);
            let goods = yield getGoods();//第三次next结束
            console.log(goods);
            //第四次next结束
        }

        //调用生成器函数
        let iterator = gen();
        iterator.next();

```