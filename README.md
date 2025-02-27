# can-Log
#Can log automatic analysis
import pandas as pd
from datetime import datetime

# 解析 ASC 格式 Log
def parse_can_log(file_path, target_ids, error_values):
    try:
        # 将目标 ID 列表和报错数值列表转换为集合，提高查找效率
        target_ids = set(target_ids)
        error_values = set(error_values)
        error_data = []
        with open(file_path, 'r') as f:
            for line in f:
                if 'CAN' in line:
                    parts = line.strip().split()
                    if len(parts) >= 4:
                        timestamp, _, frame_id, payload = parts[:4]
                        if frame_id in target_ids:
                            if payload in error_values:
                                try:
                                    # 尝试将时间戳转换为浮点数
                                    timestamp_float = float(timestamp)
                                    error_data.append([timestamp_float, frame_id, payload])
                                except ValueError:
                                    print(f"无法将时间戳 {timestamp} 转换为浮点数，跳过该行。")
    except FileNotFoundError:
        print(f"错误：未找到文件 {file_path}。")
    except Exception as e:
        print(f"发生未知错误：{e}。")
    return error_data

# 调用函数进行解析
# 指定需要解析的 ID
target_ids = input("请输入需要解析的多个 ID，用逗号分隔（例如：123,456）：").split(',')
target_ids = [id.strip() for id in target_ids]

# 指定报错的固定数值
error_values = input("请输入报错的固定数值，用逗号分隔（例如：FF00FF00,AA55AA55）：").split(',')
error_values = [val.strip() for val in error_values]

parse_can_log('vehicle_log.asc', target_ids, error_values
