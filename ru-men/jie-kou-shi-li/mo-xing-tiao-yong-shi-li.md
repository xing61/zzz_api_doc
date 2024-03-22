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

# 模型调用示例

原则上，接口请求规范完全和OpenAI一样，应当直接以OpenAI的接口文档为准：[https://platform.openai.com/docs/api-reference/introduction](https://platform.openai.com/docs/api-reference/introduction)

如果是国内模型，只需要将模型参数 model 修改为国内的模型名字即可！

（因为OpenAI接口也时常更新，建议直接以上面OpenAI的官方文档为准，我们仅列出关键补充说明和示例）

下面示例怎么调用讯飞的星火大模型：

模型名字这里有列表：[https://docs.zhizengzeng.com/ru-men/mo-xing-shuo-ming](https://docs.zhizengzeng.com/ru-men/mo-xing-shuo-ming)

```python
import os
from openai import OpenAI
import requests
import time
import json
import time

API_SECRET_KEY = "xxxxxx";
BASE_URL = "https://flag.smarttrot.com/v1/"

# chat with other model
def chat_completions4(query):
    client = OpenAI(api_key=API_SECRET_KEY, base_url=BASE_URL)
    resp = client.chat.completions.create(
        model="SparkDesk",
        messages=[
            {"role": "system", "content": "You are a helpful assistant."},
            {"role": "user", "content": query}
        ]
    )
    print(resp)
    print(resp.choices[0].message.content)
    
if __name__ == '__main__':
    chat_completions4("你是哪个公司开发的什么模型？");
```

正常就会返回：

```
ChatCompletion(id='', choices=[Choice(finish_reason='stop', index=0, message=ChatCompletionMessage(content='我是由科大讯飞开发的讯飞星火认知大模型，我的设计和构建都是由科大讯飞的团队进行的，并且不断进行更新和改进，以让我能够为用户提供更好的服务。', role='assistant', function_call=None, tool_calls=None))], created=1704960724, model=None, object='chat.completion', system_fingerprint=None, usage=CompletionUsage(completion_tokens=42, prompt_tokens=31, total_tokens=73), code=0, msg='ok')
我是由科大讯飞开发的讯飞星火认知大模型，我的设计和构建都是由科大讯飞的团队进行的，并且不断进行更新和改进，以让我能够为用户提供更好的服务。
```
