---
layout: post
title: How to Scaffold Controllers with database views to EF Core 2.1
published: true
---

### How to Scaffold Controllers with database views to EF Core 2.1
1. Create view in database.
2. Create a POCO with same structure as view.
3. Add a new Controller with POCO created in step#2
	a. If key related error occurrs, add a `Key` attribute on a column and then remove after scaffolding is completed.
4. A new property with `DbSet<T>` should have gotten added where `T` is the class created in step#2. Change `DbSet` to `DbQuery`.
5. In `OnModelCreating` method of `DbContext`, add following code:
```` csharp
modelBuilder.Query<POCO from step#2>().ToView("Name of the view");
````

[Satish Yadav Blog]: https://blog.satishyadav.com
