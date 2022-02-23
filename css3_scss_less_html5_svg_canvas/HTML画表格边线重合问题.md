
@[toc]

# 一、参考
1. [CSS border-collapse 属性 ](https://www.w3school.com.cn/cssref/pr_border-collapse.asp)

# 问题描述
工作中，需要画一个表格，需要显示border,于是使用DIV+CSS的方式，如果设置“border: 1px solid red”的样式，结果div重合的地方就会出现2倍的边线，与设计不符

# 解决思路

## 一、使用DIV + CSS
如果一定要使用DIV+CSS, 则需要区分第一行 、 最后一行 、第一列、最后一列 、中间表格的边线，然后画出一个表格，显然这种方式太麻烦了

## 二、使用table border-collapse
属性说明：为表格设置合并边框模型


# 案例

```js
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html>
  <head>
    <style type="text/css">
      table {
        border-collapse: collapse;
      }

      table,
      td,
      th {
        border: 1px solid black;
      }
    </style>
  </head>

  <body>
    <table>
      <tr>
        <th>Firstname</th>
        <th>Lastname</th>
      </tr>
      <tr>
        <td>Bill</td>
        <td>Gates</td>
      </tr>
      <tr>
        <td>Steven</td>
        <td>Jobs</td>
      </tr>
    </table>

    <p>
      <b>注释：</b>
      如果没有规定 !DOCTYPE，border-collapse 属性可能会引起意想不到的错误。
    </p>
  </body>
</html>

```



