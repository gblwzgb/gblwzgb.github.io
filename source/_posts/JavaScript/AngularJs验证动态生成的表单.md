---
title: AngularJs验证动态生成的表单
date: 2017-02-28 18:42:47
categories: JavaScript
---

有这么一个需求，我的表单是ng-repeat生成的，但是要做表单验证。  
而AngularJs的表单验证是和name绑定的。如下。
```html
<p>邮箱:<br>
    <input type="email" name="email" ng-model="user.email" required>
    <span style="color:red" ng-show="myForm.email.$dirty && myForm.email.$invalid">
        <span ng-show="myForm.email.$error.required">邮箱是必须的。</span>
        <span ng-show="myForm.email.$error.email">非法的邮箱地址。</span>
    </span>
</p>
```

我把name改成`{ {user.email} }`，验证的地方改成`myForm.{ {user.email} }.$error.required`。报错。

## 解决方法
google了一下，用`ng-form`标签
```html
<form name="outerForm">
<div ng-repeat="item in items">
   <ng-form name="innerForm">
      <input type="text" name="qwe" ng-model="item.foo" />
      <span ng-show="innerForm.qwe.$error.required">required</span>
   </ng-form>
</div>
<input type="submit" ng-disabled="outerForm.$invalid" />
</form>
```
这里的name可以随便写了，只要和验证的地方对应就好了。