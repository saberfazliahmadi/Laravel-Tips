# Eager Loading with Specific Columns

When eager loading relationships, you often don't need every column.
Loading only what you need reduces memory usage and speeds up queries.

## ❌ Loads all columns

```php
$posts = Post::with('author')->get();
```

## ✅ Loads only the columns you need

```php
$posts = Post::with('author:id,name')->get();
```

**Important:** Always include the foreign key (here `id`) in the column
list — Laravel needs it to match the related models. Without it, the
relation returns `null`.

## With nested relationships

```php
$posts = Post::with('author:id,name', 'comments:id,post_id,body')->get();
```

For `comments`, both `id` and `post_id` are required for the mapping.

**Result:** Smaller result sets, less memory, faster hydration — with
zero change to how you use the models afterwards.
