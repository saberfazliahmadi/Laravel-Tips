💡Laravel Tip => # 009-orwhere-query-mistake ⚡

## Be Careful with `orWhere()` – Avoid Incorrect Filtering  

When using Laravel’s Eloquent query builder, improper use of `orWhere()` can lead to unintended results by overriding previous conditions.  

---

## 🚨 The Problem: Unexpected Query Results  

Consider the following query:  

```php
$courses = Course::where('level', 'beginner')
    ->where('title', 'LIKE', "%queue%")
    ->orWhere('content', 'LIKE', "%queue%")
    ->get();
```

### ❌ What's Wrong?  

The generated SQL:  

```sql
SELECT * FROM `courses` WHERE `level` = 'beginner' 
AND `title` LIKE '%queue%' OR `content` LIKE '%queue%'
```

🔴 **Issue:** The `orWhere()` condition is applied to the whole query, meaning:  
- Any course where `content` contains "queue" will be included.  
- The `level = 'beginner'` constraint is ignored for `orWhere()`.  
- This can result in incorrect and unintended data being retrieved.  

---

## ✅ The Correct Approach: Using a Closure  

To ensure correct filtering, wrap the `orWhere()` condition inside a closure with `where()` to group conditions properly:

```php
$courses = Course::where('level', 'beginner')
    ->where(function ($query) {
        $query->where('title', 'LIKE', "%queue%")
              ->orWhere('content', 'LIKE', "%queue%");
    })
    ->get();
```

### 🛠️ Why This Works  

The new SQL query:  

```sql
SELECT * FROM `courses` WHERE `level` = 'beginner' 
AND (`title` LIKE '%queue%' OR `content` LIKE '%queue%')
```

✅ Now, the filtering is correct:  
- The `level = 'beginner'` condition is always applied.  
- The `orWhere()` condition is only evaluated within its intended scope.  

---

## 🎯 Key Takeaways  

- **Always check the generated SQL** when using `orWhere()` to prevent logical errors.  
- **Use closures for complex conditions** to ensure proper grouping.  
- **Test queries in Laravel Tinker** (`php artisan tinker`) before using them in production.  

---

## 📖 References  

- [Laravel Query Builder Documentation](https://laravel.com/docs/eloquent#where-clauses)  

---

🚀 **Write better Laravel queries and avoid common pitfalls!**
