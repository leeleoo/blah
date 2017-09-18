---
title: web 中各种数据的转换
date: 2017-02-18 16:42:29
tags:
---
http://blog.csdn.net/cuixiping/article/details/45932793
canvas转换为dataURL (从canvas获取dataURL)

```
var dataurl = canvas.toDataURL('image/png');
var dataurl2 = canvas.toDataURL('image/jpeg', 0.8);
```

File对象转换为dataURL、Blob对象转换为dataURL
File对象也是一个Blob对象，二者的处理相同。


```
    function readBlobAsDataURL(blob, callback) {
       var a = new FileReader(); 
       a.onload = function(e) {callback(e.target.result);};             
       a.readAsDataURL(blob);
    }
    //example:
    readBlobAsDataURL(blob, function (dataurl){ 
              console.log(dataurl);
    });
    readBlobAsDataURL(file, function (dataurl){ 
              console.log(dataurl);
    });
```
dataURL转换为Blob对象
```
function dataURLtoBlob(dataurl) { 
  var arr = dataurl.split(','), mime = arr[0].match(/:(.*?);/)[1], bstr = atob(arr[1]), n = bstr.length, u8arr = new Uint8Array(n); 
  while(n--){ u8arr[n] = bstr.charCodeAt(n); } return new Blob([u8arr], {type:mime});
}
//test:var blob = dataURLtoBlob('data:text/plain;base64,YWFhYWFhYQ==');
```
dataURL图片数据绘制到canvas
先构造Image对象，src为dataURL，图片onload之后绘制到canvas
```
var img = new Image();
img.onload = function(){ canvas.drawImage(img);};
img.src = dataurl;
```

File,Blob的图片文件数据绘制到canvas
还是先转换成一个url，然后构造Image对象，src为dataURL，图片onload之后绘制到canvas
利用上面的 readBlobAsDataURL 函数，由File,Blob对象得到dataURL格式的url，再参考 **dataURL图片数据绘制到canvas**
```
readBlobAsDataURL(file, function (dataurl){ 
    var img = new Image(); 
    img.onload = function(){ 
      canvas.drawImage(img); 
    }; 
    img.src = dataurl;});
```


