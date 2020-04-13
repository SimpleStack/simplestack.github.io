---
title: SimpleStack - Querying Data 
hero_title:
layout: page
hero_image: /assets/background.jpg
show_sidebar: false
menubar: home_menu
---
# Insert Statements
```csharp
conn.Insert(new Dog{
    Name="Popol", 
    BirthDate = new DateTime(1918,09,13), 
    Weight=2
});
```

# Update Statements

## Update based on PrimaryKey attribute
```csharp
// Update Dogs SET Breed = dog.Breed, Name = dog.Name ...  WHERE Id  = dog.Id
conn.Update(dog);

// Update Dogs Set Breed = dog.Breed, Name = dog.Name WHERE Id = dog.od
conn.Update(dog, x => new {x.Breed, x.Name} );
```
Note : Composite primary keys are supported

## Update using custom where expression

```csharp
// UPDATE Dogs SET Breed = null
conn.UpdateAll<Dog>(new {Breed = null});

// UPDATE Dogs SET Breed = 'A' WHERE Name = "Popol'
conn.UpdateAll<Dog>(new {Breed = 'A'}, x => x.Name = 'Popol');

// Update Dogs SET Breed = dog.Breed, Name = dog.Name WHERE Weight > 3
conn.UpdateAll<Dog>(dog, x => x.Weight > 3, x => new {x.Breed, x.Name} );
```

# Delete Statements

## Delete based on PrimaryKey attribute

```csharp
// DELETE FROM DOGS WHERE Id = dog.Id
conn.Delete(dog);
```
Note : Composite primary keys are supported

## Delete using custom where expression
```csharp
// DELETE FROM DOGS WHERE Weight >= 2
conn.DeleteAll(x => x.Weight >= 2);
```