---
title: SimpleStack - Attributes Reference
hero_title:
layout: page
hero_image: /assets/background.jpg
show_sidebar: false
menubar: home_menu
---

# Class Attributes
## Alias
This attribute allow you to use a different name for the table than the class name.
## Schema
This attribute can be added on a class to specify the Schema name.

# Properties Attributes
## Alias
This attribute allow you to use a different name than the class name for the table.
## Ignore
This attribute allow you to specify that a Property must be ignored during query generation.
## Compute
This attribute allow you to specify an Sql Expression the compute the value of a Property.
## PrimaryKeyAttribute
The Primary Key attribute can be used to decorate one or multiple Properties of you Type to specify the column(s) to use as Primary Key.
This attribute is used to identify your objects in methods Insert/Update/Delete.
## AutoIncrementAttribute
Specify a property as auto incremented.
## ForeignKeyAttribute
Specify a property as a ForeignKey.
## IndexAttribute
Specify a property as indexed. 
## DefaultAttribute
Specify Default value for a column.
## RequiredAttribute
Specify is a Property is nullable (mainly used for string, Nullable<> type are automatically detected)
## StringLengthAttribute
Specify the maximum size of string properties. If omitted the default length (255) will be applied.

The default length can be changed on the DialectProvider
```csharp
_connectionFactory.DialectProvider.TypesMapper.DefaultStringLength = 1000;
``` 
## DecimalLengthAttribute
Specify the Precision and Scale of numeric Properties. If omitted, the default precision (38) and scale (6) will be applied

The default precision and scale can be changed on the DialectProvider
```csharp
_connectionFactory.DialectProvider.TypesMapper.DefaultPrecision = 8;
_connectionFactory.DialectProvider.TypesMapper.DefaultScale = 4;
``` 
# Example
```csharp
[Alias("dogs")]
[Schema("dev")]
public class Dog
{
    [PrimaryKey()]
    [AutoIncrement()]
    public int Id { get; set; }

    [Index(Unique=true)]
    [StringLength(255)]
    public string Name { get; set; }

    [Alias("birth_date")]
    public DateTime? BirthDate { get; set; }

    [Default(-1)]
    [DecimalLength(Precision = 16, Scale = 5)]
    public decimal Weight { get; set; }    

    [ForeignKey(typeof(Breed))]
    public string BreedId { get; set; }

    [Ignore()]
    public double? AgeInDays
    {
        get => BirthDate.HasValue 
            ? DateTime.Now.Subtract(BirthDate.Value).TotalDays
            : default(double?);
        set => BirthDate = value.HasValue 
            ? DateTime.Now.Subtract(TimeSpan.FromDays(value.Value)) 
            : default(DateTime?);
    }    

    [Compute("Weight * 2.205")]
    public decimal WeightInPounds { get; set; }
}
```