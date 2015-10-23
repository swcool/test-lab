# 重定向路径

用户点击比价菜单项后，后端jsp对微信访问进行鉴权，并获取用户的opened，然后302重定向到`/wechat/static/quote.html#/basicInfo/` + *openid*。

前端可获取openid并以此作为前后端交互的参数。

# 基本信息

## 根据opened获取用户基本信息

```
$.post('/api/quote/getBasicInfo', {
    opened: '1234567890'
}, function (resp) {
    if(resp.code === 0) {
        basicInfo = resp.data;
    } else {
        // 显示resp.errMsg出错信息
    }
});
```

返回结果为JSON格式，正常返回结果：

```
{
  "code": 0,
  "data": {
    "openid": "1234567890",
    "city": "上海市", // 行驶城市
    "realName": "马大哈", // 姓名
    "plateNumber": "沪A1234C", // 车牌号码
    "regDate": "2004-6-12", // 初登日期
    "idCardNumber": "310104195612134429", // 身份证号
    "mobileNumber": "13913913913", // 手机号码
    "mobileVerified": true // 手机号已验证
  }
}
```
其中除openid外，其他字段若用户尚未登记信息，则为undefined。

出错时，返回的错误代码code及出错信息errMsg由后端定义，前端只负责原样显示errMsg提示给用户。

## 发送短信验证码

*复用现有接口*

## 保存用户基本信息

```
$.post('/api/quote/saveBasicInfo', $.extend(basicInfo, {
    vcode: vcode // 验证码，可选参数，仅当mobileVerified为false或修改了手机号码时才传递
}, function (rest) {
    if(rest.code === 0) {
        // 保存成功，跳转到车辆识别页面
    } else {
        // 保存失败，显示resp.errMsg出错信息如验证码校验失败……
    }
});
```

后端收到请求后，需要根据mobileVerified字段取值进行不同的处理：

1. 若mobileVerified为true，则不处理验证码，直接保存用户的其他信息；
2. 若mobileVerified为false，则进行验证码校验，校验通过后保存用户所有信息。

# 车辆识别

## 获取车辆识别信息（是否要获取已登记车辆信息？）

```
$.post('/api/quote/getCarInfo', {
    opened: '1234567890'
}, function (resp) {
    if(resp.code === 0) {
        carInfo = resp.data;
    } else {
        // 显示resp.errMsg出错信息
    }
});
```

返回结果为JSON格式，正常返回结果：

```
{
  "code": 0,
  "data": {
    "openid": "1234567890",
    "idCardNumber": "310104195612134429", // 身份证号
    "mobileNumber": "13913913913" // 手机号码
  }
}
```

出错时，返回的错误代码code及出错信息errMsg由后端定义。

## 上传并识别行驶证

*复用现有接口*

## 保存车辆识别信息

