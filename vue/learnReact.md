![reactLogo](../images/reactLogo.png)

# 前言

------

​       最近想学习一下React。所以这个文章将会以问答的形式，呈现我在学习react中自己的疑惑，并尽力思考解决我自己提出的问题。

### Q1：React中通过`map`循环元素，那么在下面代码中我可以直接把`map`换成`forEach`吗？

> ```react
> 
> import React, { PureComponent as Component } from 'react'
> function NumberList(props) {
>     const numbers = props.numbers
> // return (<ul>{numbers.forEach(num => <li>{num}</li> )}</ul>)  
>     return (<ul>{numbers.map(num=> <li>{num}</li> )}</ul>)
> }
> export default class Test extends Component {
>     constructor(props) {super(props)}
>     render() {
>         return (
>             <div>
>                 <NumberList numbers={[1, 2, 3, 5]} />
>             </div>
>         )
>     }
> }
> ```
>
> 

答案是**不可以**！如果我们把`map`直接替换成`forEach`在界面上是看不到预期的循环效果的。这是因为`map`在循环的时候最后会返回一个新数组，而`forEach`没有返回。所以就导致改成`forEach`的时候在界面上看不到效果，但实际上是循环了的。那么我们如果一定要用`forEach`的话，我们就需要个其他变量来存储循环结果。代码如下：

```js
function NumberList(props) {
    const numbers = props.numbers
    const arr = []
    numbers.forEach(num => {
     arr.push(<li>{num}</li>)   
    })
    return (<ul>{arr}</ul>)   
}
```

------

### Q2:为什么不能在自定义组件上直接绑定`onClick`事件？



