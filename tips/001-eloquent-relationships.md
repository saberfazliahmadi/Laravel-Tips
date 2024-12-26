# Tip 001: Eloquent Relationships

## Use `with` to Optimize Queries
Instead of making multiple queries to fetch related data, use Eloquent's `with` method to eager load relationships.

```php
// Without eager loading
$users = User::all();
foreach ($users as $user) {
    echo $user->posts; // Causes N+1 query problem
}

// With eager loading
$users = User::with('posts')->get();
foreach ($users as $user) {
    echo $user->posts; // Optimized
}
```
