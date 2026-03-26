# Exercise 2 - Connect to Database

In this exercise, you will connect to the database using PHP and retrieve data from the `contact` table.

## Task 1: Connect to Database

1. Create a new PHP file named `connectdb.php`.
2. Use the `mysqli` extension to connect to the `ContactList` database.
3. Use the following connection parameters:
   - Host: `127.0.0.1` (if this doesn't work, try `0.0.0.0`)
   - Port: `3306` (default MySQL/MariaDB port — no need to specify it explicitly)
   - Username: `root`
   - Password: `P@ssw0rd`
   - Database: `ContactList`
4. Test the connection by printing a success message if the connection is established, or an error message if it fails.

**Key concepts:**
- `mysqli` (MySQL Improved) is a PHP extension for connecting to MySQL and MariaDB databases. It supports both procedural and object-oriented usage — this exercise uses the object-oriented style (`new mysqli(...)`).
- The **host** is set to `127.0.0.1` (localhost) to connect to the MariaDB database server running on the same machine. If `127.0.0.1` does not work, try `0.0.0.0` instead.
- The **port** is the network port the database server listens on. MySQL and MariaDB use port `3306` by default. When no port is passed to `new mysqli(...)`, it automatically uses `3306`, so you do not need to specify it explicitly in most cases.
- Using `root` is acceptable in a local development environment, but you should always use a dedicated, least-privilege database user in production.

Example code snippet:
```php
<?php
$servername = "127.0.0.1"; // If this doesn't work, try "0.0.0.0"
$username = "root";
$password = "P@ssw0rd";
$dbname = "ContactList";

// Create connection
$conn = new mysqli($servername, $username, $password, $dbname);

// Check connection
if ($conn->connect_error) {
    die("Connection failed: " . $conn->connect_error);
}
echo "Connected successfully";
?>
```

**Explanation of the connection check:**
- `$conn->connect_error` contains the error message if the connection failed, or `null` if it succeeded.
- `die()` stops script execution and outputs the error message. This is useful during development to surface connection problems immediately.

## Task 2: Test the Connection

1. Run the `connectdb.php` file in your local PHP server or Codespaces environment. e.g.: `php connectdb.php`
2. Verify that the success message is displayed.
3. Intentionally modify the connection parameters (e.g., change the database name) to test the error handling.
4. When happy that the connection works, remove the `echo "Connected successfully";` line from the `connectdb.php` file.

## Task 3: Retrieve Data from `contact` Table

1. Create a new PHP file named `getcontacts.php`. This file will be used to retrieve data from the `contact` table.
2. Add the following code to  start of the `getcontacts.php` file to connect to the database:

```php
<?php
require_once 'connectdb.php';
```

**Why `require_once`?** `require_once` loads the specified file and ensures it is only included once, even if the statement appears multiple times in the code. This prevents duplicate function or variable declarations (such as `$conn`) that would occur if the file were included more than once. `require` (without `_once`) would include the file every time and cause errors on re-inclusion. `include` and `include_once` work similarly but emit a warning instead of a fatal error if the file is not found — `require_once` is preferred here because the connection file is essential for the script to work.

3. Add a query to retrieve all rows from the `contact` table.

```php
$sql = "SELECT id, first_name, last_name, email, phone FROM contact";
$result = $conn->query($sql);
```

**Explanation:** `$conn->query($sql)` sends the SQL statement to the database and returns a result object. The `SELECT` statement retrieves every row from the `contact` table and returns all five columns.

4. Use a `while` loop to fetch and display the data in a readable format.

```php
if ($result->num_rows > 0) {
    // Output data of each row
    while($row = $result->fetch_assoc()) {
        echo "ID: " . $row["id"] . " - Name: " . $row["first_name"] . " " . $row["last_name"] . " - Email: " . $row["email"] . " - Phone: " . $row["phone"] . "<br>";
        echo "\n";
    }
} else {
    echo "0 results";
}
```

5. Close the database connection at the end of the file.

```php
$conn->close();
```

6. Run the `getcontacts.php` file in your local PHP server or Codespaces environment. e.g.: `php getcontacts.php`. Verify that the data from the `contact` table is displayed.

---

Questions:
1. Why should `require_once 'connectdb.php';` be added to the `getcontacts.php` file? Why not `include` or `require`?
2. Is using the `root` user a good practice for connecting to a database in a production environment? Why or why not?
