Markdown# EF Core + SQLite – Quick Start (Minimal & Reliable)

For .NET 8/9 – small apps, prototypes, learning projects  
Goal: Working DB + basic CRUD fast, reusable skeleton.

## 1. Packages

```bash
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
dotnet add package Microsoft.EntityFrameworkCore.Design
```

## 2. appsettings.json

```JSON
{
  "ConnectionStrings": {
    "FridgeDb": "Data Source=fridge.db"
  }
}
```

## Entity

```c#
public enum FoodCategory { None, Vegetable, Fruit, Meat, Dairy, Drinks, Other }

public class Grocery
{
    public int Id { get; set; }
    public string Name { get; set; } = "";
    public int Quantity { get; set; }
    public string? Unit { get; set; }
    public DateTime Added { get; set; } = DateTime.UtcNow;
    public DateTime? Expires { get; set; }
    public FoodCategory Category { get; set; }
}
```

## DbContent

```c#
using Microsoft.EntityFrameworkCore;

public class FridgeContext : DbContext
{
    public DbSet<Grocery> Groceries => Set<Grocery>();

    public FridgeContext(DbContextOptions<FridgeContext> options) : base(options) { }

    protected override void OnModelCreating(ModelBuilder mb)
    {
        mb.Entity<Grocery>(e =>
        {
            e.ToTable("Groceries");
            e.Property(g => g.Name).IsRequired().HasMaxLength(150);
            e.Property(g => g.Unit).HasMaxLength(20);
            e.Property(g => g.Category).HasConversion<string>();
            e.HasIndex(g => g.Name);
        });
    }
}
```

## Register in Program.cs (ASP.NET Core)

```c#
builder.Services.AddDbContext<FridgeContext>(opt =>
    opt.UseSqlite(builder.Configuration.GetConnectionString("FridgeDb")));
```

## Simple Service

```c#
public class GroceryService
{
    private readonly FridgeContext _db;
    public GroceryService(FridgeContext db) => _db = db;

    public List<Grocery> GetAll()
    {
        _db.Database.EnsureCreated();
        return _db.Groceries.ToList();
    }

    public void Add(Grocery item)
    {
        _db.Groceries.Add(item);
        _db.SaveChanges();
    }

    public void SeedOnce()
    {
        if (!_db.Groceries.Any())
        {
            _db.Groceries.AddRange(
                new Grocery { Name = "Milch", Quantity = 1, Unit = "l", Category = FoodCategory.Dairy },
                new Grocery { Name = "Gurken", Quantity = 3, Unit = "Stk", Category = FoodCategory.Vegetable }
            );
            _db.SaveChanges();
        }
    }
}
```
