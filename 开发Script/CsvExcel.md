
### excecl2csv

1. 现在需要用python把excel文件按照一定规则转换为csv数据，excel有多个sheet，根据每个sheet生成对应名字的csv文件，把sheet的数据全部填充到csv文件中
2. 去除每个sheet中的非数据行，数据行的第一列必然是数值数字
3. 数据中有一些空列，转换成csv时会变成很长的空格，我需要把空格删掉，空列仅用逗号分隔，不要有其他表示
4. 输出路径在原路径下新建一个output文件夹
5. 文件名= {sheet_name}.csv 数据列中有为空的情况，两个逗号之间不需要空格 全部是逗号的列要删除 只有第一列是纯数字的数据行才保留，第一列含英文+数字（例如：w_nai00130）的数据行也要删除

```python
import pandas as pd
import os


def is_numeric(value):
    """检查值是否为纯数字"""
    try:
        # 转换为字符串后检查是否为数字
        return str(value).isdigit()
    except ValueError:
        return False


def clean_sheet(sheet_data):
    """移除非数据行和空数据列，并清理数据"""
    # 删除空数据列
    sheet_data.dropna(axis=1, how="all", inplace=True)

    # 仅保留第一列是纯数字的行
    sheet_data_cleaned = sheet_data[sheet_data.iloc[:, 0].apply(is_numeric)]

    # 清理数据：去除每个单元格两边的空格
    sheet_data_cleaned = sheet_data_cleaned.applymap(
        lambda x: x.strip() if isinstance(x, str) else x
    )

    return sheet_data_cleaned


def excel_to_csv(excel_file):
    # 读取Excel文件
    excel_data = pd.ExcelFile(excel_file)

    # 获取Excel文件的路径和文件名（不包括扩展名）
    file_path, file_name = os.path.split(excel_file)
    output_path = os.path.join(file_path, "output")
    os.makedirs(output_path, exist_ok=True)

    # 遍历每个sheet
    for sheet_name in excel_data.sheet_names:
        # 读取当前sheet的数据
        sheet_data = pd.read_excel(excel_file, sheet_name=sheet_name)

        # 清理数据
        cleaned_data = clean_sheet(sheet_data)

        # 构建CSV文件名
        csv_file = os.path.join(output_path, f"{sheet_name}.csv")

        # 将数据保存到CSV文件中
        cleaned_data.to_csv(
            csv_file, index=False, sep=",", quoting=0
        )  # quoting=0 对应 csv.QUOTE_MINIMAL

        print(f"Sheet '{sheet_name}' has been saved to '{csv_file}'")


# 示例调用
excel_file = "D:\\WorkSpace\\データ_羅さん.xlsx"
excel_to_csv(excel_file)


```


### replace_id

1. 现在需要对某个文件夹下的所有csv文件进行数据替换 数据替换要求
2. 生成一个id数组，id数组的数据是5位长的数字，id数组从10000开始每次递增1
3. 把id数组的数据插入到csv文件中，每一行数据的第一列数字就是要替换的数据
4. 每个csv数据的行数不确定，插入时跳过第一行表头，有多少行就替换多少数据

```python

import os
import csv


def generate_id_array(length, start=10000):
    """生成一个从start开始的指定长度的递增ID数组"""
    return [str(start + i) for i in range(length)]


# 清洗函数
def clean_data(data):
    cleaned_data = []
    for row in data:
        cleaned_row = [elem.strip() if elem.strip() else " " for elem in row]
        cleaned_data.append(cleaned_row)
    return cleaned_data


def replace_first_column_with_ids(source_folder, target_folder):
    """替换源文件夹下所有CSV文件的第一列为生成的递增ID，并将替换后的文件保存到目标文件夹"""
    if not os.path.exists(target_folder):
        os.makedirs(target_folder)

    for filename in os.listdir(source_folder):
        if filename.endswith(".csv"):
            source_file_path = os.path.join(source_folder, filename)
            target_file_path = os.path.join(target_folder, filename)

            with open(source_file_path, mode="r", newline="", encoding="utf-8") as file:
                reader = list(csv.reader(file))
                if len(reader) > 1:  # 确保文件不为空并且有数据行
                    id_array = generate_id_array(len(reader) - 1)  # 跳过表头生成ID数组

                    reader = clean_data(reader)

                    for i, row in enumerate(reader[1:], 1):  # 跳过表头行
                        row[0] = id_array[i - 1]  # 替换第一列

                    # 写入新的CSV文件到目标文件夹
                    with open(
                        target_file_path, mode="w", newline="", encoding="utf-8"
                    ) as file:
                        writer = csv.writer(file)
                        writer.writerows(reader)


# 使用方法
source_folder = "D:\WorkSpace\output\V1"  # 替换为您的CSV源文件夹路径
target_folder = os.path.join(source_folder, "replace")  # 创建目标文件夹路径
replace_first_column_with_ids(source_folder, target_folder)


```

