---
title: 异步加载图片
categories:
- ECMAScript6
tags:
- 异步
- 加载资源
---

Promise 是异步编程的一种解决方案，比传统的解决方案——回调函数和事件——更合理和更强大。它由社区最早提出和实现，ES6 将其写进了语言标准，统一了用法，原生提供了`Promise`对象。

## ES6使用Promise对象异步加载图片

可以将图片等资源的加载写成一个`Promise`，一旦加载完成，`Promise`的状态就会发生变回。

```javascript
const preloadImage = function (path) {
  return new Promise(function (resolve, reject) {
    const image = new Image();
    image.onload  = function () {
      resolve(image);
    };
    image.onerror = function () {
      reject(new Error('不能加载URL为['+path+']的图片。'));
    };
    image.src = path;
  });
};
```

上面代码中，使用`Promise`包装了一个图片加载的异步操作。如果加载成功，就调用`resolve`方法，否则就调用`reject`方法。不需要提供`resolve`方法，该方法由ES6提供。

调用的时候代码如下：

```javascript
function somefunction(path) {
    preloadImage(path)
    .then((image)=>someNode.appendChild(image))
   	.catch((error)=>console.log('Error': + error))
}
```

成功加载图片后会调用`then`指定的回调函数，参数`image`由上一步的`resolve`函数提供。回调函数中可以使用**HTML DOM**方法将图片加载到页面上或者其他操作。`preloadImage`函数或者`then`方法提供的回调函数中有任何异常，都会被`catch`指定的回调函数捕获。

下面提供一个异步加载图片的完整示例：

```javascript
import React, { Component } from 'react';
import lugia from './static/images/lugia.jpg';
import './App.css';

class App extends Component {

    constructor(props){
        super(props);
        this.image = undefined;
    }


    preloadImage = function (path) {
        return new Promise(function (resolve, reject) {
            const image = new Image();
            image.onload  = function() {
                resolve(image);
            };
            image.onerror = reject;
            image.src = path;
        });
    };
	
	// 页面加载前预加载图片
    componentWillMount() {
        this.image = this.preloadImage(lugia);
    };
	
	// 页面加载完成后加图片嵌入页面中
    componentDidMount() {
        this.image
            .then((image) => {
                document.getElementById('img_lugia').appendChild(image);
            })
            .catch((e) => console.log('Error:'+ e));
    }

  render() {
    return (
      <div className="App">
        <header className="App-header">
            <div id="img_lugia"/>
          <p>
            Edit <code>src/App.js</code> and save to reload.
          </p>
          <a
            className="App-link"
            href="https://reactjs.org"
            target="_blank"
            rel="noopener noreferrer"
          >
            Learn React
          </a>
        </header>
      </div>
    );
  }
}

export default App;
```

