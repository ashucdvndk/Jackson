# 06-LangChain入门-快速入门和Agent原理

**案例：天气查询智能体**

需求：开发一个智能体，可以帮助用户查询天气信息

**准备工作**

首先，要使用LangChain必须先安装依赖，命令如下：

```
uv add langchain
```

LangChain支持各种不同的模型，而且提供了对应的兼容SDK，不过也都需要安装对应依赖，你可以按需添加：

```
# 集成 DeepSeek
uv add langchain-deepseek

# 集成 OpenAI
uv add langchain-openai

# 集成 Anthropic
uv add langchain-anthropic
```

**代码示例**

Agent开发的基本步骤：

```
1. 加载环境变量
2. 定义工具
3.定义Agent
4. 调用Agent
```

**代码如下**

```python
# 1.加载环境变量
from dotenv import load_dotenv

load_dotenv()

# 2.定义工具，基础版，通过注释描述工具
@tool
def getWeather(location: str) -> str:
    """
    Get the weather in a given location.
    Args:
        location: city name or coordinates
    """
    return f"Current weather in {location} is sunny"
    

# 3.定义Agent
agent = create_agent(
    "deepseek-chat", # 模型名称（必须是LangChain支持的模型）
    tools=[getWeather] # 工具集
)

# 4.调用模型
print("🚀 正在调用大模型...")
response = agent.invoke({
    "messages": [
        {"role": "user", "content": "杭州今天天气如何?"}
    ]
})

# 5.打印结果
print(response)
```

**运行结果**

```
{'messages': [HumanMessage(content='西安今天天气如何?', additional_kwargs={}, response_metadata={}, id='3701e47b-25c5-45e8-84c7-cbf00e86782c'), AIMessage(content="I'll check the weather in Xi'an for you.", additional_kwargs={'refusal': None}, response_metadata={'token_usage': {'completion_tokens': 47, 'prompt_tokens': 282, 'total_tokens': 329, 'completion_tokens_details': None, 'prompt_tokens_details': {'audio_tokens': None, 'cache_write_tokens': None, 'cached_tokens': 0, 'image_tokens': None, 'text_tokens': None}, 'prompt_cache_hit_tokens': 0, 'prompt_cache_miss_tokens': 282}, 'model_provider': 'deepseek', 'model_name': 'deepseek-flash', 'system_fingerprint': 'e72d3c7f663d3e1383b00b3884cb4501', 'id': 'ceca3217-f0c5-4ed8-be18-6f999ee8441d', 'finish_reason': 'tool_calls', 'logprobs': None}, id='lc_run--01a171eb-594b-7492-8122-b1324c015393-0', tool_calls=[{'name': 'getWeather', 'args': {'location': '西安'}, 'id': 'call_00_SnxIwoTiQmsdySoz7DiZ3423', 'type': 'tool_call'}], invalid_tool_calls=[], usage_metadata={'input_tokens': 282, 'output_tokens': 47, 'total_tokens': 329, 'input_token_details': {'cache_read': 0}, 'output_token_details': {}}), ToolMessage(content='Current weather in 西安 is sunny', name='getWeather', id='24434091-9c35-4ae8-aba8-ff3e60000e94', tool_call_id='call_00_SnxIwoTiQmsdySoz7DiZ3423'), AIMessage(content='西安今天天气是**晴天** ☀️\n\n目前查询到的信息只有天气状况（晴），暂时没有气温、湿度或风力等详细数据。如果你需要更具体的信息，可以告诉我，我再帮你看看。', additional_kwargs={'refusal': None}, response_metadata={'token_usage': {'completion_tokens': 45, 'prompt_tokens': 348, 'total_tokens': 393, 'completion_tokens_details': None, 'prompt_tokens_details': {'audio_tokens': None, 'cache_write_tokens': None, 'cached_tokens': 128, 'image_tokens': None, 'text_tokens': None}, 'prompt_cache_hit_tokens': 128, 'prompt_cache_miss_tokens': 220}, 'model_provider': 'deepseek', 'model_name': 'deepseek-flash', 'system_fingerprint': 'e72d3c7f663d3e1383b00b3884cb4501', 'id': '1d0f70ee-7822-434f-a35b-94d257505c6b', 'finish_reason': 'stop', 'logprobs': None}, id='lc_run--01a171eb-5cc3-7439-a9af-f25b8088bbad-0', tool_calls=[], invalid_tool_calls=[], usage_metadata={'input_tokens': 348, 'output_tokens': 45, 'total_tokens': 393, 'input_token_details': {'cache_read': 128}, 'output_token_details': {}})]}
```

为了运行结果更加可视化，添加如下代码进行类似渲染的作用

```
for message in responses["messages"]:
    print(message.model_dump_json(indent=2))
    print("--------------------------------")
```

**最终的运行结果**

```
{
  "content": "西安今天天气如何?",
  "additional_kwargs": {},
  "response_metadata": {},
  "type": "human",
  "name": null,
  "id": "3701e47b-25c5-45e8-84c7-cbf00e86782c"
}
--------------------------------
{
  "content": "I'll check the weather in Xi'an for you.",
  "additional_kwargs": {
    "refusal": null
  },
  "response_metadata": {
    "token_usage": {
      "completion_tokens": 47,
      "prompt_tokens": 282,
      "total_tokens": 329,
      "completion_tokens_details": null,
      "prompt_tokens_details": {
        "audio_tokens": null,
        "cache_write_tokens": null,
        "cached_tokens": 0,
        "image_tokens": null,
        "text_tokens": null
      },
      "prompt_cache_hit_tokens": 0,
      "prompt_cache_miss_tokens": 282
    },
    "model_provider": "deepseek",
    "model_name": "deepseek-flash",
    "system_fingerprint": "e72d3c7f663d3e1383b00b3884cb4501",
    "id": "ceca3217-f0c5-4ed8-be18-6f999ee8441d",
    "finish_reason": "tool_calls",
    "logprobs": null
  },
  "type": "ai",
  "name": null,
  "id": "lc_run--01a171eb-594b-7492-8122-b1324c015393-0",
  "tool_calls": [
    {
      "name": "getWeather",
      "args": {
        "location": "西安"
      },
      "id": "call_00_SnxIwoTiQmsdySoz7DiZ3423",
      "type": "tool_call"
    }
  ],
  "invalid_tool_calls": [],
  "usage_metadata": {
    "input_tokens": 282,
    "output_tokens": 47,
    "total_tokens": 329,
    "input_token_details": {
      "cache_read": 0
    },
    "output_token_details": {}
  }
}
--------------------------------
{
  "content": "Current weather in 西安 is sunny",
  "additional_kwargs": {},
  "response_metadata": {},
  "type": "tool",
  "name": "getWeather",
  "id": "24434091-9c35-4ae8-aba8-ff3e60000e94",
  "tool_call_id": "call_00_SnxIwoTiQmsdySoz7DiZ3423",
  "artifact": null,
  "status": "success"
}
--------------------------------
{
  "content": "西安今天天气是**晴天** ☀️\n\n目前查询到的信息只有天气状况（晴），暂时没有气温、湿度或风力等详细数据。如果你需要更具体的信息，可以告诉我，我再帮你看看。",
  "additional_kwargs": {
    "refusal": null
  },
  "response_metadata": {
    "token_usage": {
      "completion_tokens": 45,
      "prompt_tokens": 348,
      "total_tokens": 393,
      "completion_tokens_details": null,
      "prompt_tokens_details": {
        "audio_tokens": null,
        "cache_write_tokens": null,
        "cached_tokens": 128,
        "image_tokens": null,
        "text_tokens": null
      },
      "prompt_cache_hit_tokens": 128,
      "prompt_cache_miss_tokens": 220
    },
    "model_provider": "deepseek",
    "model_name": "deepseek-flash",
    "system_fingerprint": "e72d3c7f663d3e1383b00b3884cb4501",
    "id": "1d0f70ee-7822-434f-a35b-94d257505c6b",
    "finish_reason": "stop",
    "logprobs": null
  },
  "type": "ai",
  "name": null,
  "id": "lc_run--01a171eb-5cc3-7439-a9af-f25b8088bbad-0",
  "tool_calls": [],
  "invalid_tool_calls": [],
  "usage_metadata": {
    "input_tokens": 348,
    "output_tokens": 45,
    "total_tokens": 393,
    "input_token_details": {
      "cache_read": 128
    },
    "output_token_details": {}
  }
}
--------------------------------
```

LLM = 聪明的大脑

Agent = 聪明的大脑 + 手脚



原本大模型不具备查询天气的功能，所以无法回答天气问题，但是当我们提供一个查询天气的Tool之后，就可以自动查询天气来回答用户的问题。



传统的LLM应用都是一问一答的形式，模型只能根据自己的训练数据来回答，流程很简单

![image-20261025160248646](C:\Users\20840\AppData\Roaming\Typora\typora-user-images\image-20261025160248646.png)

而Agent则可以调用工具与外界进行交互，获取实时信息，流程自然会变得复杂一些：

![image-20261025160352599](C:\Users\20840\AppData\Roaming\Typora\typora-user-images\image-20261025160352599.png)

流程如下：

1. 用户提问：西安今天天气如何？
2. 模型分析：用户查询西安天气，我不知道，需要调用查询天气的工具getWeather
3. 调用工具：调用工具，getWeather，传入城市"西安"
4. 分析结果：工具将查询到的结果返回给模型model，判断是否足以回复用户的问题

- 是：整理并生成响应结果
- 否：重复前面的流程

5. 生成结果：根据工具的结果生成响应给用户



那么，问题来了，模型是如何知道工具的信息呢？

在大模型提供的API接口中，有一个tools参数，描述了工具的详细信息：

![image-20261025161140760](C:\Users\20840\AppData\Roaming\Typora\typora-user-images\image-20261025161140760.png)

所以，LangChain会把tool的信息封装为此tool参数，与message一起发送给大模型，大模型就了解了tool的详细信息，根据用户需求判断是否需要调用tool，需要调用哪个tool



那么，当大模型决定调用某一个tool的时候，该如何进行调用？（大模型和我们自己定义的tool是独立的）

模型的确不能直接调用tool，只能返回字符串。但是它可以把要调用的tool信息、参数信息都以Json格式返回：

![image-20261025161522711](C:\Users\20840\AppData\Roaming\Typora\typora-user-images\image-20261025161522711.png)

这样的话，LangChain就会帮助我们解析响应结果中的Function信息，也就是tool信息，就知道了要调用哪个函数，以及参数是什么。LangChain就会执行该函数，再把得到的结果再次发送给大模型。

具体的流程图如下：

![image-20261025161752365](C:\Users\20840\AppData\Roaming\Typora\typora-user-images\image-20261025161752365.png)