# VanillaBlog - 簡易前端網誌

## 來源

本程式修改自 koa 的官方範例，網址如下：

-- https://github.com/koajs/examples/tree/master/blog

原本該範例是要搭配 server 的 node.js 程式，我把它改成全部在前端執行的版本。

原本資料儲存在 server 的記憶體，修改之後則是存在前端的記憶體當中。

## 功能

包含

1. 新增貼文
2. 列出貼文
3. 閱讀貼文內容

## 技術手段

1. 使用 windows.onhashchange 去做 hash 的監控，當作 router

```js
// router 路徑地圖
let routeMap = [
  [/^\/?show\/(\w+)\/?$/, show],
  [/^\/?list\/?$/, list],
  [/^\/?create\/?$/, create],
]

// 當 hash 改變時，觸發路徑地圖對應的函數。
window.onhashchange = function () {
  var hash = window.location.hash.trim().substring(1)
  for (let [regexp, f] of routeMap) {
    var m = hash.match(regexp)
    if (m) {
      f(m, hash)
      break
    }
  }
}

// 網頁載入完成後，呼叫 list() 列出所有貼文！
window.onload = function() {
  window.location.hash = '#/list/'
}
```

2. 利用 showPanel 去顯示指定版面，隱藏其他版面

```js
// 顯示 id 指定的 panel (div)
function showPanel(id) {
  document.querySelectorAll('.panel').forEach((node) => node.style.display='none')
  document.getElementById(id).style.display = 'block'
}
```

3. 對應的主要功能包含『列出貼文，新增貼文，儲存貼文，顯示貼文內容』等部分的程式碼如下：

```js

// 列出所有貼文
function list() {
  let postsHtml = []
  for (let i=0; i<posts.length; i++) {
    postsHtml.push(`<li><h2>${posts[i].title}</h2><p><a href="#/show/${i}">Read post</a></p></li>`)
  }
  document.getElementById('posts').innerHTML = postsHtml.join('\n')
  document.getElementById('postsLength').innerHTML = posts.length
  showPanel('list')
}

// 儲存貼文
function save() {
  var title = document.getElementById('postTitle').value
  var body  = document.getElementById('postBody').value
  posts.push({title: title, body: body})
  // 最後讓 hash='#/list/' 會觸發 list() 列出所有貼文
  window.location.hash = '#/list/'
}

// 顯示 id=match[1] 所指定的貼文
function show(match) {
  let i = parseInt(match[1])
  document.getElementById('showTitle').innerHTML = posts[i].title
  document.getElementById('showBody').innerHTML = posts[i].body
  showPanel('show')
}

// 顯示『創建新貼文』的畫面。
function create(match) {
  showPanel('create')
  document.getElementById('postTitle').value = ''
  document.getElementById('postBody').value = ''
}
```

