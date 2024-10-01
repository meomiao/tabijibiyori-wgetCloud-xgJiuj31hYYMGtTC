[合集 \- .NET 开源工具(21\)](https://github.com)[1\..NET 开源快捷的数据库文档查询和生成工具07\-31](https://github.com/1312mn/p/18333223)[2\..NET 结果与错误处理利器 FluentResults08\-01](https://github.com/1312mn/p/18336221)[3\..NET\+WPF 桌面快速启动工具 GeekDesk08\-19](https://github.com/1312mn/p/18361056)[4\.Gradio.NET 支持 .NET 8 简化 Web 应用开发08\-26](https://github.com/1312mn/p/18370464)[5\..NET 开源实时监控系统 \- WatchDog08\-27](https://github.com/1312mn/p/18379779)[6\.实用接地气的 .NET 微服务框架08\-28](https://github.com/1312mn/p/18381195)[7\..NET 开源报表神器 Seal\-Report08\-30](https://github.com/1312mn/p/18385442)[8\..NET 最好用的验证组件 FluentValidation09\-03](https://github.com/1312mn/p/18393208)[9\..NET 8\.0 文档管理系统网盘功能的实现09\-04](https://github.com/1312mn/p/18392313)[10\..NET 8 \+ WPF 企业级工作流系统09\-05](https://github.com/1312mn/p/18395595)[11\..NET 多版本兼容的精美 WinForm UI控件库09\-06](https://github.com/1312mn/p/18389654)[12\.超轻量级、支持插件的 .NET 网络通信框架09\-09](https://github.com/1312mn/p/18401867)[13\..NET 8 微软免费开源的 Blazor UI 组件库09\-10](https://github.com/1312mn/p/18404007)[14\..NET 多版本 WinForm 开源控件库 SunnyUI09\-12](https://github.com/1312mn/p/18405542)[15\.C\# 开源教程带你轻松掌握数据结构与算法09\-13](https://github.com/1312mn/p/18408107):[FlowerCloud机场](https://hushicha.org)[16\..NET 开源的功能强大的人脸识别 API09\-14](https://github.com/1312mn/p/18406833)[17\.C\# \+ WPF 音频播放器 界面优雅，体验良好09\-19](https://github.com/1312mn/p/18416419)[18\.C\# 开源浏览器性能提升，体验Chrome级速度09\-25](https://github.com/1312mn/p/18420917)[19\..NET 工具库高效生成 PDF 文档09\-26](https://github.com/1312mn/p/18429834)[20\..NET 开源高性能 MQTT 类库09\-27](https://github.com/1312mn/p/18412658)21\..NET 开源 EF Core 批处理扩展工具，真好用09\-30收起**阅读目录**

* [前言](#_label0)
* [EFCore.BulkExtensions](#_label1)
* [如何使用？](#_label2)
* [批量操作](#_label3)
* [性能测试结果](#_label4)
* [项目地址](#_label5)
* [总结](#_label6)
* [最后](#_label7)

## 前言


Entity Framework Core（EF Core）作为 .NET 生态系统中受欢迎的对象关系映射器（ORM），其轻量级、可扩展性和支持多个数据库引擎而备受青睐。


本文将介绍一款.NET 的开源 EF Core 批处理扩展工具，它极大地提升了数据处理的效率和性能。来看看如何轻松集成到我们现有的 EF Core 项目中。


## EFCore.BulkExtensions


Entity Framework Core (EF Core) 批量扩展库提供了支持批量复制功能的工具，适用于 .NET 平台上的插入、更新、删除、读取（CRUD）、清空表（Truncate）以及保存更改（SaveChanges）等操作。


该库支持 SQL Server、PostgreSQL、MySQL 和 SQLite 数据库。


该库轻量且高效，涵盖了常用的 CRUD 操作，并被微软评为推荐的前 20 个 EF Core 扩展之一。


### 版本说明


![](https://img2024.cnblogs.com/blog/576536/202409/576536-20240930104405868-428102632.png)


### 内部机制


SQL Server：使用 SqlBulkCopy 进行插入操作，更新和删除操作则结合了 BulkInsert 和原生 SQL 的 MERGE。


SQLite：由于没有 BulkCopy，该库使用纯 SQL 结合 UPSERT。


### 注意事项


批量测试：不能使用 UseInMemoryDb，因为 InMemoryProvider 不支持特定的关系数据库方法。


## 如何使用？


首先使用 Nuget 安装 EFCore.BulkExtensions。




```
Install-Package EFCore.BulkExtensions  
```


### 批量操作示例




```
context.BulkInsert(entities);                   
context.BulkInsertOrUpdate(entities);           
context.BulkInsertOrUpdateOrDelete(entities);   
context.BulkUpdate(entities);                  
context.BulkDelete(entities);                  
context.BulkRead(entities);                    
context.BulkSaveChanges();    
```


### 异步版本




```
context.BulkInsertAsync(entities);  
context.BulkInsertOrUpdateAsync(entities);    //Upsert  
context.BulkInsertOrUpdateOrDeleteAsync(entiti);//Sync  
context.BulkUpdateAsync(entities);  
context.BulkDeleteAsync(entities);  
context.BulkReadAsync(entities);  
context.BulkSaveChangesAsync();  
```


### 与 EF Core 使用




```
// 删除  
context.Items.Where(a => a.ItemId >  500).BatchDelete();  
context.Items.Where(a => a.ItemId >  500).BatchDeleteAsync();  
  
// 更新  
context.Items.Where(a => a.ItemId <= 500).BatchUpdate(a => new Item { Quantity = a.Quantity + 100});  
context.Items.Where(a => a.ItemId <= 500).BatchUpdateAsync(a => new Item {Quantity=a.Quantity+100});  
  // can be as value '+100' or as variable '+incrementStep' (int incrementStep = 100;)  
    
// 更新  
context.Items.Where(a => a.ItemId <= 500).BatchUpdate(new Item { Description = "Updated" });  
context.Items.Where(a => a.ItemId <= 500).BatchUpdateAsync(new Item { Description = "Updated" });   
  
// Truncate  
context.Truncate();  
context.TruncateAsync();  
```


## 批量操作


### 连接字符串配置


如果使用 Windows 身份验证，则连接字符串中应包含 Trusted\_Connection\=True，因为 SQL 身份验证信息需要保留在连接中。


### 事务管理


每个批量操作默认作为单独的事务处理，并自动提交。如果需要在一个过程中执行多个操作，则应显式使用事务。


例如，由于子表不会自动与父表一起插入，因此需要显式进行第二次调用：




```
using (var transaction = context.Database.BeginTransaction())
{
    context.BulkInsert(entitiesList);
    context.BulkInsert(subEntitiesList);
    transaction.Commit();
}
```


### 批量插入或更新


BulkInsertOrUpdate 方法适用于需要在同一数据库连接中执行插入或更新操作的场景。当主键（Primary Key）匹配时执行更新，否则执行插入。


### 批量插入、更新或删除


BulkInsertOrUpdateOrDelete 方法有效地同步表中的行与输入数据。不在输入列表中的数据库记录将被删除。


### 批量读取


BulkRead 方法基于一个或多个唯一列执行 SELECT 和 JOIN 操作，这些列在配置的 UpdateByProperties 中指定。


示例




```
using (var transaction = context.Database.BeginTransaction())
{
    // 插入或更新主表
    context.BulkInsertOrUpdate(mainEntitiesList);
    
    // 插入或更新子表
    context.BulkInsertOrUpdate(subEntitiesList);
    
    // 提交事务
    transaction.Commit();
}

// 同步表行与输入数据
context.BulkInsertOrUpdateOrDelete(allEntitiesList);

// 根据唯一列读取数据
context.BulkRead(uniqueColumnsConfig);
```


## 性能测试结果


以下是在 SQL Server 2019 上进行的性能测试结果（单位：秒）：


### 测试配置


硬件配置：Intel i7\-10510U CPU @ 2\.30GHz, DDR3 16GB, SSD Samsung 512GB


测试表结构：测试表 TestTable 包含 6 列（Guid, string x2, int, decimal?, DateTime），所有列均被插入，其中 2 列被更新


### 性能对比


![](https://img2024.cnblogs.com/blog/576536/202409/576536-20240930104610053-1167599687.png)


### 注意事项


小数据集开销：对于较小的数据集（少于 1000 行），由于大多数批量操作需要创建临时表并在完成后删除临时表，因此会有一定的开销。


建议：建议对于大于 1000 行的数据集使用批量操作以获得最佳性能。


## 项目地址


https://github.com/borisdj/EFCore.BulkExtensions


## 总结


希望本文能为朋友们在使用EF Core开发方面带来帮助。欢迎大家在评论区留言讨论，分享您的经验和建议。


## 最后


如果你觉得这篇文章对你有帮助，不妨点个赞支持一下！你的支持是我继续分享知识的动力。如果有任何疑问或需要进一步的帮助，欢迎随时留言。


也可以加入微信公众号**\[DotNet技术匠]** 社区，与其他热爱技术的同行一起交流心得，共同成长！**优秀是一种习惯，欢迎大家留言学习！**


![](https://img2024.cnblogs.com/blog/576536/202408/576536-20240814113403514-910171896.png)


