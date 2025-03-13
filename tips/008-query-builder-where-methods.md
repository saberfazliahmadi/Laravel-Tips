# 008 - 💡 Laravel Tip: Mastering `whereAll`, `whereAny`, `orWhereAll`, and `orWhereAny`  

## 🚀 **Laravel v10.47.0 Introduces Game-Changing Query Methods!**  

Laravel continuously evolves, making database queries more **efficient, readable, and powerful**. In Laravel **v10.47.0**, four new **query builder methods** were introduced to simplify searching multiple columns effortlessly:  

🔹 `whereAll()` → Ensures all specified columns **match** the given value.  
🔹 `whereAny()` → Matches if **at least one** column contains the value.  
🔹 `orWhereAll()` → Works like `whereAll` but applies an **OR condition**.  
🔹 `orWhereAny()` → Works like `whereAny` but with an **OR condition**.  

These methods **eliminate complex closures**, making queries **shorter, faster, and more readable**! Let’s explore them in depth.  

---

## 🔍 **Before Laravel 10.47.0: The Traditional Way**  

Previously, if you wanted to **search for the same value in multiple columns**, you had to use a **nested closure**, like this:  

```php  
$searchTerm = 'sample';  

User::query()  
    ->where(function ($query) use ($searchTerm) {  
        $query->where('first_name', 'LIKE', $searchTerm)  
              ->where('last_name', 'LIKE', $searchTerm);  
    })  
    ->get();  
```  

### ❌ **Problems with the Old Approach**:  
🚨 **Hard to read** – Nested closures make queries cluttered.  
🚨 **More typing** – Requires additional syntax for basic conditions.  
🚨 **Not expressive** – Doesn't clearly convey its purpose.  

---

## ✅ **Laravel 10.47.0: A Cleaner & More Efficient Way!**  

### 🔥 `whereAll()` – Match If **All Columns** Contain the Value  
```php  
User::query()  
    ->whereAll(['first_name', 'last_name'], 'LIKE', $searchTerm)  
    ->get();  
```  
📌 *Equivalent to an `AND` condition: Both columns must match.*  

### 🔥 `whereAny()` – Match If **Any Column** Contains the Value  
```php  
User::query()  
    ->whereAny(['first_name', 'last_name'], 'LIKE', $searchTerm)  
    ->get();  
```  
📌 *Equivalent to an `OR` condition: At least one column must match.*  

🔹 **These methods make queries cleaner, faster, and more readable!**  

---

## 🔬 **Behind the Scenes: What SQL Queries Are Generated?**  

Laravel **automatically converts** these methods into optimized SQL queries:  

✅ **`whereAll()` generates:**  
```sql  
SELECT * FROM `users` WHERE (`first_name` LIKE 'sample' AND `last_name` LIKE 'sample');  
```  

✅ **`whereAny()` generates:**  
```sql  
SELECT * FROM `users` WHERE (`first_name` LIKE 'sample' OR `last_name` LIKE 'sample');  
```  

**These queries are faster and easier to read! 🚀**  

---

## 🔰 **Extending with `orWhereAll()` and `orWhereAny()`**  

What if you need to combine multiple conditions **using OR logic**?  

### 📌 `orWhereAll()` – Apply `whereAll()` with an **OR Condition**  
```php  
User::query()  
    ->orWhereAll(['first_name', 'last_name'], 'LIKE', $searchTerm)  
    ->get();  
```  
📌 *Ensures all conditions match but within an `OR` logic context.*  

### 📌 `orWhereAny()` – Apply `whereAny()` with an **OR Condition**  
```php  
User::query()  
    ->orWhereAny(['first_name', 'last_name'], 'LIKE', $searchTerm)  
    ->get();  
```  
📌 *Checks if at least one condition matches using an OR clause.*  

---

## 🎯 **Why Should You Use These New Methods?**  

✅ **Less Code, More Clarity** – No more complex nested conditions.  
✅ **Better Readability** – Queries are more expressive and easy to understand.  
✅ **Performance Optimization** – Laravel optimizes execution under the hood.  
✅ **Enhanced Maintainability** – Easier to modify and extend queries.  

### 📌 **Use Case Example: Searching Users in a Database**  
Imagine you're building a **search feature** where users can search by `first_name` or `last_name`. The new Laravel methods make it **super easy**!  

```php  
User::query()  
    ->whereAny(['first_name', 'last_name'], 'LIKE', "%$searchTerm%")  
    ->get();  
```  

🔹 **This replaces complex nested conditions with a single clean line!**  

---

## 🚀 **Conclusion: Write Better Queries with Laravel 10.47.0**  

These new query builder methods **significantly improve** the way we write queries in Laravel. By using `whereAll`, `whereAny`, `orWhereAll`, and `orWhereAny`, you can:  

✅ **Make your queries more readable**  
✅ **Reduce unnecessary nesting**  
✅ **Improve query performance**  
✅ **Write more maintainable code**  

Upgrade your Laravel skills today and start using these new methods! 🚀  

---

## ⭐ **Did You Find This Helpful? Star ⭐ the Repository!**  

💡 If you enjoyed this tip, please **follow this repository for more Laravel tricks**! 🚀  

📢 **Share this with your Laravel community and keep learning together!** 🎯  

---

### 🔗 **More Laravel Tips:**  
📌 Stay updated with the latest Laravel techniques by checking out other **Laravel tips** in this repository!  

🚀 **Happy Coding!** 🎯✨

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
