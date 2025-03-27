# Exercise 6 - DELETE

In this exercise, you will learn how to delete records from a MySQL database using PHP. You will create a PHP script that deletes a record from the `contact` table based on the `id` value passed as a URL parameter.

## Task 1: Create a Delete Script

1. Create a new PHP file named `delete.php`.
2. Include the `connectdb.php` file at the beginning of the `delete.php` file.
3. Retrieve the `id` value from the URL using the `$_GET` superglobal.
4. Use the retrieved `id` value to construct an SQL `DELETE` statement to remove the record from the `contact` table.

Example code snippet:

```php
if ($_SERVER["REQUEST_METHOD"] == "GET") {
    $id = $_GET["id"];
    $sql = "DELETE FROM contact WHERE id = $id";

    if ($conn->query($sql) === TRUE) {
        echo "Record deleted successfully";
    } else {
        echo "Error deleting record: " . $conn->error;
    }
}
```


## Task 2: Test the Delete Script

1. Run the `delete.php` file in your local PHP server or Codespaces environment. e.g.: `php delete.php?id=1`
2. Verify that the record with the specified `id` is deleted from the `contact` table.
3. Test the script with different `id` values to ensure that records can be deleted successfully.


## Challenge

1. Add a button to the `update.php` file that links to the `delete.php` script with the `id` parameter of the current contact.
2. When the button is clicked, the record for the current contact should be deleted from the `contact` table.
