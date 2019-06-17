---
title: javascript Strategy pattern 적용하기
date: 2018-12-12 16:55:35
tags:
categories: javascript
---

JSbridge개발 도중, 새로운 native의 기능이 추가될 때마다 javascript를 수정하는 일이 빈번하게 발생했다. 예를 들어 카메라를 불러오는 code를 작성고, 새롭게 마이크를 불러오는 code를 작성하는 경우 이다.

처음 허접같아 보이는 코드는 다음과 같다.

```javascript
var call = function(funcName, options){
    jsWebview.call(funcName, options, 'null');
}

var btn = document.getElementById("toastBtn");
var thumImg = document.getElementById("thumbnailImg");


var showThumnail = function(response){
    thumImg.children[0].children[0].src =response;
    call("showToast", "11", "null");
}

btn.addEventListener("click", function () {
    showThumnail();
    var val = document.getElementById("email").value;
    console.log(val);
    call('showToast', val , 'null');
})

thumImg.addEventListener("click", function(){
   call("takePicture", 'null', 'null');
})

```



JSbridge역할을 하는 함수를 공통으로 만들고, 각 event가 발생할 때마다 호출한다. 그리고 showThumnail이라는 native로 부터 callback을 받는 부분을 따로 만들어 준다. 

코드의 가독성이 떨어지고, 유지보수가 힘들고, javascript 처음 짜본사람처럼 보인다. 

그래서, strategy pattern을 이용하여, 개선하게 되었다.



```javascript

/*********************************************************
    Context
**********************************************************/ 
var AndroidNative = function () {
    this.androidFunc = "";
}

AndroidNative.prototype.setStrategy = function (androidFunc) {
    this.androidFunc = androidFunc;
}

AndroidNative.prototype.excute = function () {
    return this.androidFunc.excute();
}

/*********************************************************
    Strategy
**********************************************************/ 
var Camera = function () {

    this.thumImg = document.getElementById("thumbnailImg");
    this.thumText = document.getElementById("thumbnailText");

    this.excute = function () {
        //generate trx_id
        return jsWebview.call("takePicture", "null", "null");
    }

    //Callback
    this.showThumnail = function (response) {
        this.thumImg.src = "data:image/png;base64, " + response;
        this.thumText.innerHTML = "";
    }

}

var Toast = function () {

    this.message = "";
    this.btn = document.getElementById("toastBtn");

    this.setMessage = function (message) {
        this.message = message;
    }

    this.excute = function () {
        return jsWebview.call("showToast", this.message, "null");
    }
}

/*********************************************************
    Client
**********************************************************/ 
var androidNative = new AndroidNative();


var toast = new Toast();
var camera = new Camera();


toast.btn.addEventListener("click", function () {

    var val = document.getElementById("email").value;
    toast.setMessage(val);

    androidNative.setStrategy(toast);
    androidNative.excute();
})

camera.thumImg.addEventListener("click", function () {

    androidNative.setStrategy(camera);
    androidNative.excute();

})
```

사용할 기능의 이름을 가진 객체를 만들어 그 내부에서 callback이나 DOM조작을 위한 객체들을 관리한다. androidNative는 Strategy pattern에서 Context역할을 한다.

