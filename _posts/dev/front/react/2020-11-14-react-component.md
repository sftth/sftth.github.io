---
title: "React - component"
excerpt: React component 만들기  
date: 2020-11-14 04:00:00 +0800
last_modified_at: 2020-11-14 04:00:00 +0800
header:
  teaser: /assets/images/01_teaser/home.jpg
  overlay_image: /assets/images/01_teaser/home.jpg
  overlay_filter: 0.5
  og_image: /assets/images/01_teaser/home.jpg
  caption: "Photo credit: [**Flickr***](https://live.staticflickr.com)"
categories: react
---

## 개요

기존 HTML의 한계를 개선하기 위해 React로 전환 해보면서 React의 component를
이해하기 

## 기존 HTML 페이지

```html
<html>
    <body>
        <header>
            <h1>WEB</h1>
            world wide web!
        </header>
        <nav>
            <ul>
                <li><a href="1.html">HTML</a></li>
                <li><a href="2.html">CSS</a></li>
                <li><a href="3.html">JS</a></li>
            </ul>
        </nav>
        <article>
            <h2>HTML</h2>
            HTML is HyperText Markup Language.
        </article>
    </body>
</html>
```

## React 페이지
기존 HTML 페이지 하나에 구현된 태그들을 각각 subject,toc, content js 파일로
나누고 import하여 구현 

<img src="/assets/images/react/react-component01.png" width="600" height="584" alt="npm-installation">

<img src="/assets/images/react/react-component02.png" width="600" height="584" alt="npm-installation">

### App.js

```js 
import React, {Component} from 'react';
import './App.css';
import Toc from "./components/Toc";
import Subject from "./components/Subject";
import Content from "./components/Content";


class App extends Component {
  constructor(props){
    super(props);
    this.state = {
      subject:{title:'WEB', sub:'World Wild Web!!!!!'},
      contents:[
        {id:1, title:'HTML', desc:'HTML is HyperText Markup Lanugage'},
        {id:2, title:'CSS', desc:'CSS is CSS is for design'},
        {id:3, title:'JS', desc:'JS is for interactive'},
      ]
    }
  }
  render() {
    return (
      <div className="App">
        <Subject title={this.state.subject.title} sub={this.state.subject.sub}></Subject>
        <Toc data={this.state.contents}></Toc>
        <Content title="HTML" desc="HTML is HyperText Markup Language."></Content>
      </div>
    );
  }
}

export default App;
```

### Subject.js

```js
import React, {Component} from 'react';

class Subject extends Component {
    render() {
        return (
            <header>
                <h1>{this.props.title}</h1>
                {this.props.sub}
            </header>
        );
    }
}

export default Subject;
```

### Toc.js

```js 
import React, {Component} from 'react';

class Toc extends Component {
    render() {
        var lists = [];
        var data = this.props.data;
        var i = 0;
        while(i < data.length){
            lists.push(<li key={data[i].id}><a href={data[i].id+".html"}>{data[i].title}</a></li>)
            i++;
        }
      return (
        <nav>
            <ul>
                {lists}
            </ul>
        </nav>

      )
    }
  }

  export default Toc;
```

### Content.js

```js 
import React, {Component} from 'react';

class Content extends Component {
    render() {
      return (
        <article>
          <h2>{this.props.title}</h2>
          {this.props.desc}
        </article>
      )
    }
  }

  export default Content;
```