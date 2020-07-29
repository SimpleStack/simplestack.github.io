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
This attribute allows you to use a different name for the table than the class name.
## Schema
This attribute can be added on a class to specify the Schema name.

# Properties Attributes
## Alias
This attribute allows you to use a different name than the property name for the table field.
## Ignore
This attribute allows you to specify that a Property must be ignored during query generation.
## Compute
This attribute allows you to specify an Sql Expression which computes the value of a Property.
## PrimaryKey
The Primary Key attribute can be used to decorate one or multiple Properties of your choice to specify the column(s) to use as Primary Key.
This attribute is used to identify your objects in methods Insert/Update/Delete.
## AutoIncrement
Specifies a property as auto incremented.
## ForeignKey
Specifies a property as a ForeignKey.
## Index
Specifies a property as indexed. 
## Default
Specifies the default value for a column.
## Required
Specifies whether a Property is nullable (mainly used for strings, because Nullable<> types are automatically detected)
## StringLength
Specifies the maximum size of string properties. If omitted the default length (255) will be applied.

The default length can be changed in the DialectProvider
```csharp
_connectionFactory.DialectProvider.TypesMapper.DefaultStringLength = 1000;
``` 
## DecimalLength
Specifies the Precision and Scale of numeric Properties. If omitted, the default precision (38) and scale (6) will be applied

The default precision and scale can be changed in the DialectProvider
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
