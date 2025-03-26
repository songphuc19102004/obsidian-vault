> EntityFrameworkCore is light-weight, extensible and cross-platform framework for accessing databases in .NET applications.  
> It is the most-used database framework for  
> [Asp.Net](http://asp.net/) Core Apps.

  

![[/Untitled 120.png|Untitled 120.png]]

  

**EFCore Models**

![[/Untitled 1 11.png|Untitled 1 11.png]]

### **Pros & Cons of EntityFrameworkCore**

**Shorter Code**

The CRUD operations / calling stored procedures are done with shorter amount of code than ADO.NET.

**Performance**

EFCore performs slower than ADO.NET.

So ADO.NET or its alternatives (such as Dapper) are recommended for larger & high-traffic applications.

**Strongly-Typed**

The columns as created as properties in model class.

So the Intellisense offers columns of the table as properties, while writing the code.

Plus, the developer need not convert data types of values; it's automatically done by EFCore itself.

  

### Approaches in Entity Framework Core

**EFCore Approaches**

![[/Untitled 2 9.png|Untitled 2 9.png]]

### Pros and Cons of EFCore Approaches

**CodeFirst Approach**

Suitable for newer databases.

Manual changes to DB will be most probably lost because your code defines the database.

Stored procedures are to be written as a part of C# code.

Suitable for smaller applications or prototype-level applications only; but not for larger or high data-intense applications.

**DbFirst Approach(Dapper is better)**

Suitable if you have an existing database or DB designed by DBAs, developed separately.

Manual changes to DB can be done independently.

Stored procedures, indexes, triggers etc., can be created with T-SQL independently.

Suitable for larger applications and high data-intense applications.

  

### DbContext and DbSet

![[/Untitled 3 8.png|Untitled 3 8.png]]

**DbContext**

An instance of DbContext is responsible to hold a set of DbSets' and represent a connection with database.

**DbSet**

Represents a single database table; each column is represented as a model property.

  

**Add DbContext as Service in Program.cs:**

We have to add ConnectionStrings into the appsetting.json.

```JavaScript
builder.Services.AddDbContext<DbContextClassName>( options => {
	options.UseSqlServer();
	}
);
```

Now, we will put ConnectionStrings to appsettings.json

```C#
{
	"Logging": {
	"LogLevel": {
	"Default": "Information",
	"Microsoft.AspNetCore": "Warning"
		}
	},
	"AllowedHosts": "*",
	"ConnectionStrings": {
		"DefaultConnection": "Data Source=PHUC\\PHUC;Initial Catalog=PersonsDatabase;User ID=sa;Password=123456789;Connect Timeout=30;Encrypt=False;Trust Server Certificate=False;Application Intent=ReadWrite;Multi Subnet Failover=False"
	}
}
```

After that, we will get the ConnectionString and put it in sqlserver option at program.cs

```C#
builder.Services.AddDbContext<DbContextClassName>( options => {
	options.UseSqlServer(builder.Configuration.GetConnectionString("DefaultConnection"));
});
```

  

Now assumed we have already create Person and Country tables, but it has no rows(no data), so we would want to add some initial data to them tables. It called **Seed Data**

## Seed data

> It adds initial data(initial rows) in tables, when the database is newly created

In DbContext, we will add some code:

```C#
using System;
using System.Collections.Generic;
using Microsoft.EntityFrameworkCore;

namespace Entities
{
  public class PersonsDbContext : DbContext
  {
    public DbSet<Country> Countries { get; set; }
    public DbSet<Person> Persons { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
      base.OnModelCreating(modelBuilder);

      modelBuilder.Entity<Country>().ToTable("Countries");
      modelBuilder.Entity<Person>().ToTable("Persons");

    //Seed data to countries
    modelBuilder.Entity<Country>().HasData(new Country() {
        CountryID = Guid.NewGuid(),
        CountryName = "Tien Giang"
    });
    //
    }
  }
}
```

Another prefer way is add all data in Json format and read that json format to call HasData() method

```C#
using System;
using System.Collections.Generic;
using Microsoft.EntityFrameworkCore;

namespace Entities {
    public class PersonsDbContext : DbContext {
        public DbSet<Country> Countries { get; set; }
        public DbSet<Person> Persons { get; set; }

        protected override void OnModelCreating(ModelBuilder modelBuilder) {
            base.OnModelCreating(modelBuilder);

            modelBuilder.Entity<Country>().ToTable("Countries");
            modelBuilder.Entity<Person>().ToTable("Persons");

            //Seed data to countries
            string countriesJson = System.IO.File.ReadAllText("countries.json");
            //deserialize from json string to List of countries
            List<Country> countries = System.Text.Json.JsonSerializer.Deserialize<List<Country>>(countriesJson);
            //now foreach and use HasData
            foreach(Country country in countries) {
                modelBuilder.Entity<Country>().HasData(country);
            }

            //Seed data to persons
            string personJson = System.IO.File.ReadAllText("persons.json");
            //deserialize from json string to List of persons
            List<Person> persons = System.Text.Json.JsonSerializer.Deserialize<List<Person>>(personJson);
            //now foreach and use HasData
            foreach(Person person in persons) {
                modelBuilder.Entity<Person>().HasData(person);
            }
        }
    }
}
```

Now, when we migrate, table will be created with some initial data

  

## Migration

> Creates or updates database based on the changes made in the model  
> Everytime we want to change structure, attribute of the tables, we run Migration  

- First, we will have to install Nuget package called “==**Microsoft.EntityFrameworkCore.**====**Tools**==” at which DbContext file resides. Then we will have to install Nuget package called “==**Microsoft.EntityFrameworkCore.**====**Design**==” in the start up project.
- After that, we will have to set options to **PersonsDbContext** class, because it need to know the DbContext provider(Sql server, postgres,mysql,…), which is UseSqlServer() in Program.cs, but since we didn’t create any constructor in the **PersonsDbContext** class, it didn’t know. Now we have to add it

```C#
using System;
using System.Collections.Generic;
using Microsoft.EntityFrameworkCore;

namespace Entities {
    public class PersonsDbContext : DbContext {
        public DbSet<Country> Countries { get; set; }
        public DbSet<Person> Persons { get; set; }

        public PersonsDbContext(DbContextOptions options) : base(options) {
        }

        protected override void OnModelCreating(ModelBuilder modelBuilder) {
            base.OnModelCreating(modelBuilder);

            modelBuilder.Entity<Country>().ToTable("Countries");
            modelBuilder.Entity<Person>().ToTable("Persons");

            //Seed data to countries
            string countriesJson = System.IO.File.ReadAllText("countries.json");
            //deserialize from json string to List of countries
            List<Country> countries = System.Text.Json.JsonSerializer.Deserialize<List<Country>>(countriesJson);
            //now foreach and use HasData
            foreach(Country country in countries) {
                modelBuilder.Entity<Country>().HasData(country);
            }

            //Seed data to persons
            string personJson = System.IO.File.ReadAllText("persons.json");
            //deserialize from json string to List of persons
            List<Person> persons = System.Text.Json.JsonSerializer.Deserialize<List<Person>>(personJson);
            //now foreach and use HasData
            foreach(Person person in persons) {
                modelBuilder.Entity<Person>().HasData(person);
            }
        }
    }
}
 
```

Now, what ever options we chose in Program.cs file will supply to the constructor of the DbContext class

  

For the first migration, open up nuget package console and type:

> Add-Migration Initial

Initial is preferred name for the first migration