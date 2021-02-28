---
title: "React - event"
excerpt: React event 만들기  
date: 2020-11-14 05:00:00 +0800
last_modified_at: 2020-11-14 05:00:00 +0800
header:
  teaser: /assets/images/01_teaser/home.jpg
  overlay_image: /assets/images/01_teaser/home.jpg
  overlay_filter: 0.5
  og_image: /assets/images/01_teaser/home.jpg
  caption: "Photo credit: [**Flickr***](https://live.staticflickr.com)"
categories: react
---

## 개요

React 이벤트 이해하기

## 첫번째 이벤트 
- 우선 이해를 돕기 위해 컴포넌트가 아닌 직접 태그에 이벤트 작성함  
- React 이벤트를 통해 state mode 값을 동적으로 바꾸기

## this bind

- 함수 내에서 this를 자신임을 지정하기 위해서는 `.bind(this)`를 
하여 함수 외부의 this를 함수 내부의 첫번째 인자로 설정해 주어야 함 

```jsx  
class App extends Component {
  constructor(props){
    super(props);
    this.state = {
      mode:'read', //<------- 이벤트로 바꿀 값 
//...
  render() {
//...
    return (
      <div className="App">
        <header>
          <h1><a href="/" onClick={function (e){
            e.preventDefault()
            this.setState({
              mode: 'welcome'
            })
          }.bind(this)}> {this.state.subject.title} </a></h1>
          {this.state.subject.sub}
        </header>
//...
      </div>
    )
  }
}
```

## this. setState

- 함수 내에서 state mode를 바꾸기 위해서는 this.state.mode='welcome'이 아닌
약속된 형태로 해주어야 함
- 약속된 형태라는 것은 state 값이 변경 되었을 때 react가 알도록 알려주는 방법이어야 한다는 뜻.

```js
this.setState({
  mode: 'welcome'
})
```

## 컴포넌트 이벤트

### 컴포넌트로 변경 

아래 return 함수 내 <header> 이하를 컴포넌트로 변경하기 

변경 전 
```jsx
    return (
      <div className="App">
        <header>
          <h1><a href="/" onClick={function (e){
            e.preventDefault()
            this.setState({
              mode: 'welcome'
            })
          }.bind(this)}>{this.state.subject.title}</a></h1>
          {this.state.subject.sub}
        </header>
        <Toc data={this.state.contents}></Toc>
        <Content title={_title} desc={_desc}></Content>
      </div>
    )
```

변경 후

```jsx 
    return (
      <div className="App">
        <Subject title={this.state.subject.title} sub={this.state.subject.sub}></Subject>
        <Toc data={this.state.contents}></Toc>
        <Content title={_title} desc={_desc}></Content>
      </div>
    )
```

### 컴포넌트에 이벤트 설정 

