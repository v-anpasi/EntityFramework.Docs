---
uid: modeling/relational/default-values
---
Caution: This documentation is for EF Core. For EF6.x and earlier release see [http://msdn.com/data/ef](http://msdn.com/data/ef).

Note: The configuration in this section is applicable to relational databases in general. The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).

  # Default Values

The default value of a column is the value that will be inserted if a new row is inserted but no value is specified for the column.

  ## Conventions

By convention, a default value is not configured.

  ## Data Annotations

You can not set a default value using Data Annotations.

  ## Fluent API

You can use the Fluent API to specify the default value for a property.

<!-- [!code-csharp[Main](samples/relational/Modeling/FluentAPI/Samples/Relational/DefaultValue.cs?highlight=9)] -->

````c#

       class MyContext : DbContext
       {
           public DbSet<Blog> Blogs { get; set; }

           protected override void OnModelCreating(ModelBuilder modelBuilder)
           {
               modelBuilder.Entity<Blog>()
                   .Property(b => b.Rating)
                   .HasDefaultValue(3);
           }
       }

       public class Blog
       {
           public int BlogId { get; set; }
           public string Url { get; set; }
           public int Rating { get; set; }
       }

   ````

You can also specify a SQL fragment that is used to calculate the default value.

<!-- [!code-csharp[Main](samples/relational/Modeling/FluentAPI/Samples/Relational/DefaultValueSql.cs?highlight=9)] -->

````c#

       class MyContext : DbContext
       {
           public DbSet<Blog> Blogs { get; set; }

           protected override void OnModelCreating(ModelBuilder modelBuilder)
           {
               modelBuilder.Entity<Blog>()
                   .Property(b => b.Created)
                   .HasDefaultValueSql("getdate()");
           }
       }

       public class Blog
       {
           public int BlogId { get; set; }
           public string Url { get; set; }
           public DateTime Created { get; set; }
       }

   ````