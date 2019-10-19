# Linq-fundamentals
**Language Integrated Query (LINQ)** introduces fundamental changes to the  techniques .NET developers will use to access data. 

**LINQ** brings  general-purpose query facilities to the .NET framework and allows a  developer to query data inside of in-memory objects, data inside of  relational databases, and data inside of XML using a uniform API and  syntax.

There are several varieties of **LINQ**:

- **LINQ** to **Objects**: used for working with arrays and collections

- **LINQ** to **Entities**: used when accessing databases through Entity Framework technology

- **LINQ** to **Sql**: data access technology in MS SQL Server

- **LINQ** to **XML**: used when working with XML files

- **LINQ** to **DataSet**: used when working with a DataSet

- **Parallel** **LINQ** (**PLINQ**): used to execute parallel queries

  ```c#
  string [] teams = {"Bayern", "Borussia", "Real Madrid", "Manchester City", "PSG", "Barcelona"};
   
  var selectedTeams = from t in teams // define each object from teams as t
                       where t.ToUpper (). StartsWith ("B") // filtering by criteria
                       orderby t // order ascending
                       select t; // select the object
   
  foreach (string s in selectedTeams)
       Console.WriteLine (s);
  ```

## List of LINQ Extension Methods Used

1. **Select**: defines the projection of the selected values

   ```c#
   // fetch names
   var names = users.Select (u => u.Name);
    
   // fetch objects of anonymous type
   var items = users.Select (u => new
   {
        FirstName = u.Name,
        DateOfBirth = DateTime.Now.Year - u.Age
   });
   ```

   ```c#
   var selectedUsers = users.SelectMany (u => u.Languages,
                                (u, l) => new {User = u, Lang = l})
                              .Where (u => u.Lang == "English" && u.User.Age <28)
                              .Select (u => u.User);
   ```

2. **Where**: defines the filter of selection

3. **OrderBy**: Sort items in ascending order

   ```c#
   int [] numbers = {3, 12, 4, 10, 34, 20, 55, -66, 77, 88, 4};
   var orderedNumbers = from i in numbers
                         orderby i
                         select i;
   foreach (int i in orderedNumbers)
        Console.WriteLine (i);
   ```

   ```c#
   int [] numbers = {3, 12, 4, 10, 34, 20, 55, -66, 77, 88, 4};
   IEnumerable <int> sortedNumbers = numbers.OrderBy (i => i);
                
   List <User> users = new List <User> ()
   {
        new User {Name = "Tom", Age = 33},
        new User {Name = "Bob", Age = 30},
        new User {Name = "Tom", Age = 21},
        new User {Name = "Sam", Age = 43}
   };
   var sortedUsers = users.OrderBy (u => u.Name);
   ```

4. **OrderByDescending**: Sort items in descending order.

5. **ThenBy**: Specifies additional criteria for arranging items in ascending order.

   ```c#
   var result = users.OrderBy(u => u.Name).ThenBy(u => u.Age);
   ```

6. **ThenByDescending**: sets additional criteria for sorting items in descending order

7. **Join**: connects two collections by a specific attribute

   ```c#
   var result = players.Join (teams, // second set
                 p => p.Team, // property-selector of an object from the first set
                 t => t.Name, // property-selector of an object from the second set
                 (p, t) => new {Name = p.Name, Team = p.Team, Country = t.Country}); // result
   ```

8. **GroupBy**: groups items by key

   ```c#
   List<Phone> phones = new List<Phone>
   {
       new Phone {Name="Lumia 430", Company="Microsoft" },
       new Phone {Name="Mi 5", Company="Xiaomi" },
       new Phone {Name="LG G 3", Company="LG" },
       new Phone {Name="iPhone 5", Company="Apple" },
       new Phone {Name="Lumia 930", Company="Microsoft" },
       new Phone {Name="iPhone 6", Company="Apple" },
       new Phone {Name="Lumia 630", Company="Microsoft" },
       new Phone {Name="LG G 4", Company="LG" }
   };
    
   var phoneGroups = from phone in phones
                     group phone by phone.Company;
    
   foreach (IGrouping<string, Phone> g in phoneGroups)
   {
       Console.WriteLine(g.Key);
       foreach (var t in g)
           Console.WriteLine(t.Name);
       Console.WriteLine();
   }
   ```

   ```c#
   var phoneGroups2 = from phone in phones
                      group phone by phone.Company into g
                      select new { Name = g.Key, Count = g.Count() };
   foreach (var group in phoneGroups2)
       Console.WriteLine($"{group.Name} : {group.Count}");
   ```

   ```c#
   	var phoneGroups = phones.GroupBy(p => p.Company)
                           .Select(g => new { Name = g.Key, Count = g.Count() });
   ```

   ```c#
   var phoneGroups2 = from phone in phones
                      group phone by phone.Company into g
                      select new
                      {
                           Name = g.Key,
                           Count = g.Count(),
                           Phones = from p in g select p
                      };
   foreach (var group in phoneGroups2)
   {
       Console.WriteLine($"{group.Name} : {group.Count}");
       foreach(Phone phone in group.Phones)
           Console.WriteLine(phone.Name);
       Console.WriteLine();
   ```

9. **ToLookup**: groups items by key, with all items added to the dictionary

10. **GroupJoin**: simultaneously connects collections and groups items by key

    ```c#
    var result2 = teams.GroupJoin (
                             players, // second set
                             t => t.Name, // property-selector of an object from the first set
                             pl => pl.Team, // property-selector of an object from the second set
                             (team, pls) => new // result object
                             {
                                 Name = team.Name,
                                 Country = team.Country,
                                 Players = pls.Select (p => p.Name)
                             });
     
    foreach (var team in result2)
    {
         Console.WriteLine (team.Name);
         foreach (string player in team.Players)
         {
             Console.WriteLine (player);
         }
         Console.WriteLine ();
    }
    ```

11. **Reverse**: Reorders items

12. **All**: Determines whether all items in the collection satisfy a specific condition.

    ```c#
    List <User> users = new List <User> ()
    {
         new User {Name = "Tom", Age = 23},
         new User {Name = "Sam", Age = 43},
         new User {Name = "Bill", Age = 35}
    };
     
    bool result1 = users.All (u => u.Age> 20); // true
    if (result1)
         Console.WriteLine ("All users are over the age of 20");
    else
         Console.WriteLine ("There are users with an age less than 20");
     
    bool result2 = users.All (u => u.Name.StartsWith ("T")); // false
    if (result2)
         Console.WriteLine ("For all users, the name begins with T");
    else
         Console.WriteLine ("Not all users have a name starting with T");
    ```

13. **Any**: defines, satisfies at least one element of the collection to a certain condition

    ```c#
    List <User> users = new List <User> ()
    {
         new User {Name = "Tom", Age = 23},
         new User {Name = "Sam", Age = 43},
         new User {Name = "Bill", Age = 35}
    };
     
    bool result1 = users.All (u => u.Age> 20); // true
    if (result1)
         Console.WriteLine ("All users are over the age of 20");
    else
         Console.WriteLine ("There are users with an age less than 20");
     
    bool result2 = users.All (u => u.Name.StartsWith ("T")); // false
    if (result2)
         Console.WriteLine ("For all users, the name begins with T");
    else
         Console.WriteLine ("Not all users have a name starting with T");...
    ```

14. **Contains**: Determines if the collection contains a specific item.

15. **Distinct**: removes duplicate items from the collection

    ```c#
    var result = soft.Concat(hard).Distinct();
    ```

16. **Except**: returns the difference of two collections, that is, those elements that are only in one collection

    ```c#
    string [] soft = {"Microsoft", "Google", "Apple"};
    string [] hard = {"Apple", "IBM", "Samsung"};
     
    // set difference
    var result = soft.Except (hard);
     
    foreach (string s in result)
         Console.WriteLine (s);
    ```

17. **Union**: combines two homogeneous collections

    ```c#
    string [] soft = {"Microsoft", "Google", "Apple"};
    string [] hard = {"Apple", "IBM", "Samsung"};
     
    // union of sets
    var result = soft.Union (hard);
     
    foreach (string s in result)
         Console.WriteLine (s);
    ```

18. **Intersect**: returns the intersection of two collections, i.e. those elements that occur in both collections

    ```c#
    string [] soft = {"Microsoft", "Google", "Apple"};
    string [] hard = {"Apple", "IBM", "Samsung"};
     
    // intersection of many
    var result = soft.Intersect (hard);
     
    foreach (string s in result)
         Console.WriteLine (s);
    ```

19. **Count**: Counts the number of collection items that satisfy a specific condition.

    ```c#
    int [] numbers = {1, 2, 3, 4, 10, 34, 55, 66, 77, 88};
    int size = (from i in numbers where i% 2 == 0 && i> 10 select i) .Count ();
    Console.WriteLine (size);
    ```

20. **Sum**: counts the sum of the numerical values in the collection

    ```c#
    int[] numbers = { 1, 2, 3, 4, 10, 34, 55, 66, 77, 88 };
    List<User> users = new List<User>()
    {
        new User { Name = "Tom", Age = 23 },
        new User { Name = "Sam", Age = 43 },
        new User { Name = "Bill", Age = 35 }
    };
     
    int sum1 = numbers.Sum();
    decimal sum2 = users.Sum(n => n.Age);
    ```

21. **Average**: calculates the average of the numerical values in the collection

22. **Min**: finds the minimum value

    ```c#
    int [] numbers = {1, 2, 3, 4, 10, 34, 55, 66, 77, 88};
    List <User> users = new List <User> ()
    {
         new User {Name = "Tom", Age = 23},
         new User {Name = "Sam", Age = 43},
         new User {Name = "Bill", Age = 35}
    };
     
    int min1 = numbers.Min ();
    int min2 = users.Min (n => n.Age); // minimum age
     
    int max1 = numbers.Max ();
    int max2 = users.Max (n => n.Age); // maximum age
     
    double avr1 = numbers.Average ();
    double avr2 = users.Average (n => n.Age); //average age
    ```

23. **Max**: finds the maximum value

24. **Take**: selects a certain number of elements

    ```c#
    int[] numbers = { -3, -2, -1, 0, 1, 2, 3 };
    var result = numbers.Take(3);
     
    foreach (int i in result)
        Console.WriteLine(i);
    ```

25. **Skip**: skips a certain number of elements

    ```c#
    int[] numbers = { -3, -2, -1, 0, 1, 2, 3 };
    var result = numbers.Skip(4).Take(3);
     
    foreach (int i in result)
        Console.WriteLine(i);
    ```

26. **TakeWhile**: returns a chain of sequence elements, as long as the condition is true

    ```c#
    string [] teams = {"Bayern", "Borussia", "Real Madrid", "Manchester City", "PSG", "Barcelona"};
    foreach (var t in teams.TakeWhile (x => x.StartsWith ("B")))
         Console.WriteLine (t);
    ```

27. **SkipWhile**: skips the elements in the sequence until they satisfy the specified condition, and then returns the remaining elements

    ```c#
    string [] teams = {"Bayern", "Borussia", "Real Madrid", "Manchester City", "PSG", "Barcelona"};
    foreach (var t in teams.SkipWhile (x => x.StartsWith ("B")))
         Console.WriteLine (t);
    ```

28. **Concat**: combines two collections

    ```c#
    var result = soft.Concat(hard);
    ```

29. **Zip**: combines two collections according to a specific condition

    ```c#
    List <Team> teams = new List <Team> ()
    {
         new Team {Name = "Bayern", Country = "Germany"},
         new Team {Name = "Barcelona", Country = "Spain"},
         new Team {Name = "Juventus", Country = "Italy"}
    };
    List <Player> players = new List <Player> ()
    {
         new Player {Name = "Robben", Team = "Bayern"},
         new Player {Name = "Neymar", Team = "Barcelona"},
         new Player {Name = "Buffon", Team = "Juventus"}
    };
    var result2 = players.Zip (teams,
                               (player, team) => new
                                {
                                     Name = player.Name,
                                     Team = team.Name, Country = team.Country
                                });
    foreach (var player in result2)
    {
         Console.WriteLine ($ "{player.Name} - {player.Team} ({player.Country})");
                     
         Console.WriteLine ();
    }
    ```

30. **First**: selects the first item in the collection

31. **FirstOrDefault**: selects the first item in the collection or returns the default value

32. **Single**: selects a single element of the collection, if the collection contains more or less than one element, an exception is thrown

33. **SingleOrDefault**: selects the first item in the collection or returns the default value

34. **ElementAt**: selects an element of a sequence at a specific index

35. **ElementAtOrDefault**: selects a collection item at a specific index or returns the default value if the index is out of range

36. **Last**: selects the last item in the collection

37. **LastOrDefault**: selects the last item in the collection or returns the default value

## Delayed and immediate execution of LINQ

There are two ways to execute a LINQ query: **deferred** and **immediate** execution.

In deferred execution, the LINQ expression is not executed until iterating or iterating over the selection. Consider deferred execution:

```c#
string [] teams = {"Bayern", "Borussia", "Real Madrid", "Manchester City", "PSG", "Barcelona"};
 
var selectedTeams = from t in teams where t.ToUpper (). StartsWith ("B") orderby t select t;
 
// execute LINQ query
foreach (string s in selectedTeams)
     Console.WriteLine (s);
```

That is, the actual execution of the request does not occur in the definition line: 

```c#
var selectedTeams = from t ...,
```

 but when iterates through the foreach loop.

In fact, a LINQ query breaks down into three steps:

1. Getting data source
2. Request creation
3. Fulfillment of the request and obtaining its results

How does this happen in our case:

1. Getting the data source - definition of the teams array:

```c#
string [] teams = {"Bayern", "Borussia", "Real Madrid", "Manchester City", "PSG", "Barcelona"}; 
```

2. Create a query - define the selectedTeams variable:

   ```c#
   var selectedTeams = from t in teams where t.ToUpper (). StartsWith ("B") orderby t select t;
   ```

   3. Fulfillment of the request and obtaining its results:

```c#
foreach (string s in selectedTeams)
    Console.WriteLine(s);
```

After defining a query, it can be executed many times. And until the request is completed, the data source may change. To more clearly see this, we can change some element before iterating over the selection:

```c#
var selectedTeams = from t in teams where t.ToUpper (). StartsWith ("B") orderby t select t;
// change the array after defining the LINQ query
teams [1] = "Juventus";
// execute LINQ query
foreach (string s in selectedTeams)
     Console.WriteLine (s);
```

Now the selection will contain two elements, not three, since the second element after the change will not meet the condition.

It is important to understand that the query variable by itself does not perform any actions and does not return any data. It only stores the set of commands that are needed to get the results. That is, the execution of the request after its creation is postponed. The results themselves are produced when iterating through the foreach loop.

#### Request Execution Immediately

Using a number of methods, we can apply immediate query execution. These are methods that return a single atomic value or a single element. For example, *Count (), Average (), First () / FirstOrDefault (), Min (), Max ()*, etc. For example, the Count () method returns a numeric value that represents the number of elements in the resulting sequence. And the First () method returns the first element of the sequence. But in order to execute these methods, first you need to get the sequence itself, that is, the result of the request, and go through it with a foreach loop, which is called implicitly inside the request structure.

Consider an example with the Count () method, which returns the number of elements in a sequence:

```c#
string [] teams = {"Bayern", "Borussia", "Real Madrid", "Manchester City", "PSG", "Barcelona"};
// define and execute a LINQ query
int i = (from t in teams
         where t.ToUpper (). StartsWith ("B")
         orderby t select t) .Count ();
Console.WriteLine (i); // 3
teams [1] = "Juventus";
Console.WriteLine (i); // 3
```

The result of the Count method will be an int object, so immediate execution will work.

First, a request is created:

```c#
 from t in teams where t.ToUpper (). StartsWith ("B") orderby t select t. 
```

Then the Count () method is applied to it, which executes the query, implicitly iterates over the sequence of elements generated by this query, and returns the number of elements in this sequence.

We can also change the code so that the Count () method takes into account the changes and runs separately from the request definition:

```c#
string [] teams = {"Bayern", "Borussia", "Real Madrid", "Manchester City", "PSG", "Barcelona"};
// define a LINQ request
var selectedTeams = from t in teams
                     where t.ToUpper (). StartsWith ("B")
                     orderby t select t;
// query execution
Console.WriteLine (selectedTeams.Count ()); // 3
teams [1] = "Juventus";
// query execution
Console.WriteLine (selectedTeams.Count ()); // 2
```

Also, for immediate execution of the LINQ query and caching of its results, we can use the conversion methods [ToArray <T> (), ToList <T> (), ToDictionary ()](), etc. These methods receive the query result in the form of Array, List objects and Dictionary, respectively. For example:

```c#
string [] teams = {"Bayern", "Borussia", "Real Madrid", "Manchester City", "PSG", "Barcelona"};
// execute LINQ query
var selectedTeams = (from t in teams
                     where t.ToUpper (). StartsWith ("B")
                     orderby t select t) .ToList <string> ();
// changing the array will not affect the selectedTeams list in any way
teams [1] = "Juventus";
 
foreach (string s in selectedTeams)
     Console.WriteLine (s);
```

