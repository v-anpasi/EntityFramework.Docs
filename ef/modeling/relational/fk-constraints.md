---
uid: modeling/relational/fk-constraints
---
Caution: This documentation is for EF Core. For EF6.x and earlier release see [http://msdn.com/data/ef](http://msdn.com/data/ef).

Note: The configuration in this section is applicable to relational databases in general. The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).

  # Foreign Key Constraints

A foreign key constraint is introduced for each relationship in the model.

  ## Conventions

By convention, foreign key constraints are named `FK_<dependent type name>_<principal type name>_<foreign key property name>`. For composite foreign keys `<foreign key property name>` becomes an underscore separated list of foreign key property names.

  ## Data Annotations

Foreign key constraint names cannot be configured using data annotations.

  ## Fluent API

You can use the Fluent API to configure the foreign key constraint name for a relationship.

<!-- literal_block {"language": "c#", "source": "/Users/shirhatti/src/EntityFramework.Docs/docs/modeling/relational/Modeling/FluentAPI/Samples/Relational/RelationshipConstraintName.cs", "xml:space": "preserve", "classes": [], "backrefs": [], "names": [], "dupnames": [], "highlight_args": {"hl_lines": [12], "linenostart": 1}, "ids": [], "linenos": true} -->

````c#

       class MyContext : DbContext
       {
           public DbSet<Blog> Blogs { get; set; }
           public DbSet<Post> Posts { get; set; }

           protected override void OnModelCreating(ModelBuilder modelBuilder)
           {
               modelBuilder.Entity<Post>()
                   .HasOne(p => p.Blog)
                   .WithMany(b => b.Posts)
                   .HasForeignKey(p => p.BlogId)
                   .HasConstraintName("ForeignKey_Post_Blog");
           }
       }

       public class Blog
       {
           public int BlogId { get; set; }
           public string Url { get; set; }

           public List<Post> Posts { get; set; }
       }

       public class Post
       {
           public int PostId { get; set; }
           public string Title { get; set; }
           public string Content { get; set; }

           public int BlogId { get; set; }
           public Blog Blog { get; set; }

   ````
