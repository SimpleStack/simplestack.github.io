---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

title: SimpleStack - A Query generator for Dapper
hero_title: Some hero title
layout: page
hero_image: /assets/background.jpg
callouts: home_callouts
show_sidebar: false
#menubar: home_menu
---
## Introduction

[SimpleStack.Orm](https://github.com/SimpleStack/simplestack.orm) is a layer on top of the wonderfull [Dapper](https://github.com/StackExchange/dapper-dot-net/) project that generate SQL queries based on lambda expressions. It is designed to persist POCO classes with a minimal amount of intrusion and configuration. All the generated sql queries are using parameters to improve performance and security.

Main objectives:

  * Map a POCO class 1:1 to an RDBMS table.
  * Create/Drop DB Table schemas using nothing but POCO class definitions (IOTW a true code-first ORM)
  * Simplicity - typed, wrist friendly API for common data access patterns.
  * Fully parameterized queries
  * Cross platform - supports multiple dbs (currently: Sql Server, Sqlite, MySql, PostgreSQL) running on both .NET, .Net Core and Mono platforms.
  * Support connections on multiple databases from the same application

In SimpleStak.Orm : **1 Class = 1 Table**. There should be no surprising or hidden behaviour.

Effectively this allows you to create a table from any POCO type and it should persist as expected in a DB Table with columns for each of the classes 1st level public properties. Some [attributes](#Available Attributes) can be added on your class to tune the way the queries are generated (Alias, Schema, PrimaryKey, Index,...)

### Sample usage

```csharp
using SimpleStack.Orm;
using SimpleStack.Orm.SqlServer;

namespace Test{

   public class sample{

      [Alias("dogs")]
      public class Dog{
         [PrimaryKey]
         public int Id{get; set;}
         public string Name{get; set;}
	 [Alias("birth_date")]
         public DateTime? BirthDate{get; set;}
         public decimal Weight{get; set;}
         public string Breed{get; set;}
      }

      var factory = new OrmConnectionFactory(new SqlServerDialectProvider(), "server=...");
      using (var conn = factory.OpenConnection())
      {
         conn.CreateTable<Dog>();

         conn.Insert(new Dog{Name="Snoopy", BirthDate = new DateTime(1950,10,01), Weight=25.4});
         conn.Insert(new Dog{Name="Rex", Weight=45.6});
         conn.Insert(new Dog{Name="Rintintin", BirthDate = new DateTime(1918,09,13), Weight=2});

         var rex = conn.First<Dog>(x => Id == 2);
         rex.BirthDate = new DateTime(1994,11,10);

         conn.Update(rex);

         conn.Delete<Dog>(x => x.Name == "Rintintin");
      }
   }
}
```