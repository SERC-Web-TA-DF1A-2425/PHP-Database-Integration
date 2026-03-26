# Exercise 4 - SELECT

In this exercise, you will retrieve data from the `contact` table using PHP and display it in a tabular format. You will also add a search form to filter the results based on the user's input.

## Task 1: Retrieve Data

1. Create a new PHP file named `select.php`.
2. Include the `connectdb.php` file at the beginning of the `select.php` file.
3. Use the same HTML structure as in the previous exercises to create a page title, heading, and navigation block.
4. Add a link to the `select.php` file alongside the `insert.php` file. Update the `insert.php` file to include a link back to the `select.php` file.
5. Add a table to display the contact information retrieved from the `contact` table. Include the following columns:
   - ID
   - First Name
   - Last Name
   - Email
   - Phone

Example table structure:
    
    ```html
    <table border="1">
        <tr>
            <th>ID</th>
            <th>First Name</th>
            <th>Last Name</th>
            <th>Email</th>
            <th>Phone</th>
        </tr>
        <!-- Table rows will be added dynamically -->
    </table>
    ```

6. Within the table, add inline PHP code to retrieve the data from the `contact` table and display it in the table rows.

    ```php
    <?php
        $sql = "SELECT id, first_name, last_name, email, phone FROM contact";
        $result = $conn->query($sql);
        
    ```

7. Use a `while` loop to fetch and display the data in a readable format.

    ```php
    if ($result->num_rows > 0) {
        // Output data of each row
        while ($row = $result->fetch_assoc()) {
            echo "<tr>";
            echo "<td>" . $row["id"] . "</td>";
            echo "<td>" . $row["first_name"] . "</td>";
            echo "<td>" . $row["last_name"] . "</td>";
            echo "<td>" . $row["email"] . "</td>";
            echo "<td>" . $row["phone"] . "</td>";
            echo "</tr>";
        }
    } else {
        echo "<tr><td colspan='5'>0 results</td></tr>";
    }
    ?>
    ```

9. Run the `select.php` file in your local PHP server or Codespaces environment. Verify that the data from the `contact` table is displayed in a tabular format.

## Task 2: Add Search Form

1. Add a search form to the `select.php` file above the table. The form should include a text input field to enter the search term and a submit button to trigger the search.

    ```html
    <form action="select.php" method="get">
        <label for="search">Search:</label>
        <input type="text" id="search" name="search">
        <input type="submit" value="Search">
    </form>
    ```

    **Why `method="get"`?** Using `GET` for search forms appends the search term to the URL (e.g., `select.php?search=John`), making the results bookmarkable and shareable. `POST` is used for actions that change data (such as inserting or deleting records) because it does not expose the submitted values in the URL and cannot be replayed by simply refreshing the page.

2. Modify the SQL query in the `select.php` file to include a `WHERE` clause that filters the results based on the search term entered by the user.

    ```php
    $search = $_GET["search"];
    $sql = "SELECT id, first_name, last_name, email, phone FROM contact WHERE first_name LIKE '%$search%' OR last_name LIKE '%$search%' OR email LIKE '%$search%' OR phone LIKE '%$search%'";
    $result = $conn->query($sql);
    ```

    Explanation:
    - The `$_GET["search"]` variable retrieves the search term entered by the user.
    - The SQL query uses the `LIKE` operator to search for the search term in the `first_name`, `last_name`, `email`, and `phone` columns of the `contact` table.
        - The `WHERE` clause filters the results based on if the predicate is true.
        - `LIKE` is used for pattern matching, and `%` is a wildcard character that matches any sequence of characters.

3. Verify that the search functionality works by entering a search term in the form and submitting it. The table should display only the rows that match the search criteria.
4. Fix any issues or errors that may arise during testing.

Questions:
1. What is the purpose of the `LIKE` operator in SQL queries? How does it differ from the `=` operator?
2. Why does the SQL query return results even if the search term is empty?
