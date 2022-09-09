# HarperDB EF Core NuGet Package

## Summary
This NuGet package contains the Driver Dll's created by CDATA for HarperDB.
The drivers in this package work with .NET 6.0 and EntityFramework Core.

This repo contains the .nuspec and the .nupkg files.
The .nuspec file is required to build the .nupkg file which is published to NuGet.

## Requirements
Download the nuget.exe file from Microsoft.

## How to Build
Run `nuget.exe pack` inside of this directory. If the nuget.exe file is not in the path, then it can be copied to this directory (but please do not commit it).

## How to Test
1. Use Visual Studio 2022 to create a C# Console App
2. Add the following two NuGet Packages
 - Install-Package Microsoft.EntityFrameworkCore -Version 6.0
 - Install-Package Microsoft.EntityFrameworkCore.Relational -Version 6.0
3. Add a local NuGet Package Source to use this .nupkg file locally
  - in VS2022, go to Tools -> NuGet Package Manager -> Package Manager Settings -> Package Sources
  - Add a new source, use the directory this repo is cloned into locally as the Source (ex. C:\Users\micro\Downloads\HDBNuGet)
4. Install this package - Install-Package HarperDB.EntityFrameworkCore
5. If reinstalling, make sure to delete the cached version in C:\Users\micro\.nuget\packages
6. Create a local HarperDB instance with a `dbo` schema and `Customers` table, and a record with `CompanyName`
7. Use the following code to create three classes

###
**Program.cs**
```
using System.Linq;

namespace HDBApp3_EFC
{
    internal class Program
    {
        static void Main(string[] args)
        {
            Console.WriteLine("Hello, World!");
            HarperDBContext ents = new HarperDBContext();
            var CutomersQuery = from Cutomers in ents.Cutomers
                                orderby Cutomers.CompanyName
                                select Cutomers;
            foreach (var Cutomers in CutomersQuery)
            {
                var cn = Cutomers.CompanyName;
                Console.WriteLine("Customer");
                Console.WriteLine(cn);
            }
        }
    }
}
```

**HDBContext.cs**
```
using Microsoft.EntityFrameworkCore;

public class HarperDBContext : DbContext
{
    public DbSet<Cutomers> Cutomers { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        if (!optionsBuilder.IsConfigured)
        {
            optionsBuilder.UseHarperDB("Server=172.18.49.216;User=hdbcf;Password=hdbcf;Port=9925");
        }
    }
}
```

**Customers.cs**
```
using System.ComponentModel.DataAnnotations.Schema;

[Table("Customers", Schema = "dbo")]

public class Cutomers
{
    public string Id { get; set; }
    public string CompanyName { get; set; }
}
```

8. Attempt to build and run.
9. Repeat until successful
