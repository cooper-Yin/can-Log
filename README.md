# can-Log
#Can log automatic analysis
import pandas as pd
from datetime import datetime

# 解析 ASC 格式 Log
def parse_can_log(file_path, target_ids, error_values):
    try:
        error_data = []
        with open(file_path, 'r') as f:
            for line in f:
                if 'CAN' in line:
                    parts = line.strip().split()
                    if len(parts) >= 4:
                        timestamp, _, frame_id, payload = parts[:4]
                        if frame_id in target_ids:
                            if payload in error_values:
                                error_data.append([float(timestamp), frame_id, payload])

        # 创建包含报错信息的 DataFrame
        error_df = pd.DataFrame(error_data, columns=['Timestamp', 'ID', 'Data'])

        # 获取当前时间作为解析时间
        parsing_time = datetime.now().strftime("%Y-%m-%d %H:%M:%S")

        # 在 DataFrame 中添加解析时间列
        error_df['Parsing Time'] = parsing_time

        # 调整列顺序
        error_df = error_df[['Parsing Time', 'Timestamp', 'ID', 'Data']]

        # 将报错信息保存到 Excel 文件
        error_df.to_excel('error_report.xlsx', index=False)
        print("CAN Log 文件解析完成，报错数据已保存到 error_report.xlsx。")
    except FileNotFoundError:
        print(f"错误：未找到文件 {file_path}。")
    except Exception as e:
        print(f"发生未知错误：{e}。")

# 调用函数进行解析
# 指定需要解析的 ID
target_ids = input("请输入需要解析的多个 ID，用逗号分隔（例如：123,456）：").split(',')
target_ids = [id.strip() for id in target_ids]

# 指定报错的固定数值
error_values = input("请输入报错的固定数值，用逗号分隔（例如：FF00FF00,AA55AA55）：").split(',')
error_values = [val.strip() for val in error_values]

parse_can_log('vehicle_log.asc', target_ids, error_values
