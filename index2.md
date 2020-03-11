---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

title: SimpleStack - A Query generator for Dapper
hero_title: Some hero title
layout: page
hero_image: /assets/background.jpg
callouts: home_callouts
show_sidebar: false
menubar: home_menu
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

# Install

```nuget
dotnet add package SimpleStack.Orm
```

## Depending on the database you want to target and the license of your project:

  - [SqlServer](https://www.nuget.org/packages/SimpleStack.Orm.SqlServer) (MIT licensed)
  - [PostgreSQL](https://www.nuget.org/packages/SimpleStack.Orm.PostgreSQL) (MIT licensed)
  - MySQL/MariaDB
    - [MySql](https://www.nuget.org/packages/SimpleStack.Orm.MySQL) (Using [MySql.Data](https://www.nuget.org/packages/MySql.Data/) - GPL Licensed)
    - [MySqlConnector](https://www.nuget.org/packages/SimpleStack.Orm.MySQLConnector) (Using [MySqlConnector](https://www.nuget.org/packages/MySqlConnector/) - MIT licensed)
  - SQLite
    - [Sqlite](https://www.nuget.org/packages/SimpleStack.Orm.Sqlite/) (Using [Microsoft.Data.SQLite](https://www.nuget.org/packages/Microsoft.Data.Sqlite/) - Apache2 license)
    - [SDSqlite](https://www.nuget.org/packages/SimpleStack.Orm.SDSQLite) (Using [System.Data.SQLite](https://www.nuget.org/packages/System.Data.SQLite/) - Public domain)

### 2 minutes sample

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

        using (var conn = factory.OpenConnection()){
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

## 20 Minutes sample

As SimpleStack.Orm is based on Dapper, I encourage you to have a look at [Dapper documentation](https://github.com/StackExchange/dapper-dot-net/blob/master/Readme.md).

The first thing todo is to create an OrmConnectionFactory specifying the Dialectprovider to use and the connectionstring of your database.

```csharp

var factory = new OrmConnectionFactory(new SqlServerDialectProvider(), "server=...");
using (var conn = factory.OpenConnection())
{
   //TODO use connection
}
```

The DialectProvider contains all the specific code required for each database.

## WHERE clause generation using strong type LINQ queries

Real queries uses parameters instead of direct values.

### Equals, Not equals, Bigger than, Less than, Contains,...

```csharp
db.Select<Dog>(q => q.Name == "Rex");       // WHERE ("Name" = 'Rex')
db.Select<Dog>(q => q.Name != "Rex");       // WHERE ("Name" <> 'Rex')
db.Select<Dog>(q => q.Weight == 10);        // WHERE ("Weight" = 10)
db.Select<Dog>(q => q.Weight > 10);         // WHERE ("Weight" > 10)
db.Select<Dog>(q => q.Weight >= 10);        // WHERE ("Weight" >= 10)
db.Select<Dog>(q => q.Weight < 10);         // WHERE ("Weight" < 10)
db.Select<Dog>(q => q.Weight <= 10);        // WHERE ("Weight" <= 10)
db.Select<Dog>(q => q.Name.Contains("R"));  // WHERE ("Name" LIKE("%R%"))
db.Select<Dog>(q => q.Name.StartWith("R")); // WHERE ("Name" LIKE("R%"))
db.Select<Dog>(q => q.Name.EndWidth("R"));  // WHERE ("Name" LIKE("%R"))
```

### Combine criterias with AND or OR

```csharp
db.Select<Dog>(q => q.Name.Contains("R") || q.Weight > 10); // WHERE ("Name" LIKE 'R' OR "Weight" > 10)
db.Select<Dog>(q => q.Name.Contains("R") && q.Weight > 10); // WHERE ("Name" LIKE 'R' AND "Weight" > 10)
```

### Sql class

#### IN Criteria

```csharp
string[] breeds = new {"Beagle", "Border Collie", "Golden Retriever"};
db.Select<Dog>(q => breeds.Contains(g.Breed));  // WHERE "Breed" In ('Beagle', 'Border Collie', 'Golden Retriever')
```

#### Date part methods
Use the date function (specific for each database)
```csharp
// SELECT YEAR("BirthDate") FROM DOG
conn.GetScalar<Dog, int>(x => Sql.Year(x.BirthDate))
// OR
conn.GetScalar<Dog, int>(x => x.BirthDate.Year)

// SELECT "Id","Name","Breed","DareBirth","Weight" FROM DOG WHERE MONTH("BirthDate") = 10
conn.Select<Dog>(x => Sql.Month(x.BirthDate) = 10)
// OR
conn.Select<Dog>(x => x.BirthDate.Month = 10)
```

#### Aggregation function

```csharp
// SELECT MAX("BirthDate") FROM DOG
conn.GetScalar<Dog, DateTime>(x => Sql.Max(x.BirthDate))
// SELECT AVG("Weight") FROM DOG
conn.GetScalar<Dog, decimal>(x => Sql.Avg(x.Weight))
```


## INSERT, UPDATE and DELETEs

To see the behaviour of the different APIs, all sample uses this simple model

```csharp
public class Person
{
	public int Id { get; set; }
	public string FirstName { get; set; }
	public string LastName { get; set; }
	public int? Age { get; set; }
}
```

### Update

The "Update" method will always update up to one row by generating the where clause using PrimaryKey definitions

```csharp
//UPDATE "Person" SET "FirstName" = 'Jimi',"LastName" = 'Hendrix',"Age" = 27 WHERE "Id" = 1
db.Update(new Person { Id = 1, FirstName = "Jimi", LastName = "Hendrix", Age = 27});
```

To update only some columns, you can use the "onlyField" parameter

```csharp
//UPDATE "Person" SET "Age" = 27 WHERE "Id" = 1
db.Update(new Person { Id = 1, Age = 27}, x => x.Age);
```

Anonymous object can also be used

```csharp
//UPDATE "Person" SET "LastName" = 'Hendrix',"Age" = 27 WHERE "Id" = 1
db.Update<Person>(new { Id = 1, LastName = "Hendrix", Age = 27}, x => new {x.Age, x.LastName});
```

### UpdateAll

The "UpdateAll" method will update rows using the specified where clause (if any).

```csharp
//UPDATE "Person" SET "FirstName" = 'JJ'
db.UpdateAll(new Person { FirstName = "JJ" }, p => p.FirstName);
//UPDATE "Person" SET "FirstName" = 'JJ' WHERE AGE > 27
db.UpdateAll(new Person { FirstName = "JJ" }, p => p.FirstName, x => x.Age > 27);
```

### INSERT

#### Insert a single row

```csharp
//INSERT INTO "Person" ("Id","FirstName","LastName","Age") VALUES (1,'Jimi','Hendrix',27)
db.Insert(new Person { Id = 1, FirstName = "Jimi", LastName = "Hendrix", Age = 27 });
```

#### Insert multiple rows

```csharp
//INSERT INTO "Person" ("Id","FirstName","LastName","Age") VALUES (1,'Jimi','Hendrix',27)
db.Insert(new []{
   new Person { Id = 1, FirstName = "Jimi", LastName = "Hendrix", Age = 27 },
   new Person { Id = 2, FirstName = "Kurt", LastName = "Cobain", Age = 27 },
   });
```

#### AutoIncremented Primary Keys

if you specify a PrimaryKey as AutoIncrement, the PrimaryKey is not added in the INSERT query

```csharp
public class Person
{
   [PrimaryKey]
   [AutoIncrement]
	public int Id { get; set; }
	public string FirstName { get; set; }
	public string LastName { get; set; }
	public int? Age { get; set; }
}

//INSERT INTO "Person" ("FirstName","LastName","Age") VALUES ('Jimi','Hendrix',27)
db.Insert(new Person { FirstName = "Jimi", LastName = "Hendrix", Age = 27 });

```

### Delete

#### Delete a single row

The "Delete" method will always delete up to one row by generating the where clause using PrimaryKey definitions

```csharp
//DELETE FROM "Person" WHERE ("Id" = 2)
db.Delete(new Person{Id = 2});
```

#### DeleteAll
Or an Expression Visitor:
```csharp
//DELETE FROM "Person" WHERE ("Age" = 27)
db.DeleteAll<Person>(x => x.Age = 27);
```

### Available Attributes

#### Query Attributes

##### AliasAttribute
This attribute allow you to change the default naming of Tables or Columns used in the database.
```csharp
[Alias("TableName")]
class Test
{
    [Alias("Id"]
    public string TestId{get;set;}
}

db.Select<Test>(); // SELECT Id FROM TableName
```

##### IgnoreAttribute
This attribute allow you to specify that a Property must be ignored during query generation.
```csharp
class Test
{
    public string TestId{get;set;}

    [Ignored]
    public int IgnoredProperty{get;set;}
}

db.Select<Test>(); // SELECT TestId FROM Test
```

##### ComputedAttribute
This attribute allow you to specify an Sql Expression the compute the value of a Property.
```csharp
class Test
{
    public string TestId{get;set;}

    [Computed("COALESCE(TestId, 'default')]
    public string ComputedProperty{get;set;}
}

db.Select<Test>(); // SELECT TestId FROM Test
```

##### SchemaAttribute
This attribute can be added on a class to specify the Schema name.
```csharp
[Schema("T22")]
class Dog
{
    public string Name{get;set;}
}

db.Select<Dog>(); // SELECT Name FROM T22.Dog
```

#### Table Creation Attributes

```csharp
[Alias("SomeTableName")]
class MyComplexType
{
    [PrimaryKey]
    public string Id{get;set;}

    [PrimaryKey]
    public DateTime CreationDate{get;set;}

    [Nullable]
    public string Email{get;set;}

    [ForeignKey(typeof(OtherClass))]
    public int ForeignKey{get;set;}
}

db.Select<Dog>(); // SELECT Name FROM T22.Dog
```



##### PrimaryKeyAttribute
The Primary Key attribute can be used to decorate one or multiple Properties of you Poco to specify the column to use as Primary Key.
This attribute is used to identify your objects in methods Insert/Update/Delete and while creating tables.
##### AutoIncrementAttribute
Specify a property as auto incremented.
##### ForeignKeyAttribute
Specify a property as a ForeignKey
##### IndexAttribute
Specify a property as indexed
##### DefaultAttribute
Specify Default value for a column
##### RequiredAttribute
Specify is a Property is nullable (mainly used for string, Nullable<> type are automatically detected)
##### StringLengthAttribute
Specify the maximum size of string properties
##### DecimalLengthAttribute
Specify the Precision and Scale of numeric Properties

### Table Creation

### Retrieving Database Metadata

### Dynamic Queries

### Thanks
