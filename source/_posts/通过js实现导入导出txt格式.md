---
title: 通过js实现导入导出txt格式
date: 2017-02-28 14:42:47
categories: JavaScript
---

项目中有一个需求是要导出页面上的配置，然后又不想走后台来实现。google之~

# 导出
使用FileSaver.min.js

[FileSaver.min.js的github地址](https://github.com/eligrey/FileSaver.js)

示例（项目用的是AngularJs）:
```js
PatientVisitModel.prototype.downloadConfig = function () {

    var saveText = {};
    saveText.currentType = this.currentType;
    saveText.currentConfig = this.uiConfigs;

    var blob = new Blob([JSON.stringify(saveText)], {type: "text/plain;charset=utf-8"});
    saveAs(blob, "hello world.txt");

};
```

# 导入
使用HTML5.JS的FileReader对象
```js
PatientVisitModel.prototype.uploadConfig = function (myFile) {

    var self = this;

    var file = myFile.files[0];
    var reader = new FileReader();
    //将文件以文本形式读入页面
    reader.readAsText(file);
    reader.onload = function()
    {
        var resultText = JSON.parse(this.result);
        self.uiConfigs = resultText.currentConfig;
        self.currentType = resultText.currentType;
    };
};
```

html页面上的写法
```html
<input type="file" id="file-input" onchange="angular.element(this).scope().patientVisitModel.uploadConfig(this)" >
```