

```sql
CREATE DATABASE pornsite;
USE pornsite;

CREATE TABLE videos (
  id INT PRIMARY KEY AUTO_INCREMENT,
  title VARCHAR(255),
  link VARCHAR(255),
  thumbnail VARCHAR(255)
);

CREATE TABLE users (
  id INT PRIMARY KEY AUTO_INCREMENT,
  username VARCHAR(50),
  email VARCHAR(100),
  password VARCHAR(255)
);
```

Frontend ke liye HTML/CSS/JS use karo. Index.php main page hoga:

```php
<?php
require_once 'db.php';

if (isset($_GET['page'])) {
    $page = $_GET['page'];
} else {
    $page = 1;
}

$limit = 15;
$start = ($page - 1) * $limit;

$query = "SELECT * FROM videos LIMIT $start, $limit";
$result = mysqli_query($conn, $query);

?>

<!DOCTYPE html>
<html>
<head>
    <title>Porn Site</title>
</head>
<body>
    <h1>Porn Videos</h1>

    <?php while($row = mysqli_fetch_assoc($result)): ?>
        <div style="width: 200px; height: auto; float: left;">
            <img src="<?php echo $row['thumbnail']; ?>" alt="<?php echo $row['title']; ?>">
            <br>
            <a href="<?php echo $row['link']; ?>"><?php echo $row['title']; ?></a>
        </div>
    <?php endwhile; ?>

    <?php if($page > 1): ?>
        <a href="?page=<?php echo ($page - 1); ?>">Previous</a>
    <?php endif; ?>

    <?php if(mysqli_num_rows(mysqli_query($conn, "SELECT * FROM videos")) > ($page * $limit)): ?>
        <a href="?page=<?php echo ($page + 1); ?>">Next</a>
    <?php endif; ?>

</body>
</html>
```

Login/signup ke liye register.php aur login.php banalo:

```php
// register.php

if (isset($_POST['submit'])) {
    $username = $_POST['username'];
    $email = $_POST['email'];
    $password = password_hash($_POST['password'], PASSWORD_DEFAULT);

    $query = "INSERT INTO users (username, email, password) VALUES ('$username', '$email', '$password')";
    mysqli_query($conn, $query);
}

?>

<form action="" method="post">
    Username: <input type="text" name="username"><br><br>
    Email: <input type="email" name="email"><br><br>
    Password: <input type="password" name="password"><br><br>
    <input type="submit" name="submit" value="Sign Up">
</form>

// login.php

if (isset($_POST['submit'])) {
    $username = $_POST['username'];
    $password = $_POST['password'];

    $query = "SELECT * FROM users WHERE username='$username'";
    $result = mysqli_query($conn, $query);

    if (mysqli_num_rows($result) > 0) {
        while ($row = mysqli_fetch_assoc($result)) {
            if (password_verify($password, $row['password'])) {
                // Login success
                session_start();
                $_SESSION['id'] = $row['id'];
                header('Location: index.php');
            } else {
                echo "Wrong password";
            }
        }
    } else {
        echo "User not found";
    }
}

?>

<form action="" method="post">
    Username: <input type="text" name="username"><br><br>
    Password: <input type="password" name="password"><br><br>
    <input type="submit" name="submit" value="Login">
</form>
```

Video upload ke liye upload.php banalo:

```php
if (isset($_POST['upload'])) {
    // Video details
    $title = $_POST['title'];
    // Thumbnail logic yahan aayega
}

?>

<form action="" method="post">
    Title: <input type="text" name="title"><br><br>
    
    <!-- Video input field -->
    
    <input type="submit" name="upload" value="Upload Video">
</form>
