---
tags: 'dotnet core' EntityFramework EF 'EF Core' SQL postgres LINQ
---

# Executing raw SQL with EntityFramework

Throughout the interwebs, you will find a solution for executing raw SQL using EntityFramework:  
  
```C#
dbContext.DbSet.FromSql(queryString)
```  
  
This method does not actually execute raw SQL. It interprets the SQL query and then attempts to generate a LINQ query from it. This will fail if you have SQL methods in your query string. It can also fail for some parameter types. Finally, even if it works, it will wrap your query in another query in order to make it compatible with LINQ, whether or not you actually use LINQ.  
  
In general, this is not a useful method in my opinion.  

If you do not need to retrieve data, there's another method available that actually executes raw SQL:  
  
```C#
dbContext.DbSet.ExecuteSqlCommandAsync(queryString);
```  
  
This command returns an int reflecting the number of records affected.  
  
  
But what if I want to perform a SELECT query using raw SQL? Well, EntityFramework has no option for you. However, you are in luck because there's another library that can be used with EF to accomplish your goal. It's called Dapper.  
  
Dapper provides several raw SQL execution methods that can be applied to an IDbConnection. And you can access the IDbConnection from EF, like so:  
```c#
using Dapper;
IDbConnection dbConnection = dbContext.Database.GetPgSqlConnection();
var results = await dbConnection.QueryFirstOrDefaultAsync(queryString);
```  
  
The QueryFirstOrDefaultAsync method is an extension method provided by Dapper.

