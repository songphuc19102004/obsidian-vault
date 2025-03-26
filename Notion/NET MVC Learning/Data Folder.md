Any class in **ApplicationDbContext** must be implemented with **Dbcontext** class(sytax: class nameClass : className (implements interfaces still use “:“))

  

**When ever something has to be updated to database, we have to migration**

  

Dbcontext: root class of entity framework core

  

public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options) : base(options){}

base: giống super, gọi class cha

base(options): truyền parameter options vào class Dbcontext

  

Create table:

**Step 1**: Create model (Class with a few properties, like Human)

**Step 2**: create DbSet in ADBCT

```C#
public DbSet<ClassName> TableName { get; set; }
```

```C#
public DbSet<Category> Categories { get; set; }
```

**Step 3**: we have to do **migration**:

nuget packet manager console:

```C#
add-migration meaningfulNameForAction
```

Ex:

```C#
add-migration AddCategoryTableToDb
```

Then:

```C#
update-database
```

  

Create model:

```C#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
	modelBuilder.Entity<EntityName>().HasData(
		create new object
	)
}
```

Ex:

```C#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
	modelBuilder.Entity<Category>().HasData(
	new Category { Id = 1, Name = "Action", DisplayOrder = 1 },
	new Category { Id = 1, Name = "SciFi", DisplayOrder = 2 },
	new Category { Id = 1, Name = "History", DisplayOrder = 3 }
	);
}
```