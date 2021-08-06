---
title: WPF知识点
date: 2021-01-20 16:30:00
categories: DotNET
tags: ['技术']
comment: false
---
### 2021首发
<!-- more -->
### 1. 首先写一个List转DataTable的方法
```csharp
public DataTable ListToDt<T>(IEnumerable<T> collection)
{
  var props = typeof(T).GetProperties();
  var dt = new DataTable();
  dt.Columns.AddRange(props.Select(p => new
  DataColumn(p.Name, p.PropertyType)).ToArray());
  if (collection.Count() > 0)
  {
    for (int i = 0; i < collection.Count(); i++)
    {
      ArrayList tempList = new ArrayList();
      foreach (PropertyInfo pi in props)
      {
        object obj = pi.GetValue(collection.ElementAt(i), null);
        tempList.Add(obj);
      }
      object[] array = tempList.ToArray();
      dt.LoadDataRow(array, true);
    }
  }
  return dt;
}
```
### 然后定义一个list
```csharp
public List<Teacher> list = new List<Teacher>();
public class Teacher
{
    public string Name { get; set; }

    public string Password { get; set; }
}
```
### 方法内调用
```csharp
DataTable dt = ListToDt(list);
DataView dv = new DataView(dt);
```
### 这样一个List就转成了DataView

### 2. 然后是DataTable行转列的方法
```csharp
private DataTable SwapTable(DataTable tableData)
{
  int intRows = tableData.Rows.Count;
  int intColumns = tableData.Columns.Count;

  //转二维数组
  string[,] arrayData = new string[intRows, intColumns];
  for (int i = 0; i < intRows; i++)
  {
    for (int j = 0; j < intColumns; j++)
    {
      arrayData[i, j] = tableData.Rows[i][j].ToString();
    }
  }
  //下标对换
  string[,] arrSwap = new string[intColumns, intRows];
  for (int m = 0; m < intColumns; m++)
  {
    for (int n = 0; n < intRows; n++)
    {
      arrSwap[m, n] = arrayData[n, m];
    }
  }
  DataTable dt = new DataTable();
  //添加列
  for (int k = 0; k < intRows; k++)
  {
    dt.Columns.Add(
      new DataColumn(arrSwap[0, k])
    );
  }
  //添加行
  for (int r = 1; r < intColumns; r++)
  {
    DataRow dr = dt.NewRow();
    for (int c = 0; c < intRows; c++)
    {
      dr[c] = arrSwap[r, c].ToString();
    }
    dt.Rows.Add(dr);
  }
  //添加行头
  DataColumn ColRowHead = new DataColumn(tableData.Columns[0].ColumnName);
  dt.Columns.Add(ColRowHead);
  dt.Columns[ColRowHead.ColumnName].SetOrdinal(0);
  for (int i = 0; i < intColumns - 1; i++)
  {
    dt.Rows[i][ColRowHead.ColumnName] = tableData.Columns[i + 1].ColumnName;
  }
  return dt;
}
```
### 3. WPF获取选中某行的值
```csharp
DataRowView mySelectedItem = (DataRowView)dataGrid.SelectedItem;
//判断有没有选中
if (mySelectedItem != null)
{
  DataRow result = mySelectedItem.Row;
  DataTable dataTableNew = dataTable.Clone();
  dataTableNew.ImportRow(result);
  this.dataGrid1.ItemsSource = new DataView(dataTableNew);
}
```
### 4. DataTable筛选数据
```csharp
DataRow[] dr = dataTable.Select("Name ='张三'", "Time DESC");
DataTable dataTableNew = dataTable.Clone();
for (int i = 0; i < dr.Length; i++)
{
  dataTableNew.ImportRow(dr[i]);
}
this.dataGrid1.ItemsSource = new DataView(dataTableNew);
```
### 5. TextBox实时更新Binding的Property
```csharp
Text="{Binding SearchText,UpdateSourceTrigger=PropertyChanged}"
```
![鸭梨好大](coder.gif)