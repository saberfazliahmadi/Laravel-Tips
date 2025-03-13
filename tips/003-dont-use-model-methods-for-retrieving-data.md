## 003 - 💡 Don't Use Model Methods to Retrieve Data

If you want to retrieve some data from a model, **create an accessor** instead of using methods.  
Keep methods for things that **change the model** in some way.

---

### 🚫 Avoid This:
Using model methods like `$user->gravatarUrl()` for retrieving data:

```php
class User extends Authenticatable
{
    // ...
    public function gravatarUrl()
    {
        return "https://www.gravatar.com/avatar/" . md5(strtolower(trim($this->email)));
    }
}

// Usage
$user->gravatarUrl();
```

---

### ✅ Do This Instead:
Define an accessor for computed values to make your code more intuitive and align with Laravel's best practices:

```php
class User extends Authenticatable
{
    // ...
    public function getGravatarUrlAttribute()
    {
        return "https://www.gravatar.com/avatar/" . md5(strtolower(trim($this->email)));
    }
}

// Usage
$user->gravatar_url; // Accessed like a property
```

---

### Why Use Accessors?
- **Improves readability** by allowing attributes to be accessed like properties.  
- **Keeps methods reserved for actions** that modify the model.  
- **Aligns with Laravel's design philosophy**, ensuring cleaner and maintainable code.

💡 **Tip:** Always use accessors for computed values to make your models simpler and more consistent.

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
