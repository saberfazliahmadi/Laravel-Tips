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
<a href="https://github.com/saberfazliahmadi/Laravel-Tips" >➡️More Tips...</a>
</br>
<a href="https://github.com/saberfazliahmadi/Laravel-Tips/blob/main/CONTRIBUTING.md" >➡️Contributing Guidelines</a>
</br>
<a href="https://github.com/saberfazliahmadi/Laravel-Tips/blob/main/LICENSE" >➡️License</a>
</br>
</br>
