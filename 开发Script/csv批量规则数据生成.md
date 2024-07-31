futekisei_kubun1 不为4567


### 多规则数据替换
```python

import os
import csv
import logging

# 设置日志配置
logging.basicConfig(
    level=logging.INFO, format="%(asctime)s - %(levelname)s - %(message)s"
)
logger = logging.getLogger(__name__)

# 定义每个字段的起始值和递增步长
start_values = {
    "seq_id": 10000,
    "toroku_renban": 333,
    "batchno": 333,
    "tsumi_techo": 1111111111,
    "kumiaino": 666666,
    "torokuno": 7777777,
    "tsumi_techo_renban": 55555,
    "tsumi_techo_renban_id": 55555,
    "積立組合番号": 35555,  # 3+tsumi_techo_renban_id
    "タスクID": 2022567890,
}

# 定义循环取值字段，包括固定值
cyclic_values = {
    "torokummdd_id": [528, 1228],
    "nendo_cource_id": [2413, 2412],
    "tsumi_kaikubun_id": [4],
    "tsumi_kinkubun_id": [2],
    "boshu_nendo_id": [2024],
    "boshu_kaiji_id": [3],
}


def generate_data(row_index, column_name):
    if column_name in start_values:
        return str(start_values[column_name] + row_index)
    elif column_name in cyclic_values:
        return str(
            cyclic_values[column_name][row_index % len(cyclic_values[column_name])]
        )
    else:
        raise KeyError(
            f"Column name {column_name} not in start values or cyclic values"
        )


def process_csv_file(input_file, output_file):
    logger.info(f"开始处理文件: {input_file}")

    with open(input_file, "r", encoding="utf-8") as infile:
        reader = csv.reader(infile)
        headers = next(reader)

        data_rows = [row for row in reader]

    replacements_count = 0
    # Generate the new data
    new_data = []
    for i, row in enumerate(data_rows):
        new_row = []
        for j, field in enumerate(headers):
            if j < len(row):  # 检查是否有足够的字段
                if field in start_values or field in cyclic_values:
                    try:
                        new_value = generate_data(i, field)
                        if new_value != row[j]:
                            replacements_count += 1
                        new_row.append(new_value)
                    except KeyError:
                        new_row.append(row[j])
                else:
                    new_row.append(row[j])
            else:  # 用空字符串填充缺少的字段
                if field in start_values or field in cyclic_values:
                    try:
                        new_value = generate_data(i, field)
                        new_row.append(new_value)
                        replacements_count += 1
                    except KeyError:
                        new_row.append("")
                else:
                    new_row.append("")
        new_data.append(new_row)

    logger.info(f"文件 {input_file} 替换了 {replacements_count} 条数据")

    # Write the new data to the output file
    os.makedirs(os.path.dirname(output_file), exist_ok=True)
    with open(output_file, "w", newline="", encoding="utf-8") as outfile:
        writer = csv.writer(outfile)
        writer.writerow(headers)
        writer.writerows(new_data)

    logger.info(f"文件 {input_file} 处理完毕，保存到 {output_file}")


def process_all_csv_files(input_directory, output_directory):
    for file in os.listdir(input_directory):
        if file.endswith(".csv"):
            input_file = os.path.join(input_directory, file)
            output_file = os.path.join(output_directory, "replace", file)
            process_csv_file(input_file, output_file)


input_directory = r"D:\WorkSpace\now\old"
output_directory = input_directory
process_all_csv_files(input_directory, output_directory)


```

### 递增替换
```python

import os
import csv

# 定义每个字段的起始值和递增步长
start_values = {
    "seq_id": 10000,
    "toroku_renban": 333,
    "batchno": 333,
    "tsumi_techo": 1111111111,
    "kumiaino": 666666,
    "torokuno": 7777777,
    "tsumi_techo_renban": 55555,
}


def generate_data(row_index, column_name):
    if column_name in start_values:
        return str(start_values[column_name] + row_index)
    else:
        raise KeyError(f"Column name {column_name} not in start values")


def process_csv_file(input_file, output_file):
    with open(input_file, "r", encoding="utf-8") as infile:
        reader = csv.reader(infile)
        headers = next(reader)

        data_rows = [row for row in reader]

    # Generate the new data
    new_data = []
    for i, row in enumerate(data_rows):
        new_row = []
        for j, field in enumerate(headers):
            if field in start_values:
                try:
                    new_row.append(generate_data(i, field))
                except KeyError:
                    new_row.append(row[j])
            else:
                new_row.append(row[j])
        new_data.append(new_row)

    # Write the new data to the output file
    os.makedirs(os.path.dirname(output_file), exist_ok=True)
    with open(output_file, "w", newline="", encoding="utf-8") as outfile:
        writer = csv.writer(outfile)
        writer.writerow(headers)
        writer.writerows(new_data)


def process_all_csv_files(input_directory, output_directory):
    for file in os.listdir(input_directory):
        if file.endswith(".csv"):
            input_file = os.path.join(input_directory, file)
            output_file = os.path.join(output_directory, "replace", file)
            process_csv_file(input_file, output_file)


input_directory = "D:/WorkSpace/output/V1"
output_directory = input_directory
process_all_csv_files(input_directory, output_directory)


```

