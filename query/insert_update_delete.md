---
title: SimpleStack - Querying Data 
hero_title:
layout: page
hero_image: /assets/background.jpg
show_sidebar: false
menubar: home_menu
---
# Insert Statements
Inserting data is very simple and straightforward
```csharp
await conn.InsertAsync(new Dog{
    Name="Popol", 
    BirthDate = new DateTime(1918,09,13), 
    Weight=2
});
```

# Update Statements

## Update a single row (based on PrimaryKey)

To update a record based on the specified [Primary Key attribute](/attributes). Note that Composite primary keys are supported.
```csharp
// Update Dogs SET Breed = dog.Breed, Name = dog.Name ...  WHERE Id  = dog.Id
await conn.UpdateAsync(dog);
```
It is also possible to update only some columns
```csharp
// Update Dogs Set Breed = dog.Breed, Name = dog.Name WHERE Id = dog.Id
await conn.UpdateAsync(dog, x => new {x.Breed, x.Name} );
```

## Update multiple rows

```csharp
// UPDATE Dogs SET Breed = null
await conn.UpdateAllAsync<Dog>(new {Breed = null});

// UPDATE Dogs SET Breed = 'A' WHERE Name = "Popol'
await conn.UpdateAllAsync<Dog>(new {Breed = 'A'}, x => x.Name = 'Popol');

// Update Dogs SET Breed = dog.Breed, Name = dog.Name WHERE Weight > 3
await conn.UpdateAllAsync<Dog>(dog, x => x.Weight > 3, x => new {x.Breed, x.Name} );
```

# Delete Statements

## Delete a single row (based on PrimaryKey)
To delete a record based on the specified [Primary Key attribute](/attributes). Note that Composite primary keys are supported.
```csharp
// DELETE FROM DOGS WHERE Id = dog.Id
await conn.DeleteAsync(dog);
```

## Delete multiple rows
```csharp
// DELETE FROM DOGS WHERE Weight >= 2
await conn.DeleteAllAsync(x => x.Weight >= 2);
```