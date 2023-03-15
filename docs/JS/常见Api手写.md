---

title: 常见Api手写
date: 2023-01-03
---

## instanceof

`instanceof` 运算符用于检测构造函数的 `prototype` 属性是否出现在某个实例对象的原型链上

使用如下：

```js
object instanceof constructor
```

`object`为实例对象，`constructor`为构造函数

实现`instanceof`

```html
     function myInstanceof(left,right){
		//判断基础类型和null
      if(typeof left !="object"||left==null) return false;
      let proto=Object.getPrototypeOf(left);
      if(true){
        if(proto===null) return false;
        if(proto===right.prototype) return true;
        proto=Object.getPrototypeOf(proto);
      }
     }
```

## new的实现
- 创建一个新的对象`obj`
- 将对象与构建函数通过原型链连接起来
- 将构建函数中的`this`绑定到新建的对象`obj`上
- 根据构建函数返回类型作判断，如果是原始值则被忽略，如果是返回对象，需要正常处理

```html
 function newFun(Fun, ...arg) {
        const obj = {};
        obj.__proto__ = Fun.prototype;
        let result = Fun.call(obj, args);
        return result instanceof Object ? result : obj;
      }
```


## 深拷贝

```js
const obj2=JSON.parse(JSON.stringify(obj1));
```

递归实现

```html
      function deepClone(obj, map = new Map()) {
        if (obj === null) return obj;
 		if (obj instanceof Date) return new Date(obj);
  		if (obj instanceof RegExp) return new RegExp(obj);
        if (typeof obj !== "object")  return obj;
        if (map.get(obj)) return map.get(obj);
        let cloneObj = Array.isArray(obj) ? [] : {};
        map.set(obj, cloneObj);
        for (const key in obj) {
          if (Object.hasOwnProperty(key)) {
            const element = obj[key];
            cloneObj[key] = deepClone(obj[key], map);
          }
        }
        return obj;
      }
```



## ajax实现

实现 `Ajax`异步交互需要服务器逻辑进行配合，需要完成以下步骤：

- 创建 `Ajax`的核心对象 `XMLHttpRequest`对象
- 通过 `XMLHttpRequest` 对象的 `open()` 方法与服务端建立连接
- 构建请求所需的数据内容，并通过`XMLHttpRequest` 对象的 `send()` 方法发送给服务器端
- 通过 `XMLHttpRequest` 对象提供的 `onreadystatechange` 事件监听服务器端你的通信状态
- 接受并处理服务端向客户端响应的数据结果
- 将处理结果更新到 `HTML`页面中

```html
//封装一个ajax请求
function myAjax(options) {
    //创建XMLHttpRequest对象
    const xhr = new XMLHttpRequest()
    //初始化参数的内容
    options = options || {}
    options.type = (options.type || 'GET').toUpperCase()
    options.dataType = options.dataType || 'json'
    const params = options.data

    //发送请求
    if (options.type === 'GET') {
        xhr.open('GET', options.url + '?' + params, true)
        xhr.send(null)
    } else if (options.type === 'POST') {
        xhr.open('POST', options.url, true)
        xhr.send(params)

    //接收请求
    xhr.onreadystatechange = function () {
        if (xhr.readyState === 4) {
            let status = xhr.status
            if (status >= 200 && status < 300) {
                options.success && options.success(xhr.responseText, xhr.responseXML)
            } else {
                options.fail && options.fail(status)
            }
        }
    }
}
```

>  
