---
tags:
  - 脚本
  - 文件处理
---
Server=localhost;Port=5432;User Id=postgres;Password=rootroot;Database=postgres;SearchPath=takusai_tanntai;

### excel自动缩放图片

为了避免这种错误，我们可以将全局变量的声明移到模块顶部，而不是放在 `End Sub` 后面。以下是修正后的完整代码安排：

使用步骤：

1. 打开Excel文件并按 `Alt + F11` 打开VBA编辑器。
2. 在VBA编辑器中，找到你的工作簿，在左侧的项目窗口中选择 `ThisWorkbook`，并将以下代码粘贴进去：

    ```vb
    Private Sub Workbook_SheetSelectionChange(ByVal Sh As Object, ByVal Target As Range)
        ' 启动定时器，每秒检查一次
        Application.OnTime Now + TimeValue("00:00:01"), "CheckNewPictures"
    End Sub

    Private Sub Workbook_Open()
        ' 初始化图片字典
        Set PicDict = CreateObject("Scripting.Dictionary")
        Dim Sh As Worksheet
        Dim Pic As Picture
        
        ' 将所有图片添加到字典中
        For Each Sh In ThisWorkbook.Sheets
            For Each Pic In Sh.Pictures
                PicDict.Add Pic.Name, True
            Next Pic
        Next Sh
    End Sub
    ```

3. 在VBA编辑器中，插入一个新模块（点击“插入”>“模块”），将以下代码粘贴到新模块中：

    ```vb
    ' 声明全局变量
    Public PicDict As Object

    Public Sub CheckNewPictures()
        Dim Sh As Worksheet
        Dim Pic As Picture
        Dim NewPic As Picture
        
        ' 查找新粘贴的图片
        For Each Sh In ThisWorkbook.Sheets
            For Each Pic In Sh.Pictures
                If Not PicDict.exists(Pic.Name) Then
                    Set NewPic = Pic
                    Exit For
                End If
            Next Pic
        Next Sh

    ' 如果找到新粘贴的图片，则缩小其尺寸，由于excel默认强制保持高宽比，Width和Height的缩放是同步的，缩放一个导致全部缩放
    If Not NewPic Is Nothing Then
        With NewPic
            .Width = .Width * 0.5
            '.Height = .Height * 0.5
        End With
        ' 将新图片添加到字典中
        PicDict.Add NewPic.Name, True
    End If
End Sub
    ```

这样设置后，脚本会在图片粘贴时通过定时器检查并处理新粘贴的图片。

### 分隔数据转换成多个csv
我现在有很多csv数据文件，需要导入到postgre数据库，csv文件名就是数据库的table name，csv文件第一行对应的字段就是数据库表的字段，下面的就是需要导入的数据
csv里面的字段用,分隔 用""包裹每一个字段和值类型，有办法批量导入数据库吗，因为csv文件没办法知道每一个字段的值类型
```python
import csv
import os


def parse_data_file(input_file):
    # 用于存储每个表的数据
    tables = {}
    current_table = None

    with open(input_file, "r", encoding="utf-8") as file:
        lines = file.readlines()

    for line in lines:
        line = line.strip()
        if not line:
            # 空行，表示表的结束
            current_table = None
        elif current_table is None:
            # 这是表名，去除制表符并作为表名
            current_table = line.replace("\t", " ").strip()
            tables[current_table] = []
        else:
            # 这是表的数据，替换制表符为逗号
            tables[current_table].append(line.replace("\t", ","))

    return tables


def save_tables_to_csv(tables, output_dir):
    # 确保输出目录存在
    os.makedirs(output_dir, exist_ok=True)

    for table_name, rows in tables.items():
        # 处理非法文件名字符（例如 \t）
        table_name = table_name.replace("\t", "_")
        output_file = os.path.join(output_dir, f"{table_name}.csv")
        try:
            with open(output_file, "w", newline="", encoding="utf-8") as csvfile:
                csv_writer = csv.writer(csvfile, quoting=csv.QUOTE_ALL)
                for row in rows:
                    csv_writer.writerow(row.split(","))  # 数据已经用逗号分隔
        except OSError as e:
            print(f"Error writing file {output_file}: {e}")


def main():
    input_file = "D:/WorkSpace/now/new/all.csv"  # Replace with your actual file name
    output_dir = "D:/WorkSpace/now/output"  # Specify your output directory here
    tables = parse_data_file(input_file)
    save_tables_to_csv(tables, output_dir)


if __name__ == "__main__":
    main()

```

## 提取代码的SQL

```python
from ast import IsNot
from os import path
import os
import re

# 定义函数来读取 VB 代码文件并处理
def read_vb_code(filename):
    with open(filename, "r", encoding="utf-8") as file:
        vb_code = file.read()
    return vb_code


# 提取注释信息
def extract_comment_info(vb_code):
    program_name = None
    program_id = None

    # 匹配注释信息的正则表达式
    comment_pattern = r"'*\s*プログラム名\s*：\s*(.*)\s*"
    program_name_match = re.search(comment_pattern, vb_code)
    if program_name_match:
        program_name = program_name_match.group(1).strip()

    comment_pattern = r"'*\s*プログラムＩＤ\s*：\s*(.*)\s*"
    program_id_match = re.search(comment_pattern, vb_code)
    if program_id_match:
        program_id = program_id_match.group(1).strip()

    return program_name, program_id


# 提取函数和子程序信息
def extract_functions_and_subs(vb_code, program_id):
    function_info = {}

    # 匹配 Function 和 Sub 的正则表达式
    print(f"开始搜索{program_id}")

    pattern = r"(?s)(Function.*?End Function)|(Sub.*?End Sub)"
    sql_pattern = r'strSQL\.AppendLine\("(.*?)"\)'
    func_sub_name_pattern = r"(?:Function|Sub)\s+(\w+)\s*\((.*?)\)"

    # 查找所有 Function 和 Sub
    func_sub = re.findall(pattern, vb_code, re.DOTALL)
    
    # print(func_sub)
    for code in func_sub:
        # print(code)
        # 查找函数内的 SQL 语句
        if code != None:
            if code[0]:  # Function
                func_sub_name = re.search(func_sub_name_pattern, code[0]).group(1)
                sql_matches = re.findall(sql_pattern, code[0], re.DOTALL)
            else:  # Sub
                func_sub_name = re.search(func_sub_name_pattern, code[1]).group(1)
                sql_matches = re.findall(sql_pattern, code[1], re.DOTALL)
            sql_statements = [sql.strip() for sql in sql_matches]
            # print(sql_statements)
        # 添加到字典中

        function_info[f"Sub/Func: {func_sub_name}"] = sql_statements

    return function_info


# 保存信息到文件
def save_to_file(program_name, program_id, function_info, output_file, filePath):
    with open(output_file, "w", encoding="utf-8") as file:
        file.write(f"ファイル：{filePath}\n")
        file.write(f"プログラム名：{program_name}\n")
        file.write(f"プログラムＩＤ：{program_id}\n\n")

        for func_name, sql_statements in function_info.items():
            file.write(f"{func_name}   ===================\n")
            for sql in sql_statements:
                file.write(f"{sql}\n")
            file.write("\n")


# 主程序入口
def one_extract(filePath, table):
    ignore_file = ["NAZJ50000"]
    # filePath = r"TakusaiJob/NA201000/NAZJ23003/NAZJ23003.vb"  # 替换成你的 VB 代码文件名

    # 读取 VB 代码文件
    vb_code = read_vb_code(filePath)
    # print(vb_code)

    # 提取注释信息
    program_name, program_id = extract_comment_info(vb_code)
    # print(program_id, program_name)

    if program_id in ignore_file:
        return

    # 提取函数和子程序信息
    function_info = extract_functions_and_subs(vb_code, program_id)
    # function_info = search(vb_code)

    output_file = f"txt/{table}/{program_id}.txt"  # 输出文件名

    # 保存信息到文件
    save_to_file(program_name, program_id, function_info, output_file, filePath)

    print(f"提取并保存成功到 {output_file}")


def search(vb_code):

    # 匹配 Function ... End Function
    function_pattern = r"(?s)Function.*?End Function"
    function_matches = re.findall(function_pattern, vb_code)
    print("Functions:")
    for match in function_matches:
        print(match)
    print()

    # 匹配 Sub ... End Sub
    sub_pattern = r"(?s)Sub.*?End Sub"
    sub_matches = re.findall(sub_pattern, vb_code)
    print("Subroutines:")
    for match in sub_matches:
        print(match)


def main():
    current_directory = os.getcwd()
    print(f"Current directory: {current_directory}")

    table = input("请输入分析表名:")

    # 目标目录路径
    target_directory = os.path.join(current_directory, "txt", table)

    # 创建目标目录及其父目录（如果它们不存在）
    os.makedirs(target_directory, exist_ok=True)

    # 读取文本文件内容
    with open("input.txt", "r", encoding="utf-8") as file:
        text = file.read()

    # 使用正则表达式匹配VB文件路径
    pattern = re.compile(r"([a-zA-Z0-9_\\]+\.vb):")
    vb_files = pattern.findall(text)
    # vb_files = vb_files[:-1]
    # 打印提取的VB文件路径
    print(vb_files)
    for vb_file in vb_files:
        one_extract(vb_file, table)


if __name__ == "__main__":
    # filePath = r"D:\WorkSpace\Takusai\TakusaiJob\NA103000\NAMJ20105\NAMJ20105.vb"
    # filePath = input("请输入分析文件路径:  ")
    # one_extract(filePath)
    main()

```


## 替换md图片引用
```python
import re

def replace_image_references(input_file, output_file):
    with open(input_file, "r", encoding="utf-8") as file:
        content = file.read()

    # 使用正则表达式替换![[图片路径]]为![](图片路径)
    updated_content = re.sub(r"!\[\[(.*?)\]\]", r"![](\1)", content)

    with open(output_file, "w", encoding="utf-8") as file:
        file.write(updated_content)


# 输入和输出文件路径
input_file = "E:/Note/开发/Test-NADJ20010.md"
output_file = "E:/Note/开发/output.md"

# 调用函数
replace_image_references(input_file, output_file)

```

## csv批量插入数据库

```python
import os
import psycopg2
import pandas as pd
# takusai_tanntai
# 数据库连接参数
db_params = {
    'dbname': 'takusai_tanntai',
    'user': 'postgres',
    'password': 'rootroot',
    'host': 'localhost',
    'port': '5432'
}

# CSV文件所在目录
csv_directory = 'D:/WorkSpace/now/20004'

# 连接到PostgreSQL数据库
conn = psycopg2.connect(**db_params)
cur = conn.cursor()

# 遍历目录中的每个CSV文件
for csv_file in os.listdir(csv_directory):
    if csv_file.endswith('.csv'):
        table_name = csv_file.replace('.csv', '')
        file_path = os.path.join(csv_directory, csv_file)
        
        # 使用pandas读取CSV文件到DataFrame
        df = pd.read_csv(file_path, quotechar='"')
        
        # 获取列名和数据类型
        columns = df.columns
        data_types = {col: 'TEXT' for col in columns}  # 默认使用TEXT类型
        
        # 如果表不存在，则创建表
        create_table_query = f"CREATE TABLE IF NOT EXISTS {table_name} (" + \
                             ", ".join([f"{col} {data_types[col]}" for col in columns]) + ");"
        cur.execute(create_table_query)
        
        # 将数据插入到表中
        for _, row in df.iterrows():
            insert_query = f"INSERT INTO {table_name} (" + \
                           ", ".join(columns) + ") VALUES (" + \
                           ", ".join([f"'{str(value).replace('\'', '\'\'')}'" for value in row]) + ");"
            cur.execute(insert_query)

# 提交更改并关闭连接
conn.commit()
cur.close()
conn.close()

```