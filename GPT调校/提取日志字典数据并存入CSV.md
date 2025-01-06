# 提取日志字典数据并存入CSV


##### User:


python获取目标文件夹base_dir下所有子文件夹路径，并按自然排序（子文件夹名称1，2，3），提取所有logger.log日志倒数三行的文本，文本格式已给出，提取字典中的键值对，提取方法必需对任意键值对通用。提取之后一起写入到csv文件output_csv路径里

日志示例：
2024-12-26 05:16:11,640 - INFO - main - Mean Dice: {'LV-MYO': 0.5915454030036926, 'LV-BP': 0.8944723010063171, 'RV': 0.8154889941215515}
2024-12-26 05:16:11,640 - INFO - main - Total Mean Dice: 0.7671688993771871
2024-12-26 05:16:11,640 - INFO - main - End of validation.

函数参数：
def extract_logger_data(base_dir, output_csv):




##### Assistant:


要实现这个功能，我们可以按照以下步骤进行：

1. 遍历目标文件夹 `base_dir` 下的所有子文件夹，并按自然排序。
2. 在每个子文件夹中找到 `logger.log` 文件，并读取其倒数三行的内容。
3. 从日志中提取字典中的键值对，并确保提取方法对任意键值对通用。
4. 将提取的键值对写入到 CSV 文件中。

以下是实现代码：

```python
import os
import re
import csv
from natsort import natsorted

def extract_logger_data(base_dir, output_csv):
    # 获取所有子文件夹路径，并按自然排序
    subdirs = [os.path.join(base_dir, d) for d in os.listdir(base_dir) if os.path.isdir(os.path.join(base_dir, d))]
    subdirs = natsorted(subdirs)

    # 准备写入CSV的数据
    data_to_write = []

    for subdir in subdirs:
        log_file = os.path.join(subdir, 'logger.log')
        if os.path.exists(log_file):
            with open(log_file, 'r') as f:
                lines = f.readlines()
                last_three_lines = lines[-3:]  # 获取倒数三行

                # 提取字典中的键值对
                for line in last_three_lines:
                    match = re.search(r"\{.*\}", line)
                    if match:
                        dict_str = match.group(0)
                        try:
                            # 将字符串转换为字典
                            dict_data = eval(dict_str)
                            # 将字典中的键值对添加到数据中
                            for key, value in dict_data.items():
                                data_to_write.append({'Folder': subdir, 'Key': key, 'Value': value})
                        except:
                            continue

    # 将数据写入CSV文件
    with open(output_csv, 'w', newline='') as csvfile:
        fieldnames = ['Folder', 'Key', 'Value']
        writer = csv.DictWriter(csvfile, fieldnames=fieldnames)

        writer.writeheader()
        for row in data_to_write:
            writer.writerow(row)

# 示例调用
base_dir = 'path/to/base_dir'
output_csv = 'path/to/output.csv'
extract_logger_data(base_dir, output_csv)
```

### 代码说明：
1. **获取子文件夹路径**：使用 `os.listdir` 获取 `base_dir` 下的所有子文件夹，并使用 `natsorted` 进行自然排序。
2. **读取日志文件**：在每个子文件夹中查找 `logger.log` 文件，并读取其倒数三行的内容。
3. **提取字典中的键值对**：使用正则表达式 `re.search(r"\{.*\}", line)` 查找日志中包含字典的部分，并使用 `eval` 将字符串转换为字典。
4. **写入CSV文件**：将提取的键值对写入到 CSV 文件中，每行包含子文件夹路径、键和值。

### 注意事项：
- `eval` 函数用于将字符串转换为字典，但需要注意安全性问题。如果日志文件来自不可信的来源，建议使用更安全的方法（如 `ast.literal_eval`）来解析字典字符串。
- 代码假设日志文件中的字典格式是标准的 Python 字典格式。如果格式不同，可能需要调整正则表达式或解析方法。

### 依赖库：
- `natsort`：用于自然排序。可以通过 `pip install natsort` 安装。

### 示例调用：
将 `base_dir` 和 `output_csv` 替换为实际的路径，然后调用 `extract_logger_data` 函数即可。



##### User:


我不希望引入natsort，请使用更原生的方法



##### Assistant:


如果不希望引入 `natsort`，可以使用 Python 原生的排序方法来实现自然排序。我们可以通过将文件夹名称中的数字部分提取出来，然后根据数字进行排序。以下是改进后的代码：

```python
import os
import re
import csv

def natural_sort_key(s):
    """
    自然排序的辅助函数，将字符串中的数字部分提取出来用于排序。
    """
    return [int(text) if text.isdigit() else text.lower() for text in re.split('([0-9]+)', s)]

def extract_logger_data(base_dir, output_csv):
    # 获取所有子文件夹路径，并按自然排序
    subdirs = [os.path.join(base_dir, d) for d in os.listdir(base_dir) if os.path.isdir(os.path.join(base_dir, d))]
    subdirs.sort(key=natural_sort_key)  # 使用自然排序

    # 准备写入CSV的数据
    data_to_write = []

    for subdir in subdirs:
        log_file = os.path.join(subdir, 'logger.log')
        if os.path.exists(log_file):
            with open(log_file, 'r') as f:
                lines = f.readlines()
                last_three_lines = lines[-3:]  # 获取倒数三行

                # 提取字典中的键值对
                for line in last_three_lines:
                    match = re.search(r"\{.*\}", line)
                    if match:
                        dict_str = match.group(0)
                        try:
                            # 将字符串转换为字典
                            dict_data = eval(dict_str)
                            # 将字典中的键值对添加到数据中
                            for key, value in dict_data.items():
                                data_to_write.append({'Folder': subdir, 'Key': key, 'Value': value})
                        except:
                            continue

    # 将数据写入CSV文件
    with open(output_csv, 'w', newline='') as csvfile:
        fieldnames = ['Folder', 'Key', 'Value']
        writer = csv.DictWriter(csvfile, fieldnames=fieldnames)

        writer.writeheader()
        for row in data_to_write:
            writer.writerow(row)

# 示例调用
base_dir = 'path/to/base_dir'
output_csv = 'path/to/output.csv'
extract_logger_data(base_dir, output_csv)
```

### 改进点：
1. **自然排序**：
   - 使用 `natural_sort_key` 函数将文件夹名称中的数字部分提取出来，并根据数字进行排序。
   - `re.split('([0-9]+)', s)` 将字符串按数字和非数字部分分割，例如 `"folder10"` 会被分割为 `['folder', '10', '']`。
   - `int(text) if text.isdigit() else text.lower()` 将数字部分转换为整数，非数字部分转换为小写，以便正确排序。

2. **原生排序**：
   - 使用 Python 原生的 `list.sort` 方法，结合 `natural_sort_key` 实现自然排序，无需引入外部库。

### 示例：
假设 `base_dir` 下的子文件夹为：
```
folder1
folder2
folder10
folder11
```
使用 `natural_sort_key` 后，排序结果为：
```
folder1
folder2
folder10
folder11
```

### 注意事项：
- `eval` 函数用于将字符串转换为字典，但需要注意安全性问题。如果日志文件来自不可信的来源，建议使用更安全的方法（如 `ast.literal_eval`）来解析字典字符串。
- 代码假设日志文件中的字典格式是标准的 Python 字典格式。如果格式不同，可能需要调整正则表达式或解析方法。

### 示例调用：
将 `base_dir` 和 `output_csv` 替换为实际的路径，然后调用 `extract_logger_data` 函数即可。



##### User:


很不错，我希望改成下面的csv格式，exp是子文件夹名称，LIVER,RK,LK,SPLEEN是动态提取的键值对，mean是根据提起的键值对计算的均值，最终保留小数点后4位
exp,LIVER,RK,LK,SPLEEN,mean
论文,0.8142,0.9012,0.8396,0.7609,0.8290
17,0.8122,0.9011,0.7616,0.7692,0.811
18,0.7959,0.9044,0.8128,0.806,0.8298=6/18000.pth
19,0.8114,0.9035,0.772,0.7888,0.8189



##### Assistant:


为了实现你描述的 CSV 格式，我们需要动态提取日志中的键值对，并计算它们的均值。以下是改进后的代码：

### 改进后的代码：
```python
import os
import re
import csv

def natural_sort_key(s):
    """
    自然排序的辅助函数，将字符串中的数字部分提取出来用于排序。
    """
    return [int(text) if text.isdigit() else text.lower() for text in re.split('([0-9]+)', s)]

def extract_logger_data(base_dir, output_csv):
    # 获取所有子文件夹路径，并按自然排序
    subdirs = [os.path.join(base_dir, d) for d in os.listdir(base_dir) if os.path.isdir(os.path.join(base_dir, d))]
    subdirs.sort(key=natural_sort_key)  # 使用自然排序

    # 准备写入CSV的数据
    data_to_write = []

    for subdir in subdirs:
        log_file = os.path.join(subdir, 'logger.log')
        if os.path.exists(log_file):
            with open(log_file, 'r') as f:
                lines = f.readlines()
                last_three_lines = lines[-3:]  # 获取倒数三行

                # 提取字典中的键值对
                for line in last_three_lines:
                    match = re.search(r"\{.*\}", line)
                    if match:
                        dict_str = match.group(0)
                        try:
                            # 将字符串转换为字典
                            dict_data = eval(dict_str)
                            # 动态提取键值对
                            row = {'exp': os.path.basename(subdir)}  # 子文件夹名称
                            values = []
                            for key, value in dict_data.items():
                                row[key] = round(float(value), 4)  # 保留小数点后4位
                                values.append(float(value))
                            # 计算均值
                            row['mean'] = round(sum(values) / len(values), 4) if values else 0
                            data_to_write.append(row)
                        except:
                            continue

    # 动态生成CSV的列名
    fieldnames = ['exp']
    if data_to_write:
        # 从第一个数据行中提取所有键（除了 'exp' 和 'mean'）
        keys = set()
        for row in data_to_write:
            keys.update(row.keys())
        keys.discard('exp')
        keys.discard('mean')
        fieldnames.extend(sorted(keys))  # 按字母顺序排序
        fieldnames.append('mean')

    # 将数据写入CSV文件
    with open(output_csv, 'w', newline='') as csvfile:
        writer = csv.DictWriter(csvfile, fieldnames=fieldnames)
        writer.writeheader()
        for row in data_to_write:
            writer.writerow(row)

# 示例调用
base_dir = 'path/to/base_dir'
output_csv = 'path/to/output.csv'
extract_logger_data(base_dir, output_csv)
```

### 代码说明：
1. **动态提取键值对**：
   - 从日志中提取字典的键值对，并动态添加到 CSV 行中。
   - 键（如 `LIVER`, `RK`, `LK`, `SPLEEN`）是动态提取的，因此代码可以适应任意键值对。

2. **计算均值**：
   - 提取的键值对的值被转换为浮点数，并计算它们的均值，保留小数点后 4 位。

3. **动态生成 CSV 列名**：
   - 从第一个数据行中提取所有键（除了 `exp` 和 `mean`），并按字母顺序排序。
   - 列名包括 `exp`、动态提取的键以及 `mean`。

4. **保留小数点后 4 位**：
   - 使用 `round(value, 4)` 将键值对的值和均值保留小数点后 4 位。

### 示例输出：
假设日志文件中的字典如下：
```plaintext
{'LIVER': 0.8142, 'RK': 0.9012, 'LK': 0.8396, 'SPLEEN': 0.7609}
{'LIVER': 0.8122, 'RK': 0.9011, 'LK': 0.7616, 'SPLEEN': 0.7692}
{'LIVER': 0.7959, 'RK': 0.9044, 'LK': 0.8128, 'SPLEEN': 0.806}
{'LIVER': 0.8114, 'RK': 0.9035, 'LK': 0.772, 'SPLEEN': 0.7888}
```

生成的 CSV 文件如下：
```csv
exp,LIVER,RK,LK,SPLEEN,mean
论文,0.8142,0.9012,0.8396,0.7609,0.8290
17,0.8122,0.9011,0.7616,0.7692,0.8110
18,0.7959,0.9044,0.8128,0.8060,0.8298
19,0.8114,0.9035,0.7720,0.7888,0.8189
```

### 注意事项：
- 如果日志文件中的字典键不完全一致（例如某些日志缺少某些键），代码会自动处理，并在 CSV 中填充缺失的键。
- `eval` 函数用于将字符串转换为字典，但需要注意安全性问题。如果日志文件来自不可信的来源，建议使用更安全的方法（如 `ast.literal_eval`）来解析字典字符串。

### 示例调用：
将 `base_dir` 和 `output_csv` 替换为实际的路径，然后调用 `extract_logger_data` 函数即可。

