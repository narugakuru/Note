### SQL
我希望像sql数据插入下面的数据，表结构如下
comments对应每条数据下面的#注释说明，
请你编写sql插入数据  

```sql
CREATE TABLE start_values (
    id TEXT PRIMARY KEY,
    replace_values TEXT,
    comments TEXT
);
CREATE TABLE cyclic_values (
    id TEXT PRIMARY KEY,
    replace_values TEXT,
    comments TEXT
);

```

# API
- csv_path = csv文件夹/文件路径
- CSVtoPostgresInserter
	- replace_csv_insert2db，输入csv_path，调用CSVProcessor处理数据。获取处理后的replace文件夹路径，读取csv插入数据库
- CSVProcessor
	- process_csv，输入csv_path，对文件进行处理后输出到replace文件夹
	- insert_csv_to_postgresql_with_transaction，执行插入
	- filter_latest_csv_files，过滤同名文件
- config_setup
	- 单例模式管理全局参数
	- ListHandler，用于QT界面的日志
	- StreamHandler，控制台输出日志
- backup
	- 备份文件


### 对话框添加数据
```python
import sys
from PySide6.QtWidgets import (
    QApplication, QWidget, QTableView, QVBoxLayout, QPushButton, QDialog,
    QLineEdit, QMessageBox, QHBoxLayout, QLabel
)
from PySide6.QtSql import QSqlDatabase, QSqlTableModel

# 创建一个对话框用于新增数据
class AddDataDialog(QDialog):
    def __init__(self, parent=None):
        super().__init__(parent)
        self.setWindowTitle("新增数据")

        # 设置布局
        layout = QVBoxLayout()
        self.input1 = QLineEdit(self)
        self.input2 = QLineEdit(self)
        submit_button = QPushButton("提交", self)
        submit_button.clicked.connect(self.submit_data)

        # 添加输入字段和按钮到布局
        layout.addWidget(QLabel("Column1:"))
        layout.addWidget(self.input1)
        layout.addWidget(QLabel("Column2:"))
        layout.addWidget(self.input2)
        layout.addWidget(submit_button)
        self.setLayout(layout)

    def submit_data(self):
        if self.input1.text() and self.input2.text():
            self.accept()
        else:
            QMessageBox.warning(self, "错误", "请完整输入数据。")

    def get_data(self):
        return self.input1.text(), self.input2.text()

# 创建主窗口
class MainWindow(QWidget):
    def __init__(self):
        super().__init__()
        self.setWindowTitle("SQLite 数据表格")

        # 创建数据库连接
        db = QSqlDatabase.addDatabase('QSQLITE')
        db.setDatabaseName('database.db')

        if not db.open():
            QMessageBox.critical(self, "错误", "无法打开数据库")
            sys.exit(1)

        # 创建模型
        self.model = QSqlTableModel(self)
        self.model.setTable('your_table_name')  # 你的表名
        self.model.select()

        # 创建视图
        self.view = QTableView()
        self.view.setModel(self.model)

        # 创建新增数据按钮
        self.add_button = QPushButton("新增数据")
        self.add_button.clicked.connect(self.open_add_data_dialog)

        # 设置布局
        layout = QVBoxLayout()
        layout.addWidget(self.view)
        layout.addWidget(self.add_button)
        self.setLayout(layout)

    def open_add_data_dialog(self):
        dialog = AddDataDialog(self)
        if dialog.exec():
            data1, data2 = dialog.get_data()
            new_record = self.model.record()
            new_record.setValue("column1", data1)
            new_record.setValue("column2", data2)
            if not self.model.insertRecord(-1, new_record):
                QMessageBox.critical(self, "错误", "无法新增数据。")
            else:
                self.model.submitAll()  # 提交更改

if __name__ == "__main__":
    app = QApplication(sys.argv)

    # 创建并展示主窗口
    window = MainWindow()
    window.show()

    sys.exit(app.exec())

```