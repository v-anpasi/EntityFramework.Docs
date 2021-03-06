---
uid: providers/sqlite/limitations
---
Caution: This documentation is for EF Core. For EF6.x and earlier release see [http://msdn.com/data/ef](http://msdn.com/data/ef).

  # SQLite Limitations

When using the SQLite provider, there are a number of limitations you should be aware of. Most of these are a result of limitations in the underlying SQLite database engine and are not specific to EF.

  ## Modeling Limitations

The common relational library (shared by Entity Framework relational database providers) defines APIs for modelling concepts that are common to most relational database engines. A number of these concepts are not supported by the SQLite provider.

* Schemas

* Sequences

  ## Migrations Limitations

The SQLite database engine does not support a number of schema operations that are supported by the majority of other relational databases. If you attempt to apply one of the unsupported operations to a SQLite database then a `NotSupportedException` will be thrown.

<!--     Operation  Supported?  AddColumn  ✔  AddForeignKey  ✗  AddPrimaryKey  ✗  AddUniqueConstraint  ✗  AlterColumn  ✗  AlterSequence  ✗  CreateIndex  ✔  CreateSchema  ✗  CreateSequence  ✗  CreateTable  ✔  DropColumn  ✗  DropForiegnKey  ✗  DropIndex  ✔  DropPrimaryKey  ✗  DropSchema  ✗  DropSequence  ✗  DropTable  ✔  DropUniqueConstraint  ✗  RenameColumn  ✗  RenameIndex  ✗  RenameSequence  ✗  RenameTable  ✔  RestartSequence  ✗ -->

Tip: You can workaround some of these limitations by manually writing code in your migrations to perform a table rebuild. A table rebuild involves renaming the existing table, creating a new table, copying data to the new table, and dropping the old table. You will need to use the `Sql(string)` method to perform some of these steps.See [Making Other Kinds Of Table Schema Changes](http://sqlite.org/lang_altertable.html#otheralter) in the SQLite documentation for more details.In the future, EF may support some of these operations by using the table rebuild approach under the covers. You can [track this feature on our GitHub project](https://github.com/aspnet/EntityFramework/issues/329).
