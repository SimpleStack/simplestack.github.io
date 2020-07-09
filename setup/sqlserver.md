---
title: SimpleStack - MS SQLServer Setup
hero_title:
layout: page
hero_image: /assets/background.jpg
show_sidebar: false
menubar: home_menu
tabs: database_type_tabs
---
![sqlserver](/assets/sqlserver.png){:style="float:right"}
SQLServer Dialect is based on the new [Microsoft.Data.SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient)
# Add packages in your project
```nuget
dotnet add package SimpleStack.Orm
dotnet add package SimpleStack.Orm.SQLServer
```

# Sample usage

```csharp
using SimpleStack.Orm;
using SimpleStack.Orm.SqlServer;

var factory = new OrmConnectionFactory(new SqlServerDialectProvider(), "CONNECTION_STRING");

using (var connection = factory.OpenConnection())
{
    // Start Querying Database
    var dogs = await connection.SelectAsync<Dog>(x => x.Age > 20);
}
```