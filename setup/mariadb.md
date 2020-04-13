---
title: SimpleStack - MySQL/MariaDB Setup
hero_title:
layout: page
hero_image: /assets/background.jpg
show_sidebar: false
menubar: home_menu
tabs: database_type_tabs
---
![mariadb](/assets/mariadb.png){:style="float:right"}
Two options are available depending on your requirements.

- [SimpleStack.Orm.MySqlConnector](https://www.nuget.org/packages/SimpleStack.Orm.MySQLConnector) - ***Recommended*** - (Using [MySqlConnector](https://mysqlconnector.net/) - MIT)
- [SimpleStack.Orm.MySql](https://www.nuget.org/packages/SimpleStack.Orm.MySQL) (Using [MySql.Data](https://www.nuget.org/packages/MySql.Data/) - GPL)

# Using MySQLConnector

## Add packages in your project
```nuget
dotnet add package SimpleStack.Orm
dotnet add package SimpleStack.Orm.MySQLConnector
```

## Sample usage

```csharp
using SimpleStack.Orm;
using SimpleStack.Orm.MySQLConnector;

var factory = new OrmConnectionFactory(new MySqlConnectorDialectProvider(), "CONNECTION_STRING");

using (var connection = factory.OpenConnection())
{
    // Start Querying Database
    var dogs = await connection.SelectAsync<Dog>(x => x.Age > 20);
}
```

# Using MySQL.Data

## Add packages in your project
```nuget
dotnet add package SimpleStack.Orm
dotnet add package SimpleStack.Orm.MySQL
```

## Sample usage

```csharp
using SimpleStack.Orm;
using SimpleStack.Orm.MySQL;

var factory = new OrmConnectionFactory(new MySqlDialectProvider(), "server=...");

using (var connection = factory.OpenConnection())
{
    // Start Querying Database
    var dogs = await connection.SelectAsync<Dog>(x => x.Age > 20);
}
```