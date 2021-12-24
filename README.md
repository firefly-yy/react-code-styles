# react 代码编写规范建议
https://alexkondov.com/tao-of-react/  
### 1.使用函数组件替代类组件（语法更简洁、更好的统一管理组件的生命周期及状态、同样的逻辑实现代码量更少同时可读性更好、有大量的钩子对数据的管理存储更友好）
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
### 2.尽量用纯函数，函数定义在function组件外面，通过传值的形式进行数据处理
```
// 👎 Helper functions shouldn't read from the component's state
export default function Component() {
  const [value, setValue] = useState('')

  function isValid() {
    // ...
  }

  return (
    <>
      <input
        value={value}
        onChange={e => setValue(e.target.value)}
        onBlur={validateInput}
      />
      <button
        onClick={() => {
          if (isValid) {
            // ...
          }
        }}
      >
        Submit
      </button>
    </>
  )
}

// 👍 Extract them and pass only the values they need
function isValid(value) {
  // ...
}

export default function Component() {
  const [value, setValue] = useState('')

  return (
    <>
      <input
        value={value}
        onChange={e => setValue(e.target.value)}
        onBlur={validateInput}
      />
      <button
        onClick={() => {
          if (isValid(value)) {
            // ...
          }
        }}
      >
        Submit
      </button>
    </>
  )
}
```
### 3.不要硬编码，使用数组循环遍历元素
```
// 👎 Hardcoded markup is harder to manage.
function Filters({ onFilterClick }) {
  return (
    <>
      <p>Book Genres</p>
      <ul>
        <li>
          <div onClick={() => onFilterClick('fiction')}>Fiction</div>
        </li>
        <li>
          <div onClick={() => onFilterClick('classics')}>
            Classics
          </div>
        </li>
        <li>
          <div onClick={() => onFilterClick('fantasy')}>Fantasy</div>
        </li>
        <li>
          <div onClick={() => onFilterClick('romance')}>Romance</div>
        </li>
      </ul>
    </>
  )
}

// 👍 Use loops and configuration objects
const GENRES = [
  {
    identifier: 'fiction',
    name: Fiction,
  },
  {
    identifier: 'classics',
    name: Classics,
  },
  {
    identifier: 'fantasy',
    name: Fantasy,
  },
  {
    identifier: 'romance',
    name: Romance,
  },
]

function Filters({ onFilterClick }) {
  return (
    <>
      <p>Book Genres</p>
      <ul>
        {GENRES.map(genre => (
          <li>
            <div onClick={() => onFilterClick(genre.identifier)}>
              {genre.name}
            </div>
          </li>
        ))}
      </ul>
    </>
  )
}
```
### 4.在JSX中写必要的注释，增加代码可读性
```
function Component(props) {
  return (
    <>
      {/* If the user is subscribed we don't want to show them any ads */}
      {user.subscribed ? null : <SubscriptionPlans />}
    </>
  )
}
```
### 5.组件间传参时尽量进行对象参数解构
```
// 👎 Don't repeat props everywhere in your component
function Input(props) {
  return <input value={props.value} onChange={props.onChange} />
}

// 👍 Destructure and use the values directly
function Component({ value, onChange }) {
  const [state, setState] = useState('')

  return <div>...</div>
}
```
### 6.传递对象，尽量避免传递元数据（如果后期对象增key，无需多处调整，减少维护成本）
```
// 👎 Don't pass values on by one if they're related
<UserProfile
  bio={user.bio}
  name={user.name}
  email={user.email}
  subscription={user.subscription}
/>

// 👍 Use an object that holds all of them instead
<UserProfile user={user} />
```
### 7.控制是否渲染组件使用三元组判断，尽量不要用短路运算符&&（条件不成立是有没有必要的渲染）
```
// 👎 Try to avoid short-circuit operators
function Component() {
  const count = 0

  return <div>{count && <h1>Messages: {count}</h1>}</div>
}

// 👍 Use a ternary instead
function Component() {
  const count = 0

  return <div>{count ? <h1>Messages: {count}</h1> : null}</div>
}
```
### 8.避免嵌套三元运算符（可读性极差）
```
// 👎 Nested ternaries are hard to read in JSX
isSubscribed ? (
  <ArticleRecommendations />
) : isRegistered ? (
  <SubscribeCallToAction />
) : (
  <RegisterCallToAction />
)

// 👍 Place them inside a component on their own
function CallToActionWidget({ subscribed, registered }) {
  if (subscribed) {
    return <ArticleRecommendations />
  }

  if (registered) {
    return <SubscribeCallToAction />
  }

  return <RegisterCallToAction />
}

function Component() {
  return (
    <CallToActionWidget
      subscribed={subscribed}
      registered={registered}
    />
  )
}
```

### 9. 大量数据映射的时候将数据抽出成一个组件的形式，不要在div中map元素，这样可读性较差
```
// 👎 Don't write loops together with the rest of the markup
function Component({ topic, page, articles, onNextPage }) {
  return (
    <div>
      <h1>{topic}</h1>
      {articles.map(article => (
        <div>
          <h3>{article.title}</h3>
          <p>{article.teaser}</p>
          <img src={article.image} />
        </div>
      ))}
      <div>You are on page {page}</div>
      <button onClick={onNextPage}>Next</button>
    </div>
  )
}

// 👍 Extract the list in its own component
function Component({ topic, page, articles, onNextPage }) {
  return (
    <div>
      <h1>{topic}</h1>
      <ArticlesList articles={articles} />
      <div>You are on page {page}</div>
      <button onClick={onNextPage}>Next</button>
    </div>
  )
}
```

### 10.组件传参进行对象解构时，对解构的key分配默认的对象值，减少组件间的跳转，便于直接阅读
```
// 👎 Don't define the default props outside of the function
function Component({ title, tags, subscribed }) {
  return <div>...</div>
}

Component.defaultProps = {
  title: '',
  tags: [],
  subscribed: false,
}

// 👍 Place them in the arguments list
function Component({ title = '', tags = [], subscribed = false }) {
  return <div>...</div>
}
```

### 11.避免嵌套定义及渲染组件，提高组件函数的可读性
```
// 👎 Don't write nested render functions
function Component() {
  function renderHeader() {
    return <header>...</header>
  }
  return <div>{renderHeader()}</div>
}

// 👍 Extract it in its own component
import Header from '@modules/common/components/Header'

function Component() {
  return (
    <div>
      <Header />
    </div>
  )
}
```

### 12.文件目录解构
```
// 👎 Don't group by technical details
├── containers
|   ├── Dashboard.jsx
|   ├── Details.jsx
├── components
|   ├── Table.jsx
|   ├── Form.jsx
|   ├── Button.jsx
|   ├── Input.jsx
|   ├── Sidebar.jsx
|   ├── ItemCard.jsx

// 👍 Group by module/domain
├── modules
|   ├── common
|   |   ├── components
|   |   |   ├── Button.jsx
|   |   |   ├── Input.jsx
|   ├── dashboard
|   |   ├── components
|   |   |   ├── Table.jsx
|   |   |   ├── Sidebar.jsx
|   ├── details
|   |   ├── components
|   |   |   ├── Form.jsx
|   |   |   ├── ItemCard.jsx
```

### 13.使用绝对路径 
```
// 👎 Don't use relative paths
import Input from '../../../modules/common/components/Input'

// 👍 Absolute ones don't change
import Input from 'modules/common/components/Input'
```
(使用方法： 在项目的根目录下创建jsconfig.json文件)
  ```
  {
  "compilerOptions": {
    "baseUrl": "src"
  },
  "include": ["src"]
}
  ```

### 14.包装额外的组件库
```
// 👎 Don't import directly
import { Button } from 'semantic-ui-react'
import DatePicker from 'react-datepicker'

// 👍 Export the component and use it referencing your internal module
import { Button, DatePicker } from 'modules/common/components'
```
