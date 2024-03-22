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

# 常见问题

## 为什么问GPT4是不是GPT4，他说自己是GPT3?

注意：如果直接问openai时他的回答可能还是gpt-3，这是openai的问题，我们保证原封不动调用的gpt4哦\~\~\
可以通过其它回答的智能程度来判断是否gpt4。\
这是因为：This question is a common one, the dataset that GPT-4 is based on does not have GPT-4 in it as it’s cut off is 2021.\
可以参考这里：[为什么问GPT4是不是GPT4，他说自己是GPT3?](https://community.openai.com/t/gpt-4-through-api-says-its-gpt-3/286881)

<figure><img src="../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

## 为什么API老是提示我缺少某个参数？

请按接口文档检查一下是不是哪里设置的不对。一般来说很重要的，参数要是json格式的。可以这样来检查：将传递的参数使用json格式器看一下，是否报错。就能确定是否json格式的问题。如果还是有问题，可以联系客服人员，一起和智增增技术人员联调下解决问题

## 智增增Key（个人版）和智增增API有什么区别？

智增增官网：[https://zhizengzeng.com/ ](https://zhizengzeng.com/)

企业版和开发版，叫大模型的API接口：[http://gpt.zhizengzeng.com/#/my ](http://gpt.zhizengzeng.com/#/my)

个人版，叫智增增Key：[http://plugin.smarttrot.com/#/home ](http://plugin.smarttrot.com/#/home)

区别在于：企业版，支持高并发的，支持用写代码方式使用。而个人版，不支持高并发，不支持写代码方式使用，仅限一个人自己使用，用在一些AI工具里面。

不过要注意：两者的密钥不能混淆。

智增增key的密钥以zp-开头，使用的主机地址是：https://plugin.smarttrot.com

而API接口的密钥以zk-开头，使用的主机地址是：https://flag.smarttrot.com

## 大模型怎么实现连续对话？记忆上下文？

1、OpenAI或者其它大语言模型本身是没有记忆的，如果你不告诉他你之前说了什么以及他之前回答了什么，那么他只会根据你最近一次发送的内容进行回答。

2、所以，要想实现“连续对话”，每次发送消息时，你需要将你之前发送的内容(\*\*user\*\*)以及大模型之前返回的内容(\*\*assistant\*\*)，再结合你本次想发送的内容(\*\*user\*\*) 按 \*\*时序\*\* 组合成一个 messages\[] 数组，然后再将这个数组发送给OpenAI就行了，就是这么简单。

3、有一点需要注意，这样虽然可以实现“连续对话”，但势必造成每次发送的消息内容会非常多，而OpenAI之流是按字数计费的，所以请自行权衡每次应该携带的数量。

官方参考文档：[https://platform.openai.com/docs/guides/text-generation/chat-completions-api](https://platform.openai.com/docs/guides/text-generation/chat-completions-api)

\> Chat models take a series of messages as input, and return a model-generated message as output.

\> 聊天模型将一系列消息作为输入，并返回模型生成的消息作为输出。

下面是一个实现连续聊天的代码示例，主要看这个 messges 数组：

```python
import openai
from openai import OpenAI
API_SECRET_KEY = "你的智增增获取的api_key";
BASE_URL = "https://flag.smarttrot.com/v1/"; #智增增的base_url
# 连续对话的实例，自己根据业务逻辑填充messages即可
def lianxu():
    client = OpenAI(api_key=API_SECRET_KEY, base_url=BASE_URL)
    resp = client.chat.completions.create(
        model="gpt-3.5-turbo",
        messages=[
            {"role": "system", "content": "You are a helpful assistant."},
            {"role": "user", "content": "Who won the world series in 2020?"},
            {"role": "assistant", "content": "The Los Angeles Dodgers won the World Series in 2020."},
            {"role": "user", "content": "Where was it played?"}
        ]
    )
    print(resp)
```
