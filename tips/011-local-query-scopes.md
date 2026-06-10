# 011 - 💡 Laravel Tip: Keep Queries Clean with Local Query Scopes

Writing expressive, reusable queries is a sign of a mature Laravel developer. One of the most underused tools in Eloquent's arsenal is **Local Query Scopes** — a simple pattern that eliminates repeated `where` conditions scattered across your codebase and replaces them with readable, chainable methods.

---

## 🚨 The Problem: Scattered, Repeated Query Conditions

Imagine you have an `active` filter on your `User` model. You need it in many places:

```php
// In UserController
$activeUsers = User::where('active', true)->get();

// In AdminController
$activeAdmins = User::where('active', true)
    ->where('role', 'admin')
    ->get();

// In DashboardController
$activeRecentUsers = User::where('active', true)
    ->where('created_at', '>=', now()->subDays(30))
    ->get();

// In ReportController
$activeSubscribers = User::where('active', true)
    ->where('subscribed', true)
    ->get();
```

### ❌ What's Wrong?

🔴 **The `where('active', true)` condition is repeated everywhere.**

- If the definition of "active" ever changes (e.g., you add a `banned` column), you must **update every single query** across the codebase.
- Queries become **harder to read** — a new developer must understand the raw condition before understanding the intent.
- It's easy to **forget the condition** in one controller, causing subtle data-consistency bugs.

---

## ✅ The Solution: Define a Local Query Scope Once

Laravel allows you to define a scope directly on your Eloquent model using a method prefixed with `scope`:

```php
// app/Models/User.php

class User extends Model
{
    // Scope: active users only
    public function scopeActive(Builder $query): Builder
    {
        return $query->where('active', true);
    }
}
```

Now use it anywhere with a clean, chainable method — **no raw conditions needed**:

```php
// In UserController
$activeUsers = User::active()->get();

// In AdminController
$activeAdmins = User::active()->where('role', 'admin')->get();

// In DashboardController
$activeRecentUsers = User::active()
    ->where('created_at', '>=', now()->subDays(30))
    ->get();

// In ReportController
$activeSubscribers = User::active()->where('subscribed', true)->get();
```

### 🛠️ Why This Works

✅ **One definition, used everywhere.** Change the logic in `scopeActive()` — all queries instantly reflect it.  
✅ **Self-documenting queries.** `User::active()` communicates intent; `where('active', true)` communicates implementation.  
✅ **Fully chainable.** Scopes integrate seamlessly with other `where()`, `orderBy()`, `with()` calls — they're just query builder methods under the hood.

---

## 🚀 Advanced: Scopes with Parameters

Scopes can accept parameters, making them even more flexible:

```php
// app/Models/User.php

public function scopeRecent(Builder $query, int $days = 30): Builder
{
    return $query->where('created_at', '>=', now()->subDays($days));
}

public function scopeRole(Builder $query, string $role): Builder
{
    return $query->where('role', $role);
}
```

Usage — still clean, still chainable:

```php
// Active users registered in the last 30 days (default)
User::active()->recent()->get();

// Active users registered in the last 7 days
User::active()->recent(7)->get();

// Active admins from the last 14 days
User::active()->role('admin')->recent(14)->get();
```

---

## 🧠 Chaining Multiple Scopes

Scopes chain just like any other Eloquent method. You can stack as many as you need:

```php
$results = User::active()
    ->role('editor')
    ->recent(14)
    ->orderBy('name')
    ->paginate(20);
```

The generated SQL is clean and optimized — each scope simply appends its conditions to the query:

```sql
SELECT * FROM `users`
WHERE `active` = 1
  AND `role` = 'editor'
  AND `created_at` >= '2026-05-27 00:00:00'
ORDER BY `name` ASC
LIMIT 20 OFFSET 0
```

---

## 🎯 Key Takeaways

🔹 **Prefix model methods with `scope`** to define a local query scope (e.g., `scopeActive` → called as `->active()`).  
🔹 **Centralize query logic** — define the condition once, use it everywhere.  
🔹 **Scopes accept parameters** — pass arguments after the `$query` parameter for dynamic filtering.  
🔹 **Scopes are fully chainable** — mix and match with other scopes, `where()`, `orderBy()`, `with()`, and `paginate()`.  
🔹 **Always type-hint `Builder`** — import `Illuminate\Database\Eloquent\Builder` for IDE support and clarity.

---

## 📖 References

- 📜 [Laravel Eloquent: Local Scopes](https://laravel.com/docs/eloquent#local-scopes)
- 🔍 [Laravel Query Builder](https://laravel.com/docs/queries)
- 🔗 [Eloquent: Getting Started](https://laravel.com/docs/eloquent)

---

🚀 **Master Local Query Scopes & Write Queries That Are Clean, Reusable, and a Joy to Maintain!**

---

Happy coding!

---

*Laravel Tips Repository by <a href="https://github.com/saberfazliahmadi/">Saber Fazliahmadi</a>*

---

## 📚 LARAVEL TIPS Repository Contents:
</br>
1 - 💡 <a href="https://github.com/saberfazliahmadi/Laravel-Tips/blob/main/tips/001-eloquent-relationships.md" >Eloquent Relationships</a>  
</br>
2 - 💡 <a href="https://github.com/saberfazliahmadi/Laravel-Tips/blob/main/tips/002-query-optimization.md" >Query Optimization</a>
</br>
3 - 💡 <a href="https://github.com/saberfazliahmadi/Laravel-Tips/blob/main/tips/003-dont-use-model-methods-for-retrieving-data.md" >Avoid Model Methods for Data Retrieval</a>
</br>
4 - 💡 <a href="https://github.com/saberfazliahmadi/Laravel-Tips/blob/main/tips/004-use-optimize-clear-command.md" >Simplify Cache Management</a>  
</br>
5 - 💡 <a href="https://github.com/saberfazliahmadi/Laravel-Tips/blob/main/tips/005-querying-with-relationships.md" >Cleaner Queries with Relationships</a>
</br>
6 - 💡 <a href="https://github.com/saberfazliahmadi/Laravel-Tips/blob/main/tips/006-dynamic-where-methods.md" >Supercharge Your Queries with Dynamic where Methods</a>
</br>
7 - 💡 <a href="https://github.com/saberfazliahmadi/Laravel-Tips/blob/main/tips/007-faker_image_generation.md" >Generate Fake Images and URLs with Faker</a>
</br>
8 - 💡 <a href="https://github.com/saberfazliahmadi/Laravel-Tips/blob/main/tips/008-query-builder-where-methods.md" >Mastering whereAll, whereAny, orWhereAll, and orWhereAny</a>
</br>
9 - 💡 <a href="https://github.com/saberfazliahmadi/Laravel-Tips/blob/main/tips/009-orwhere-query-mistake.md" >Avoid orWhere() Pitfalls</a>
</br>
10 - 💡 <a href="https://github.com/saberfazliahmadi/Laravel-Tips/blob/main/tips/010-customizing-faker-locale-for-authentic-dummy-data.md" >Customizing Faker Locale for Authentic Dummy Data</a>
</br>
11 - 💡 <a href="https://github.com/saberfazliahmadi/Laravel-Tips/blob/main/tips/011-local-query-scopes.md" >Keep Queries Clean with Local Query Scopes</a>
</br>
<a href="https://github.com/saberfazliahmadi/Laravel-Tips" >➡️More Tips...</a>
</br>
<a href="https://github.com/saberfazliahmadi/Laravel-Tips/blob/main/CONTRIBUTING.md" >➡️Contributing Guidelines</a>
</br>
<a href="https://github.com/saberfazliahmadi/Laravel-Tips/blob/main/LICENSE" >➡️License</a>
</br>
</br>

---
