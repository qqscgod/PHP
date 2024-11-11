<?php
$servername = "localhost"; // Your server name
$username = "root";        // Your database username
$password = "";            // Your database password
$dbname = "log_db";        // Your database name

// Create connection
$conn = new mysqli($servername, $username, $password, $dbname);

// Check connection
if ($conn->connect_error) {
    die("Connection failed: " . $conn->connect_error);
}
?>
<?php
include('db.php'); // Include the database connection

// Check if form is submitted
if ($_SERVER['REQUEST_METHOD'] == 'POST') {
    $user_id = $_POST['user_id'];
    $status = $_POST['status'];

    // Prepare the SQL query to insert the log entry
    $sql = "INSERT INTO log_entries (user_id, status) VALUES ('$user_id', '$status')";

    if ($conn->query($sql) === TRUE) {
        echo "Log entry recorded successfully!";
    } else {
        echo "Error: " . $sql . "<br>" . $conn->error;
    }

    // Close the database connection
    $conn->close();
}
?>

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Scan In/Out</title>
</head>
<body>
    <h1>Log In/Out</h1>

    <form method="POST" action="log.php">
        <label for="user_id">User ID:</label>
        <input type="text" id="user_id" name="user_id" required><br><br>

        <label for="status">Status:</label>
        <select name="status" id="status" required>
            <option value="in">In</option>
            <option value="out">Out</option>
        </select><br><br>

        <input type="submit" value="Submit">
    </form>

    <h2>Recent Logs</h2>
    <table border="1">
        <tr>
            <th>User ID</th>
            <th>Status</th>
            <th>Timestamp</th>
        </tr>

        <?php
        // Fetch and display recent log entries
        $result = $conn->query("SELECT * FROM log_entries ORDER BY timestamp DESC LIMIT 10");

        while ($row = $result->fetch_assoc()) {
            echo "<tr>
                    <td>" . $row['user_id'] . "</td>
                    <td>" . $row['status'] . "</td>
                    <td>" . $row['timestamp'] . "</td>
                  </tr>";
        }

        $conn->close();
        ?>
    </table>
</body>
</html>
