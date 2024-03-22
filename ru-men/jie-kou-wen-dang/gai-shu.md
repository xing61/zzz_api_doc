---
layout:
  title:
    visible: true
  description:
    visible: false
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: false
---

# 概述

**说明**

1、接口请求规范完全和OpenAI一样，应当直接以OpenAI的接口文档为准：[https://platform.openai.com/docs/api-reference/introduction](https://platform.openai.com/docs/api-reference/introduction)

如果是国内模型，只需要将模型参数 model 修改为国内的模型名字即可！

（因为OpenAI接口也时常更新，建议直接以上面OpenAI的官方文档为准，我们仅列出关键补充说明和示例）

2、智增增Github地址：[https://github.com/xing61/xiaoyi-robot](https://github.com/xing61/xiaoyi-robot)

3、支持所有的OpenAI的接口

4、智增增自有API接口，区别于各大模型的接口（如OpenAI，文心一言等），一般用来操作和查询智增增账户相关，比如查询余额等

5、智增增仅在外层增加两个字段：code，msg，用来表示当下接口的状态。code=0表示接口调用成功，非0表示失败，msg表明错误信息

示例：

```
{
  "code": 0,
  "msg": "",
  "id": "as-bcmt5ct4iy",
  "created": 1680167072,
  "choices":[{"message":{"role":"assistant","content":"1+100=101"},"finish_reason":"stop","index":0}],
  "usage": {
    "prompt_tokens": 470,
    "completion_tokens": 198,
    "total_tokens": 668
  }
}
```

* **注意事项**\
  注意事项！！

```
现在主要发现是有几个问题，  
1、要加一个请求头，api接口文档中有说明：
curl -H "Content-Type: application/json" -H "Authorization: Bearer 你在智增增的key" -XPOST https://flag.smarttrot.com/v1/chat/completions -d '{"messages": [{"role":"user","content":"请介绍一下你自己"}]}'  | iconv -f utf-8 -t utf-8  
2、messages传的不对，messages是array
3、api_secret_key传的不对，不能再传openai的key了，你要传你从智增增拿到的key（不需要有openai的key）
4、如果是python，注意openai包的版本要对，它升级了！！要注意，关键是base_url要设置成智增增的，如果这个不正确，其它肯定都不行。所以一定要注意他在不同的包中base_url的设置方式，目前已知的是：在老版本中的设置方式是：openai.api_base = BASE_URL，而在新版本中的设置方式是：client = OpenAI(api_key=API_SECRET_KEY, base_url=BASE_URL)，别问为什么，问就是openai的锅      
```

注：\
1、以下所有接口的base\_url: `https://flag.smarttrot.com/` （支持https）\
2、API通过HTTP请求调用。每次请求，需要在HTTP头中携带用户的api\_secret\_key，用于认证。 开发者单独的api\_secret\_key，请从智增增管理后台获得。 请求头形如：

```
Content-Type: application/json
Authorization: Bearer 你在智增增的key  
```
