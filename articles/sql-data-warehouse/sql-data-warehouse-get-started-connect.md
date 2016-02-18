<properties
   pageTitle="使用 Visual Studio 连接到 SQL 数据仓库 | Microsoft Azure"
   description="开始连接到 SQL 数据仓库并运行一些查询。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="twounder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.date="10/22/2015"
   wacn.date=""/>

# 使用 Visual Studio 连接到 SQL 数据仓库

> [AZURE.SELECTOR]
- [Visual Studio](sql-data-warehouse-get-started-connect.md)
- [SQLCMD](sql-data-warehouse-get-started-connect-sqlcmd.md)

本演练说明如何使用 Visual Studio 中的 SQL Server Data Tools 在短时间内连接到 Azure SQL 数据仓库数据库。连接后，你将运行一个简单的查询。

## 先决条件

+ SQL 数据仓库中的 AdventureWorksDW 示例数据库。若要创建此数据库，请参阅[创建 SQL 数据仓库数据库](sql-data-warehouse-get-started-create.md)。 
+ SQL Server Data Tools for Visual Studio。有关安装指说明和选项，请参阅[安装 Visual Studio 和/或 SSDT](sql-data-warehouse-install-visual-studio.md)

## 步骤 1：查找完全限定的 Azure SQL 服务器名称

你的数据库与 Azure SQL 服务器相关联。若要连接到你的数据库，需要服务器的完全限定名称 (**servername**.chinacloudapi.cn*)。

若要查找完全限定的服务器名称，请执行以下操作。

1. 转到 [Azure 预览门户](https://manage.windowsazure.cn/)。
2. 单击“SQL 数据库”，然后单击想要连接的数据库。本示例使用 AdventureWorksDW 示例数据库。
3. 找到完整的服务器名称。

    ![完整服务器名称][1]

## 步骤 2：连接到你的 SQL 数据库

1. 打开 Visual Studio。
2. 打开 SQL Server 对象资源管理器。为此，请选择“视图”>“SQL Server 对象资源管理器”。
 
    ![SQL Server 对象资源管理器][2]

3. 单击“添加 SQL Server”图标。

    ![添加 SQL 服务器][3]

1. 填写“连接到服务器”窗口中的字段。

    ![连接到服务器][4]

    - **服务器名称**。输入我们前面找到的*服务器名称*。
    - **身份验证**。选择“SQL Server 身份验证”。
    - **登录名**和**密码**。输入 Azure SQL 数据库的登录名和密码。
    - 单击“连接”。

1. 若要浏览，请展开你的 Azure SQL 服务器。你可以查看与服务器关联的数据库。展开 AdventureWorksDW 以查看示例数据库中的表。

    ![浏览 AdventureWorksDW][5]


## 步骤 3：运行示例查询

现在，我们已连接到服务器，接下来继续编写查询。

1. 在 SQL Server 对象资源管理器中右键单击你的数据库。 

2. 选择“新建查询”。此时将打开一个新的查询窗口。

    ![新建查询][6]

3. 将以下 TSQL 查询复制到查询窗口中：

	```
	SELECT COUNT(*) FROM dbo.FactInternetSales;
	```

4. 运行该查询。为此，请单击绿色箭头，或使用以下快捷键：`CTRL`+`SHIFT`+`E`。

    ![运行查询][7]

1. 查看查询结果。在此示例中，FactInternetSales 表包含 60398 行。

    ![查询结果][8]

## 后续步骤

既然你可以执行连接和查询，接下来请尝试[使用 PowerBI 可视化数据][]。

[使用 PowerBI 可视化数据]: ./sql-data-warehouse-get-started-visualize-with-power-bi.md


<!--Image references-->

[1]: ./media/sql-data-warehouse-get-started-connect/get-server-name.png
[2]: ./media/sql-data-warehouse-get-started-connect/open-ssdt.png
[3]: ./media/sql-data-warehouse-get-started-connect/add-server.png
[4]: ./media/sql-data-warehouse-get-started-connect/connection-dialog.png
[5]: ./media/sql-data-warehouse-get-started-connect/explore-sample.png
[6]: ./media/sql-data-warehouse-get-started-connect/new-query2.png
[7]: ./media/sql-data-warehouse-get-started-connect/run-query.png
[8]: ./media/sql-data-warehouse-get-started-connect/query-results.png

<!---HONumber=Mooncake_1207_2015-->