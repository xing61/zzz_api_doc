---
layout:
  title:
    visible: true
  description:
    visible: false
  tableOfContents:
    visible: true
  outline:
    visible: false
  pagination:
    visible: false
---

# 大模型怎么实现连续对话（记忆上下文）

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
