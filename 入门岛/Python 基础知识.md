
## Leetcode 383

```python
class Solution:
    def canConstruct(self, ransomNote: str, magazine: str) -> bool:
        # return Counter(ransomNote) <= Counter(magazine)

        ransomCounter_ransom = Counter(ransomNote)
        magazine_Counter = Counter(magazine)
        return all(ransomCounter_ransom[char] <= magazine_Counter[char] for char in ransomNote)
```

截图如下：
![](./pics/Leetcode%20383.jpg)


## Vscode连接InternStudio debug笔记

安装`openai`
```shell
pip install openai
```

原始代码报错：
```shell
The above exception was the direct cause of the following exception:

  File "/root/demo.py", line 11, in internlm_gen
    response = client.chat.completions.create(
               ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "/root/demo.py", line 32, in <module>
    res = internlm_gen(prompt,client)
          ^^^^^^^^^^^^^^^^^^^^^^^^^^^
openai.APIConnectionError: Connection error.
```

基于环境变量配置`api key`之后，遇到报错：
```shell
During handling of the above exception, another exception occurred:

  File "/root/demo.py", line 33, in <module>
    res_json = json.loads(res)
               ^^^^^^^^^^^^^^^
json.decoder.JSONDecodeError: Expecting value: line 1 column 1 (char 0)
```

发现变量`res`的值为'```json\n{\n  "model_name": "书生浦语InternLM2.5",\n  "development_institution": "上海人工智能实验室",\n  "parameter_versions": [1.8B, 7B, 20B],\n  "context_length": "1M"\n}\n```'，模型生成的字符串的格式并非总是正确的。完整代码如下：

```python
from openai import OpenAI
import json
import os

def internlm_gen(prompt,client):
    '''
    LLM生成函数
    Param prompt: prompt string
    Param client: OpenAI client 
    '''
    response = client.chat.completions.create(
        model="internlm2.5-latest",
        messages=[
            {"role": "user", "content": prompt},
      ],
        stream=False
    )
    return response.choices[0].message.content

# api_key = ''
api_key = os.getenv('api_key')
client = OpenAI(base_url="https://internlm-chat.intern-ai.org.cn/puyu/api/v1/",api_key=api_key)

content = """
书生浦语InternLM2.5是上海人工智能实验室于2024年7月推出的新一代大语言模型，提供1.8B、7B和20B三种参数版本，以适应不同需求。
该模型在复杂场景下的推理能力得到全面增强，支持1M超长上下文，能自主进行互联网搜索并整合信息。
"""
prompt = f"""
请帮我从以下``内的这段模型介绍文字中提取关于该模型的信息，要求包含模型名字、开发机构、提供参数版本、上下文长度四个内容，以json格式返回。注意正确使用引号。
`{content}`
"""
res = internlm_gen(prompt,client)
res = res[8:-4].replace('\n', '')
try:
	res_json = json.loads(res)
	print(res_json)
except json.JSONDecodeError as e:
    print(e)
```

<!-- ## pip安装到指定目录

```shell
mkdir -p /root/myenvs
pip install numpy --target /root/myenvs

``` -->