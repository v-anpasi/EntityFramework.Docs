---
uid: modeling/inheritance
---
Caution: This documentation is for EF Core. For EF6.x and earlier release see [http://msdn.com/data/ef](http://msdn.com/data/ef).

  # Inheritance

Inheritance in the EF model is used to control how inheritance in the entity classes is represented in the database.

  ## Conventions

By convention, it is up to the database provider to determine how inheritance will be represented in the database. See [Inheritance (Relational Database)](relational/inheritance.md) for how this is handled with a relational database provider.

EF will only setup inheritance if two or more inherited types are explicitly included in the model. EF will not scan for base or derived types that were not otherwise included in the model. You can include types in the model by exposing a *DbSet<TEntity>* for each type in the inheritance hierarchy.

<!-- literal_block {"ids": [], "classes": [], "xml:space": "preserve", "backrefs": [], "linenos": true, "dupnames": [], "language": "c#", "highlight_args": {"linenostart": 1, "hl_lines": [3, 4]}, "names": [], "source": "/Users/shirhatti/src/EntityFramework.Docs/docs/modeling/Modeling/Conventions/Samples/InheritanceDbSets.cs"} -->

````c#

       class MyContext : DbContext
       {
           public DbSet<Blog> Blogs { get; set; }
           public DbSet<RssBlog> RssBlogs { get; set; }
       }

       public class Blog
       {
           public int BlogId { get; set; }
           public string Url { get; set; }
       }

       public class RssBlog : Blog
       {
           public string RssUrl { get; set; }
       }

   ````

If you don't want to expose a *DbSet<TEntity>* for one or more entities in the hierarchy, you can use the Fluent API to ensure they are included in the model.

<!-- literal_block {"ids": [], "classes": [], "xml:space": "preserve", "backrefs": [], "linenos": true, "dupnames": [], "language": "c#", "highlight_args": {"linenostart": 1, "hl_lines": [7]}, "names": [], "source": "/Users/shirhatti/src/EntityFramework.Docs/docs/modeling/Modeling/Conventions/Samples/InheritanceModelBuilder.cs"} -->

````c#

       class MyContext : DbContext
       {
           public DbSet<Blog> Blogs { get; set; }

           protected override void OnModelCreating(ModelBuilder modelBuilder)
           {
               modelBuilder.Entity<RssBlog>();
           }
       }

   ````

  ## Data Annotations

You cannot use Data Annotations to configure inheritance.

  ## Fluent API

The Fluent API for inheritance depends on the database provider you are using. See [Inheritance (Relational Database)](relational/inheritance.md) for the configuration you can perform for a relational database provider.
