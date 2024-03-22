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

# 自有API接口

**说明**

1、智增增自有API接口，区别于各大模型的接口（如OpenAI，文心一言等），一般用来操作和查询智增增账户相关，比如查询余额等

* **注意事项**

```
现在主要发现是有3个问题，  
1、要加一个请求头，api接口文档中有说明：
curl -H "Content-Type: application/json" -H "Authorization: Bearer $api_secret_key" -XPOST https://flag.smarttrot.com/v1/chat/completions -d '{"messages": [{"role":"user","content":"请介绍一下你自己"}]}'  | iconv -f utf-8 -t utf-8  
2、messages传的不对，messages是array
3、api_secret_key传的不对，不能再传openai的key了，你要传你从智增增拿到的key（不需要有openai的key）       
```

注：\
1、以下所有接口的base\_url: `https://flag.smarttrot.com/` （支持https）\
2、API通过HTTP请求调用。每次请求，需要在HTTP头中携带用户的api\_secret\_key，用于认证。 开发者单独的api\_secret\_key，请从智增增管理后台获得。 请求头形如：

```
Content-Type: application/json
Authorization: Bearer $api_secret_key
```
