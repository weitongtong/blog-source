---
title: Version number contrast
date: 2017-09-16 15:14:03
tags:
---
```javascript
function toNum(a){
    var a = a.toString();
    var c = a.split('.');
    var num_place = ["","0","00","000","0000"], r = num_place.reverse();
    for (var i = 0; i < c.length; i++){
        var len = c[i].length;
        c[i] = r[len] + c[i];
    }
    var res = c.join('');
    return res;
}
function cpr_version(a,b){
    var _a = toNum(a), _b = toNum(b);
    if(_a == _b) return 0;
    if(_a > _b) return 1;
    if(_a < _b) return -1;
}
//var a = "2.2.3", b = "2.3.0";
//转化
//000200020003  000200030000
//cpr_version(a,b);
```