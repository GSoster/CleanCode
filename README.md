# CleanCode

Clean Code tips I learned from working daily with programming with C#.  

Clean Code should:
> Be easily readeable.  
> Reveals its intent.

### Output parameters:
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

