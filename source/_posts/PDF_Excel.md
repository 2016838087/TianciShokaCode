---
title: .NET Core导出PDF和Excel
date: 2021-12-30 14:00:00
categories: DotNET #分类
tags: ['技术'] #标签
comment: false
---
### 关于生成PDF和Excel文件的最简单方法
<!-- more -->
### 导出Excel需要导入EPPlus包
### 导入命名空间
```csharp
using OfficeOpenXml;
```
### 示例代码如下
```csharp
/// <summary>
/// 请求接口直接下载Excel
/// </summary>
/// <returns></returns>
[HttpGet]
public async Task<IActionResult> GetExcel()
{
    string fileName = $"{Guid.NewGuid()}.xlsx";
    var stream = new MemoryStream();
    ExcelPackage.LicenseContext = LicenseContext.NonCommercial;
    using (ExcelPackage package = new ExcelPackage(stream))
    {
        // 添加worksheet
        ExcelWorksheet worksheet = package.Workbook.Worksheets.Add("DeliveryReceiptSignLog");
        //添加头
        //worksheet.Cells.Style.ShrinkToFit = true;//单元格自动适应大小
        worksheet.Cells[1, 1].Value = "跟踪码";
        worksheet.Cells[1, 2].Value = "收款金额";
        worksheet.Cells[1, 3].Value = "收款方式";
        worksheet.Cells[1, 4].Value = "POS流水号";
        worksheet.Cells[1, 5].Value = "终端号";
        worksheet.Column(1).Width = 25;
        worksheet.Column(2).Width = 25;
        worksheet.Column(3).Width = 25;
        worksheet.Column(4).Width = 25;
        worksheet.Column(5).Width = 25;
        worksheet.Column(2).Style.Numberformat.Format = "￥#,##0.00";//金额格式
        //从第二行第三列到第一万行第三列，三列被设置为下拉框
        var unitmeasure = worksheet.DataValidations.AddListValidation(worksheet.Cells[2, 3, 10000, 3].Address);
        unitmeasure.Formula.Values.Add("现金");
        unitmeasure.Formula.Values.Add("刷卡");
        package.Save();
    }
    stream.Position = 0;
    return await Task.FromResult(File(stream, "application/vnd.openxmlformats-officedocument.spreadsheetml.sheet", fileName));
}
```
### .NET Core导出PDF需要导入iTextSharp.LGPLv2.Core
### 导入命名空间
```csharp
using iTextSharp.text;
using iTextSharp.text.pdf;
```
### Controller代码如下
```csharp
/// <summary>
/// 直接获取文件（PDF）
/// </summary>
/// <returns></returns>
[HttpGet]
public async Task<IActionResult> GetPDF()
{
    string fileName = $"{Guid.NewGuid()}.pdf";
    DataEntity data = new DataEntity();
    data.TaskName = "SHXH0514huangjinjin05142020/09/14-3";
    data.SiteName = "浦东配送站";
    var users = new List<User>()
    {
        new User
        {
            Id=1,
            OrderNo="200911SHDF01634290",
            Route="SHXH10b",
            FromToTime="2020-09-13 13:33:00 14:33:00",
            Address="上海 上海市 黄浦区南京东路街道9999999999999",
            Remark="（生日快乐）",
            Count=1,
            Amount=0.01M
        },
        new User
        {
            Id=2,
            OrderNo="210831SHKF01938125",
            Route="SHCN#2a",
            FromToTime="2021-09-03 10:00:00 10:30:00",
            Address="上海 上海市 徐汇区田林路140号",
            Remark="",
            Count=1,
            Amount=0.00M+6
        }
    };

    data.users = users;
    if (PDFHelper.GetPDF(data).Length>0)
    {
        var stream = new MemoryStream(PDFHelper.GetPDF(data));
        return await Task.FromResult(File(stream, "application/pdf", fileName));
    }
    return null;
}
```
### PDFHelper.GetPDF代码全篇
### 需导入命名空间
```csharp
using iTextSharp.text;
using iTextSharp.text.pdf;
```
```csharp
public static class PDFHelper
{
    /// <summary>
    /// 返回字节
    /// </summary>
    /// <param name="data"></param>
    /// <returns></returns>
    public static byte[] GetPDF(DataEntity data)
    {
        //生成pdf
        Document document = new Document();
        //var fileStream = new MemoryStream();
        string fileName = "测试.pdf";
        var fileStream = File.Create(fileName);//C:\\Users\\Tianci\\Desktop\\PDF\\
        PdfWriter pw = PdfWriter.GetInstance(document, fileStream);
        document.Open();
        //指定字体文件，IDENTITY_H：支持中文
        string fontpath = @"C:\Windows\Fonts\SIMHEI.TTF";
        BaseFont customfont = BaseFont.CreateFont(fontpath, BaseFont.IDENTITY_H, BaseFont.NOT_EMBEDDED);
        //设置字体颜色样式
        var baseFont = new Font(customfont)
        {
            //System.Drawing.Color.Black
            Color = new BaseColor(0,0,0),  //设置字体颜色
            Size = 8  //字体大小
        };

        #region 头部
        //定义table行列数据
        //PdfPTable tableRow_1 = new PdfPTable(1);  //生成只有一列的行数据
        //tableRow_1.DefaultCell.Border = Rectangle.NO_BORDER;  //无边框
        //tableRow_1.WidthPercentage = 100;
        //tableRow_1.DefaultCell.MinimumHeight = 80f; //高度
        //float[] headWidths_1 = new float[] { 3000f }; //宽度
        //tableRow_1.SetWidths(headWidths_1);


        //定义字体样式
        var headerStyle = new Font(customfont)
        {
            Color = new BaseColor(0,0,0),//System.Drawing.Color.Black
            Size = 18,
        };
        //var Row_1_Cell_1 = new PdfPCell(new Paragraph("配送任务单", headerStyle));
        //Row_1_Cell_1.HorizontalAlignment = Element.ALIGN_CENTER;//居中
        //tableRow_1.AddCell(Row_1_Cell_1);


        var head = new Paragraph("配送任务单", headerStyle);
        head.IndentationLeft = 200f;

        var headerStyle2 = new Font(customfont)
        {
            Color = new BaseColor(0,0,0),//System.Drawing.Color.Black
            Size = 10,
        };
        var para = new Paragraph(string.Format("任务名称：{0}            站点：{1}", data.TaskName, data.SiteName), headerStyle2);

        para.IndentationLeft = -30f;

        var placeholder = new Paragraph("   ", headerStyle2);//上方文字与表格相隔间距

        PdfPTable tableRow_2 = new PdfPTable(8);
        tableRow_2.TotalWidth = 580f;
        tableRow_2.LockedWidth = true;
        tableRow_2.DefaultCell.Border = Rectangle.NO_BORDER;
        tableRow_2.WidthPercentage = 100;
        tableRow_2.DefaultCell.MinimumHeight = 80f;
        //float[] headWidths_2 = new float[] { 100f, 300f, 120f, 300f, 540f, 300f, 100f, 200f };
        float[] headWidths_2 = new float[] { 50f, 120f, 60f, 150f, 220f, 140f, 50f, 70f };//搭配TotalWidth和LockedWidth使用

        tableRow_2.SetWidths(headWidths_2);

        var Row_2_Cell_1 = new PdfPCell(new Paragraph("序号", baseFont));
        Row_2_Cell_1.HorizontalAlignment = Element.ALIGN_CENTER;//文字居中
        Row_2_Cell_1.BackgroundColor = BaseColor.LightGray;
        tableRow_2.AddCell(Row_2_Cell_1);

        var Row_2_Cell_2 = new PdfPCell(new Paragraph("跟踪代码", baseFont));
        Row_2_Cell_2.HorizontalAlignment = Element.ALIGN_CENTER;
        Row_2_Cell_2.BackgroundColor = BaseColor.LightGray;
        tableRow_2.AddCell(Row_2_Cell_2);

        var Row_2_Cell_3 = new PdfPCell(new Paragraph("线路", baseFont));
        Row_2_Cell_3.HorizontalAlignment = Element.ALIGN_CENTER;
        Row_2_Cell_3.BackgroundColor = BaseColor.LightGray;
        tableRow_2.AddCell(Row_2_Cell_3);

        var Row_2_Cell_4 = new PdfPCell(new Paragraph("配送时间", baseFont));
        Row_2_Cell_4.HorizontalAlignment = Element.ALIGN_CENTER;
        Row_2_Cell_4.BackgroundColor = BaseColor.LightGray;
        tableRow_2.AddCell(Row_2_Cell_4);

        var Row_2_Cell_5 = new PdfPCell(new Paragraph("地址", baseFont));
        Row_2_Cell_5.HorizontalAlignment = Element.ALIGN_CENTER;
        Row_2_Cell_5.BackgroundColor = BaseColor.LightGray;
        tableRow_2.AddCell(Row_2_Cell_5);

        var Row_2_Cell_6 = new PdfPCell(new Paragraph("备注", baseFont));
        Row_2_Cell_6.HorizontalAlignment = Element.ALIGN_CENTER;
        Row_2_Cell_6.BackgroundColor = BaseColor.LightGray;
        tableRow_2.AddCell(Row_2_Cell_6);

        var Row_2_Cell_7 = new PdfPCell(new Paragraph("商品数", baseFont));
        Row_2_Cell_7.HorizontalAlignment = Element.ALIGN_CENTER;
        Row_2_Cell_7.BackgroundColor = BaseColor.LightGray;
        tableRow_2.AddCell(Row_2_Cell_7);

        var Row_2_Cell_8 = new PdfPCell(new Paragraph("应收金额", baseFont));
        Row_2_Cell_8.HorizontalAlignment = Element.ALIGN_CENTER;
        Row_2_Cell_8.BackgroundColor = BaseColor.LightGray;
        tableRow_2.AddCell(Row_2_Cell_8);

        document.Add(head);
        document.Add(placeholder);
        document.Add(para);
        document.Add(placeholder);
        //document.Add(tableRow_1);
        document.Add(tableRow_2);
        #endregion

        #region 填充List数据


        Type t = new User().GetType();//获得该类的Type

        for (int i = 0; i < data.users.Count; i++)
        {
            PdfPTable tableRow_3 = new PdfPTable(8);
            tableRow_3.TotalWidth = 580f;
            tableRow_3.LockedWidth = true;
            tableRow_3.DefaultCell.Border = Rectangle.NO_BORDER;
            tableRow_3.WidthPercentage = 100;
            tableRow_3.DefaultCell.MinimumHeight = 80f;
            //float[] headWidths_3 = new float[] { 100f, 300f, 120f, 300f, 540f, 300f, 100f, 200f };
            float[] headWidths_3 = new float[] { 50f, 120f, 60f, 150f, 220f, 140f, 50f, 70f };
            tableRow_3.SetWidths(headWidths_3);
            foreach (PropertyInfo pi in t.GetProperties())//遍历属性值
            {
                var value = pi.GetValue(data.users[i]).ToString();
                var txt = new Paragraph(value, baseFont);
                var cell = new PdfPCell(txt);
                tableRow_3.AddCell(cell);
            }
            document.Add(tableRow_3);
        }

        #endregion

        //页脚
        PDFFooter footer = new PDFFooter();
        footer.OnEndPage(pw, document);
        document.Close();
        fileStream.Close();
        fileStream.Dispose();
        return GetFileStream(fileName);
    }

    /// <summary>
    /// 文件转成字节
    /// 并删除文件
    /// 返回字节
    /// </summary>
    /// <param name="filePath"></param>
    /// <returns></returns>
    public static byte[] GetFileStream(string filePath)
    {
        var byteBuffer = File.ReadAllBytes(filePath);
        if (byteBuffer.Length > 0)
        {
            if (File.Exists(filePath))
            {
                File.Delete(filePath);
            }
            return byteBuffer;
        }
        return null;
    }
    public class PDFFooter : PdfPageEventHelper
    {
        // write on top of document
        public override void OnOpenDocument(PdfWriter writer, Document document)
        {
            base.OnOpenDocument(writer, document);
            PdfPTable tabFot = new PdfPTable(new float[] { 1F });
            tabFot.SpacingAfter = 10F;
            PdfPCell cell;
            //tabFot.TotalWidth = 300F;
            cell = new PdfPCell(new Phrase("Header"));
            tabFot.AddCell(cell);
            tabFot.WriteSelectedRows(0, -1, 150, document.Top, writer.DirectContent);
        }

        // write on start of each page
        public override void OnStartPage(PdfWriter writer, Document document)
        {
            base.OnStartPage(writer, document);
        }

        // write on end of each page
        public override void OnEndPage(PdfWriter writer, Document document)
        {
            base.OnEndPage(writer, document);

            var footFont = FontFactory.GetFont("Lato", 12 * 0.667f, new BaseColor(60, 60, 60));//*
        }

        //write on close of document
        public override void OnCloseDocument(PdfWriter writer, Document document)
        {
            base.OnCloseDocument(writer, document);
        }
    }
}

public class User
{
    public int Id { get; set; }

    public string OrderNo { get; set; }

    public string Route { get; set; }

    public string FromToTime { get; set; }

    public string Address { get; set; }

    public string Remark { get; set; }

    public int Count { get; set; }

    public decimal Amount { get; set; }
}

public class DataEntity
{
    public string TaskName { get; set; }

    public string SiteName { get; set; }

    public List<User> users = new List<User>();
}
```
### 散会!!!

![我怎么这么垃圾，我要有技术会是这鬼样？](dalao.gif "我怎么这么垃圾，我要有技术会是这鬼样？")