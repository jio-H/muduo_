# MiniCPM-V 4.0 - SGLang

## 1. SGLang安装
### SGLang源码安装
```bash
git clone https://github.com/sgl-project/sglang.git
cd sglang

pip install --upgrade pip
pip install -e "python[all]"
```

### flashinfer依赖安装

方法1:pip 安装（网速可能不行）

```cpp
pip install flashinfer -i https://flashinfer.ai/whl/cu121/torch2.4/
```

方法2: whl文件安装

- 网页访问：`https://flashinfer.ai/whl/cu121/torch2.4/flashinfer/`
- 找到适合自己服务器的版本并且下载whl文件:例如`flashinfer-0.1.6+cu121torch2.4-cp310-cp310-linux_x86_64.whl`
- 使用`pip install ***.whl`进行安装，比如:
  
    ```cpp
    pip install flashinfer-0.1.6+cu121torch2.4-cp310-cp310-linux_x86_64.whl
    ```
如果在安装过程中遇到问题，请随时查阅[官方安装文档](https://docs.sglang.ai/start/install.html)

## 2. sglang发起推理服务
--model 后面可以改为本地的minicpm3地址
如果出现out of memory，加上参数--disable-cuda-graph再试
```cpp
CUDA_VISIBLE_DEVICES=1,2 python -m sglang.launch_server --model-path /cache/huwenhao/models/MiniCPM-V4 --port 30000 --device cuda --trust-remote-code
```

## 3. 调用服务接口
- bash调用
    ```python
    curl -s http://localhost:30000/v1/chat/completions \
    -H "Content-Type: application/json" \
    -d '{
        "model": "MiniCPM-V_4.0",
        "messages": [
        {
            "role": "user",
            "content": [
            {
                "type": "text",
                "text": "What’s in this image?"
            },
            {
                "type": "image_url",
                "image_url": {
                "url": "https://github.com/sgl-project/sglang/blob/main/test/lang/example_image.png?raw=true"
                }
            }
            ]
        }
        ],
        "max_tokens": 300
    }'
    ```

- python调用
    ```python
        from openai import OpenAI

    client = OpenAI(base_url=f"http://localhost:30000/v1", api_key="None")

    response = client.chat.completions.create(
        model="MiniCPM-V_4.0",
        messages=[
            {
                "role": "user",
                "content": [
                    {
                        "type": "text",
                        "text": "What is in this image?",
                    },
                    {
                        "type": "image_url",
                        "image_url": {
                            "url": "/cache/huwenhao/example_images/example_image.png",
                        },
                    },
                ],
            }
        ],
        max_tokens=300,
    )

    print(response.choices[0].message.content)
    ```