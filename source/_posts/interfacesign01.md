title: 接口签名学习(一)
date: 2016-05-13 15:36:48
tags: node
original: false
categories: 后端
---

#### 接口签名
不同系统平台之间可能要互相调用彼此的一些服务,此时接口开发是各系统之间对接的重要方式，其数据是通过开放的互联网传输，对数据的安全性要有一定要求。为了提高传输过程参数的防篡改性，签名sign的方式是目前比较常用的方式，工作中使用的也是这种方式，应该属于数字签名的方式。也是目前国内互联网公司常用的一种方式。比如[腾讯的支付接口签名](http://kf.qq.com/faq/120322fu63YV130422aqUNJv.html).
#### 参数sign生成的方法
- 第1步: 将所有参数（注意是所有参数），除去sign本身，以及值是空的参数，按参数名字母升序排序。
- 第2步: 然后把排序后的参数按参数1值1参数2值2…参数n值n的方式拼接成一个字符串。
- 第3步: 在上一步得到的字符串前面加上验证密钥key(这里的密钥key是接口提供方分配给接口接入方的)。
- 第4步: 计算第3步字符串的md5值(32位),然后转成大写,得到的字符串作为sign的值调用接口时把该sign值也发送过去。
<!-- more -->

这里自己写了一个node版本的生成sign的函数(代码写的不好,欢迎吐槽)，同时发布到了npm上https://www.npmjs.com/package/getsignature

    var crypto = require('crypto');
    var util = require('util');
    function getsignature(paramObject, secretKey) {
      if( util.isObject(paramObject) && !util.isArray(paramObject) && secretKey!=null && secretKey!=''){
        var tmp = [];//存放参数名key
        var str = '';//存放拼接字符
        for (var key in paramObject) {
          tmp.push(key);
        }
        //参数名排序
        tmp.sort();
        // 拼接字符串 key1+value1+key2+value2
        for (var i = 0, len = tmp.length; i < len; i++) {
          str += (paramObject[tmp[i]] != null && paramObject[tmp[i]] != '') ? (tmp[i] + '' + paramObject[tmp[i]]) : '';
        }
        //拼接秘钥
        str = secretKey + str;
        // md5加密
        str =  crypto.createHash("md5").update(str, 'utf8').digest("hex");
        return str.toUpperCase();
      }
      return '';
    }
    module.exports = getsignature;

**安装**

    $ npm install getsignature
**示例**
    
    var getsignature = require('getsignature');
    // 生成签名所用参数 （调用外部接口要发送的参数）
    var params = { 'name':'abc','age':24,'sex':1 };
    // 秘钥参数(约定好的)
    var secretKey = 'abcdef';
    // 获取签名参数
    var signature = getsignature(params, secretKey);
    console.log(signature); 
    // '71A9D9D21A122E287DA3051A9573F313'

#### 签名验证方法
根据前面描述的签名参数sign生成的方法规则，计算得到参数的签名值，和参数中通知过来的sign对应的参数值进行对比，如果是一致的，那么就校验通过，如果不一致，说明参数被修改过。
示例:（假定上方的node系统调用的是一个php接口）
    
    <?php
      $secretKey = 'abcdef';//先前约定好的秘钥
      //接受到的参数不包含sign参数 放进一个数组中
      $param = array('name' =>'abc','age'=>24,'sex'=>1 );
      $sign = $_REQUEST['sign'];
      //同样的方式计算签名
      function getSignature($param, $secretKey) {
        if(!is_array($param)){
            return NULL;
        }
        ksort($param);
        
        $str = '';
        foreach ($param as $key=>$value) {
           if($value!=NULL && $value!=''){
             $str .= ($key.$value); 
           }
        }
    
        $str= $secretKey. $str;
        return strtoupper(md5($str));
      }
      //比较签名是否一致
      echo (getSignature($param, $secretKey) == $sign )?'签名有效':'参数被修改过';

参考文档:http://kf.qq.com/faq/120322fu63YV130422aqUNJv.html
参考文档:http://www.tuicool.com/articles/jQJV3i
参考文档:http://www.oicto.com/web-api-sign-key-a/


