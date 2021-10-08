# react-notes

# 属性展开  ...传值运用  APP1 和 APP2等价
function App1() {
  return <Greeting firstName="Ben" lastName="Hector" />;
}

function App2() {
  const props = {firstName: 'Ben', lastName: 'Hector'};
  return <Greeting {...props} />;
}

# es6
  setTheArray(oldArray => [...oldArray, newElement]);
  array push 更新赋值操作
# node
如果没有 json-parser，body 属性将是undefined的。 Json-parser 的功能是获取请求的 JSON 数据，将其转换为 JavaScript 对象，然后在调用路由处理程序之前将其附加到请求对象的 body 属性。

