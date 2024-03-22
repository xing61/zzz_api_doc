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

# 接口列表

### （<mark style="color:red;">`注意以下所有url中的：https://api.openai.com，需要替换为：https://flag.smarttrot.com/`</mark>）

### 一. Introduction 介绍

如果你想使用我们的API，你可以通过 **HTTP** 请求从任何语言与 API 进行交互，也可以使用我们的官方 Python 绑定、官方 Node.js库 或 **社区维护的库**。

若要安装官方 Python 绑定，请运行以下命令：

```python
pip install openai
```

要安装官方的 Node.js 库，请在您的 Node.js 项目目录中运行以下命令：

```js
npm install openai
```

### 二. Authentication 认证

#### 1. OpenAI-API-KEY

**OpenAI API** 使用 API密钥 进行身份验证。请访问您的 **API密钥** 页面以检索您在请求中使用的API密钥。

**请记住，您的API密钥是机密的！** 不要与他人分享它或在任何客户端代码（浏览器、应用程序）中公开它。生产请求必须通过您自己的后端服务器路由，其中您的 API密钥 可以从环境变量或密钥管理服务中安全加载。

所有API请求都应在 `Authorization` HTTP标头中包含您的API密钥，如下所示：

```http
注意Bearer OPENAI_API_KEY，Bearer的后面是有一个空格的
Authorization: Bearer OPENAI_API_KEY
```

#### 2. OpenAI-Organization

**Requesting organization 请求组织**

对于属于多个组织的用户，您可以传递一个 **表头** 来指定用于 API请求 的组织。这些 API请求 的使用将计入指定组织的订阅配额。

示例 **curl** 命令：

```http
curl https://api.openai.com/v1/models \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -H "OpenAI-Organization: org-gth0C8mT2wnKealyCkSRrpQk"
```

使用 **openai Python包** 的示例：

```python
import os
import openai
openai.organization = "org-gth0C8mT2wnKealyCkSRrpQk"
openai.api_key = os.getenv("OPENAI_API_KEY")
openai.Model.list()
```

使用 **openai Node.js包** 的示例：

```js
import { Configuration, OpenAIApi } from "openai";
const configuration = new Configuration({
    organization: "org-gth0C8mT2wnKealyCkSRrpQk",
    apiKey: process.env.OPENAI_API_KEY,
});
const openai = new OpenAIApi(configuration);
const response = await openai.listEngines();
```

可以在 **组织设置** 页面上找到 **组织ID**

### 三. Making requests 提出请求

您可以将下面的命令粘贴到您的终端中，以运行您的第一个 API请求。请确保将 **$OPENAI\_API\_KEY** 替换为您的 **API密钥**。

```http
curl https://api.openai.com/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -d '{
     "model": "gpt-3.5-turbo",
     "messages": [{"role": "user", "content": "Say this is a test!"}],
     "temperature": 0.7
   }'
```

此请求查询 `gpt-3.5-turbo模型`，以完成从提示“Say this is a test”开始的文本。您应该会收到类似以下内容的响应：

```json
{
   "id":"chatcmpl-abc123",
   "object":"chat.completion",
   "created":1677858242,
   "model":"gpt-3.5-turbo-0301",
   "usage":{
      "prompt_tokens":13,
      "completion_tokens":7,
      "total_tokens":20
   },
   "choices":[
      {
         "message":{
            "role":"assistant",
            "content":"\n\nThis is a test!"
         },
         "finish_reason":"stop",
         "index":0
      }
   ]
}
```

现在您已经生成了第一个聊天完成。我们可以看到 `finish_reason` 是 `stop`，这意味着API返回了模型生成的完整完成。在上面的请求中，我们只生成了一条消息，但您可以将 `n` 参数设置为生成多个消息选项。在此示例中，`gpt-3.5-turbo` 被用于更传统的 文本完成任务。该模型也针对 聊天应用 进行了优化。

### 四. Models 模型

列出并描述 API 中可用的各种模型。您可以参考 模型文档 以了解可用的模型以及它们之间的差异。

#### 1. List models 列出模型

```http
GET 
https://api.openai.com/v1/models
```

列出当前可用的模型，并提供有关每个模型的基本信息，例如所有者和可用性。

请求演示：

```http
curl https://api.openai.com/v1/models \
  -H "Authorization: Bearer $OPENAI_API_KEY"
```

响应：

```json
{
  "data": [
    {
      "id": "model-id-0",
      "object": "model",
      "owned_by": "organization-owner",
      "permission": [...]
    },
    {
      "id": "model-id-1",
      "object": "model",
      "owned_by": "organization-owner",
      "permission": [...]
    },
    {
      "id": "model-id-2",
      "object": "model",
      "owned_by": "openai",
      "permission": [...]
    },
  ],
  "object": "list"
}
```

#### 2. Retrieve model 检索模型

```http
GET 
https://api.openai.com/v1/models/{model}
```

检索模型实例，提供有关模型的基本信息，例如所有者和权限。

其中，`model` 为必填的字符串类型，用于此请求的模型的 ID。

请求演示：

```http
curl https://api.openai.com/v1/models/text-davinci-003 \
  -H "Authorization: Bearer $OPENAI_API_KEY"
```

响应：

```json
{
  "id": "text-davinci-003",
  "object": "model",
  "owned_by": "openai",
  "permission": [...]
}
```

### 五. Completions 完成

给定一个提示，模型将返回一个或多个预测的完成，并且还可以在每个位置返回替代令牌的概率。

#### 1. Create completion

```http
POST 
https://api.openai.com/v1/completions
```

为提供的提示和参数创建完成。

请求演示：

```http
curl https://api.openai.com/v1/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -d '{
    "model": "text-davinci-003",
    "prompt": "Say this is a test",
    "max_tokens": 7,
    "temperature": 0
  }'
```

响应：

```json
{
  "id": "cmpl-uqkvlQyYK7bGYrRHQ0eXlWi7",
  "object": "text_completion",
  "created": 1589478378,
  "model": "text-davinci-003",
  "choices": [
    {
      "text": "\n\nThis is indeed a test",
      "index": 0,
      "logprobs": null,
      "finish_reason": "length"
    }
  ],
  "usage": {
    "prompt_tokens": 5,
    "completion_tokens": 7,
    "total_tokens": 12
  }
}
```

**Request body(入参详解)**

> * **`model` （string，必填）**
>
> 要使用的模型的 ID。可以使用 列表模型API (GET api.openai.com/v1/models) 查看所有可用模型，或参阅 模型概述 了解它们的描述。
>
> * **`prompt` （string or array，选填，Defaults to <|endoftext|>）**
>
> 用于生成完成、编码为字符串、字符串数组、标记数组或标记数组数组的提示。
>
> 注意 |endoftext| 是模型在训练期间看到的文档分隔符，因此如果未指定提示，模型将生成，就像从新文档的开头一样。
>
> * **`suffix` （string，选填，Defaults to null）**
>
> 完成插入文本后的后缀。
>
> * **`max_tokens` （integer，选填，Defaults to 16）**
>
> 完成时要生成的最大 token 数。
>
> 提示 `max_tokens` 的 token 计数不能超过模型的上下文长度。大多数模型的上下文长度为 2048 个令牌（最新模型除外，它支持 4096）
>
> * **`temperature` （number，选填，Defaults to 1）**
>
> 使用哪个采样温度，在 **0和2之间**。
>
> 较高的值，如0.8会使输出更随机，而较低的值，如0.2会使其更加集中和确定性。
>
> 我们通常建议修改这个（`temperature` ）为 `top_p` 但两者不能同时存在，二选一。
>
> * **`top_p` （number，选填，Defaults to 1）**
>
> 一种替代温度采样的方法叫做核心采样，模型会考虑到具有 top\_p 概率质量的标记结果。因此，0.1 表示只有占前 10% 概率质量的标记被考虑。
>
> 我们通常建议修改这个（`top_p` ）或者 `temperature`，但不要同时修改两者。
>
> * **`n` （integer，选填，Defaults to 1）**
>
> 每个 `prompt` 生成的完成次数。
>
> 注意：由于此参数会生成许多完成，因此它会快速消耗您的令牌配额。小心使用，并确保对 `max_tokens` 和 `stop` 进行合理的设置。
>
> * **`stream` （boolean，选填，Defaults to false）**
>
> 是否返回部分进度流。如果设置，令牌将作为数据服务器推送事件随着它们变得可用而发送，流通过 `data: [DONE]` 消息终止。
>
> * **`logprobs` （integer，选填，Defaults to null）**
>
> 在 `logprobs` 返回的最有可能的标记列表中，包括所选标记和对应的对数概率。
>
> 例如，如果 `logprobs` 为 5，则 API 将返回一个由 5 个最有可能的标记组成的列表。API 总是会返回采样标记的对数概率，因此响应中可能会有多达 `logprobs+1` 个元素。
>
> `logprobs` 的最大值为 5。如果您需要更多，请通过我们的 帮助中心 联系我们并描述您的用例。
>
> * **`echo` （boolean，选填，Defaults to false）**
>
> 除了完成之外，还回显提示
>
> * **`stop` （string or array，选填，Defaults to null）**
>
> 最多生成4个序列，API将停止生成更多的标记。返回的文本不包含停止序列。
>
> * **`presence_penalty` （number，选填，Defaults to 0）**
>
> 介于 **-2.0 和 2.0 之间**的数字。正值会根据它们是否出现在文本中迄今为止来惩罚新令牌，从而增加模型谈论新主题的可能性。
>
> 请参阅有关频率和状态惩罚的更多信息
>
> * **`frequency_penalty` （number，选填，Defaults to 0）**
>
> 介于-2.0和2.0之间的数字。正值会根据文本中新令牌的现有频率对其进行惩罚，从而降低模型重复相同行的可能性。
>
> 请参阅有关频率和存在惩罚的更多信息
>
> * **`best_of` （integer，选填，Defaults to 1）**
>
> 在生成服务器端生成 `best_of` 完成，并返回“最佳”（每个标记具有最高对数概率的那一个）。结果无法流式传输。
>
> 当与 `n` 一起使用时，`best_of` 控制候选完成的数量，`n` 指定要返回多少个 - `best_of` 必须大于 `n`。
>
> 注意：由于此参数生成许多完成，因此可能会快速消耗您的令牌配额。请小心使用并确保 `max_tokens` 和 `stop` 设置合理。
>
> * **`logit_bias` （map，选填，Defaults to null）**
>
> 修改指定标记在完成中出现的可能性。
>
> 接受一个JSON对象，将标记（由GPT分词器中的标记ID指定）映射到从 -100 到 100 的相关偏差值。您可以使用此 [分词器工具](https://link.juejin.cn/?target=https%3A%2F%2Fplatform.openai.com%2Ftokenizer%3Fview%3Dbpe)（适用于GPT-2和GPT-3）将文本转换为令牌ID。数学上，在采样之前，模型生成的 logits 会添加偏差。确切的效果因模型而异，但是介于-1和1之间的值应该会减少或增加选择的可能性；像 -100 或 100 这样的值应该会导致相关令牌被禁止或独占选择。
>
> 例如，您可以传递 `{"50256": -100}` 来防止生成
>
> * **`user` （string，选填）**
>
> 一个唯一的标识符，代表您的终端用户，可以帮助OpenAI监测和检测滥用。了解更多信息。

### 六. Chat 聊天

给定一组描述对话的消息列表，模型将返回一个回复。

#### 1. Create chat completion

```http
POST
https://api.openai.com/v1/chat/completions
```

为给定的聊天对话创建模型响应。

请求演示：

```http
curl https://api.openai.com/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -d '{
    "model": "gpt-3.5-turbo",
    "messages": [{"role": "user", "content": "Hello!"}]
  }'
```

响应：

```json
{
  "id": "chatcmpl-123",
  "object": "chat.completion",
  "created": 1677652288,
  "choices": [{
    "index": 0,
    "message": {
      "role": "assistant",
      "content": "\n\nHello there, how may I assist you today?",
    },
    "finish_reason": "stop"
  }],
  "usage": {
    "prompt_tokens": 9,
    "completion_tokens": 12,
    "total_tokens": 21
  }
}
```

**Request body(入参详解)**

> *   **`model` （string，必填）**
>
>     要使用的模型ID。有关哪些模型适用于Chat API的详细信息，请查看 模型端点兼容性表
> *   **`messages` （array，必填）**
>
>     迄今为止描述对话的消息列表
>
>     *   **`role` （string，必填）**
>
>         此消息的作者角色。`system` 、`user` 或 `assistant` 之一
>     *   **`content` （string，必填）**
>
>         消息的内容
>     *   **`name` （string，选填）**
>
>         此消息的作者的姓名。可以包含 a-z、A-Z、0-9 和下划线，最大长度为 64 个字符
> *   **`temperature` （number，选填，Defaults to 1）**
>
>     使用哪个采样温度，在 **0和2之间**。
>
>     较高的值，如0.8会使输出更随机，而较低的值，如0.2会使其更加集中和确定性。
>
>     我们通常建议修改这个（`temperature` ）为 `top_p` 但两者不能同时存在，二选一。
> *   **`top_p` （number，选填，Defaults to 1）**
>
>     一种替代温度采样的方法叫做核心采样，模型会考虑到具有 top\_p 概率质量的标记结果。因此，0.1 表示只有占前 10% 概率质量的标记被考虑。
>
>     我们通常建议修改这个（`top_p` ）或者 `temperature`，但不要同时修改两者。
> *   **`n` （integer，选填，Defaults to 1）**
>
>     每个输入消息要生成多少聊天完成选项数
> *   **`stream` （boolean，选填，Defaults to false）**
>
>     如果设置了，将发送部分消息增量，就像在 ChatGPT 中一样。令牌将作为数据 服务器推送事件 随着它们变得可用而被发送，流通过 `data: [DONE]` 消息终止。请参阅OpenAI Cookbook 以获取 示例代码。
> *   **stop （string or array，选填，Defaults to null）**
>
>     最多生成4个序列，API将停止生成更多的标记。
> *   **`max_tokens` （integer，选填，Defaults to inf）**
>
>     在聊天完成中生成的最大 tokens 数。
>
>     输入令牌和生成的令牌的总长度受模型上下文长度的限制。
> *   **`presence_penalty` （number，选填，Defaults to 0）**
>
>     介于 **-2.0 和 2.0 之间**的数字。正值会根据它们是否出现在文本中迄今为止来惩罚新令牌，从而增加模型谈论新主题的可能性。
>
>     请参阅有关频率和状态惩罚的更多信息
> *   **`frequency_penalty` （number，选填，Defaults to 0）**
>
>     介于-2.0和2.0之间的数字。正值会根据文本中新令牌的现有频率对其进行惩罚，从而降低模型重复相同行的可能性。
>
>     请参阅有关频率和存在惩罚的更多信息
> *   **`logit_bias` （map，选填，Defaults to null）**
>
>     修改完成时指定标记出现的可能性。
>
>     接受一个JSON对象，将标记（由分词器中的标记ID指定）映射到从 -100 到 100 的相关偏差值。在采样之前，模型生成的logits会加上这个偏差。确切的影响因模型而异，但是 -1 到 1 之间的值应该会减少或增加选择概率；像 -100 或 100 这样的值应该会导致相关标记被禁止或独占选择。
> *   **`user` （string，选填）**
>
>     一个唯一的标识符，代表您的终端用户，可以帮助OpenAI监测和检测滥用。了解更多信息。

### 七. Edits 编辑

给定一个提示和一条指令，模型将返回提示的编辑版本。

#### 1. Create edit

```http
POST
https://api.openai.com/v1/edits
```

为提供的输入、指令和参数创建一个新的编辑。

请求演示：

```http
curl https://api.openai.com/v1/edits \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -d '{
    "model": "text-davinci-edit-001",
    "input": "What day of the wek is it?",
    "instruction": "Fix the spelling mistakes"
  }'
```

响应：

```json
{
  "object": "edit",
  "created": 1589478378,
  "choices": [
    {
      "text": "What day of the week is it?",
      "index": 0,
    }
  ],
  "usage": {
    "prompt_tokens": 25,
    "completion_tokens": 32,
    "total_tokens": 57
  }
}
```

**Request body(入参详解)**

> *   **`model` （string，必填）**
>
>     要使用的模型ID。您可以在此端点中使用 `text-davinci-edit-001` 或 `code-davinci-edit-001` 模型。
> *   **`input` （string，选填，Defaults to ''）**
>
>     用作编辑起点的输入文本。
> *   **`instruction` （string，必填）**
>
>     指导模型如何编辑提示的说明。
> *   **`n` （integer，选填，Defaults to 1）**
>
>     输入和指令需要生成多少次编辑。
> *   **`temperature` （number，选填，Defaults to 1）**
>
>     使用哪个采样温度，在 **0和2之间**。
>
>     较高的值，如0.8会使输出更随机，而较低的值，如0.2会使其更加集中和确定性。
>
>     我们通常建议修改这个（`temperature` ）为 `top_p` 但两者不能同时存在，二选一。
> *   **`top_p` （number，选填，Defaults to 1）**
>
>     一种替代温度采样的方法叫做核心采样，模型会考虑到具有 top\_p 概率质量的标记结果。因此，0.1 表示只有占前 10% 概率质量的标记被考虑。
>
>     我们通常建议修改这个（`top_p` ）或者 `temperature`，但不要同时修改两者。

### 八. Images 图像

给定一个提示和/或输入图像，模型将生成一张新的图像。

相关指南：图像生成。

#### 1. Create image

```http
POST
https://api.openai.com/v1/images/generations
```

根据提示创建图像。

请求演示：

```http
curl https://api.openai.com/v1/images/generations \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -d '{
    "prompt": "A cute baby sea otter",
    "n": 2,
    "size": "1024x1024"
  }'
```

响应：

```json
{
  "created": 1589478378,
  "data": [
    {
      "url": "https://..."
    },
    {
      "url": "https://..."
    }
  ]
}
```

**Request body(入参详解)**

> *   **`prompt` （string，必填）**
>
>     所需图像的文本描述。最大长度为1000个字符。
> *   **`n` （integer，选填，Defaults to 1）**
>
>     要生成的图像数量。必须在1到10之间。
> *   **`size` （string，选填，Defaults to 1024x1024）**
>
>     生成图像的尺寸。必须是 `256x256` 、`512x512` 或 `1024x1024` 之一。
> *   **`response_format` （string，选填，Defaults to url）**
>
>     生成的图像返回格式。必须是 `url` 或 `b64_json` 之一。
> *   **`user` （string，选填）**
>
>     一个唯一的标识符，代表您的终端用户，可以帮助OpenAI监测和检测滥用。了解更多信息。

#### 2. Create image edit

```http
POST
https://api.openai.com/v1/images/edits
```

根据原始图像和提示创建编辑或扩展的图像。

请求演示：

```http
curl https://api.openai.com/v1/images/edits \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -F image="@otter.png" \
  -F mask="@mask.png" \
  -F prompt="A cute baby sea otter wearing a beret" \
  -F n=2 \
  -F size="1024x1024"
```

响应：

```json
{
  "created": 1589478378,
  "data": [
    {
      "url": "https://..."
    },
    {
      "url": "https://..."
    }
  ]
}
```

**Request body(入参详解)**

> *   **`image` （string，必填）**
>
>     要编辑的图像。必须是有效的PNG文件，小于4MB且为正方形。如果未提供遮罩，则图像必须具有透明度，该透明度将用作遮罩。
> *   **`mask` （string，选填）**
>
>     一个额外的图像，其完全透明的区域（例如 alpha 值为零的区域）指示应该编辑图像的位置。`image` 必须是有效的 PNG 文件，小于 4MB，并且具有与 `image` 相同的尺寸。
> *   **`prompt` （string，必填）**
>
>     所需图像的文本描述。最大长度为1000个字符。
> *   **`n` （integer，选填，Defaults to 1）**
>
>     要生成的图像数量。必须在1到10之间。
> *   **`size` （string，选填，Defaults to 1024x1024）**
>
>     生成图像的尺寸。必须是 `256x256` 、`512x512` 或 `1024x1024` 之一。
> *   **`response_format` （string，选填，Defaults to url）**
>
>     生成的图像返回格式。必须是 `url` 或 `b64_json` 之一。
> *   **`user` （string，选填）**
>
>     一个唯一的标识符，代表您的终端用户，可以帮助OpenAI监测和检测滥用。了解更多信息。

#### 3. Create image variation

```http
POST
https://api.openai.com/v1/images/variations
```

创建给定图像的变体。

请求演示：

```http
curl https://api.openai.com/v1/images/variations \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -F image="@otter.png" \
  -F n=2 \
  -F size="1024x1024"
```

响应：

```json
{
  "created": 1589478378,
  "data": [
    {
      "url": "https://..."
    },
    {
      "url": "https://..."
    }
  ]
}
```

**Request body(入参详解)**

> *   **`image` （string，必填）**
>
>     用作变体基础的图像。必须是有效的PNG文件，小于4MB，并且为正方形。
> *   **`n` （integer，选填，Defaults to 1）**
>
>     要生成的图像数量。必须在1到10之间。
> *   **`size` （string，选填，Defaults to 1024x1024）**
>
>     生成图像的尺寸。必须是 `256x256` 、`512x512` 或 `1024x1024` 之一。
> *   **`response_format` （string，选填，Defaults to url）**
>
>     生成的图像返回格式。必须是 `url` 或 `b64_json` 之一。
> *   **`user` （string，选填）**
>
>     一个唯一的标识符，代表您的终端用户，可以帮助OpenAI监测和检测滥用。了解更多信息。

### 九. Embeddings 嵌入

获得一个给定输入的向量表示，可以轻松地被机器学习模型和算法使用。

相关指南：嵌入

#### 1. **Create embeddings**

```http
POST
https://api.openai.com/v1/embeddings
```

创建一个嵌入向量，代表输入的文本。

请求演示：

```http
curl https://api.openai.com/v1/embeddings \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "input": "The food was delicious and the waiter...",
    "model": "text-embedding-ada-002"
  }'
```

响应：

```json
{
  "object": "list",
  "data": [
    {
      "object": "embedding",
      "embedding": [
        0.0023064255,
        -0.009327292,
        .... (1536 floats total for ada-002)
        -0.0028842222,
      ],
      "index": 0
    }
  ],
  "model": "text-embedding-ada-002",
  "usage": {
    "prompt_tokens": 8,
    "total_tokens": 8
  }
}
```

**Request body(入参详解)**

> *   **`model` （string，必填）**
>
>     要使用的 **模型ID**。您可以使用 列出模型 API查看所有可用模型，或者请参阅我们的 模型概述 以了解它们的描述。
> *   **`input` （string or array，必填）**
>
>     输入文本以获取嵌入，编码为字符串或令牌数组。要在单个请求中获取多个输入的嵌入，请传递字符串数组或令牌数组的数组。每个输入长度**不得超过 8192 个标记**。
> *   **`user` （string，选填）**
>
>     一个唯一的标识符，代表您的终端用户，可以帮助OpenAI监测和检测滥用。了解更多信息。

### 十. Audio 音频

了解如何将音频转换为文本。

相关指南：语音转文本

#### 1. Create transcription

```http
POST
https://api.openai.com/v1/audio/transcriptions
```

将音频转录为输入语言。

请求演示：

```http
curl https://api.openai.com/v1/audio/transcriptions \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -H "Content-Type: multipart/form-data" \
  -F file="@/path/to/file/audio.mp3" \
  -F model="whisper-1"
```

响应：

```json
{
  "text": "Imagine the wildest idea that you've ever had, and you're curious about how it might scale to something that's a 100, a 1,000 times bigger. This is a place where you can get to do that."
}
```

**Request body(入参详解)**

> *   **`file` （string，必填）**
>
>     要转录的音频文件，格式为以下之一：mp3、mp4、mpeg、mpga、m4a、wav 或 webm。
> *   **`model` （string，必填）**
>
>     要使用的模型ID。目前仅有 `whisper-1` 可用。
> *   **`prompt` （string，选填）**
>
>     一个可选的文本，用于指导模型的风格或继续之前的音频片段。 prompt 应该与音频语言相匹配。
> *   **`response_format` （string，选填，Defaults to json）**
>
>     转录输出的格式，可选项包括：json、文本、srt、verbose\_json或vtt。
> *   **`temperature` （number，选填，Defaults to 0）**
>
>     采样温度介于0和1之间。较高的值（如0.8）会使输出更随机，而较低的值（如0.2）则会使其更加集中和确定性。如果设置为0，则模型将使用 log probability(对数概率) 自动增加温度，直到达到某些阈值。
> *   **`language` （string，选填）**
>
>     输入音频的语言。以 ISO-639-1 格式提供输入语言将提高准确性和延迟。

#### 2. Create translation

```http
POST
https://api.openai.com/v1/audio/translations
```

将音频翻译成英语。

请求演示：

```http
curl https://api.openai.com/v1/audio/translations \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -H "Content-Type: multipart/form-data" \
  -F file="@/path/to/file/german.m4a" \
  -F model="whisper-1"
```

响应：

```json
{
  "text": "Hello, my name is Wolfgang and I come from Germany. Where are you heading today?"
}
```

**Request body(入参详解)**

> *   `file` （string，必填）
>
>     要翻译的音频文件必须是以下格式之一：mp3、mp4、mpeg、mpga、m4a、wav或webm。
> *   **`model` （string，必填）**
>
>     要使用的模型ID。目前仅有 `whisper-1` 可用。
> *   **`prompt` （string，选填）**
>
>     一个可选的文本，用于指导模型的风格或继续之前的音频片段。 prompt 应该与音频语言相匹配。
> *   **`response_format` （string，选填，Defaults to json）**
>
>     转录输出的格式，可选项包括：json、文本、srt、verbose\_json或vtt。
> *   **`temperature` （number，选填，Defaults to 0）**
>
>     采样温度介于0和1之间。较高的值（如0.8）会使输出更随机，而较低的值（如0.2）则会使其更加集中和确定性。如果设置为0，则模型将使用 log probability(对数概率) 自动增加温度，直到达到某些阈值。

### 十一. Files 文件

**Files** 用于上传文档，可与 Fine-tuning 等功能一起使用。

#### 1. List files

```http
GET
https://api.openai.com/v1/files
```

返回属于用户组织的文件列表。

请求演示：

```http
curl https://api.openai.com/v1/files \
  -H "Authorization: Bearer $OPENAI_API_KEY"
```

响应：

```json
{
  "data": [
    {
      "id": "file-ccdDZrC3iZVNiQVeEA6Z66wf",
      "object": "file",
      "bytes": 175,
      "created_at": 1613677385,
      "filename": "train.jsonl",
      "purpose": "search"
    },
    {
      "id": "file-XjGxS3KTG0uNmNOK362iJua3",
      "object": "file",
      "bytes": 140,
      "created_at": 1613779121,
      "filename": "puppy.jsonl",
      "purpose": "search"
    }
  ],
  "object": "list"
}
```

#### 2. Upload file

```http
POST
https://api.openai.com/v1/files
```

上传包含文档的文件以在各个端点/功能之间使用。目前，一个组织上传的所有文件的大小可以高达1 GB。如果您需要增加存储限制，请与我们联系。

请求演示：

```http
curl https://api.openai.com/v1/files \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -F purpose="fine-tune" \
  -F file="@mydata.jsonl"
```

响应：

```json
{
  "id": "file-XjGxS3KTG0uNmNOK362iJua3",
  "object": "file",
  "bytes": 140,
  "created_at": 1613779121,
  "filename": "mydata.jsonl",
  "purpose": "fine-tune"
}
```

**Request body(入参详解)**

> *   `file` （string，必填）
>
>     要上传的 JSON Lines 文件名。
>
>     如果 `purpose` 设置为 **“fine-tune”**，则每行都是一个JSON记录，其中包含“prompt”和“completion”字段，表示您的 training examples(训练示例)。
> *   `purpose` （string，必填）
>
>     上传文档的预期用途。
>
>     使用 **“fine-tune”** 进行 Fine-tuning(微调)。这样可以验证上传文件的格式。

#### 3. Delete file

```http
DELETE
https://api.openai.com/v1/files/{file_id}
```

删除文件。

请求演示：

```http
curl https://api.openai.com/v1/files/file-XjGxS3KTG0uNmNOK362iJua3 \
  -X DELETE \
  -H "Authorization: Bearer $OPENAI_API_KEY"
```

其中，`{file_id}` 为 string类型的必填项，用于此请求的文件的 ID

响应：

```json
{
  "id": "file-XjGxS3KTG0uNmNOK362iJua3",
  "object": "file",
  "deleted": true
}
```

#### 4. Retrieve file

```http
GET
https://api.openai.com/v1/files/{file_id}
```

返回有关特定文件的信息。

请求演示：

```http
curl https://api.openai.com/v1/files/file-XjGxS3KTG0uNmNOK362iJua3 \
  -H "Authorization: Bearer $OPENAI_API_KEY"
```

其中，`{file_id}` 为 string类型的必填项，用于此请求的文件的 ID

响应：

```json
{
  "id": "file-XjGxS3KTG0uNmNOK362iJua3",
  "object": "file",
  "bytes": 140,
  "created_at": 1613779657,
  "filename": "mydata.jsonl",
  "purpose": "fine-tune"
}
```

#### 5. Retrieve file content

```http
GET
https://api.openai.com/v1/files/{file_id}/content
```

返回指定文件的内容。

请求演示：

```http
curl https://api.openai.com/v1/files/file-XjGxS3KTG0uNmNOK362iJua3/content \
  -H "Authorization: Bearer $OPENAI_API_KEY" > file.jsonl
```

其中，`{file_id}` 为 string类型的必填项，用于此请求的文件的 ID

### 十二. Fine-tunes 微调

管理微调作业以将模型定制为您的特定训练数据。

相关指南：Fine-tune models(微调模型)

#### 1. Create fine-tune

```http
POST
https://api.openai.com/v1/fine-tunes
```

创建一个工作，从给定的数据集中微调指定模型。

响应包括已入队的作业的详细信息，包括 作业状态 和 完成后微调模型的名称。

了解有关微调的更多信息。

请求演示：

```http
curl https://api.openai.com/v1/fine-tunes \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -d '{
    "training_file": "file-XGinujblHPwGLSztz8cPS8XY"
  }'
```

响应：

```json
{
  "id": "ft-AF1WoRqd3aJAHsqc9NY7iL8F",
  "object": "fine-tune",
  "model": "curie",
  "created_at": 1614807352,
  "events": [
    {
      "object": "fine-tune-event",
      "created_at": 1614807352,
      "level": "info",
      "message": "Job enqueued. Waiting for jobs ahead to complete. Queue number: 0."
    }
  ],
  "fine_tuned_model": null,
  "hyperparams": {
    "batch_size": 4,
    "learning_rate_multiplier": 0.1,
    "n_epochs": 4,
    "prompt_loss_weight": 0.1,
  },
  "organization_id": "org-...",
  "result_files": [],
  "status": "pending",
  "validation_files": [],
  "training_files": [
    {
      "id": "file-XGinujblHPwGLSztz8cPS8XY",
      "object": "file",
      "bytes": 1547276,
      "created_at": 1610062281,
      "filename": "my-data-train.jsonl",
      "purpose": "fine-tune-train"
    }
  ],
  "updated_at": 1614807352,
}
```

**Request body(入参详解)**

> *   `training_file` （string，必填）
>
>     包含 **训练数据** 的已上传文件的ID。
>
>     请参阅 [upload file](https://link.juejin.cn/?target=https%3A%2F%2Fplatform.openai.com%2Fdocs%2Fapi-reference%2Ffiles%2Fupload) 以了解如何上传文件。
>
>     您的数据集必须格式化为 **JSONL文件**，其中每个训练示例都是一个带有 “prompt” 和 “completion” keys 的 JSON对象。此外，您必须上传带有 `fine-tune` 目的的文件。
>
>     有关更多详细信息，请参阅 微调指南。
> *   `validation_file` （string，选填）
>
>     包含 **验证数据** 的已上传文件的ID。
>
>     如果您提供此文件，则数据将在微调期间定期用于生成验证指标。这些指标可以在 微调结果文件 中查看。您的训练和验证数据应该是互斥的。
>
>     您的数据集必须格式化为 **JSONL文件**，其中每个验证示例都是一个带有 “prompt” 和 “completion” keys 的 JSON对象。此外，您必须上传带有 `fine-tune` 目的的文件。
>
>     有关更多详细信息，请参阅 微调指南。
> *   `model` （string，选填，Defaults to curie）
>
>     要微调的基础模型名称。
>
>     您可以选择其中之一："ada"、"babbage"、"curie"、"davinci"，或 2022年4月21日 后创建的经过微调的模型。要了解这些模型的更多信息，请参阅 Models 文档。
> *   `n_epochs` （integer，选填，Defaults to 4）
>
>     训练模型的时期数。一个 epoch 指的是完整地遍历一次训练数据集
> *   `batch_size` （integer，选填，Defaults to null）
>
>     用于训练的批次大小。批次大小是用于训练单个前向和后向传递的训练示例数量。
>
>     默认情况下，批量大小将动态配置为训练集示例数量的约 0.2％，上限为256。
>
>     通常，我们发现较大的批量大小对于更大的数据集效果更好。
> *   `learning_rate_multiplier` （number，选填，Defaults to null）
>
>     用于训练的学习率倍增器。微调学习率 是预训练时使用的 原始学习率 乘以 此值 得到的。
>
>     默认情况下，学习率的倍增器为 0.05、0.1 或 0.2，具体取决于最终 `batch_size`（较大的批量大小通常使用较大的学习率效果更好）。我们建议尝试在 0.02 到 0.2 范围内实验不同值以找出产生最佳结果的值。
> *   `prompt_loss_weight` （number，选填，Defaults to 0.01）
>
>     用于提示 tokens 损失的权重。这控制了模型尝试学习生成提示的程度（与始终具有 1.0 权重的完成相比），并且可以在完成很短时为训练添加稳定效果。
>
>     如果提示非常长（相对于完成而言），那么减少此权重可能是有意义的，以避免过度优先考虑学习提示。
> *   `compute_classification_metrics` （boolean，选填，Defaults to false）
>
>     如果设置了，我们会在每个 epoch 结束时使用验证集计算特定于分类的指标，例如准确率和 F-1 分数。这些指标可以在 结果文件 中查看。
>
>     为了计算分类指标，您必须提供一个`validation_file(验证文件)`。此外，对于多类分类，您必须指定 `classification_n_classes`；对于二元分类，则需要指定 `classification_positive_class`。
> *   `classification_n_classes` （integer，选填，Defaults to null）
>
>     分类任务中的类别数量。
>
>     这个参数在多分类任务中是必需的。
> *   `classification_positive_class` （string，选填，Defaults to null）
>
>     二元分类中的正类。
>
>     在进行二元分类时，需要此参数来生成精确度、召回率和 F1 指标。
> *   `classification_betas` （array，选填，Defaults to null）
>
>     如果提供了这个参数，我们会在指定的 beta 值上计算 F-beta分数。F-beta分数 是 F-1分数 的一般化。这仅用于二元分类。
>
>     当 beta 为1时（即F-1分数），精确度和召回率被赋予相同的权重。较大的 beta 值更加注重召回率而不是精确度。较小的 beta 值更加注重精确度而不是召回率。
> *   `suffix` （string，选填，Defaults to null）
>
>     一个长度最多为 40个字符 的字符串，将被添加到您的 微调模型名称 中。
>
>     例如，`suffix` 为 “custom-model-name” 会生成一个模型名称，如 `ada:ft-your-org:custom-model-name-2022-02-15-04-21-04`。

#### 2. List fine-tunes

```http
GET
https://api.openai.com/v1/fine-tunes
```

列出组织的微调作业

请求演示：

```http
curl https://api.openai.com/v1/fine-tunes \
  -H "Authorization: Bearer $OPENAI_API_KEY"
```

响应：

```json
{
  "object": "list",
  "data": [
    {
      "id": "ft-AF1WoRqd3aJAHsqc9NY7iL8F",
      "object": "fine-tune",
      "model": "curie",
      "created_at": 1614807352,
      "fine_tuned_model": null,
      "hyperparams": { ... },
      "organization_id": "org-...",
      "result_files": [],
      "status": "pending",
      "validation_files": [],
      "training_files": [ { ... } ],
      "updated_at": 1614807352,
    },
    { ... },
    { ... }
  ]
}
```

#### 3. Retrieve fine-tune.

```http
GET
https://api.openai.com/v1/fine-tunes/{fine_tune_id}
```

获取有关微调作业的信息。

了解更多关于微调的内容。

请求演示：

```http
curl https://api.openai.com/v1/fine-tunes/ft-AF1WoRqd3aJAHsqc9NY7iL8F \
  -H "Authorization: Bearer $OPENAI_API_KEY"
```

其中，`fine_tune_id` 为 string类型 的字符串，**必传**；微调作业的 ID

响应：

```json
{
  "id": "ft-AF1WoRqd3aJAHsqc9NY7iL8F",
  "object": "fine-tune",
  "model": "curie",
  "created_at": 1614807352,
  "events": [
    {
      "object": "fine-tune-event",
      "created_at": 1614807352,
      "level": "info",
      "message": "Job enqueued. Waiting for jobs ahead to complete. Queue number: 0."
    },
    {
      "object": "fine-tune-event",
      "created_at": 1614807356,
      "level": "info",
      "message": "Job started."
    },
    {
      "object": "fine-tune-event",
      "created_at": 1614807861,
      "level": "info",
      "message": "Uploaded snapshot: curie:ft-acmeco-2021-03-03-21-44-20."
    },
    {
      "object": "fine-tune-event",
      "created_at": 1614807864,
      "level": "info",
      "message": "Uploaded result files: file-QQm6ZpqdNwAaVC3aSz5sWwLT."
    },
    {
      "object": "fine-tune-event",
      "created_at": 1614807864,
      "level": "info",
      "message": "Job succeeded."
    }
  ],
  "fine_tuned_model": "curie:ft-acmeco-2021-03-03-21-44-20",
  "hyperparams": {
    "batch_size": 4,
    "learning_rate_multiplier": 0.1,
    "n_epochs": 4,
    "prompt_loss_weight": 0.1,
  },
  "organization_id": "org-...",
  "result_files": [
    {
      "id": "file-QQm6ZpqdNwAaVC3aSz5sWwLT",
      "object": "file",
      "bytes": 81509,
      "created_at": 1614807863,
      "filename": "compiled_results.csv",
      "purpose": "fine-tune-results"
    }
  ],
  "status": "succeeded",
  "validation_files": [],
  "training_files": [
    {
      "id": "file-XGinujblHPwGLSztz8cPS8XY",
      "object": "file",
      "bytes": 1547276,
      "created_at": 1610062281,
      "filename": "my-data-train.jsonl",
      "purpose": "fine-tune-train"
    }
  ],
  "updated_at": 1614807865,
}
```

#### 4. Cancel fine-tune

```http
POST
https://api.openai.com/v1/fine-tunes/{fine_tune_id}/cancel
```

立即取消微调工作。

请求演示：

```http
curl https://api.openai.com/v1/fine-tunes/ft-AF1WoRqd3aJAHsqc9NY7iL8F/cancel \
  -H "Authorization: Bearer $OPENAI_API_KEY"
```

其中，`fine_tune_id` 为 string类型 的字符串，**必传**；微调作业的 ID

响应：

```json
{
  "id": "ft-xhrpBbvVUzYGo8oUO1FY4nI7",
  "object": "fine-tune",
  "model": "curie",
  "created_at": 1614807770,
  "events": [ { ... } ],
  "fine_tuned_model": null,
  "hyperparams": { ... },
  "organization_id": "org-...",
  "result_files": [],
  "status": "cancelled",
  "validation_files": [],
  "training_files": [
    {
      "id": "file-XGinujblHPwGLSztz8cPS8XY",
      "object": "file",
      "bytes": 1547276,
      "created_at": 1610062281,
      "filename": "my-data-train.jsonl",
      "purpose": "fine-tune-train"
    }
  ],
  "updated_at": 1614807789,
}
```

#### 5. List fine-tune events

```http
GET
https://api.openai.com/v1/fine-tunes/{fine_tune_id}/events
```

获取微调作业的精细状态更新。

请求演示：

```http
curl https://api.openai.com/v1/fine-tunes/ft-AF1WoRqd3aJAHsqc9NY7iL8F/events \
  -H "Authorization: Bearer $OPENAI_API_KEY"
```

其中，`fine_tune_id` 为 string类型 的字符串，**必传**；微调作业的 ID；

响应：

```json
{
  "object": "list",
  "data": [
    {
      "object": "fine-tune-event",
      "created_at": 1614807352,
      "level": "info",
      "message": "Job enqueued. Waiting for jobs ahead to complete. Queue number: 0."
    },
    {
      "object": "fine-tune-event",
      "created_at": 1614807356,
      "level": "info",
      "message": "Job started."
    },
    {
      "object": "fine-tune-event",
      "created_at": 1614807861,
      "level": "info",
      "message": "Uploaded snapshot: curie:ft-acmeco-2021-03-03-21-44-20."
    },
    {
      "object": "fine-tune-event",
      "created_at": 1614807864,
      "level": "info",
      "message": "Uploaded result files: file-QQm6ZpqdNwAaVC3aSz5sWwLT."
    },
    {
      "object": "fine-tune-event",
      "created_at": 1614807864,
      "level": "info",
      "message": "Job succeeded."
    }
  ]
}
```

**Query parameters**

> *   `stream` （boolean，选填，Defaults to false）
>
>     是否对微调作业进行事件流。
>
>     如果设置为 true，则事件将作为数据 服务器发送的事件 随时可用。当作业完成（成功、取消或失败）时，流将以 `data：[DONE]` 消息终止。
>
>     如果设置为 false，则仅返回到目前为止生成的事件。

#### 6. Delete fine-tune model

```http
DELETE
https://api.openai.com/v1/models/{model}
```

删除微调的模型。您必须在组织中具有所有者的角色。

请求演示：

```http
curl https://api.openai.com/v1/models/curie:ft-acmeco-2021-03-03-21-44-20 \
  -X DELETE \
  -H "Authorization: Bearer $OPENAI_API_KEY"
```

其中，`model` 为 string类型 的字符串，**必传**；要删除的模型

响应：

```json
{
  "id": "curie:ft-acmeco-2021-03-03-21-44-20",
  "object": "model",
  "deleted": true
}
```

### 十三. Moderations 审核

给定一个输入文本，输出模型是否将其分类为违反 OpenAI 内容政策。

相关指南：Moderations

#### 1. Create moderation

```http
POST
https://api.openai.com/v1/moderations
```

分类判断文本是否违反 OpenAI 的内容政策

请求演示：

```http
curl https://api.openai.com/v1/moderations \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -d '{
    "input": "I want to kill them."
  }'
```

响应：

```json
{
  "id": "modr-5MWoLO",
  "model": "text-moderation-001",
  "results": [
    {
      "categories": {
        "hate": false,
        "hate/threatening": true,
        "self-harm": false,
        "sexual": false,
        "sexual/minors": false,
        "violence": true,
        "violence/graphic": false
      },
      "category_scores": {
        "hate": 0.22714105248451233,
        "hate/threatening": 0.4132447838783264,
        "self-harm": 0.005232391878962517,
        "sexual": 0.01407341007143259,
        "sexual/minors": 0.0038522258400917053,
        "violence": 0.9223177433013916,
        "violence/graphic": 0.036865197122097015
      },
      "flagged": true
    }
  ]
}
```

**Request body(入参详解)**

> *   `input` （string or array，必填）
>
>     要分类的输入文本
> *   `model` （string，选填，Defaults to text-moderation-latest）
>
>     有两个内容审核模型可用：`text-moderation-stable` 和 `text-moderation-latest`。
>
>     默认情况下，使用的是 `text-moderation-latest` 模型，该模型会随着时间自动升级。这确保您始终使用我们最准确的模型。如果您使用 `text-moderation-stable`，则在更新模型之前我们将提供高级通知。`text-moderation-stable` 的准确性可能略低于 `text-moderation-latest`。

### 十四. Engines 引擎

> **引擎端点已过时。** 请使用它们的替代品模型。了解更多信息。

这些端点描述并提供了 API 中可用的各种引擎的访问。

#### 1. ~~List engines~~ <荒废的>

```http
GET
https://api.openai.com/v1/engines
```

列出当前可用的（未经微调的）模型，并提供有关每个模型的基本信息，例如所有者和可用性。

请求演示：

```http
curl https://api.openai.com/v1/engines \
  -H "Authorization: Bearer $OPENAI_API_KEY"
```

响应：

```json
{
  "data": [
    {
      "id": "engine-id-0",
      "object": "engine",
      "owner": "organization-owner",
      "ready": true
    },
    {
      "id": "engine-id-2",
      "object": "engine",
      "owner": "organization-owner",
      "ready": true
    },
    {
      "id": "engine-id-3",
      "object": "engine",
      "owner": "openai",
      "ready": false
    },
  ],
  "object": "list"
}
```

#### 2. ~~Retrieve engine~~ <荒废的>

```http
GET
https://api.openai.com/v1/engines/{engine_id}
```

检索模型实例，提供基本信息，如 所有者 和 可用性。

请求演示：

```http
curl https://api.openai.com/v1/engines/text-davinci-003 \
  -H "Authorization: Bearer $OPENAI_API_KEY"
```

其中，`engine_id` 为 string类型 的字符串，**必传**；用于此请求的引擎ID。

响应：

```json
{
  "id": "text-davinci-003",
  "object": "engine",
  "owner": "openai",
  "ready": true
}
```

### 十五. Parameter details 参数细节

**频率和存在惩罚**

在 Completions API 中发现的频率和存在惩罚可以用于减少采样重复令牌序列的可能性。它们通过直接向 logits（未归一化的对数概率）添加贡献来进行修改。

```python
mu[j] -> mu[j] - c[j] * alpha_frequency - float(c[j] > 0) * alpha_presence
```

Where:

* `mu[j]` is the logits of the j-th token
* `c[j]` is how often that token was sampled prior to the current position
* `float(c[j] > 0)` is 1 if `c[j] > 0` and 0 otherwise
* `alpha_frequency` is the frequency penalty coefficient
* `alpha_presence` is the presence penalty coefficient

正如我们所看到的，存在惩罚是一次性的加法贡献，适用于所有已经被采样至少一次的标记，并且频率惩罚是与特定标记已经被采样的频率成比例的贡献。

如果目的只是稍微减少重复样本，那么惩罚系数的合理值大约在 0.1 到 1 之间。如果目的是强烈抑制重复，则可以将系数增加到 2，但这可能会明显降低样本质量。负值可用于增加重复出现的可能性。
