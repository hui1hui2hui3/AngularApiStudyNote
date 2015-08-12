
#ng-bind-html & $sce学习笔记

> **注意事项：** 用该指令bind内容时，必须依赖ngSanitize模块，同时调用$sce.trustAsHtml等方法，否则会出现 `Attempting to use an unsafe value in a safe context.` 异常

## 官方代码Code
```html
<div ng-controller="AppController as myCtrl">
  <i ng-bind-html="myCtrl.explicitlyTrustedHtml" id="explicitlyTrustedHtml"></i><br><br>
  <b>User comments</b><br>
  By default, HTML that isn't explicitly trusted (e.g. Alice's comment) is sanitized when
  $sanitize is available.  If $sanitize isn't available, this results in an error instead of an
  exploit.
  <div class="well">
    <div ng-repeat="userComment in myCtrl.userComments">
      <b>{{userComment.name}}</b>:
      <span ng-bind-html="userComment.htmlComment" class="htmlComment"></span>
      <br>
    </div>
  </div>
</div>
```
```js
angular.module('mySceApp', ['ngSanitize'])
.controller('AppController', ['$http', '$templateCache', '$sce',
  function($http, $templateCache, $sce) {
    var self = this;
    $http.get("test_data.json", {cache: $templateCache}).success(function(userComments) {
      self.userComments = userComments;
    });
    self.explicitlyTrustedHtml = $sce.trustAsHtml(
        '<span onmouseover="this.textContent=&quot;Explicitly trusted HTML bypasses ' +
        'sanitization.&quot;">Hover over this text.</span>');
  }]);
```
```json
[
  { "name": "Alice",
    "htmlComment":
        "<span onmouseover='this.textContent=\"PWN3D!\"'>Is <i>anyone</i> reading this?</span>"
  },
  { "name": "Bob",
    "htmlComment": "<i>Yes!</i>  Am I the only other one?"
  }
]
```

------------
> Written with [StackEdit](https://stackedit.io/).