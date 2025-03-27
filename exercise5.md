# Exercise 5 - UPDATE

In this exercise, you will update data in the `contacts` table using PHP. You will create a form to retrieve the contact information and display it for editing. The user will be able to update the contact details and submit the changes to the database.

## Task 1: Create an Update Form

1. Create a new PHP file named `update.php`.
2. Include the `connectdb.php` file at the beginning of the `update.php` file.
3. Create the outline of an HTML page with a title and a heading. Update all the navigation blocks to include a link to the `update.php` file.
4. Add a form to retrieve the contact information based on the contact ID. Include the following fields:
   - Contact ID (text input)

Example form snippet:
    
    ```html
    <form action="update.php" method="get">
        <label for="id">Contact ID:</label>
        <input type="text" id="id" name="id" required><br><br>
        <input type="submit" value="Retrieve">
    </form>
    ```

## Task 2: Retrieve and Display Contact Information

1. In the `update.php` file, retrieve the form data using the `$_GET` superglobal.
2. Retrieve the contact information based on the contact ID entered in the form.

Example code snippet:

```php
if ($_SERVER["REQUEST_METHOD"] == "GET") {
    $id = $_GET["id"];
    $sql = "SELECT id, first_name, last_name, email, phone FROM contacts WHERE id = $id";
    $result = $conn->query($sql);

    // use first row only
    if ($result->num_rows > 0) {
        $row = $result->fetch_assoc();
        $first_name = $row["first_name"];
        $last_name = $row["last_name"];
        $email = $row["email"];
        $phone = $row["phone"];
    }
}
```
3. Add a form to display the contact information in the form fields for editing. Place this below the form that retrieves the contact information.

Example form snippet:

```html
<form action="update.php" method="post">
    <label for="first_name">First Name:</label>
    <input type="text" id="first_name" name="first_name" value="<?php echo $first_name; ?>" required><br><br>
    <label for="last_name">Last Name:</label>
    <input type="text" id="last_name" name="last_name" value="<?php echo $last_name; ?>" required><br><br>
    <label for="email">Email:</label>
    <input type="email" id="email" name="email" value="<?php echo $email; ?>" required><br><br>
    <label for="phone">Phone:</label>
    <input type="tel" id="phone" name="phone" value="<?php echo $phone; ?>" required><br><br>
    <input type="hidden" name="id" value="<?php echo $id; ?>">
    <input type="submit" value="Update">
</form>
```

## Task 3: Update Contact Information

1. In the `update.php` file, retrieve the updated form data using the `$_POST` superglobal.

Example code snippet:

```php
if ($_SERVER["REQUEST_METHOD"] == "POST") {
    $id = $_POST["id"];
    $first_name = $_POST["first_name"];
    $last_name = $_POST["last_name"];
    $email = $_POST["email"];
    $phone = $_POST["phone"];
}
```

2. Update the contact information in the `contacts` table using an SQL `UPDATE` statement. Display a success message if the data is updated successfully, or an error message if it fails.

Example code snippet:

```php
$sql = "UPDATE contacts SET first_name='$first_name', last_name='$last_name', email='$email', phone='$phone' WHERE id=$id";

if ($conn->query($sql) === TRUE) {
    echo "Record updated successfully";
} else {
    echo "Error updating record: " . $conn->error;
}
```

3. Run the `update.php` file in your local PHP server or Codespaces environment. Verify that you can retrieve contact information, edit it, and update the details in the `contacts` table.

## Challenge

- Add a confirmation prompt before updating the contact information to confirm the changes.