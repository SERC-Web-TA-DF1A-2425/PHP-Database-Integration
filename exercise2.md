# Exercise 2 - Connect to Database

In this exercise, you will connect to the database using PHP and retrieve data from the `contact` table.

## Task 1: Connect to Database

1. Create a new PHP file named `connectdb.php`.
2. Use the `mysqli` extension to connect to the `ContactList` database.
3. Use the following connection parameters:
   - Host: `127.0.0.1`
   - Username: `root`
   - Password: `mariadb`
   - Database: `ContactList`
4. Test the connection by printing a success message if the connection is established, or an error message if it fails.

Example code snippet:
```php
<?php
$servername = "127.0.0.1";
$username = "root";
$password = "mariadb";
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

3. Add a query to retrieve all rows from the `contact` table.

```php
$sql = "SELECT id, first_name, last_name, email, phone FROM contact";
$result = $conn->query($sql);
```

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
