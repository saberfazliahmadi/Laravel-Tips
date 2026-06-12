# 012 - 💡 Laravel Tip: Atomic Find-or-Create with `updateOrCreate`, `firstOrCreate`, and `firstOrNew`

One of the most common tasks in any Laravel application is: *"Find this record if it exists, otherwise create it — and maybe update it while you're at it."* Laravel ships with three elegant Eloquent methods for exactly this. Knowing when to use each one will make your code shorter, safer, and free of subtle race-condition bugs.

---

## 🚨 The Problem: Manual Find-or-Create is Verbose and Unsafe

This is how many developers write a find-or-create operation by hand:

```php
// ❌ Wrong: verbose, race condition risk, 2–3 DB queries
$user = User::where('email', $email)->first();

if ($user) {
    $user->update([
        'name' => $name,
        'role' => $role,
    ]);
} else {
    $user = User::create([
        'email' => $email,
        'name'  => $name,
        'role'  => $role,
    ]);
}
```

### ❌ What's Wrong?

🔴 **Verbose.** Eight lines to do one logical operation.  
🔴 **Race condition.** Between the `SELECT` and the `INSERT`, another request can insert the same row — causing a duplicate or a crash.  
🔴 **Scattered logic.** Every developer writes this pattern differently, making the codebase inconsistent.

---

## ✅ The Solution: Three Eloquent Methods for Every Situation

### 1. `updateOrCreate()` — Find and Update, or Create

Use this when you always want the latest values saved, regardless of whether the record existed before. Perfect for **syncing external data** and **import jobs**.

```php
// ✅ One statement. One logical query. No race condition.
$user = User::updateOrCreate(
    ['email' => $email],              // ← search criteria (WHERE)
    ['name' => $name, 'role' => $role] // ← values to set (always applied)
);
```

> **How it works:** Laravel runs a `SELECT`. If a matching row exists, it runs `UPDATE`. If not, it runs `INSERT`. The second array is applied on both create **and** update.

---

### 2. `firstOrCreate()` — Find It, or Create It Once

Use this when you only want to set values on **creation** — not overwrite them on every call. Perfect for **seeders** and **tagging systems**.

```php
// ✅ Creates the tag only if it doesn't already exist.
$tag = Tag::firstOrCreate(
    ['name' => 'laravel'],                          // ← search criteria
    ['slug' => 'laravel', 'color' => '#F05340']     // ← only used on INSERT
);
```

> **Key difference from `updateOrCreate`:** The second array is **ignored** if the record already exists. Existing data is never overwritten.

---

### 3. `firstOrNew()` — Find It, or Build It (Without Saving)

Use this when you need to **modify the model further** before deciding to save. It returns a model that is either retrieved from the database or freshly instantiated — but **never persisted automatically**.

```php
// ✅ Find or build a draft — then customise before saving.
$article = Article::firstOrNew(
    ['slug' => $slug],
    ['title' => $title, 'status' => 'draft']
);

// Modify freely — the record is not saved yet.
$article->content    = $request->content;
$article->updated_by = auth()->id();
$article->save();  // ← you control when (and whether) it's saved
```

> **Key difference from `firstOrCreate`:** Nothing is written to the database until you call `->save()` yourself.

---

## 🔬 Side-by-Side Comparison

```php
// Scenario: sync a user record from an external API payload
$payload = ['email' => 'dr.smith@hospital.de', 'name' => 'Dr. Smith', 'role' => 'doctor'];

// updateOrCreate — ALWAYS writes the latest values
User::updateOrCreate(
    ['email' => $payload['email']],
    ['name'  => $payload['name'], 'role' => $payload['role']]
);

// firstOrCreate — only creates; never touches an existing record
User::firstOrCreate(
    ['email' => $payload['email']],
    ['name'  => $payload['name'], 'role' => $payload['role']]
);

// firstOrNew — build, customise, then save manually
$user = User::firstOrNew(['email' => $payload['email']]);
$user->last_seen_at = now();
$user->save();
```

---

## 🚀 Real-World Use Cases

| Method | When to use |
|---|---|
| `updateOrCreate` | Importing/syncing records from an API or CSV |
| `updateOrCreate` | Upsert rows during a scheduled job |
| `firstOrCreate` | Seeding lookup tables (roles, categories, tags) |
| `firstOrCreate` | Creating a user's settings row on first visit |
| `firstOrNew` | Multi-step forms — build the record, add fields, then save |
| `firstOrNew` | Draft systems — retrieve or scaffold without auto-persisting |

---

## 🧠 Bonus: `createOrFirst()` (Laravel 10.29+)

If concurrent requests are a concern, `createOrFirst()` is the truly atomic alternative to `firstOrCreate`. It **attempts the `INSERT` first** and only falls back to a `SELECT` if a unique constraint violation occurs — eliminating the SELECT→INSERT race window entirely.

```php
// ✅ Truly atomic — no SELECT before the INSERT
$user = User::createOrFirst(
    ['email' => $email],
    ['name' => $name, 'role' => $role]
);
```

> Available since **Laravel 10.29**. For most applications `firstOrCreate` is sufficient; reach for `createOrFirst` when you expect heavy concurrent inserts on the same key.

---

## 🎯 Key Takeaways

🔹 **`updateOrCreate($search, $values)`** — always writes values; use for data sync and imports.  
🔹 **`firstOrCreate($search, $extra)`** — creates once, never overwrites; use for seeders and lookup tables.  
🔹 **`firstOrNew($search, $extra)`** — same as `firstOrCreate` but you call `->save()` yourself; use when you need to modify the model before persisting.  
🔹 **The first array is always the search key** — make sure it maps to a unique or indexed column for performance.  
🔹 **`createOrFirst`** is the race-condition-proof option for high-concurrency inserts (Laravel 10.29+).

---

## 📖 References

- 📜 [Laravel Eloquent: Inserting & Updating — updateOrCreate](https://laravel.com/docs/eloquent#upserts)
- 📜 [Laravel Eloquent: firstOrCreate / firstOrNew](https://laravel.com/docs/eloquent#retrieving-or-creating-models)
- 🔗 [Eloquent: Getting Started](https://laravel.com/docs/eloquent)

---

🚀 **Master these three methods and you will never write a manual find-or-create block again!**

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
12 - 💡 <a href="https://github.com/saberfazliahmadi/Laravel-Tips/blob/main/tips/012-update-or-create-first-or-create.md" >Atomic Find-or-Create with updateOrCreate, firstOrCreate, and firstOrNew</a>
</br>
<a href="https://github.com/saberfazliahmadi/Laravel-Tips" >➡️More Tips...</a>
</br>
<a href="https://github.com/saberfazliahmadi/Laravel-Tips/blob/main/CONTRIBUTING.md" >➡️Contributing Guidelines</a>
</br>
<a href="https://github.com/saberfazliahmadi/Laravel-Tips/blob/main/LICENSE" >➡️License</a>
</br>
</br>

---
