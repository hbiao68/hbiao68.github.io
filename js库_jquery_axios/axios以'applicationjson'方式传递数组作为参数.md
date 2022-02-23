@[toc]

# 一、问题描述
前后端分离，跟同事联调接口，后台定义的接口是`application/json`方式请求，参数为数组，却没有key，如图所示



![1645433157612](.\axios以'applicationjson'方式传递数组作为参数\1645433157612.png)



我第一反应是如果要传递JSON对象，至少要告诉我 JSON的 key 值是什么吧，不然后台怎么接收我的参数呢？前端怎么传递JSON呢？毕竟数组也不是JSON对象（**后面才知道这句话是错的**）。




# 二、解决过程

## 2.1 尝试着用后台提供的swagger发送请求，看看是否成功？





![1645433101180](.\axios以'applicationjson'方式传递数组作为参数\1645433101180.png)



## 2.2 尝试着用postman 发送请求，看看如何传递参数？



![1645432983280](.\axios以'applicationjson'方式传递数组作为参数\1645432983280.png)




# 三、使用axios 传递参数

因为前面使用 `postman` 和 `swagger` 请求都没有问题，说明后台逻辑没有问题，理论上 ajax 也是能够发送的数据给后台的，只是自己不知道



## 3.1 参考[axios gihub](https://github.com/axios/axios) 中的案例

![1645433646896](.\axios以'applicationjson'方式传递数组作为参数\1645433646896.png)



## 3.2 给出案例的的答案

1. 在Vue文件中调用 服务

```js
// 删除模态框
delMessageDialog (msgObj) {
    this.$confirm('此操作将删除模板, 是否继续?', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        onConfirm: () => {
            const params = [msgObj.id]
            API.delMsgTemplateService(params).then(res => {
                if (res.msg === 'success') {
                    this.$message({
                        type: 'success',
                        message: '删除模板成功!'
                    })
                    this.getTableData()
                }
            })
        },
        onCancel: () => {
            // this.$message({
            //   type: 'success',
            //   message: '已取消删除!'
            // })
        }
    })
},
```



2. 在axios 中定义传递 数组 的方式

```js
// 删除模板
function delMsgTemplateService (params) {
  const userParam = params || []
  // http 为 axios 实例（对象）
  return http({
    headers: {
      'content-type': 'application/json'
    },
    method: 'post',
    url: `/${ctx}/api/msg/msgTemplate/delete`,
    // 将数组转为字符串
    data: JSON.stringify(userParam)
  })
}
```






