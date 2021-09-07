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
    resolve(num);
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
("Promise对象的resolve方法,保存成功值","Promise对象的reject方法,保存失败值") => {
  "to do something..." 
  if("成功后"){
    resolve("成功值");
  }else{
    reject("失败值");
  }
}
```
then方法的参数，伪代码
```javascript
then("匿名函数,处理成功值,(data)=>{ }","匿名函数,处理失败值,(data)=>{ }");
```
