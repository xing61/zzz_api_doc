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

# 查询余额

**1、账户相关**

获取账户相关信息：余额等。

**1.1、查询余额**

获取账户余额

* **请求URL**

> `v1/dashboard/billing/credit_grants`

* **请求方式**

> **POST**

* **Header参数**

>

| 名称            | 值                        |
| ------------- | ------------------------ |
| Content-Type  | application/json         |
| Authorization | Bearer $api\_secret\_key |

* **请求参数**

>

* **请求示例**

>

```
BASE_URL = "https://flag.smarttrot.com/v1"

# credit_grants
def credit_grants(query):
    api_secret_key = API_SECRET_KEY;  # 智增增的secret_key
    url = BASE_URL+'/dashboard/billing/credit_grants'; # 余额查询url
    headers = {'Content-Type': 'application/json', 'Accept':'application/json',
               'Authorization': "Bearer "+api_secret_key}
    resp = requests.post(url, headers=headers)
    resp = resp.json();
    json_str = json.dumps(resp, ensure_ascii=False)
    print(json_str)

```

* **返回示例**

>

```
{
    "code": 0,
    "msg": "ok",
    "object": "credit_summary",
    "grants": {
        "available_amount": "111.8658"
    }
}
```

\
