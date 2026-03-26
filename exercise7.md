# Exercise 7 - PDO (PHP Data Objects)

In this exercise, you will be introduced to **PDO (PHP Data Objects)**, a modern and flexible way to interact with databases in PHP. Until now, you have been using the **MySQLi** extension. PDO works in a similar way but adds two important advantages:

- **Database portability** — PDO supports many database drivers (MySQL, PostgreSQL, SQLite, and more). Switching databases requires only a small change to the connection string.
- **Prepared statements** — PDO makes it straightforward to use prepared statements, which protect your application against **SQL injection** attacks. You may have noticed the security warnings in earlier exercises; prepared statements are the proper solution.

By the end of this exercise, you will be able to connect to a database using PDO, query data, and safely insert records using prepared statements.

---

## Task 1: Create a PDO Connection File

In earlier exercises you created `connectdb.php` using MySQLi. You will now create a separate connection file using PDO. Having both files lets you compare the two approaches side-by-side.

**Steps:**

1. Create a new PHP file named `pdo_connect.php`.
2. Inside the file, write a PDO connection using a `try/catch` block.

```php
<?php
$host = "127.0.0.1";
$dbname = "ContactList";
$username = "root";
$password = "P@ssw0rd";

try {
    $pdo = new PDO("mysql:host=$host;dbname=$dbname;charset=utf8", $username, $password);
    $pdo->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
} catch (PDOException $e) {
    die("Connection failed: " . $e->getMessage());
}
```

> **Security note:** Hardcoding credentials directly in source files is fine for this learning environment, but in a real application you should never commit passwords to version control. Production applications store credentials in environment variables or in a configuration file that is kept outside the web root and excluded from source control.

**What is happening here?**

- `new PDO(...)` creates a PDO connection object. The first argument is called a **DSN (Data Source Name)** and tells PDO which database driver to use (`mysql:`), where the server is, which database to open, and the character encoding.
- `setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION)` tells PDO to throw a `PDOException` whenever a database error occurs. This means errors will not silently pass unnoticed.
- The `try/catch` block catches any `PDOException` and stops the script with a clear error message rather than letting the application continue in a broken state.

3. Save the file. You will include it in every other PDO exercise file instead of `connectdb.php`.

---

## Task 2: Retrieve All Contacts Using PDO

Now that you have a connection, you will use it to retrieve all contacts and display them in the browser.

**Steps:**

1. Create a new PHP file named `pdo_select.php`.
2. Include your new connection file at the top of the script.

```php
<?php
require_once "pdo_connect.php";
```

3. Use `$pdo->query()` to run a simple SELECT statement and loop through the results.

```php
$sql = "SELECT id, first_name, last_name, email, phone FROM contact";
$stmt = $pdo->query($sql);
```

**What is a statement object?** When you call `$pdo->query()`, PDO runs the SQL and returns a **PDOStatement** object (stored in `$stmt`). You then call methods on this object to fetch rows, rather than working directly with the connection.

4. Fetch and display the results inside an HTML table.

```php
?>
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Contacts - PDO</title>
</head>
<body>
    <h1>Contact List (PDO)</h1>
    <table border="1" cellpadding="5">
        <tr>
            <th>ID</th>
            <th>First Name</th>
            <th>Last Name</th>
            <th>Email</th>
            <th>Phone</th>
        </tr>
        <?php while ($row = $stmt->fetch(PDO::FETCH_ASSOC)): ?>
        <tr>
            <td><?php echo htmlspecialchars($row["id"]); ?></td>
            <td><?php echo htmlspecialchars($row["first_name"]); ?></td>
            <td><?php echo htmlspecialchars($row["last_name"]); ?></td>
            <td><?php echo htmlspecialchars($row["email"]); ?></td>
            <td><?php echo htmlspecialchars($row["phone"]); ?></td>
        </tr>
        <?php endwhile; ?>
    </table>
</body>
</html>
```

**Key differences from MySQLi:**

| MySQLi | PDO |
|---|---|
| `$conn->query($sql)` | `$pdo->query($sql)` |
| `$result->fetch_assoc()` | `$stmt->fetch(PDO::FETCH_ASSOC)` |
| `$result->num_rows` | `$stmt->rowCount()` |

**Why `htmlspecialchars()`?** This function converts special characters (like `<`, `>`, `"`) into safe HTML entities before outputting them. This prevents **Cross-Site Scripting (XSS)** attacks, where a malicious user could store HTML or JavaScript in the database to run in other users' browsers. Always use `htmlspecialchars()` when echoing database values into HTML.

5. Open `pdo_select.php` in your browser (via the PHP server) and confirm that all contacts are displayed in the table.

---

## Task 3: Insert a Contact Using a Prepared Statement

This is the most important task in this exercise. You will insert a new contact using a **prepared statement**, which is the safe way to include user input in SQL.

**What is a prepared statement and why does it matter?**

In `insert.php` (Exercise 3), the SQL was built by directly joining PHP variables into the query string:

```php
// UNSAFE — from Exercise 3
$sql = "INSERT INTO contact (first_name, last_name, email, phone)
        VALUES ('$first_name', '$last_name', '$email', '$phone')";
```

If `$first_name` contained `'; DROP TABLE contact; --`, the resulting SQL would be:

```sql
INSERT INTO contact (first_name, ...) VALUES (''; DROP TABLE contact; --', ...)
```

The database would run the `DROP TABLE` command, destroying your data. This is called **SQL injection**.

A **prepared statement** separates the SQL structure from the data values. The database receives the query template first, then the values separately — it never interprets the values as SQL code.

**Steps:**

1. Create a new PHP file named `pdo_insert.php`.
2. Include `pdo_connect.php` at the top.
3. Create an HTML form to collect contact information.

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Add Contact - PDO</title>
</head>
<body>
    <h1>Add a New Contact (PDO)</h1>
    <form action="pdo_insert.php" method="post">
        <label for="first_name">First Name:</label>
        <input type="text" id="first_name" name="first_name" required><br><br>

        <label for="last_name">Last Name:</label>
        <input type="text" id="last_name" name="last_name" required><br><br>

        <label for="email">Email:</label>
        <input type="email" id="email" name="email" required><br><br>

        <label for="phone">Phone:</label>
        <input type="tel" id="phone" name="phone" required><br><br>

        <input type="submit" value="Add Contact">
    </form>
</body>
</html>
```

4. Above the HTML, add the PHP block that runs when the form is submitted.

```php
<?php
require_once "pdo_connect.php";

if ($_SERVER["REQUEST_METHOD"] == "POST") {
    $first_name = $_POST["first_name"];
    $last_name  = $_POST["last_name"];
    $email      = $_POST["email"];
    $phone      = $_POST["phone"];

    // Step 1: Prepare the statement with placeholders
    $stmt = $pdo->prepare(
        "INSERT INTO contact (first_name, last_name, email, phone)
         VALUES (:first_name, :last_name, :email, :phone)"
    );

    // Step 2: Bind the actual values to the placeholders
    $stmt->bindParam(":first_name", $first_name);
    $stmt->bindParam(":last_name",  $last_name);
    $stmt->bindParam(":email",      $email);
    $stmt->bindParam(":phone",      $phone);

    // Step 3: Execute the statement
    try {
        $stmt->execute();
        echo "<p>Contact added successfully!</p>";
    } catch (PDOException $e) {
        echo "<p>Error adding contact: " . htmlspecialchars($e->getMessage()) . "</p>";
    }
}
?>
```

**How prepared statements work — step by step:**

1. **`$pdo->prepare(...)`** sends the SQL template to the database. The placeholders (`:first_name`, `:last_name`, etc.) mark where values will go. The database parses and compiles the query at this point, before any data is involved.
2. **`$stmt->bindParam(...)`** associates each placeholder with a PHP variable. The database knows these values are data, not SQL, so they can never alter the query's structure.
3. **`$stmt->execute()`** sends the bound values to the database and runs the query. Because the structure was already compiled, user input cannot change it. Because PDO is configured with `ERRMODE_EXCEPTION`, any database error throws a `PDOException`, which is caught by the `try/catch` block and displayed as a readable message.

**Named vs positional placeholders:** PDO supports two styles. Named placeholders use `:name` syntax (as above) and are easier to read. Positional placeholders use `?` and are bound in order. Both are equally safe.

5. Open `pdo_insert.php` in your browser, fill in the form, and submit it. Open `pdo_select.php` to confirm the new contact appears.

---

## Task 4: Update a Contact Using PDO (Partially Guided)

You will now write an update page using PDO prepared statements. This task is more independent — a description of each step is given, but the full code is not provided.

**What to build:** A page `pdo_update.php` that:
1. Shows a form asking for a contact ID.
2. When the ID is submitted (GET), retrieves that contact's details from the database and pre-fills an edit form.
3. When the edit form is submitted (POST), updates the contact in the database using a prepared statement.

**Guidance:**

- Include `pdo_connect.php` at the top.
- For the GET request, use a prepared statement with a `:id` placeholder to `SELECT` the contact with the matching ID. Fetch the row using `$stmt->fetch(PDO::FETCH_ASSOC)`.
- For the POST request, prepare an `UPDATE` statement with named placeholders for each column and `:id` in the `WHERE` clause. Bind and execute as in Task 3.
- Remember to use a hidden `<input>` to carry the contact ID from the GET form through to the POST form.

**Hint — preparing an UPDATE:**

```php
$stmt = $pdo->prepare(
    "UPDATE contact SET first_name=:first_name, last_name=:last_name,
     email=:email, phone=:phone WHERE id=:id"
);
```

Test your page by retrieving an existing contact, changing one of the fields, and submitting the form. Confirm the change appears in `pdo_select.php`.

---

## Task 5: Delete a Contact Using PDO (Independent)

Write a page `pdo_delete.php` that deletes a contact by ID, using a prepared statement. You should be able to complete this task independently based on what you have learned.

**Requirements:**

- Accept the contact ID via a URL parameter (e.g., `pdo_delete.php?id=3`).
- Use a PDO prepared statement with a named placeholder to perform the `DELETE`.
- Display a success message if the deletion works, or an error message if it fails.
- Return to `pdo_select.php` and confirm the contact is no longer listed.

**Think about:** What happens if the `id` parameter is missing from the URL? How could you handle that case gracefully?

---

## Discussion

1. In Task 3, why is it unsafe to build an SQL query by concatenating user-supplied variables directly into the string? What could a malicious user do?
2. What is the difference between `$pdo->query()` and `$pdo->prepare()` followed by `$stmt->execute()`? When would you use each?
3. The connection string `"mysql:host=$host;dbname=$dbname;charset=utf8"` is specific to MySQL/MariaDB. If you wanted to switch to PostgreSQL, what part of the code would need to change?
4. Both MySQLi and PDO can use prepared statements. What advantage does PDO offer that MySQLi does not?
