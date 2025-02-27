# 009 - 💡 Laravel Tip: Avoid `orWhere()` Pitfalls 🚨  

## Be Careful with `orWhere()` – Prevent Unexpected Query Behavior!  

Using Laravel’s Eloquent `orWhere()` incorrectly can unintentionally **override filtering conditions**, leading to inaccurate query results. Understanding how `orWhere()` affects query logic is **crucial** for writing precise database queries.  

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

🔴 **Issue:** The `orWhere()` condition affects the entire query, not just the previous `where` conditions!  

### 🚨 What Does This Mean?  
- Any course where `content` contains "queue" **will be included**, even if `level != 'beginner'`.  
- The `level = 'beginner'` filter is **ignored for `orWhere()`**, making the query behave incorrectly.  
- This can cause **incorrect data retrieval** and logic errors in your application.  

---

## ✅ The Correct Approach: Using a Closure  

To ensure proper filtering, **group conditions** correctly using a closure inside `where()`.  

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

✅ **Now, the filtering is correct:**  
✔️ The `level = 'beginner'` condition is **always applied**.  
✔️ The `title` OR `content` condition is **grouped correctly**.  
✔️ The query behaves **as expected**, ensuring accurate results.  

---

## 🎯 Key Takeaways  

🔹 **Always check the generated SQL** to confirm the query structure.  
🔹 **Use closures (`where(function ($query) {...})`)** to ensure `orWhere()` behaves correctly.  
🔹 **Test your queries in Laravel Tinker** (`php artisan tinker`) before deploying to production.  

---

## 📖 References  

- 📜 [Laravel Query Builder Documentation](https://laravel.com/docs/eloquent#where-clauses)  
- 🔍 [Eloquent Query Scopes](https://laravel.com/docs/eloquent#query-scopes)  

---

🚀 **Master Laravel Query Builder & Write Efficient, Bug-Free Queries!**
