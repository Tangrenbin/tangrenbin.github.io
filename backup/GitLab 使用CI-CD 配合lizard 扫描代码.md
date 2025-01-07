```yml
stages:
  - quality

code_complexity:
  stage: quality
  tags:
    - linux
  variables:
    GIT_DEPTH: 0
    GIT_STRATEGY: clone
    CCN_THRESHOLD: 10      # 圈复杂度阈值
    LENGTH_THRESHOLD: 50   # 函数长度阈值
    PARAM_THRESHOLD: 4     # 参数个数阈值

  script:
    - |
      # 创建空的报告文件
      echo "[]" > complexity_report.json

      # 提取变更文件
      if [ -n "$CI_MERGE_REQUEST_ID" ]; then
        echo "合并请求检测，提取变更文件..."
        git fetch origin "$CI_MERGE_REQUEST_TARGET_BRANCH_NAME" --depth=50
        changed_files=$(git diff --name-only FETCH_HEAD "$CI_COMMIT_SHA" | grep -E '\.(c|h)$' || true)
      else
        echo "直接提交检测，提取最近一次提交变更文件..."
        changed_files=$(git diff --name-only HEAD~1 HEAD | grep -E '\.(c|h)$' || true)
      fi

      # 检查变更文件并运行复杂度检查
      if [ -z "$changed_files" ]; then
        echo "未检测到变更文件，跳过复杂度检查"
        exit 0
      else
        echo "检测到以下变更文件："
        echo "$changed_files"

        # 运行 lizard 检查
        lizard \
          --CCN $CCN_THRESHOLD \
          --length $LENGTH_THRESHOLD \
          --arguments $PARAM_THRESHOLD \
          --warnings_only \
          --modified \
          $changed_files | tee complexity_report.txt

        # 检查是否有超过阈值的函数
        if [ -s complexity_report.txt ]; then
          echo "发现以下复杂度问题："
          cat complexity_report.txt
          echo "建议:"
          echo "1. 考虑将复杂的逻辑拆分为多个子函数"
          echo "2. 使用策略模式或状态模式简化条件判断"
          echo "3. 减少嵌套的条件语句"
          echo "4. 考虑使用结构体封装参数"
          echo "5. 检查是否有可以合并或省略的参数"
          exit 1
        else
          echo "代码复杂度检查通过！"
        fi
      fi

  artifacts:
    when: always
    paths:
      - complexity_report.txt
      - complexity_report.json
    expire_in: 1 week

  rules:
    - if: $CI_PIPELINE_SOURCE == "merge_request_event"
    - if: $CI_COMMIT_BRANCH == $CI_DEFAULT_BRANCH

  allow_failure: false

```

# 遇到的问题
1. 老是在容器环境和宿主环境中乱跳