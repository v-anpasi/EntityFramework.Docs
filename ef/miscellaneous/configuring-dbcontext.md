---
uid: miscellaneous/configuring-dbcontext
---
Caution: This documentation is for EF Core. For EF6.x and earlier release see [http://msdn.com/data/ef](http://msdn.com/data/ef).

  # Configuring a DbContext

This article shows patterns for configuring a `DbContext` with `DbContextOptions`. Options are primarily used to select and configure the data store.

  ## Configuring DbContextOptions

`DbContext` must have an instance of `DbContextOptions` in order to execute. This can be supplied to `DbContext` in one of two ways.

1. [Constructor argument](#constructor-argument)

2. [OnConfiguring](#onconfiguring)

If both are used, "OnConfiguring" takes higher priority, which means it can overwrite or change options supplied by the constructor argument.

  ### Constructor argumentContext code with constructor

<!-- literal_block {"language": "csharp", "xml:space": "preserve", "classes": [], "backrefs": [], "names": [], "dupnames": [], "highlight_args": {}, "ids": [], "linenos": false} -->

````csharp

   public class BloggingContext : DbContext
   {
       public BloggingContext(DbContextOptions<BloggingContext> options)
           : base(options)
       { }

       public DbSet<Blog> Blogs { get; set; }
   }
   ````

Tip: The base constructor of DbContext also accepts the non-generic version of `DbContextOptions`. Using the non-generic version is not recommended for applications with multiple context types.

Application code to initialize from constructor argument

<!-- literal_block {"language": "csharp", "xml:space": "preserve", "classes": [], "backrefs": [], "names": [], "dupnames": [], "highlight_args": {}, "ids": [], "linenos": false} -->

````csharp

   var optionsBuilder = new DbContextOptionsBuilder<BloggingContext>();
   optionsBuilder.UseSqlite("Filename=./blog.db");

   using (var context = new BloggingContext(optionsBuilder.Options))
   {
       // do stuff
   }
   ````

  ### OnConfiguring

Caution: `OnConfiguring` occurs last and can overwrite options obtained from DI or the constructor. This approach does not lend itself to testing (unless you target the full database).

Context code with OnConfiguring

<!-- literal_block {"language": "csharp", "xml:space": "preserve", "classes": [], "backrefs": [], "names": [], "dupnames": [], "highlight_args": {}, "ids": [], "linenos": false} -->

````csharp

   public class BloggingContext : DbContext
   {
       public DbSet<Blog> Blogs { get; set; }

       protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
       {
           optionsBuilder.UseSqlite("Filename=./blog.db");
       }
   }
   ````

Application code to initialize with "OnConfiguring"

<!-- literal_block {"language": "csharp", "xml:space": "preserve", "classes": [], "backrefs": [], "names": [], "dupnames": [], "highlight_args": {}, "ids": [], "linenos": false} -->

````csharp

   using (var context = new BloggingContext())
   {
       // do stuff
   }
   ````

  ## Using DbContext with dependency injection

EF supports using `DbContext` with a dependency injection container. Your DbContext type can be added to the service container by using `AddDbContext<TContext>`.

`AddDbContext` will add make both your DbContext type, `TContext`, and `DbContextOptions<TContext>` to the available for injection from the service container.

See [more reading](#more-reading) below for information on dependency injection.

Adding dbcontext to dependency injection

<!-- literal_block {"language": "csharp", "xml:space": "preserve", "classes": [], "backrefs": [], "names": [], "dupnames": [], "highlight_args": {}, "ids": [], "linenos": false} -->

````csharp

   public void ConfigureServices(IServiceCollection services)
   {
       services.AddDbContext<BloggingContext>(options => options.UseSqlite("Filename=./blog.db"));
   }
   ````

This requires adding a [constructor argument](#constructor-argument) to you DbContext type that accepts `DbContextOptions`.

Context code

<!-- literal_block {"language": "csharp", "xml:space": "preserve", "classes": [], "backrefs": [], "names": [], "dupnames": [], "highlight_args": {}, "ids": [], "linenos": false} -->

````csharp

   public class BloggingContext : DbContext
   {
       public BloggingContext(DbContextOptions<BloggingContext> options)
         :base(options)
       { }

       public DbSet<Blog> Blogs { get; set; }
   }
   ````

Application code (in ASP.NET Core)

<!-- literal_block {"language": "csharp", "xml:space": "preserve", "classes": [], "backrefs": [], "names": [], "dupnames": [], "highlight_args": {}, "ids": [], "linenos": false} -->

````csharp

   public MyController(BloggingContext context)
   ````

Application code (using ServiceProvider directly, less common)

<!-- literal_block {"language": "csharp", "xml:space": "preserve", "classes": [], "backrefs": [], "names": [], "dupnames": [], "highlight_args": {}, "ids": [], "linenos": false} -->

````csharp

   using (var context = serviceProvider.GetService<BloggingContext>())
   {
     // do stuff
   }

   var options = serviceProvider.GetService<DbContextOptions<BloggingContext>>();
   ````

<a name=use-idbcontextfactory></a>

  ## Using `IDbContextFactory<TContext>`

As an alternative to the options above, you may also provide an implementation of `IDbContextFactory<TContext>`. EF command line tools and dependency injection can use this factory to create an instance of your DbContext. This may be required in order to enable specific design-time experiences such as migrations.

Implement this interface to enable design-time services for context types that do not have a public default constructor. Design-time services will automatically discover implementations of this interface that are in the same assembly as the derived context.

Example:

<!-- literal_block {"language": "csharp", "xml:space": "preserve", "classes": [], "backrefs": [], "names": [], "dupnames": [], "highlight_args": {}, "ids": [], "linenos": false} -->

````csharp

   using Microsoft.EntityFrameworkCore;
   using Microsoft.EntityFrameworkCore.Infrastructure;

   namespace MyProject
   {
       public class BloggingContextFactory : IDbContextFactory<BloggingContext>
       {
           public BloggingContext Create()
           {
               var optionsBuilder = new DbContextOptionsBuilder<BloggingContext>();
               optionsBuilder.UseSqlite("Filename=./blog.db");

               return new BloggingContext(optionsBuilder.Options);
           }
       }
   }
   ````

  ## More reading

* Read [Getting Started on ASP.NET Core](../platforms/aspnetcore/index.md) for more information on using EF with ASP.NET Core.

* Read [Dependency Injection](https://docs.asp.net/en/latest/fundamentals/dependency-injection.html) to learn more about using DI.

* Read [Testing with InMemory](testing.md) for more information.

* Read [Understanding EF Services](internals/services.md) for more details on how EF uses dependency injection internally.
