![](../images/微信图片_20200109212419.jpg)

### 1.一个数组对象，根据某个属性进行去重

##### 源数据

```js
const array = [
    {
    name:'array',
    age:13
    },
    {
    name:'array',
    age:12
    },
    {
    name:'obui',
    age:12
    },
    {
    name:'obssssui',
    age:12
    },
]
```

##### 代码函数

```js
function deduplication(source, props) {
    let reducedData = source.reduce((prev,cur,index)=>{
        let obj = {}
        const tmp = cur[props]
        obj[tmp] = cur
        return {
            ...prev,
            ...obj
        }
    }
    , {})
    return Object.values(reducedData)
}
```

##### 解

维护一个以props的值为key的对象，实现去重。所以后面的值会覆盖掉前面的值。

### 2.暂无