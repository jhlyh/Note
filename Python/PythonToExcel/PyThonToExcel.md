# Python操作Excel知识库

## 1. openpyxl库基础操作

### 1.1 安装和导入
```python
# 安装openpyxl
pip install openpyxl

# 导入必要的模块
import openpyxl
from openpyxl import Workbook
from openpyxl import load_workbook
```

### 1.2 创建新工作簿
```python
# 创建新的工作簿
wb = Workbook()
# 获取活动工作表
ws = wb.active
# 创建新的工作表
ws2 = wb.create_sheet("新表名", 0)  # 0表示位置，可选
```

### 1.3 读取现有Excel文件
```python
# 加载现有Excel文件
wb = load_workbook('example.xlsx')
# 获取所有工作表名称
sheet_names = wb.sheetnames
# 选择特定工作表
ws = wb['Sheet1']
```

### 1.4 单元格操作

```python
# 写入单元格
ws['A1'] = '姓名'
ws.cell(row=1, column=2, value='年龄')

# 读取单元格
value1 = ws['A1'].value
value2 = ws.cell(row=1, column=1).value

# 合并单元格
ws.merge_cells('A1:B1')
# 取消合并
ws.unmerge_cells('A1:B1')
```

### 1.5 行和列操作

```python
# 插入行
ws.insert_rows(2)  # 在第2行前插入一行
# 删除行
ws.delete_rows(2)  # 删除第2行

# 插入列
ws.insert_cols(2)  # 在B列前插入一列
# 删除列
ws.delete_cols(2)  # 删除B列

# 获取最大行和列
max_row = ws.max_row
max_column = ws.max_column
```

### 1.6 样式设置

```python
from openpyxl.styles import Font, PatternFill, Alignment, Border, Side

# 设置字体
cell = ws['A1']
cell.font = Font(name='微软雅黑', size=12, bold=True, color='FF0000')

# 设置填充颜色
cell.fill = PatternFill(fill_type='solid', fgColor='FFFF00')

# 设置对齐方式
cell.alignment = Alignment(horizontal='center', vertical='center')

# 设置边框
border = Border(left=Side(style='thin'), 
               right=Side(style='thin'),
               top=Side(style='thin'),
               bottom=Side(style='thin'))
cell.border = border
```

## 2. pandas操作Excel

### 2.1 基础操作

```python
import pandas as pd

# 读取Excel文件
df = pd.read_excel('example.xlsx', sheet_name='Sheet1')

# 写入Excel文件
df.to_excel('output.xlsx', sheet_name='Sheet1', index=False)

# 读取多个工作表
excel_file = pd.ExcelFile('example.xlsx')
df1 = pd.read_excel(excel_file, 'Sheet1')
df2 = pd.read_excel(excel_file, 'Sheet2')
```

### 2.2 高级操作

```python
# 写入多个数据框到不同工作表
with pd.ExcelWriter('output.xlsx') as writer:
    df1.to_excel(writer, sheet_name='Sheet1', index=False)
    df2.to_excel(writer, sheet_name='Sheet2', index=False)

# 设置列宽
writer.sheets['Sheet1'].set_column('A:B', 15)  # A和B列宽设为15

# 条件格式
def highlight_max(s):
    is_max = s == s.max()
    return ['background-color: yellow' if v else '' for v in is_max]

df.style.apply(highlight_max).to_excel('styled.xlsx')
```

## 3. 实用示例

### 3.1 批量处理Excel文件

```python
import os
import pandas as pd

def process_excel_files(folder_path):
    # 获取文件夹中所有Excel文件
    excel_files = [f for f in os.listdir(folder_path) 
                  if f.endswith(('.xlsx', '.xls'))]
    
    # 合并所有文件
    all_data = []
    for file in excel_files:
        df = pd.read_excel(os.path.join(folder_path, file))
        all_data.append(df)
    
    # 合并数据框
    result = pd.concat(all_data, ignore_index=True)
    
    # 保存结果
    result.to_excel('合并结果.xlsx', index=False)
```

### 3.2 数据处理和分析

```python
# 数据透视表
pivot_table = df.pivot_table(
    values='销售额',
    index='产品类别',
    columns='日期',
    aggfunc='sum'
)

# 保存带有多个工作表的分析结果
with pd.ExcelWriter('分析报告.xlsx') as writer:
    df.to_excel(writer, sheet_name='原始数据', index=False)
    pivot_table.to_excel(writer, sheet_name='透视表')
    
    # 添加图表（需要配合其他库，如openpyxl）
    workbook = writer.book
    worksheet = writer.sheets['透视表']
```