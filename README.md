# CleanCode

Clean Code tips I learned from working daily with programming with C#.  

Clean Code should:
> Be easily readeable.  
> Reveals its intent.

## What this repo is

I only added here what I considered relevant. Here are some notes I gathered from my experience, from books like *clean code* by robert c. martin and from online videos and courses.  
For a more complete documentation on clean code please refear to [https://github.com/thangchung/clean-code-dotnet](https://github.com/thangchung/clean-code-dotnet)

### Output parameters

Avoid as much as possible output parameters, usually it is a good idea to return a class with meaningful properties:  

```cs
// Code smell:
var customersCount = 0;
const int pageIndex = 3;
var customers = GetCustomers(pageIndex);

public IEnumerable<Customer> GetCustomers(int pageIndex, out int count)
{
    count = 100;
    return new List<Customer>();
}
// ################
//Clean:
public class CustomerResult
{
    public IEnumerable<Customer> Customers {get;set;}
    public int Count {get;set;}
}

const int pageIndex = 3;
CustomerResult result = GetCustomers(pageIndex);

public CustomerResult GetCustomers(int pageIndex)
{
    const int count = 100;
    return new CustomerResult() {
        Customers = List<Customer>(),
        Count = count
    };
}
```

But *there are exceptions*!!  
In the .Net world methods like `TryGetValue`and `TryParse` follow the patter of returning a boolean as method return an the desired value as an out parameter:  
[TryGetValue](https://docs.microsoft.com/en-us/dotnet/api/system.collections.generic.dictionary-2.trygetvalue?view=netcore-2.2)  
[TryParse](https://docs.microsoft.com/en-us/dotnet/api/system.int32.tryparse?view=netcore-2.2)

### Number of parameters

As always there is no silver bullet, but it is usually a good idea to keep the number of parameters below 3.  
In case the method signature starts to get too big it is possibly a indicator that either the method is doing too much OR a class should be created to encapsulate it.  

### Return Types instead of null

Sometimes our methods don't have the information that was requested. In those cases what should be returned? Null? Default (type)?

* If the method is expected to return a *collection* (group of items) then it *should return an empty collection*.

```cs
public IEnumerable<User> GetActiveUsers()
{
     //...code here
    // in the negative scenario where we dont have what we expect, just return an empty collection
    return new List<User>();
}
```

* Otherwise if the type is not a collection, it is better to return null (exceptions should be used for non expected behaviour, not common situations)

```cs
public MyFileType LoadFile(string path)
{
    //..code here
    return null;
}

```

### Nested conditionals

There are some ways to avoid nested conditionals:

* Early Exit

```cs
if (a)
{
    if (b)
    {
        //do stuff
    }
}
//becomes:
if (!a)
    return;
if (!b)
    return;
//do stuff

```

* Ternary Operator

```cs
if (player.HitPoints > 50)
    regenBonus = 0.5f;
else
    regenBonus = 0.1f;
//becomes:
regenBonus = (player.HitPoints > 50) ? 0.5f : 0.1f;

```

### Switch Statements

In many cases it can be replaced with polymorphic dispatch.

```cs

//this
public void TakeDamage(float damage)
{
    switch (livingEntity.Type)
    {
        case EntityTypes.Monster:
            livingEntity.HP -= (damage * 1.1);

        case  EntityTypes.ArmoredMonster:
            livingEntity.HP -= (damage * 0.5);

        case  EntityTypes.Player:
            livingEntity.HP -= (damage);
    }
}

//becomes:

public abstract class LivingEntity
{
    public float HP {get;set;}
    public abstract void TakeDamage(float damage);
}

public class Monster : LivingEntity
{
    public override void TakeDamage(float damage) => HP -= (damage * 1.1);
}

public class Player : LivingEntity
{
    public override void TakeDamage(float damage) => HP -= damage;
}

public class ArmoredMonster : LivingEntity
{
    public override void TakeDamage(float damage) => HP -= (damage * 0.5);
}

```
