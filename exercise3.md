# Exercise 3 - INSERT Data

In this exercise, you will insert data into the `contact` table using PHP, using a form to collect user input.

## Task 1: Create a Form

1. Create a new PHP file named `insert.php`.
2. Include the `connectdb.php` file at the beginning of the `insert.php` file.
3. Create the outline of an HTML page with a title and a heading. Include a navigation block with a link to the `insert.php` file.
4. Add a form to collect the following information from the user:
   - First Name (text input)
   - Last Name (text input)
   - Email (text input)
   - Phone (text input)

Example form snippet:

```html
<form action="insert.php" method="post">
    <label for="first_name">First Name:</label>
    <input type="text" id="first_name" name="first_name" required><br><br>
    <label for="last_name">Last Name:</label>
    <input type="text" id="last_name" name="last_name" required><br><br>
    <label for="email">Email:</label>
    <input type="email" id="email" name="email" required><br><br>
    <label for="phone">Phone:</label>
    <input type="tel" id="phone" name="phone" required><br><br>
    <input type="submit" value="Submit">
</form>
```

## Task 2: Insert Data

1. In the `insert.php` file, retrieve the form data using the `$_POST` superglobal.

Example code snippet:

```php
if ($_SERVER["REQUEST_METHOD"] == "POST") {
    $first_name = $_POST["first_name"];
    $last_name = $_POST["last_name"];
    $email = $_POST["email"];
    $phone = $_POST["phone"];
}
```

2. Insert the form data into the `contact` table using an SQL `INSERT` statement. Display a success message if the data is inserted successfully, or an error message if it fails.

Example code snippet:

```php
$sql = "INSERT INTO contact (first_name, last_name, email, phone) VALUES ('$first_name', '$last_name', '$email', '$phone')";

if ($conn->query($sql) === TRUE) {
    echo "New record created successfully";
} else {
    echo "Error: " . $sql . "<br>" . $conn->error;
}
```

## Task 3: Test the Form

1. Start the PHP server in your local environment or Codespaces. `php -S localhost:8080`
2. Open the `insert.php` file in your browser.
3. Fill out the form and submit it.
4. Verify that the data is inserted into the `contact` table.


## Challenge

- Add validation to the form fields to ensure that the user enters valid data.
