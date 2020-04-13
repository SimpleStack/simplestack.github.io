---
title: SimpleStack - PostgreSQL Setup
hero_title:
layout: page
hero_image: /assets/background.jpg
show_sidebar: false
menubar: home_menu
tabs: database_type_tabs
---
![postgresql](/assets/postgresql.png){:style="float:right"}
PostgreSQL Dialect is based on [NpgSql](https://www.nuget.org/packages/Npgsql/)

```nuget
dotnet add package SimpleStack.Orm
dotnet add package SimpleStack.Orm.PostgreSQL
```

# Sample usage

```csharp
using SimpleStack.Orm;
using SimpleStack.Orm.PostgreSQL;

var factory = new OrmConnectionFactory(new PostgreSQLDialectProvider(), "CONNECTION_STRING");

using (var connection = factory.OpenConnection())
{
    // Start Querying Database
    var dogs = await connection.SelectAsync<Dog>(x => x.Age > 20);
}
```