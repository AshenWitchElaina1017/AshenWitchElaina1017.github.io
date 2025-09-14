---
title: React
date: 2025-09-12
summary: React 是用于构建用户界面的库，组件化与声明式让前端开发更高效。
image: https://picgo-elaina.oss-cn-chengdu.aliyuncs.com/typora/202507280817461.jpg
---


# react18速成
## 核心语法
### 1.创建项目
```bash
pnpm create vite
pnpm i
http://localhost:3000/ 查看项目
```
### 2. JSX
将html与JavaScript结合
1. 换行是需要使用()，单行直接return
2. JSX只能返回一个根元素，多个根元素需要使用一个容器包裹，如:`<></>`或者`<Fragment></Fragment>`，或者使用`<div></div>`
### 3. 插值
- 使用{}进行插值
- 可以使用表达式，如：`{1+1}`，`{true ? 'yes' : 'no'}`，`{Math.random()}`，`{arr.map(item => <div>{item}</div>)}`
1. 条件渲染
```jsx
export default function App() {
  let flag=true
  let context=null
  if(flag){
    context=<h1>Flag为true</h1>
  }else{
    context=<h1>Flag为false</h1>
  }
  return (
    <div>
      {context}
    </div>
  )
}
```
2. 列表渲染
```jsx
import { Fragment } from "react"
export default function App() {
  let arr=[{id:1,name:"伊蕾娜"},{id:2,name:"爱莉希雅"},{id:3,name:"两仪式"}]
  let list = arr.map(item =>(
    <Fragment key={item.id}>
      <li>{item.name}</li>
      {item.id<3&&<hr />}
    </Fragment>
  ))
  return (
    <ul>
      {list}
    </ul>
  )
}
```
### 4. 事件操作
属性以驼峰命名为形式
```jsx
function handleClick(e){
  alert('点击了')
  console.log(e)
}
export default function App() {
  return (
    <>
      <button onClick={handleClick}>按钮</button>
    </>
  )
}
```
### useState状态处理
1. 基础用法
```jsx
import { useState } from "react"
export default function App() {
  const [content,setContent]=useState("Old Content")
  function handleClick(){
    setContent("New Content")
  }
  return (
    <>
      <div>{content}</div>
      <button onClick={handleClick}>按钮</button>
    </>
  )
}
```
2. 对象用法
```jsx
import { useState } from "react"
export default function App() {
  const [data,setData]=useState({title:"Elaina",content:"1017"})
  function handleClick(){
    setData({...data,content:"0831"})
  }
  return (
    <>
      <div>{data.title} <br />{data.content} </div>
      <button onClick={handleClick}>按钮</button>
    </>
  )
}
```
3. 数组用法
```jsx
import { useState } from "react"
export default function App() {
  const [data,setData]=useState([{id:1,name:"伊蕾娜"},{id:2,name:"爱莉希雅"},{id:3,name:"两仪式"}])
  const listData=data.map(item=>{
    return <li key={item.id}>{item.name}</li>
  })
  function handleClick(){
    setData([{id:0,name:"艾米莉亚"},...data,{id:4,name:"莉莉丝"}])
  }
  function handleDeleteClick(){
    setData(data.filter(item=>item.id%2==0))
  }
  return (
    <>
      <ul>{listData}</ul>
      <button onClick={handleClick}>按钮</button>
      <button onClick={handleDeleteClick}>删除</button>
    </>
  )
}
```
## 组件通信与插槽
### 1. DOM组件的类属性设置
- 在React中使用className而不是class；内联样式使用对象（驼峰命名）。
- 动态类名常用模板字符串或条件运算。
```jsx
import Img from './assets/react.svg'
export default function App() {
  const ImgStyle={
        width: 200,
        height: '200px',
        backgroundColor: 'grey',
      }
  return (
    <>
      <img 
      src={Img}
      alt="" 
      className="small"
      style={ImgStyle}
      />
    </>
  )
}
```
```jsx
// 展开语法写法
import Img from './assets/react.svg'
export default function App() {
  const ImgData={
    className:"small",
    style:{
        width: 200,
        height: '200px',
        backgroundColor: 'grey',
      }
  }
  return (
    <>
      <img 
      src={Img}
      alt="" 
      {...ImgData}
      />
    </>
  )
}
```
### 2. React组件的Props
**操作步骤：**
1. 请求功能所需的数据（例如文章信息）
2. 创建Article组件
3. 将文章的数据分别传递给Article组件
```jsx
// function Article(props){
//   return (
//     <>
//       <h1>{props.title}</h1>
//       <p>{props.content}</p>
//     </>
//   )
// }
function Article({title, content,active}){//解构
  return (
    <>
      <h1>{title}</h1>
      <p>{content}</p>
      {active && <p>激活状态</p>}
    </>
  )
}
export default function App() {
  return (
    <>
      <Article 
        title="Elaina"
        content="1017"
        active
      />
      <Article 
        title="Miku"
        content="0831"
      />
    </>
  )
}
```
### 3. 在React组件中展开props的场景
```jsx
function Detail({content,active}){
  return (
    <>
      <p>{content}</p>
      <p>激活状态:{active?"显示中":"已隐藏"}</p>
    </>
  )
}
function Article({title, detailData}){//解构
  return (
    <>
      <h1>{title}</h1>
      <Detail {...detailData}/>
    </>
  )
}
export default function App() {
  const articleData={
    title:"Elaina",
    detailData:{
      content:"1017",
      active:true
    }
  }
  return (
    <>
      <Article {...articleData}/>
    </>
  )
}
```
### 4. 将JSX作为props传递（组件插槽）
1. 默认插槽
```jsx
function List({children}){
  return (
    <ul>
      {children}
    </ul>
  )
}
export default function App() {
  return (
    <>
      <List>
        <li>1</li>
        <li>2</li>
        <li>3</li>
      </List>
    </>
  )
}
```
2. 命名插槽
```jsx
function List({children,title,footer=<h2>默认值</h2>}){
  return (
    <>
    <h2>{title}</h2>
      <ul>
        {children}
      </ul>
      <h2>{footer}</h2>
    </>
  )
}
export default function App() {
  return (
    <>
      <List
       title="列表1"
       footer={<p>这里是底部内容1</p>}
       >
        <li>Elaina</li>
        <li>1017</li>
      </List>
      <List
       title="列表2"
       footer={<p>这里是底部内容2</p>}
       >
        <li>Miku</li>
        <li>0831</li>
      </List>
      <List
       title="列表3"
       >
        <li>Elysia</li>
        <li>1111</li>
      </List>
    </>
  )
}
```
### 5. 子组件向父组件传递数据
```jsx
import { useState } from "react"

function Detail({onActive}){
  const [status,setStatus]=useState(false)
  function handleClick(){
    setStatus(!status)
    onActive(status)
  }
  return (
    <>
      <p style={{
        display:status?"block":"none"
      }}>Detail的内容</p>
      <p>status:{status?"true":"false"}</p>
      <button onClick={handleClick}>按钮</button>
    </>
  )
}
export default function App() {
  function handleActive(status){
    console.log(status)
  }
  return (
    <>
      <Detail onActive={handleActive}/>
    </>
  )
}
```
### 6. 使用context进行多级组件传值
```jsx
import { useContext } from "react";
import { createContext } from "react";
function Section({ children }) {
  const level=useContext(levelContext)
  return (
    <levelContext.Provider value={level+1}>
      {children}
    </levelContext.Provider>
  );
}
function Heading({children }) {
  const level=useContext(levelContext)
  switch (level) {
    case 1:
      return <h1>{children}</h1>;
    case 2:
      return <h2>{children}</h2>;
    case 3:
      return <h3>{children}</h3>;
    case 4:
      return <h4>{children}</h4>;
    case 5:
      return <h5>{children}</h5>;
    case 6:
      return <h6>{children}</h6>;
    default:
      throw Error('未知的 level：' + level);
  }
}
const levelContext= createContext(0)
export default function Page() {
  return (
     <Section>
      <Heading>主标题</Heading>
      <Section>
        <Heading>副标题</Heading>
        <Section>
          <Heading>子标题</Heading>
          <Section>
            <Heading>子子标题</Heading>
            <Section>
              <Heading>子子子标题</Heading>
              <Section>
                <Heading>子子子子标题</Heading>
              </Section>
            </Section>
          </Section>
        </Section>
      </Section>
    </Section>
  );
}
```
## React Hooks
### 1. reducer
**reducer用于统一管理状态的操作方式**
```jsx
import { useReducer } from "react"
function countReducer(state, action){
  switch (action.type) {
    case 'increment':
      return state + 1
    case 'decrement':
      return state - 1
    default:
      return state
  }
}
export default function App () {
  const [state,dispatch] = useReducer(countReducer, 0)
  const handleIncrement = () => dispatch({type: 'increment'})
  const handleDecrement = () => dispatch({type: 'decrement'})
  return (
    <div style={{ padding: 10 }}>
      <button onClick={handleIncrement}>+</button>
      <span> {state} </span>
      <button onClick={handleDecrement}>-</button>
    </div>
  )
}
```
### 2. ref
```jsx
import { useRef } from 'react'
import { useState } from 'react'

export default function App () {
  const [count, setCount] = useState(0)
  const prevCount= useRef()
  function handleClick() {
    prevCount.current = count
    setCount(count + 1)
  }
  return (
    <div>
      <p>最新的count: {count}</p>
      <p>上次的count: {prevCount.current}</p>
      <button onClick={handleClick}>增大count</button>
    </div>
  )
}
```
**获取焦点**
```jsx
import { useRef } from 'react'
export default function App () {
  const inputRef= useRef(null)
  function handleClick(){
    inputRef.current.focus()
  }
  return (
    <div>
      <input type="text" ref={inputRef} name="" id="" />
      <button onClick={handleClick}>增大count</button>
    </div>
  )
}

```
### 3. uselmperatvieHandle&forwardRef
```jsx
import { useRef,forwardRef,useImperativeHandle } from 'react'
const Child= forwardRef( function (props,ref){

  useImperativeHandle(ref,()=>({
    myFN:()=>{
      console.log('子组件方法')
    }
  }))

  return (
    <div>
      <h1>子组件</h1>
    </div>
  )
})
export default function App () {
  const childRef= useRef()
  function handleClick(){
    childRef.current.myFN()
  }
  return (
    <div>
      <Child ref={childRef}/>
      <button onClick={handleClick}>增大count</button>
    </div>
  )
}
```
### 4. useEffect
```jsx
import { useEffect } from "react"
import { useState } from "react"

export default function App () {
  // 计数器
  const [count, setCount] = useState(0)
  const handleIncrement = () => setCount(count + 1)
  const handleDecrement = () => setCount(count - 1)
  useEffect(()=>{
    console.log("count changes")
  },[count])
  return (
    <div style={{ padding: 10 }}>
      <button onClick={handleIncrement}>-</button>
      <span> {count} </span>
      <button onClick={handleDecrement}>+</button>
    </div>
  )
}
```
### 5. useMemo
```jsx
import { useMemo,useState } from "react";
function DoSomeMath({ value }) {
  const result= useMemo(()=>{
    console.log("DoSomeMath被调用了")
    let result = 0;
    for (let i = 0; i < 1000000; i++) {
      result += value * 2;
    }
    return result;
  },[value])
  return (
    <div>
      <p>输入内容: {value}</p>
      <p>经过复杂计算的数据: {result}</p>
    </div>
  );
}
export default function App() {
  const [inputValue, setInputValue] = useState(5);
  const [count, setCount] = useState(0);
  return (
    <div>
      <p>count的值为: {count}</p>
      <button
        onClick={() => setCount(count + 1)}
      >点击更新</button>
      <br />
      <br />
      <input
        type="number"
        value={inputValue}
        onChange={(e) => setInputValue(parseInt(e.target.value))}
      />
      <DoSomeMath value={inputValue} />
    </div>
  );
}
```
### 6. useCallBack
```jsx
import { useState,memo,useCallback } from 'react';
const Button=memo(function({ onClick }) {
  console.log('Button渲染了');
  return <button onClick={onClick}>子组件</button>;
})
export default function App() {
  const [count, setCount] = useState(0);
  const handleClick = useCallback(() => {
    console.log('点击按钮');
  },[])
  const handleUpdate = () => {
    setCount(count + 1);
  };
  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={handleUpdate}>点击</button>
      <br />
      <Button onClick={handleClick} />
    </div>
  );
}
```

## React TypeScript案例

### 项目搭建

```
npx create-next-app
```

