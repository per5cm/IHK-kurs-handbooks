# EF Core + SQLite – Quick Start (Minimal Working Setup)

For small .NET apps (console, WinForms, ASP.NET Core, MAUI, etc.)  
Goal: Working DB + CRUD in < 10 min, no crashes, reusable template.

## 1. Packages (add to your .csproj)

```bash
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
dotnet add package Microsoft.EntityFrameworkCore.Design     # for future migrations
# Optional: dotnet add package Microsoft.EntityFrameworkCore.Tools
2. appsettings.json (or hard-code for console)
JSON{
  "ConnectionStrings": {
    "FridgeDb": "Data Source=fridge.db"
  }
}
3. Entity Example
C#namespace FridgeApp.Models;

public enum FoodCategory { None, Vegetable, Fruit, Meat, Dairy, Drinks, Other }

public class Grocery
{
    public int Id { get; set; }                     // PK, auto
    public string Name { get; set; } = "";
    public int Quantity { get; set; }
    public string? Unit { get; set; }               // Stk, l, kg, Pck...
    public DateTime Added { get; set; } = DateTime.UtcNow;
    public DateTime? Expires { get; set; }
    public FoodCategory Category { get; set; }
}
4. DbContext (copy-paste base)
C#using Microsoft.EntityFrameworkCore;
using FridgeApp.Models;

namespace FridgeApp.Data;

public class FridgeContext : DbContext
{
    public DbSet<Grocery> Groceries => Set<Grocery>();

    public FridgeContext(DbContextOptions<FridgeContext> options) : base(options) { }

    // Hard-code fallback (good for console tests)
    // protected override void OnConfiguring(DbContextOptionsBuilder o)
    //     => o.UseSqlite("Data Source=fridge.db");

    protected override void OnModelCreating(ModelBuilder mb)
    {
        mb.Entity<Grocery>(e =>
        {
            e.ToTable("Groceries");                    // override default FoodConstructors

            e.Property(g => g.Name).IsRequired().HasMaxLength(150);
            e.Property(g => g.Unit).HasMaxLength(20);
            e.Property(g => g.Category).HasConversion<string>();  // "Meat" instead of 3

            e.HasIndex(g => g.Name);
            e.HasIndex(g => g.Category);
        });
    }
}
5. Dependency Injection (ASP.NET / MAUI / services)
C#// Program.cs or Startup
builder.Services.AddDbContext<FridgeContext>(opt =>
    opt.UseSqlite(builder.Configuration.GetConnectionString("FridgeDb")));
6. Basic Service / Usage (inject or new)
C#public class GroceryService
{
    private readonly FridgeContext _db;

    public GroceryService(FridgeContext db) => _db = db;

    public List<Grocery> GetAll()
    {
        _db.Database.EnsureCreated();           // creates DB + table if missing
        return _db.Groceries.ToList();
    }

    public void Add(Grocery item)
    {
        _db.Groceries.Add(item);
        _db.SaveChanges();
    }

    // One-time seed (call manually or in dev)
    public void SeedIfEmpty()
    {
        if (!_db.Groceries.Any())
        {
            _db.Groceries.AddRange(
                new Grocery { Name = "Milch", Quantity = 1, Unit = "l", Category = FoodCategory.Dairy },
                new Grocery { Name = "Gurken", Quantity = 4, Unit = "Stk", Category = FoodCategory.Vegetable }
            );
            _db.SaveChanges();
        }
    }
}
7. Quick Console Test (no DI needed)
C#using var db = new FridgeContext(new DbContextOptionsBuilder<FridgeContext>()
    .UseSqlite("Data Source=test.db").Options);

db.Database.EnsureCreated();

if (!db.Groceries.Any())
{
    db.Groceries.Add(new Grocery { Name = "Käse", Quantity = 1, Category = FoodCategory.Dairy });
    db.SaveChanges();
}

foreach (var g in db.Groceries)
    Console.WriteLine($"{g.Name} - {g.Quantity} {g.Unit} ({g.Category})");