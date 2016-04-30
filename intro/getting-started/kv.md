## 键/值 数据

除了提供服务发现和综合健康检查,高提供了一个易于使用的键/值存储。 这可以用来保存动态配置,协助服务协调,建立领导人选举,使其他开发人员可以构建。

这个步骤假设您至少有一个领事代理已经运行。

### 简单的使用

演示如何简单的开始,我们将操作几个键K / V商店。

查询当地的代理我们开始在运行代理步骤中,我们可以先确认没有现有的键k / v商店:

    $ curl -v http://localhost:8500/v1/kv/?recurse
    * About to connect() to localhost port 8500 (#0)
    *   Trying 127.0.0.1... connected
    > GET /v1/kv/?recurse HTTP/1.1
    > User-Agent: curl/7.22.0 (x86_64-pc-linux-gnu) libcurl/7.22.0 OpenSSL/1.0.1 zlib/1.2.3.4 libidn/1.23 librtmp/2.3
    > Host: localhost:8500
    > Accept: */*
    >
    < HTTP/1.1 404 Not Found
    < X-Consul-Index: 1
    < Date: Fri, 11 Apr 2014 02:10:28 GMT
    < Content-Length: 0
    < Content-Type: text/plain; charset=utf-8
    <
    * Connection #0 to host localhost left intact
    * Closing connection #0

因为没有钥匙,我们得到一个404响应。 现在,我们可以放一些示例键:

    $ curl -X PUT -d 'test' http://localhost:8500/v1/kv/web/key1
    true
    $ curl -X PUT -d 'test' http://localhost:8500/v1/kv/web/key2?flags=42
    true
    $ curl -X PUT -d 'test'  http://localhost:8500/v1/kv/web/sub/key3
    true
    $ curl http://localhost:8500/v1/kv/?recurse
    [{"CreateIndex":97,"ModifyIndex":97,"Key":"web/key1","Flags":0,"Value":"dGVzdA=="},
     {"CreateIndex":98,"ModifyIndex":98,"Key":"web/key2","Flags":42,"Value":"dGVzdA=="},
     {"CreateIndex":99,"ModifyIndex":99,"Key":"web/sub/key3","Flags":0,"Value":"dGVzdA=="}]

 这里我们创建了3键,每个都有“测试”的价值。 注意,返回值字段是base64编码允许non-UTF8字符。 对于关键的“web / key2”,我们设置一个标志42的价值。所有的钥匙都支持设置一个64位整数标志值。 这不是由领事在内部使用,但它可以使用任何KV客户添加有意义的元数据。

后设置的值,然后发出一个GET请求来检索多个键使用吗? 递归参数。

你也可以拿一个键很容易:   

    $ curl http://localhost:8500/v1/kv/web/key1
    [{"CreateIndex":97,"ModifyIndex":97,"Key":"web/key1","Flags":0,"Value":"dGVzdA=="}]

删除键也很简单,通过使用删除动词。 我们可以删除一个密钥通过指定完整路径,或者我们可以递归地删除所有的钥匙在一个根使用“?递归”:

    $ curl -X DELETE http://localhost:8500/v1/kv/web/sub?recurse
    $ curl http://localhost:8500/v1/kv/web?recurse
    [{"CreateIndex":97,"ModifyIndex":97,"Key":"web/key1","Flags":0,"Value":"dGVzdA=="},
     {"CreateIndex":98,"ModifyIndex":98,"Key":"web/key2","Flags":42,"Value":"dGVzdA=="}]

一把钥匙可以修改发出PUT请求相同的URI和提供一个不同的消息体。 此外,高提供了Check-And-Set操作,使原子键更新。 这是通过提供吗? 中科院最后ModifyIndex =参数值的GET请求。 例如,假设我们想更新“web / key1”:

    $ curl -X PUT -d 'newval' http://localhost:8500/v1/kv/web/key1?cas=97
    true
    $ curl -X PUT -d 'newval' http://localhost:8500/v1/kv/web/key1?cas=97
    false

在这种情况下,第一个CAS更新成功,因为ModifyIndex是97。 然而第二操作失败,因为ModifyIndex不再是97人。

我们也可以利用ModifyIndex等关键价值的改变。 例如,假设我们想等待key2要修改的:

    $ curl "http://localhost:8500/v1/kv/web/key2?index=101&wait=5s"
    [{"CreateIndex":98,"ModifyIndex":101,"Key":"web/key2","Flags":42,"Value":"dGVzdA=="}]

通过提供”? 指数= ",我们要求等到ModifyIndex大于101的关键。 然而,”? 等待= 5 s”参数限制查询最多5秒,返回当前,不变的价值。 这可以用于有效地等待键修改。 此外,这种技术可以用来等待键的列表,等待直到任何键有一个更新的修改时间。

### 下一步
 这些只是几个例子的API支持。 完整的说明,请参见/ kv / HTTP API的路线。

 接下来,我们将看看领事支持的web UI选项。 
