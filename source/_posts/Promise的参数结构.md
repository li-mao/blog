---
title: Promise的参数结构
date: 2021-09-06 10:22:49
tags:
---

Promise对象是要通过new方法构造的，构造参数是一个匿名函数。
```javascript
p = new Promise((resolve,reject)=>{
  console.log("do something...");
  const num = Math.random();
  if( num > 0.5 ){
    resole(num);
  }else{
    reject(num); 
  }
})

p.then((data)=>{
  console.log("big: ",data);
},(data)=>{
  console.log('little: ',data);
});
```
构造参数，伪代码
```javascript
("后续的匿名函数resolve,目前仅作占位,传给它的参数,被认为是成功后的数据结果","后续的匿名函数reject,目前仅作占位,传给它的参数,被认为是失败后的数据结果") => {
  "to do something..." 
  if("成功后"){
    resolve("成功后的数据");
  }else{
    reject("失败后的数据");
  }
}
```
then方法的参数，伪代码
```javascript
then("真正的匿名函数resolve,(data)=>{ }","真正的匿名函数reject,(data)=>{ }");
```
