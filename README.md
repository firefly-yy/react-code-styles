# react 代码编写规范建议
https://alexkondov.com/tao-of-react/  
1.使用函数组件替代类组件（语法更简洁、更好的统一管理组件的生命周期及状态、同样的逻辑实现代码量更少同时可读性更好、有大量的钩子对数据的管理存储更友好）
```
// 👎 Class components are verbose
class Counter extends React.Component {
  state = {
    counter: 0,
  }

  constructor(props) {
    super(props)
    this.handleClick = this.handleClick.bind(this)
  }

  handleClick() {
    this.setState({ counter: this.state.counter + 1 })
  }

  render() {
    return (
      <div>
        <p>counter: {this.state.counter}</p>
        <button onClick={this.handleClick}>Increment</button>
      </div>
    )
  }
}

// 👍 Functional components are easier to read and maintain
function Counter() {
  const [counter, setCounter] = useState(0)

  handleClick = () => setCounter(counter + 1)

  return (
    <div>
      <p>counter: {counter}</p>
      <button onClick={handleClick}>Increment</button>
    </div>
  )
}
```

1.尽量用纯函数，函数定义在function组件外面，通过传值的形式进行数据处理
2.组件间传参时尽量进行参数解构
3.控制是否渲染组件使用三元组判断，尽量不要用短路运算符&&（有出错的可能）
4.
