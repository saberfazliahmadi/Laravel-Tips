## 003 - Don't Use Model Methods to Retrieve Data

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
