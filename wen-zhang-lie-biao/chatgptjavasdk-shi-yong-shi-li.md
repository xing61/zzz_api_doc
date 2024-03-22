---
description: >-
  ChatGPT Java SDK支持流式输出、Gpt插件、联网。支持OpenAI官方所有接口。ChatGPT的Java客户端。OpenAI
  GPT-3.5-Turb GPT-4 Api Client for Java
layout:
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: false
---

# chatgpt-java-sdk使用示例

Github地址：[https://github.com/Grt1228/chatgpt-java](https://github.com/Grt1228/chatgpt-java)

* 第1步：用手机号登录智增增，获取复制出key和url，地址：[http://gpt.zhizengzeng.com/#/login](http://gpt.zhizengzeng.com/#/login)

<figure><img src="../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

* 第2步：使用sdk时，将上一步复制的key和url填入，即可正常使用。注意配置的主机地址是：`https://flag.smarttrot.com/。`设置apiKey为智增增的key，apiHost为智增增的base\_url

如下代码所示：

```
// Some code
public class Test {
    public static void main(String[] args) {
        OkHttpClient okHttpClient = new OkHttpClient
                .Builder()
                .connectTimeout(300, TimeUnit.SECONDS)//自定义超时时间
                .writeTimeout(300, TimeUnit.SECONDS)//自定义超时时间
                .readTimeout(300, TimeUnit.SECONDS)//自定义超时时间
                .build();
        OpenAiStreamClient client = OpenAiStreamClient.builder()
                .apiKey(“你的智增增的key”)
                .okHttpClient(okHttpClient)//设置自定义的okHttpClient客户端
                //自定义ApiHost,注意后面的/
                .apiHost("https://flag.smarttrot.com/")
                .build();
    }
}
```
