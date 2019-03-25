# 虚拟DOM

DOM结构在JS里的映射对象，称之为虚拟DOM。是用JS按照DOM结构来实现的树形结构对象。

流程：
1. 创建虚拟DOM
2. render [渲染分2步，1.将虚拟DOM转成真实DOM，2.插入页面]
3. diff算法 遍历比较找补丁 [比较虚拟DOM树的差异，得到差异对象]
4. patch 根据patch描述打补丁 [将差异对象应用到真正的DOM树上]

### 1. 创建虚拟DOM
```
// element.js

// DOM结构：类型、属性、子节点

// 虚拟DOM元素的类，构建示例对象，用来描述DOM
class Element {
  constructor (type, props, children) {
    this.type = type;
    this.props = props;
    this.children = children;
  }
}

// 创建虚拟DOM，返回虚拟节点(object)
function createElement (type, props, children) {
  return new Element(type, props, children);
}

export {
  Element,
  createElement
}


// index.js

import { createElement } from './element';

let virtualDom = createElement('ul', {class: 'list'}, [
  createElement('li', {class: 'item'}, ['周杰伦']),
  createElement('li', {class: 'item'}, ['林俊杰']),
  createElement('li', {class: 'item'}, ['王力宏'])
 ]);
 
 console.log(virtualDom);
 
 // 以上，只是一个JS对象，实际上与DOM结构毫无关系，接下来就是将它渲染到页面里

```

### 2. render
```
 // element.js
 
 class Element {
  // ...
 }
 
 function createElement () {
  // ...
 }
 
 function render (domObj) {
  // 根据TYPE类型，创建真实对应元素
  let el = document.createElement(domObj.type);
  
  // 遍历props属性，然后给创建的元素el设置属性
  for (let key in domObj.props) {
    // 自定义设置属性的方法
    setAttr(el, key, domObj.props[key]);
  }
  
  // 遍历子节点
  // 如果是虚拟DOM，就继续递归渲染
  // 不是就代表是文本节点，直接创建
  domObj.children.forEach(child => {
    child = (child instanceof Element) ? render(child) : document.createTextNode(child);
    
    // 添加到对应的元素内
    el.appendChild(child);
  });
  
  return el;
 }
 
 // 自定义设置属性
 function setAttr (node, key, value) {
  switch (key) {
    case 'value':
      // node是一个input或者textarea就直接设置value
      if (node.tagName.toLowerCase() === 'input' || node.tagName.toLowerCase() === 'textarea') {
        node.value = value;
      } else {
        node.setAttribute(key, value);
      }
      break;
    case 'style':
      // 行内样式直接赋值
      node.style.cssText = value;
      break;
    default:
      node.setAttribute(key, value);
      break;
  }
 }
 
 // 将元素插入到页面中
 function renderDom(el, target) {
  target.appendChild(el);
 }
 
 export {
  Element,
  createElement,
  render, 
  setAttr,
  renderDom
 }
 
 
 // index.js
 
 import { createElement, render, renderDom } from './element';
 
 let virtualDom = createElement('ul', {class: 'list'}, [
    createElement('li', {class: 'item'}, ['周杰伦']),
    createElement('li', {class: 'item'}, ['林俊杰']),
    createElement('li', {class: 'item'}, ['王力宏'])
]);

// 渲染
// 1. 虚拟DOM → 真实DOM
let el = render(virtualDom);

// 2. 将真实DOM插入页面
renderDom(el, document.getElementById('body'));
```

### 3. Dom-diff
根据两个虚拟对象创建出补丁，描述改变的内容，将这个补丁用来更新DOM

> **比较规则**
> * 新的DOM节点不存在 { type: 'REMOVE', index }
> * 文本的变化 { type: 'TEXT', text: 1 }
> * 当节点类型一致时，查看属性值变化 { type: 'ATTR', attr: {class: 'list-group'} }
> * 节点类型不一致时，直接替换 { type: 'REPLACE', newNode }

```
// diff.js
// 以下概念中：tree = node，因为结构是一样的，可以将结构更复杂一些的称为树，结构更简单一点的称为节点
function diff (oldTree, newTree) {
  // 补丁对象
  let patches = {};
  
  let index = 0;
  
  // 递归树，比较后的结构放到补丁里
  walk(oldTree, newTree, index, patches);
  
  return patches;
}

// 遍历节点的函数
function walk(oldNode, newNode, index, patches) {
  // 每一个虚拟节点都有一个补丁，用来描述改变的内容
  let current = [];
  
  if (!newNode) {
    // 新旧对比，发现旧里没有新，说明要移除
    current.push({ type: 'REMOVE', index });
  } else if (isString(oldNode) && isString(newNode)) {
    // 文本值修改
    if (oldNode !== newNode) {
      current.push({ type: 'TEXT', text: newNode });
    }
  } else if (oldNode.type === newNode.type) {
    // 属性值修改
    let attr = diffAttr(oldNode.props, newNode.props);
    if (Object.keys(attr).length > 0) {
      current.push({ type: 'ATTR', attr });
    }
    // 如果有子节点，遍历子节点
    diffChildren(oldNode.children, newNode.children, patches);
  } else { // 说明节点发生替换
    current.push({ type: 'REPLACE', newNode });
  }
  
  if (current.length) {
    // 添加到大补丁包里
    patches[index] = current;
  }
}

function isString (obj) {
  return typeof obj === 'string';
}

function diffAttr (oldAttrs, newAttrs) {
  let patch = {};
  
  // 找出修改后的属性
  for (let key in oldAttrs) {
    if (oldAttrs[key] !== newAttrs[key]) {
      patch[key] = newAttrs[key];
    }
  }
  
  // 找出新增的属性
  for (let key in newAttrs) {
    if (!oldAttrs.hasOwnProperty(key)) {
      patch[key] = newAttrs[key];
    }
  }
  
  return patch;
}

let num = 0;

function diffChildren (oldChildren, newChildren, patches) {
  oldChildren.forEach((child, index) => {
    walk(child, newChildren[index], ++num, patches);
   });
}

export default diff;
```

### 4. patch补丁更新

```
import { Element, render, setAttr } from './element';

let allPatches;
let index = 0;

// 参数1：谁要打补丁
// 参数2：打哪个补丁
function patch (node, patches) {
  allPatches = patches;
  
  // 给某个node打补丁
  walk(node);
}

function walk (node) {
  let current = allPatches[index++];
  let childNodes = node.childNodes;
  
  // 先序遍历
  childNodes.forEach(child => walk(child));
  
  if (current) {
    doPatch(node, current); // 打补丁
  }
}

function doPatch (node, patches) {
  patches.forEach(patch => {
    switch (patch.type) {
      case 'ATTR':
        for (let key in patch.attr) {
          let value = patch.attr[key];
          if (value) {
            setAttr(node, key, value);
          } else {
            node.removeAttribute(key);
          }
        }
        break;
      case 'TEXT':
        node.textContent = patch.text;
        break;
      case 'REPLACE':
        let newNode = patch.newNode;
        newNode = (newNode instanceof Element) ? render(newNode) : document.createTextNode(newNode);
        node.parentNode.replaceChild(newNode, node);
        break;
      case 'REMOVE':
        node.parentNode.removeChild(node);
        break;
      default:
        break;
      }
    }
  });
}

export default patch;


// index.js
// index.js

import { createElement, render, renderDom } from './element';
// +++ 引入diff和patch方法
import diff from './diff';
import patch from './patch';
// +++

let virtualDom = createElement('ul', {class: 'list'}, [
    createElement('li', {class: 'item'}, ['周杰伦']),
    createElement('li', {class: 'item'}, ['林俊杰']),
    createElement('li', {class: 'item'}, ['王力宏'])    
]);

let el = render(virtualDom);
renderDom(el, window.root);

// +++
// 创建另一个新的虚拟DOM
let virtualDom2 = createElement('ul', {class: 'list-group'}, [
    createElement('li', {class: 'item active'}, ['七里香']),
    createElement('li', {class: 'item'}, ['一千年以后']),
    createElement('li', {class: 'item'}, ['需要人陪'])    
]);
// diff一下两个不同的虚拟DOM
let patches = diff(virtualDom, virtualDom2);
console.log(patches);
// 将变化打补丁，更新到el
patch(el, patches);
// +++
```
