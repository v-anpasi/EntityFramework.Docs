---
uid: modeling/relational/sequences
---
Caution: This documentation is for EF Core. For EF6.x and earlier release see [http://msdn.com/data/ef](http://msdn.com/data/ef).

Note: The configuration in this section is applicable to relational databases in general. The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).

  # Sequences

A sequence generates a sequential numeric values in the database. Sequences are not associated with a specific table.

  ## Conventions

By convention, sequences are not introduced in to the model.

  ## Data Annotations

You can not configure a sequence using Data Annotations.

  ## Fluent API

You can use the Fluent API to create a sequence in the model.

<!-- literal_block {"language": "c#", "source": "/Users/shirhatti/src/EntityFramework.Docs/docs/modeling/relational/Modeling/FluentAPI/Samples/Relational/Sequence.cs", "xml:space": "preserve", "classes": [], "backrefs": [], "names": [], "dupnames": [], "highlight_args": {"hl_lines": [7], "linenostart": 1}, "ids": [], "linenos": true} -->

````c#

       class MyContext : DbContext
       {
           public DbSet<Order> Orders { get; set; }

           protected override void OnModelCreating(ModelBuilder modelBuilder)
           {
               modelBuilder.HasSequence<int>("OrderNumbers");
           }
       }

       public class Order
       {
           public int OrderId { get; set; }
           public int OrderNo { get; set; }
           public string Url { get; set; }
       }

   ````

You can also configure additional aspect of the sequence, such as its schema, start value, and increment.

<!-- literal_block {"language": "c#", "source": "/Users/shirhatti/src/EntityFramework.Docs/docs/modeling/relational/Modeling/FluentAPI/Samples/Relational/SequenceConfigured.cs", "xml:space": "preserve", "classes": [], "backrefs": [], "names": [], "dupnames": [], "highlight_args": {"hl_lines": [7, 8, 9], "linenostart": 1}, "ids": [], "linenos": true} -->

````c#

       class MyContext : DbContext
       {
           public DbSet<Order> Orders { get; set; }

           protected override void OnModelCreating(ModelBuilder modelBuilder)
           {
               modelBuilder.HasSequence<int>("OrderNumbers", schema: "shared")
                   .StartsAt(1000)
                   .IncrementsBy(5);
           }
       }

   ````

Once a sequence is introduced, you can use it to generate values for properties in your model. For example, you can use [Default Values](default-values.md) to insert the next value from the sequence.

<!-- literal_block {"language": "c#", "source": "/Users/shirhatti/src/EntityFramework.Docs/docs/modeling/relational/Modeling/FluentAPI/Samples/Relational/SequenceUsed.cs", "xml:space": "preserve", "classes": [], "backrefs": [], "names": [], "dupnames": [], "highlight_args": {"hl_lines": [11, 12, 13], "linenostart": 1}, "ids": [], "linenos": true} -->

````c#

       class MyContext : DbContext
       {
           public DbSet<Order> Orders { get; set; }

           protected override void OnModelCreating(ModelBuilder modelBuilder)
           {
               modelBuilder.HasSequence<int>("OrderNumbers", schema: "shared")
                   .StartsAt(1000)
                   .IncrementsBy(5);

               modelBuilder.Entity<Order>()
                   .Property(o => o.OrderNo)
                   .HasDefaultValueSql("NEXT VALUE FOR shared.OrderNumbers");
           }
       }

       public class Order
       {
           public int OrderId { get; set; }
           public int OrderNo { get; set; }
           public string Url { get; set; }
       }

   ````
