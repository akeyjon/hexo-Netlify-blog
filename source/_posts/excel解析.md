---
title: java-utils
date: 2019-04-01 23:03:21
tags: java-utils
categories: weekReview
---

## java 解析excel技术
一个Excel就是一个工作簿(Workbook)<br>
一个Sheet就是一张表格<br>
一个Workbook可以包含多个Sheet<br>
每一行Row的每一列就是一个单元格(Cell)

<!--more-->


### apache poi 解析
1、HSSF支持.xls为后缀的二进制格式，并提供了流解析模式的HSSFListener相关API以及基于内存模型的HSSFWorkbook相关API。

2、XSSF支持.xlsx为后缀的OpenXML格式。因为是底层文件是XML所以可以使用SAX解析，POI提供了XSSFReader用来获取压缩包中的各个XML文件相应的输入流；另外提供了基于DOM解析模式的XSSFWorkbook相关API。

#### HSSF 读取解析excel文件

```
引入依赖
 <dependency>
            <groupId>de.twentyeleven.skysail</groupId>
            <artifactId>org.apache.poi-osgi</artifactId>
            <version>3.8</version>
 </dependency>


package excel.jxl.poi;

import org.apache.commons.io.FileUtils;
import org.apache.commons.lang.StringUtils;
import org.apache.poi.hssf.usermodel.HSSFCell;
import org.apache.poi.hssf.usermodel.HSSFRow;
import org.apache.poi.hssf.usermodel.HSSFSheet;
import org.apache.poi.hssf.usermodel.HSSFWorkbook;

import java.io.File;
import java.io.IOException;

public class PoiReaderExcel {

    public static void main(String[] args) {
        File file = new File("C:/testpoi.xls");
        try {
            //创建excel 读取文件内容
            HSSFWorkbook workbook = new HSSFWorkbook(FileUtils.openInputStream(file));
            //获取第一个sheet
            HSSFSheet sheet = workbook.getSheetAt(0);
            int firstRow = 0;
            //获取sheet中最后一行 行号
            int lastRow = sheet.getLastRowNum();
            for (int i = firstRow; i < lastRow+1 ; i++) {
                HSSFRow row = sheet.getRow(i);
                //获取当前行的最后单元格
                int lastCellNum = row.getLastCellNum();
                for (int j = 0; j < lastCellNum; j++) {
                    HSSFCell cell = row.getCell(j);
                    if(cell != null){
                        String value = cell.getStringCellValue();
                        if(StringUtils.isNotBlank(value)){
                            System.out.print(value+" ");
                        }else{
                            System.out.print(" = ");
                        }
                    }else{
                        System.out.print(" + ");
                    }

                }
                System.out.println(" ");

            }


        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}


```

#### HSSF 生成导出excel文件

```
package excel.jxl.poi;

import org.apache.commons.io.FileUtils;
import org.apache.poi.hssf.usermodel.HSSFCell;
import org.apache.poi.hssf.usermodel.HSSFRow;
import org.apache.poi.hssf.usermodel.HSSFSheet;
import org.apache.poi.hssf.usermodel.HSSFWorkbook;

import java.io.File;
import java.io.FileOutputStream;
import java.io.IOException;

public class PoiExcel {

    public static void main(String[] args) {
        String[] title = {"id", "name", "sex"};
        //创建Excel工作簿
        HSSFWorkbook hssfWorkbook = new HSSFWorkbook();

        //创建一个工作表
        HSSFSheet sheet = hssfWorkbook.createSheet();

        //创建第一行
        HSSFRow row = sheet.createRow(0);

        HSSFCell cell = null;

        //插入第一行数据
        for (int i = 0; i < title.length; i++) {
            cell = row.createCell(i);
            cell.setCellValue(title[i]);
        }

        for (int i = 1; i < 10; i++) {
            row = sheet.createRow(i);
            cell = row.createCell(0);
            cell.setCellValue("b"+i);
            cell = row.createCell(1);
            cell.setCellValue("user"+i);
            cell = row.createCell(2);
            cell.setCellValue("女");

        }
        File file = new File("C:/testpoi.xls");
        try {
            file.createNewFile();
            FileOutputStream outputStream = FileUtils.openOutputStream(file);
            hssfWorkbook.write(outputStream);
            outputStream.close();
        } catch (IOException e) {
            e.printStackTrace();
        }


    }
}


``` 

### Jxl 解析excel

```
引入依赖
<dependency>
            <groupId>com.hynnet</groupId>
            <artifactId>jxl</artifactId>
            <version>2.6.12.1</version>
 </dependency>


```
#### 读取excel文件

```
package excel.jxl;

import jxl.Cell;
import jxl.Sheet;
import jxl.Workbook;
import jxl.read.biff.BiffException;

import java.io.File;
import java.io.IOException;

public class JxlReader {

    public static void main(String[] args) {
        try {
            //创建workbook
            Workbook workbook = Workbook.getWorkbook(new File("C:/test1.xlsx"));
            //获取第一个sheet
            Sheet sheet = workbook.getSheet(0);
            //获取数据
            for (int i = 0; i < sheet.getRows(); i++) {
                for (int j = 0; j < sheet.getColumns(); j++) {
                    Cell cell = sheet.getCell(j, i);
                    System.out.print(cell.getContents()+" ");

                }
                System.out.println();

            }
            workbook.close();
        } catch (IOException e) {
            e.printStackTrace();
        } catch (BiffException e) {
            e.printStackTrace();
        }
    }
}

```

#### 生成导出excel文件

```
package excel.jxl;

import jxl.Workbook;
import jxl.write.Label;
import jxl.write.WritableSheet;
import jxl.write.WritableWorkbook;
import jxl.write.WriteException;
import jxl.write.biff.RowsExceededException;

import java.io.File;
import java.io.IOException;

public class ExcelJxlTest {

    public static void main(String[] args) {
        //创建excel文件
        File file = new File("C:/test1.xlsx");

        //文件表名
        String[] title = {"id", "name", "sex"};

        try {
            file.createNewFile();
            WritableWorkbook workbook = Workbook.createWorkbook(file);
            WritableSheet sheet = workbook.createSheet("sheet1", 0);
            Label label = null;

            //设置第一列名
            for (int i = 0; i < title.length; i++) {
                label = new Label(i, 0, title[i]);
                sheet.addCell(label);
            }

            //追加数据
            for (int i = 1; i < 10; i++) {
                label = new Label(0, i, "a" + i);
                sheet.addCell(label);
                label = new Label(1, i, "user"+i);
                sheet.addCell(label);
                label = new Label(2, i, "nan");
                sheet.addCell(label);

            }
            workbook.write();
            workbook.close();


        } catch (IOException e) {
            e.printStackTrace();
        } catch (RowsExceededException e) {
            e.printStackTrace();
        } catch (WriteException e) {
            e.printStackTrace();
        }
    }
}


```