1. gemini 配置

```json
{
  "Providers": [
    {
      "name": "gemini",
      "api_base_url": "https://generativelanguage.googleapis.com/v1beta/models/",
      "api_key": "AIzaSyDOLKXvMTqw0EgLHVOA13fgBZHbS8NEvBs",
      "models": [
        "gemini-2.5-flash",
        "gemini-2.5-pro"
      ],
      "transformer": {
        "use": [
          "gemini"
        ]
      }
    }
  ],
  "Router": {
    "default": "gemini,gemini-2.5-flash",
    "think": "gemini,gemini-2.5-pro"
  }
}
```

2.Qwen3-Coder 配置
``` json
{
  "Providers": [
    {
      "name": "modelscope",
      "api_base_url": "https://api-inference.modelscope.cn/v1/chat/completions",
      "api_key": "ms-6e7a0ca9-0e2c-484c-b867-1aea38b38a13",
      "models": [
        "Qwen/Qwen3-Coder-480B-A35B-Instruct",
        "Qwen/Qwen3-235B-A22B-Thinking-2507"
      ],
      "transformer": {
        "use": [
          [
            "maxtoken",
            {
              "max_tokens": 65536
            }
          ],
          "enhancetool"
        ],
        "Qwen/Qwen3-235B-A22B-Thinking-2507": {
          "use": [
            "reasoning"
          ]
        }
      }
    }
  ],
  "Router": {
    "default": "modelscope,Qwen/Qwen3-Coder-480B-A35B-Instruct",
    "think": "modelscope,Qwen/Qwen3-235B-A22B-Thinking-2507"
  }
}
```
