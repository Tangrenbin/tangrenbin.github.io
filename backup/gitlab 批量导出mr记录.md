```python
import os
import requests
import pandas as pd
from datetime import datetime, timedelta
import logging

# 配置
BASE_URL = "http://172.17.0.100:8080/api/v4"
PRIVATE_TOKEN = "h_8duuSiAJsP4Kd-uEyn"
USERNAME = "kanghongyan"  # 替换为实际的用户名
START_DATE = "2024-01-01"
END_DATE = "2025-01-01"
OUTPUT_FILE_CSV = "merge_requests_report.csv"
OUTPUT_FILE_EXCEL = "merge_requests_report.xlsx"

# 初始化日志
logging.basicConfig(level=logging.INFO, format="%(asctime)s - %(levelname)s - %(message)s")

# 请求头
HEADERS = {"PRIVATE-TOKEN": PRIVATE_TOKEN}

# 获取用户 ID
def fetch_author_id(username):
    url = f"{BASE_URL}/users"
    params = {"username": username}
    try:
        response = requests.get(url, headers=HEADERS, params=params)
        response.raise_for_status()
        users = response.json()
        if users:
            return users[0]["id"]
        else:
            logging.error(f"未找到用户名 {username} 对应的用户。")
            return None
    except requests.exceptions.RequestException as e:
        logging.error(f"获取用户 ID 失败: {e}")
        return None

# 获取用户的 MR 列表
def fetch_merge_requests(author_id, start_date, end_date):
    url = f"{BASE_URL}/merge_requests"
    params = {
        "author_id": author_id,
        "created_after": start_date,
        "created_before": end_date,
        "state": "merged",
        "per_page": 100,
        "page": 1,
    }
    results = []
    try:
        while True:
            response = requests.get(url, headers=HEADERS, params=params)
            response.raise_for_status()
            data = response.json()
            if not data:
                break
            results.extend(data)
            params["page"] += 1
        return results
    except requests.exceptions.RequestException as e:
        logging.error(f"获取 MR 列表失败: {e}")
        return []

# 获取 MR 的代码变更详情
def fetch_merge_request_changes(project_id, mr_id):
    url = f"{BASE_URL}/projects/{project_id}/merge_requests/{mr_id}/changes"
    try:
        response = requests.get(url, headers=HEADERS)
        response.raise_for_status()
        return response.json()
    except requests.exceptions.RequestException as e:
        logging.error(f"获取变更详情失败: {e}")
        return {}

# 统计增删行数
def calculate_code_changes(changes):
    additions = deletions = 0
    for change in changes.get("changes", []):
        diff = change.get("diff", "")
        additions += diff.count("\n+")
        deletions += diff.count("\n-")
    return additions, deletions

# 主函数
def main():
    # 动态获取 author_id
    author_id = fetch_author_id(USERNAME)
    if not author_id:
        logging.error("无法继续执行，因为未能成功获取 author_id。")
        return

    # 检查并移除旧文件
    if os.path.exists(OUTPUT_FILE_CSV):
        os.remove(OUTPUT_FILE_CSV)
    if os.path.exists(OUTPUT_FILE_EXCEL):
        os.remove(OUTPUT_FILE_EXCEL)

    # 初始化结果存储
    results = []

    # 日期范围
    start_date = datetime.strptime(START_DATE, "%Y-%m-%d")
    end_date = datetime.strptime(END_DATE, "%Y-%m-%d")
    current_date = start_date

    while current_date <= end_date:
        month_start_date = current_date.replace(day=1)
        next_month = (month_start_date + timedelta(days=31)).replace(day=1)
        month_end_date = next_month - timedelta(days=1)

        # 日期格式化
        start_date_str = month_start_date.strftime("%Y-%m-%d")
        end_date_str = min(month_end_date, end_date).strftime("%Y-%m-%d")

        logging.info(f"处理日期范围: {start_date_str} 到 {end_date_str}")

        # 获取 MR 数据
        merge_requests = fetch_merge_requests(author_id, start_date_str, end_date_str)
        if not merge_requests:
            logging.info(f"日期范围 {start_date_str} 到 {end_date_str} 没有找到 MR 数据。")
            current_date = next_month
            continue

        for mr in merge_requests:
            project_id = mr.get("project_id")
            mr_id = mr.get("iid")
            title = mr.get("title", "无标题")
            created_date = mr.get("created_at", "")[:10]
            target_branch = mr.get("target_branch", "未知分支")

            # 获取变更详情
            changes_data = fetch_merge_request_changes(project_id, mr_id)
            additions, deletions = calculate_code_changes(changes_data)

            results.append({
                "日期": created_date,
                "标题": title,
                "目标分支": target_branch,
                "新增行数": additions,
                "删除行数": deletions,
            })

        # 跳转到下个月
        current_date = next_month

    # 保存结果到 CSV 和 Excel
    if results:
        df = pd.DataFrame(results)
        # 保存为 CSV 文件，使用 utf-8-sig 编码
        df.to_csv(OUTPUT_FILE_CSV, index=False, encoding="utf-8-sig")
        logging.info(f"统计结果已保存到 CSV 文件: {OUTPUT_FILE_CSV}")

        # 保存为 Excel 文件
        df.to_excel(OUTPUT_FILE_EXCEL, index=False, engine="openpyxl")
        logging.info(f"统计结果已保存到 Excel 文件: {OUTPUT_FILE_EXCEL}")
    else:
        logging.info("未查询到符合条件的 Merge Request。")

if __name__ == "__main__":
    main()

```
