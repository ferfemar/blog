+++
categories = ["openpyxl", "python"]
date = "2019-07-15"
description = "Working with Excel in Python"
title = "Working with Excel in Python"
+++

# Working with Excel in Python

Import module, create workbook

```python
from openpyxl import Workbook
wb = Workbook()

```

Create a sheet

```python
wb.create_sheet(title='rtr1')
```

Get sheet by name

```python
ws1 = wb['rtr1']
```

Delete a sheet

```python
del wb['Sheet']
```

Display list of sheetnames

```python
wb.sheetnames
```

Access a cell in a sheet

```python
cell = ws1['A1']
```

Save workbook to a file

```python
wb.save(filename='vrftestreport.xlsx')
```

Sources and references

http://www.pythonexcel.com/
https://openpyxl.readthedocs.io/en/stable/usage.html