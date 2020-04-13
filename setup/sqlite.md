---
title: SimpleStack - SQLite Setup
hero_title:
layout: page
hero_image: /assets/background.jpg
show_sidebar: false
menubar: home_menu
tabs: database_type_tabs
---
![sqlite](/assets/sqlite.png){:style="float:right"}
Two Provider are supported regarding SQLite
- [SQLite](https://www.nuget.org/packages/SimpleStack.Orm.Sqlite/) (Using [Microsoft.Data.SQLite](https://www.nuget.org/packages/Microsoft.Data.Sqlite/) - Apache2 license)
- [SDSqlite](https://www.nuget.org/packages/SimpleStack.Orm.SDSQLite) (Using [System.Data.SQLite](https://www.nuget.org/packages/System.Data.SQLite/) - Public domain)

# Using Microsoft SQLite

## Add packages in your project
```nuget
dotnet add package SimpleStack.Orm
dotnet add package SimpleStack.Orm.SQLite
```

## Sample usage

```csharp
using SimpleStack.Orm;
using SimpleStack.Orm.MySQLConnector;

var factory = new OrmConnectionFactory(new SqliteDialectProvider(), "CONNECTION_STRING");

using (var connection = factory.OpenConnection())
{
    // Start Querying Database
    var dogs = await connection.SelectAsync<Dog>(x => x.Age > 20);
}
```

# Using SQLite

## Add packages in your project
```nuget
dotnet add package SimpleStack.Orm
dotnet add package SimpleStack.Orm.SDSQLite
```

## Sample usage

```csharp
using SimpleStack.Orm;
using SimpleStack.Orm.SDSQLite;

var factory = new OrmConnectionFactory(new SqliteDialectProvider(), "CONNECTION_STRING");

using (var connection = factory.OpenConnection())
{
    // Start Querying Database
    var dogs = await connection.SelectAsync<Dog>(x => x.Age > 20);
}
```