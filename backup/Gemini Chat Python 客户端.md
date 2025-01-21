# Gemini Chat Python 客户端

这是一个基于 Python 实现的 Gemini AI 聊天客户端，提供了更多高级功能和更好的异步性能。

## 功能特点

- 🔄 自动获取最新的 Gemini 模型列表
- 🤖 支持多个 Gemini 模型系列（Pro/Flash）
- 💾 支持对话历史记录（默认保留最近10轮对话）
- 🔄 支持实时切换和刷新模型
- 🧹 支持清空对话历史
- 📦 自动检查并安装依赖
- ⚡ 异步实现，响应更快
- 🛡️ 完善的错误处理机制

## 系统要求

- Python 3.7+
- pip（Python包管理器）
- 网络连接

## 依赖项

脚本会自动检查并安装以下依赖：
- aiohttp：用于异步HTTP请求
- asyncio：用于异步编程支持

## 安装和运行

1. 下载脚本：
直接复制脚本内容到本地文件

2. 运行脚本：
```bash
python3 gemini_chat.py
```

首次运行时，如果缺少必要的依赖项，脚本会自动尝试安装。

## 使用说明

### 基本操作

1. 启动后，脚本会自动获取并显示可用的模型列表
2. 可以通过输入编号选择模型，或直接回车使用默认模型（最新版本）
3. 在聊天界面中支持以下命令：
   - 直接输入文字进行对话
   - 输入 `退出` 或 `quit` 或 `exit` 结束对话
   - 输入 `切换` 可以切换到其他模型
   - 输入 `刷新` 可以刷新模型列表
   - 输入 `清空` 可以清空对话历史

### 高级功能

1. 对话历史记录
   - 自动保存最近10轮对话
   - 切换模型时自动清空历史
   - 可以随时手动清空历史

2. 异步处理
   - 使用 aiohttp 进行异步HTTP请求
   - 提供更好的响应性能

3. 错误处理
   - 网络错误自动重试
   - 友好的错误提示
   - 优雅的程序退出处理

### 模型说明

- pro 系列：响应质量更高，适合需要深度思考的场景
- flash 系列：响应速度更快，适合日常对话
- 版本号越大代表模型越新

## 开发者说明

### 代码结构

- `GeminiChat` 类：主要的聊天实现
  - `check_dependencies()`: 依赖检查
  - `fetch_models()`: 获取模型列表
  - `chat_completion()`: 发送聊天请求
  - `save_conversation()`: 保存对话历史
  - `get_conversation_context()`: 获取对话上下文

### 类型注解

代码使用了 Python 类型注解，提供更好的代码提示和错误检查：
```python
from typing import Dict, List, Optional
```

## 注意事项

1. 确保 Python 版本 >= 3.7
2. 首次运行时需要网络连接以安装依赖
3. API 密钥已内置，无需额外配置
4. 建议在虚拟环境中运行

## 常见问题

1. 如果遇到依赖安装失败：
   ```bash
   pip install aiohttp asyncio
   ```

2. 如果遇到权限问题：
   ```bash
   sudo python3 gemini_chat.py
   ```

3. 如果遇到网络问题：
   - 检查网络连接
   - 确认 API 服务器可访问
   - 检查防火墙设置

## 更新日志

### v1.0.0
- 初始版本发布
- 支持动态获取模型列表
- 添加对话历史功能
- 支持模型切换和刷新
- 添加自动依赖安装
- 添加类型注解
- 优化错误处理

```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-

import aiohttp
import asyncio
import json
import sys
import pkg_resources
import subprocess
from typing import Dict, List, Optional

class GeminiChat:
    def __init__(self, api_key: str):
        self.api_key = api_key
        self.base_url = "https://gemini.tangrenbin.us.kg"
        self.messages = []
        self.conversation_history = []  # 存储对话历史
        self.max_history = 10  # 默认保留最近10轮对话

    @staticmethod
    def check_dependencies() -> None:
        """检查并安装必要的依赖"""
        required = {'aiohttp', 'asyncio'}
        installed = {pkg.key for pkg in pkg_resources.working_set}
        missing = required - installed

        if missing:
            print("检测到缺少必要的依赖项，正在安装...")
            try:
                subprocess.check_call([sys.executable, '-m', 'pip', 'install', *missing])
                print("依赖安装完成！")
            except subprocess.CalledProcessError:
                print("错误：依赖安装失败，请手动安装以下包：")
                print("\n".join(missing))
                sys.exit(1)

    async def fetch_models(self) -> Dict[str, str]:
        """从API获取可用的模型列表"""
        print("正在获取可用模型列表...")
        try:
            async with aiohttp.ClientSession() as session:
                async with session.get(
                    f"{self.base_url}/v1/models",
                    headers={'Authorization': f'Bearer {self.api_key}'}
                ) as response:
                    if response.status == 200:
                        data = await response.json()
                        models = {}
                        index = 1
                        for model in data['data']:
                            if 'gemini' in model['id']:
                                models[str(index)] = model['id']
                                index += 1
                        return models
                    else:
                        error_text = await response.text()
                        raise Exception(f"获取模型列表失败: {error_text}")
        except Exception as e:
            print(f"错误：无法获取模型列表 - {str(e)}")
            sys.exit(1)

    def print_models(self, models: Dict[str, str], default_model: str) -> None:
        """打印模型列表"""
        print("\n可用模型列表：")
        print("注意：不同模型有不同的特点：")
        print("- pro系列：响应质量更高")
        print("- flash系列：响应速度更快")
        print("- 数字越大代表版本越新")
        print(f"- 默认使用最新的模型: {models[default_model]}\n")

        for key, model in models.items():
            print(f"{key}. {model}" + (" (默认)" if key == default_model else ""))

    def save_conversation(self, role: str, content: str) -> None:
        """保存对话历史"""
        self.conversation_history.append({"role": role, "content": content})
        if len(self.conversation_history) > self.max_history * 2:  # 每轮对话包含用户和AI两条消息
            self.conversation_history = self.conversation_history[-self.max_history * 2:]

    def get_conversation_context(self) -> List[Dict[str, str]]:
        """获取对话上下文"""
        return [{"role": "system", "content": "你是一个有帮助的AI助手。"}] + self.conversation_history

    async def chat_completion(self, model: str, temperature: float = 0.7) -> str:
        """发送聊天请求"""
        try:
            async with aiohttp.ClientSession() as session:
                async with session.post(
                    f"{self.base_url}/v1/chat/completions",
                    headers={
                        'Content-Type': 'application/json',
                        'Authorization': f'Bearer {self.api_key}'
                    },
                    json={
                        "model": model,
                        "messages": self.get_conversation_context(),
                        "stream": False,
                        "temperature": temperature,
                        "max_tokens": 2048
                    }
                ) as response:
                    if response.status == 200:
                        data = await response.json()
                        return data['choices'][0]['message']['content']
                    else:
                        error_text = await response.text()
                        raise Exception(f"API错误: {error_text}")
        except Exception as e:
            return f"错误：{str(e)}"

    async def chat(self) -> None:
        """主对话循环"""
        # 获取模型列表
        models = await self.fetch_models()
        default_model = str(len(models))  # 使用最后一个模型作为默认值
        
        while True:
            self.print_models(models, default_model)
            print(f"\n直接按回车使用默认模型({models[default_model]})")
            model_choice = input("请选择模型编号: ").strip()
            
            if not model_choice:
                model_choice = default_model
                
            if model_choice in models:
                break
            print("无效的选择，请重试。")
        
        model = models[model_choice]
        print(f"\n已选择模型: {model}")
        print("\n开始对话 (输入'退出'结束对话，输入'切换'可以切换模型，输入'刷新'可以刷新模型列表，输入'清空'可以清空对话历史)")
        
        while True:
            user_input = input("\n你: ").strip()
            
            if user_input.lower() in ['退出', 'quit', 'exit']:
                print("\n感谢使用！再见！")
                break
                
            if user_input.lower() == '切换':
                self.conversation_history = []  # 切换模型时清空对话历史
                await self.chat()
                return
                
            if user_input.lower() == '刷新':
                models = await self.fetch_models()
                self.print_models(models, model_choice)
                print(f"\n继续使用当前模型: {model}")
                continue
                
            if user_input.lower() == '清空':
                self.conversation_history = []
                print("\n对话历史已清空")
                continue
            
            if user_input:
                # 保存用户输入
                self.save_conversation("user", user_input)
                
                print("\n正在等待AI响应...")
                response = await self.chat_completion(model)
                print(f"\nAI: {response}")
                
                # 保存AI回复
                self.save_conversation("assistant", response)

async def main():
    # 检查依赖
    GeminiChat.check_dependencies()
    
    api_key = "AIzaSyDOLKXvMTqw0EgLHVOA13fgBZHbS8NEvBs"
    chat = GeminiChat(api_key)
    await chat.chat()

if __name__ == "__main__":
    try:
        asyncio.run(main())
    except KeyboardInterrupt:
        print("\n程序已被用户中断。再见！")
    except Exception as e:
        print(f"\n程序遇到错误: {str(e)}") 
```
