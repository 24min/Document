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

**解：**答案是**不可以**！如果我们把`map`直接替换成`forEach`在界面上是看不到预期的循环效果的。这是因为`map`在循环的时候最后会返回一个新数组，而`forEach`没有返回。所以就导致改成`forEach`的时候在界面上看不到效果，但实际上是循环了的。那么我们如果一定要用`forEach`的话，我们就需要个其他变量来存储循环结果。代码如下：

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

**解：**自定义组件并不是一个真实的DOM元素。它不存在`onClick`事件，React只会认为这是`onClick`属性。

------

### Q3：通过`class`类定义的组件中构造器（constructor）函数中的`super()`起什么作用？

> ```js
> export default class Test extends Component {
>     constructor(props) {super(props)}
>     componentWillMount() { }
>     componentDidMount() { }
>     render() {return (<div>111</div>)}
> }
> ```
>
> 

**解：**这个知识点是关于es6的对象的继承。([存疑？](https://github.com/ruanyf/es6tutorial/issues/959))子类必须在`constructor`方法中调用`super`方法，否则新建实例时会报错。这是因为子类自己的`this`对象，必须先通过父类的构造函数完成塑造，得到与父类同样的实例属性和方法，然后再对其进行加工，加上子类自己的实例属性和方法。如果不调用`super`方法，子类就得不到`this`对象。比如下面的代码：

```js
class father{}
class son extends father{}
let new_son = new son() 
```

