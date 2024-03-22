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

# HelloWord-第一个示例

* 第1步：用手机号登录智增增，获取复制出key和url，地址：[http://gpt.zhizengzeng.com/#/login](http://gpt.zhizengzeng.com/#/login)
* 第2步：编写代码。注意配置的base\_url是：`https://flag.smarttrot.com/v1`

```python
如果是python，注意openai包的版本要对，它升级了！！
要注意，关键是base_url要设置成智增增的，如果这个不正确，其它肯定都不行。
所以一定要注意他在不同的包中base_url的设置方式，
目前已知的是：在老版本中的设置方式是：openai.api_base = BASE_URL，
而在新版本中的设置方式是：client = OpenAI(api_key=API_SECRET_KEY, base_url=BASE_URL)，
别问为什么，问就是openai的锅      

方式1：使用openai的官方包（新版）：
import os
from openai import OpenAI
import openai
import requests
import time
import json
import time

API_SECRET_KEY = "xxxxxx";
BASE_URL = "https://flag.smarttrot.com/v1/"

# chat
def chat_completions3(query):
    client = OpenAI(api_key=API_SECRET_KEY, base_url=BASE_URL)
    resp = client.chat.completions.create(
        model="gpt-3.5-turbo",
        messages=[
            {"role": "system", "content": "You are a helpful assistant."},
            {"role": "user", "content": query}
        ]
    )
    print(resp)
    print(resp.choices[0].message.content)

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

方式2：使用openai的官方包（旧版）：
import os
import openai
import requests
import time
import json
import time

API_SECRET_KEY = "xxxxxx";
BASE_URL = "https://flag.smarttrot.com/v1/"
# chat
def chat_completions2(query):
    openai.api_key = API_SECRET_KEY
    openai.api_base = BASE_URL
    resp = openai.ChatCompletion.create(
        model="gpt-3.5-turbo",
        messages=[
            {"role": "system", "content": "You are a helpful assistant."},
            {"role": "user", "content": query}
        ]
    )
    print(resp)
    print(resp.choices[0].message.content)

if __name__ == '__main__':
    chat_completions2("圆周率的前10位");


方式3：使用http请求：
import os
import requests
import time
import json

def chat_completions():
    url="https://flag.smarttrot.com/v1/chat/completions"
    api_secret_key = 'xxxxxxxxx';  # 你的api_secret_key
    headers = {'Content-Type': 'application/json', 'Accept':'application/json',
               'Authorization': "Bearer "+api_secret_key}
    params = {'user':'张三','model':"gpt-3.5-turbo",
              'messages':[{'role':'user', 'content':'1+100='}]};
    r = requests.post(url, json.dumps(params), headers=headers)
    print(r.json())

if __name__ == '__main__':
    chat_completions();
```
