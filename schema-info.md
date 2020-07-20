---
title: SimpleStack - Database Schema
hero_title:
layout: page
hero_image: /assets/background.jpg
show_sidebar: false
menubar: home_menu
---

SimpleStack allow you to read or update the database schema
# Reading Database Schema
Retrieving available tables or views
```csharp
var tables = await conn.GetTablesAsync();
var tables = await conn.GetTablesAsync(schemaName:"myschema");
var tables = await conn.GetTablesAsync(schemaName:"myschema", includeViews:true);



```

Retrieving table or view columns
```csharp
var columns = await conn.GetTableColumnsAsync("mytable");
var columns = await conn.GetTablesAsync(schemaName:"myschema");
var columns = await conn.GetTablesAsync(schemaName:"myschema", includeViews:true);

interface IColumnDefinition
{
    string Name { get; }
    bool Nullable { get; }
    int? Length { get; }
    int? Precision { get; }
    int? Scale { get; }
    string Definition { get; }
    DbType DbType { get; }
    bool PrimaryKey { get; }
    bool Unique { get; }
    string DefaultValue { get; }
}
```

# Updating Database Schema
```csharp

```