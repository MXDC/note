#### Promise

```javascript
  <script type="text/javascript">
        var p = new Promise(function (resolve, reject) {
            setInterval(() => {
                var flag = false;
                if (flag) {
                    resolve("hello");
                } else {
                    reject("出错了");
                }
            }, 1000)
        });
        p.then((resovle) => {
            console.log(resovle);
        }, (reject) => {
            console.log(reject);
        })
    </script>
```

* `Promise`是一个异步任务结果的容器，`new Promise()`接收一个`function`作为回调函数，以执行异步任务。
* `Promise`是异步任务的结果容器，异步任务的回调函数有两个**函数形参**`resolve`和`reject`分别用于处理异步任务的成功或失败结果。
* `Promise.then(function(){},function(){})`用于获取执行结果，其中第一个函数用于处理`resolve`状态的结果，第二个函数用户处理`rejected`状态的结果。

#### Axiso

1.form格式，将Content-Type类型设置为application/x-www-form-urlencode，POST请求时将data序列化,提交的数据会按照 key1=val1&key2=val2 的方式进行编码，key 和 val 都进行了 URL 转码

```json
 headers: {
      "Content-Type": "application/x-www-form-urlencoded; charset=utf-8;"
    }
```

2.json格式，有时候后台需要body传送的是json数据，将Content-Type类型设置为application/json，注意POST请求时data不要序列化

![](https://pq3kbw.ch.files.1drv.com/y4pVImJLLHaQ8JD4o1jP7Yc6mxfU-vvKh8p2_2ACC4j8bDt6UApX3pffY0su3509SA_3wHbtnvpVLu3se3FBZ8OuWGexflBicIwms6SqNd0hyKXuAuyoMoVGU1yltaOY1TsBNRW0vkT4277bJ_NJvCXtgoZrrb4Krc5plsnAjKmykscEWxWNFPiSXP0aCPfsrrjQoNrLpy627h9ya3XIG_0rBTQrt7XLL08I4Lkny1wGlk)

