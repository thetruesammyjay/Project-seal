# Project Seal - PHP File Structure & Code Architecture

## PHP Architecture Overview

This document details the PHP-specific implementation structure for Project Seal, including code organization, class structures, and implementation patterns.

## Core PHP Files Breakdown

### Configuration Layer

#### `/config/config.php`
```php
<?php
// Environment Configuration
define('ENVIRONMENT', 'development'); // development | production
define('BASE_URL', 'http://localhost:8000');
define('SITE_NAME', 'Project Seal');

// Database Configuration
define('DB_HOST', 'localhost');
define('DB_NAME', 'project_seal');
define('DB_USER', 'root');
define('DB_PASS', '');
define('DB_CHARSET', 'utf8mb4');

// Security
define('SESSION_NAME', 'project_seal_session');
define('CSRF_TOKEN_NAME', 'csrf_token');
define('PASSWORD_HASH_ALGO', PASSWORD_BCRYPT);

// File Upload
define('MAX_FILE_SIZE', 5242880); // 5MB
define('ALLOWED_IMAGE_TYPES', ['jpg', 'jpeg', 'png', 'gif']);
define('UPLOAD_PATH', __DIR__ . '/../public/uploads/');

// Email Configuration
define('SMTP_HOST', 'smtp.gmail.com');
define('SMTP_PORT', 587);
define('SMTP_USER', 'noreply@projectseal.edu');
define('SMTP_PASS', 'your_password');

// Pagination
define('ITEMS_PER_PAGE', 12);

// Admin
define('ADMIN_EMAIL', 'admin@projectseal.edu');
```

#### `/config/database.php`
```php
<?php
class Database {
    private static $instance = null;
    private $connection;
    
    private function __construct() {
        try {
            $dsn = "mysql:host=" . DB_HOST . ";dbname=" . DB_NAME . ";charset=" . DB_CHARSET;
            $options = [
                PDO::ATTR_ERRMODE => PDO::ERRMODE_EXCEPTION,
                PDO::ATTR_DEFAULT_FETCH_MODE => PDO::FETCH_ASSOC,
                PDO::ATTR_EMULATE_PREPARES => false,
            ];
            $this->connection = new PDO($dsn, DB_USER, DB_PASS, $options);
        } catch(PDOException $e) {
            error_log("Database Connection Error: " . $e->getMessage());
            die("Database connection failed");
        }
    }
    
    public static function getInstance() {
        if (self::$instance === null) {
            self::$instance = new self();
        }
        return self::$instance;
    }
    
    public function getConnection() {
        return $this->connection;
    }
}
```

---

### Entry Point

#### `/public/index.php`
```php
<?php
session_start();

// Autoload
require_once __DIR__ . '/../vendor/autoload.php';
require_once __DIR__ . '/../config/config.php';
require_once __DIR__ . '/../config/database.php';

// Simple Router
$request_uri = parse_url($_SERVER['REQUEST_URI'], PHP_URL_PATH);
$request_uri = str_replace(parse_url(BASE_URL, PHP_URL_PATH), '', $request_uri);

// Route handling
switch($request_uri) {
    case '/':
    case '/home':
        require __DIR__ . '/pages/home.php';
        break;
    case '/about':
        require __DIR__ . '/pages/about.php';
        break;
    case '/alumni-directory':
        require __DIR__ . '/pages/alumni-directory.php';
        break;
    case '/login':
        require __DIR__ . '/pages/login.php';
        break;
    // ... more routes
    default:
        http_response_code(404);
        require __DIR__ . '/pages/404.php';
        break;
}
```

---

### Controllers

#### `/src/Controllers/AuthController.php`
```php
<?php
namespace App\Controllers;

use App\Models\User;
use App\Helpers\SecurityHelper;
use App\Services\AuthService;

class AuthController {
    private $authService;
    
    public function __construct() {
        $this->authService = new AuthService();
    }
    
    public function login($email, $password) {
        // Validate input
        if (empty($email) || empty($password)) {
            return ['success' => false, 'message' => 'All fields required'];
        }
        
        // Authenticate
        $result = $this->authService->authenticate($email, $password);
        
        if ($result['success']) {
            $_SESSION['user_id'] = $result['user']['id'];
            $_SESSION['user_role'] = $result['user']['role'];
            $_SESSION['logged_in'] = true;
        }
        
        return $result;
    }
    
    public function logout() {
        session_destroy();
        header('Location: /login');
        exit();
    }
    
    public function register($data) {
        // Validation
        $errors = $this->validateRegistration($data);
        if (!empty($errors)) {
            return ['success' => false, 'errors' => $errors];
        }
        
        // Hash password
        $data['password'] = password_hash($data['password'], PASSWORD_HASH_ALGO);
        
        // Create user
        $user = new User();
        $result = $user->create($data);
        
        return $result;
    }
    
    private function validateRegistration($data) {
        $errors = [];
        // Validation logic
        return $errors;
    }
}
```

#### `/src/Controllers/AlumniController.php`
```php
<?php
namespace App\Controllers;

use App\Models\Alumni;
use App\Helpers\ValidationHelper;

class AlumniController {
    private $alumniModel;
    
    public function __construct() {
        $this->alumniModel = new Alumni();
    }
    
    public function getAllAlumni($filters = [], $page = 1) {
        $offset = ($page - 1) * ITEMS_PER_PAGE;
        return $this->alumniModel->getAll($filters, ITEMS_PER_PAGE, $offset);
    }
    
    public function getAlumniById($id) {
        return $this->alumniModel->getById($id);
    }
    
    public function searchAlumni($query, $filters = []) {
        return $this->alumniModel->search($query, $filters);
    }
    
    public function registerAlumni($data) {
        // Validate
        if (!ValidationHelper::validateEmail($data['email'])) {
            return ['success' => false, 'message' => 'Invalid email'];
        }
        
        // Set pending status
        $data['status'] = 'pending';
        
        return $this->alumniModel->create($data);
    }
    
    public function approveAlumni($id) {
        return $this->alumniModel->updateStatus($id, 'approved');
    }
    
    public function rejectAlumni($id, $reason = '') {
        return $this->alumniModel->updateStatus($id, 'rejected', $reason);
    }
}
```

#### `/src/Controllers/AdminController.php`
```php
<?php
namespace App\Controllers;

use App\Models\Content;
use App\Models\User;
use App\Models\Alumni;

class AdminController {
    public function getDashboardStats() {
        return [
            'total_users' => User::count(),
            'total_alumni' => Alumni::count(),
            'pending_alumni' => Alumni::countPending(),
            'total_content' => Content::count(),
            'recent_registrations' => User::getRecent(5),
        ];
    }
    
    public function manageContent($action, $data) {
        $content = new Content();
        
        switch($action) {
            case 'create':
                return $content->create($data);
            case 'update':
                return $content->update($data['id'], $data);
            case 'delete':
                return $content->delete($data['id']);
            default:
                return ['success' => false, 'message' => 'Invalid action'];
        }
    }
}
```

---

### Models

#### `/src/Models/User.php`
```php
<?php
namespace App\Models;

use PDO;

class User {
    private $db;
    private $table = 'users';
    
    public function __construct() {
        $this->db = \Database::getInstance()->getConnection();
    }
    
    public function create($data) {
        $sql = "INSERT INTO {$this->table} (email, password, full_name, role, created_at) 
                VALUES (:email, :password, :full_name, :role, NOW())";
        
        try {
            $stmt = $this->db->prepare($sql);
            $stmt->execute([
                ':email' => $data['email'],
                ':password' => $data['password'],
                ':full_name' => $data['full_name'],
                ':role' => $data['role'] ?? 'student'
            ]);
            
            return [
                'success' => true,
                'user_id' => $this->db->lastInsertId()
            ];
        } catch(\PDOException $e) {
            error_log("User Creation Error: " . $e->getMessage());
            return ['success' => false, 'message' => 'Registration failed'];
        }
    }
    
    public function findByEmail($email) {
        $sql = "SELECT * FROM {$this->table} WHERE email = :email LIMIT 1";
        $stmt = $this->db->prepare($sql);
        $stmt->execute([':email' => $email]);
        return $stmt->fetch();
    }
    
    public function findById($id) {
        $sql = "SELECT * FROM {$this->table} WHERE id = :id LIMIT 1";
        $stmt = $this->db->prepare($sql);
        $stmt->execute([':id' => $id]);
        return $stmt->fetch();
    }
    
    public static function count() {
        $db = \Database::getInstance()->getConnection();
        $result = $db->query("SELECT COUNT(*) FROM users")->fetchColumn();
        return $result;
    }
    
    public static function getRecent($limit = 10) {
        $db = \Database::getInstance()->getConnection();
        $stmt = $db->prepare("SELECT * FROM users ORDER BY created_at DESC LIMIT :limit");
        $stmt->bindValue(':limit', $limit, PDO::PARAM_INT);
        $stmt->execute();
        return $stmt->fetchAll();
    }
}
```

#### `/src/Models/Alumni.php`
```php
<?php
namespace App\Models;

use PDO;

class Alumni {
    private $db;
    private $table = 'alumni';
    
    public function __construct() {
        $this->db = \Database::getInstance()->getConnection();
    }
    
    public function create($data) {
        $sql = "INSERT INTO {$this->table} 
                (user_id, graduation_year, field_of_study, current_position, 
                 company, linkedin_url, bio, status, created_at) 
                VALUES (:user_id, :graduation_year, :field_of_study, :current_position, 
                        :company, :linkedin_url, :bio, :status, NOW())";
        
        try {
            $stmt = $this->db->prepare($sql);
            $stmt->execute([
                ':user_id' => $data['user_id'],
                ':graduation_year' => $data['graduation_year'],
                ':field_of_study' => $data['field_of_study'],
                ':current_position' => $data['current_position'] ?? null,
                ':company' => $data['company'] ?? null,
                ':linkedin_url' => $data['linkedin_url'] ?? null,
                ':bio' => $data['bio'] ?? null,
                ':status' => $data['status']
            ]);
            
            return ['success' => true, 'alumni_id' => $this->db->lastInsertId()];
        } catch(\PDOException $e) {
            error_log("Alumni Creation Error: " . $e->getMessage());
            return ['success' => false, 'message' => 'Registration failed'];
        }
    }
    
    public function getAll($filters = [], $limit = 12, $offset = 0) {
        $sql = "SELECT a.*, u.full_name, u.email 
                FROM {$this->table} a 
                JOIN users u ON a.user_id = u.id 
                WHERE a.status = 'approved'";
        
        $params = [];
        
        if (!empty($filters['field'])) {
            $sql .= " AND a.field_of_study = :field";
            $params[':field'] = $filters['field'];
        }
        
        if (!empty($filters['year'])) {
            $sql .= " AND a.graduation_year = :year";
            $params[':year'] = $filters['year'];
        }
        
        $sql .= " ORDER BY a.graduation_year DESC LIMIT :limit OFFSET :offset";
        
        $stmt = $this->db->prepare($sql);
        $stmt->bindValue(':limit', $limit, PDO::PARAM_INT);
        $stmt->bindValue(':offset', $offset, PDO::PARAM_INT);
        
        foreach ($params as $key => $value) {
            $stmt->bindValue($key, $value);
        }
        
        $stmt->execute();
        return $stmt->fetchAll();
    }
    
    public function getById($id) {
        $sql = "SELECT a.*, u.full_name, u.email 
                FROM {$this->table} a 
                JOIN users u ON a.user_id = u.id 
                WHERE a.id = :id LIMIT 1";
        
        $stmt = $this->db->prepare($sql);
        $stmt->execute([':id' => $id]);
        return $stmt->fetch();
    }
    
    public function search($query, $filters = []) {
        $sql = "SELECT a.*, u.full_name, u.email 
                FROM {$this->table} a 
                JOIN users u ON a.user_id = u.id 
                WHERE a.status = 'approved' 
                AND (u.full_name LIKE :query 
                     OR a.field_of_study LIKE :query 
                     OR a.current_position LIKE :query)";
        
        $params = [':query' => "%{$query}%"];
        
        $stmt = $this->db->prepare($sql);
        $stmt->execute($params);
        return $stmt->fetchAll();
    }
    
    public function updateStatus($id, $status, $reason = '') {
        $sql = "UPDATE {$this->table} SET status = :status";
        
        if (!empty($reason)) {
            $sql .= ", rejection_reason = :reason";
        }
        
        $sql .= " WHERE id = :id";
        
        try {
            $stmt = $this->db->prepare($sql);
            $params = [':status' => $status, ':id' => $id];
            
            if (!empty($reason)) {
                $params[':reason'] = $reason;
            }
            
            $stmt->execute($params);
            return ['success' => true];
        } catch(\PDOException $e) {
            return ['success' => false, 'message' => 'Update failed'];
        }
    }
    
    public static function count() {
        $db = \Database::getInstance()->getConnection();
        return $db->query("SELECT COUNT(*) FROM alumni WHERE status = 'approved'")->fetchColumn();
    }
    
    public static function countPending() {
        $db = \Database::getInstance()->getConnection();
        return $db->query("SELECT COUNT(*) FROM alumni WHERE status = 'pending'")->fetchColumn();
    }
}
```

#### `/src/Models/Content.php`
```php
<?php
namespace App\Models;

class Content {
    private $db;
    private $table = 'content';
    
    public function __construct() {
        $this->db = \Database::getInstance()->getConnection();
    }
    
    public function create($data) {
        $sql = "INSERT INTO {$this->table} 
                (title, slug, content_type, body, author_id, status, created_at) 
                VALUES (:title, :slug, :content_type, :body, :author_id, :status, NOW())";
        
        try {
            $stmt = $this->db->prepare($sql);
            $stmt->execute([
                ':title' => $data['title'],
                ':slug' => $this->generateSlug($data['title']),
                ':content_type' => $data['content_type'],
                ':body' => $data['body'],
                ':author_id' => $data['author_id'],
                ':status' => $data['status'] ?? 'draft'
            ]);
            
            return ['success' => true, 'content_id' => $this->db->lastInsertId()];
        } catch(\PDOException $e) {
            return ['success' => false, 'message' => 'Content creation failed'];
        }
    }
    
    public function getByType($type, $limit = 10) {
        $sql = "SELECT * FROM {$this->table} 
                WHERE content_type = :type AND status = 'published' 
                ORDER BY created_at DESC LIMIT :limit";
        
        $stmt = $this->db->prepare($sql);
        $stmt->bindValue(':type', $type);
        $stmt->bindValue(':limit', $limit, \PDO::PARAM_INT);
        $stmt->execute();
        return $stmt->fetchAll();
    }
    
    private function generateSlug($title) {
        return strtolower(trim(preg_replace('/[^A-Za-z0-9-]+/', '-', $title), '-'));
    }
    
    public static function count() {
        $db = \Database::getInstance()->getConnection();
        return $db->query("SELECT COUNT(*) FROM content WHERE status = 'published'")->fetchColumn();
    }
}
```

---

### Middleware

#### `/src/Middleware/AuthMiddleware.php`
```php
<?php
namespace App\Middleware;

class AuthMiddleware {
    public static function check() {
        if (!isset($_SESSION['logged_in']) || $_SESSION['logged_in'] !== true) {
            header('Location: /login');
            exit();
        }
    }
    
    public static function guest() {
        if (isset($_SESSION['logged_in']) && $_SESSION['logged_in'] === true) {
            header('Location: /dashboard');
            exit();
        }
    }
}
```

#### `/src/Middleware/AdminMiddleware.php`
```php
<?php
namespace App\Middleware;

class AdminMiddleware {
    public static function check() {
        AuthMiddleware::check();
        
        if (!isset($_SESSION['user_role']) || $_SESSION['user_role'] !== 'admin') {
            http_response_code(403);
            die('Access Denied');
        }
    }
}
```

---

### Helpers

#### `/src/Helpers/ValidationHelper.php`
```php
<?php
namespace App\Helpers;

class ValidationHelper {
    public static function validateEmail($email) {
        return filter_var($email, FILTER_VALIDATE_EMAIL) !== false;
    }
    
    public static function validatePassword($password) {
        // Min 8 chars, 1 uppercase, 1 lowercase, 1 number
        return preg_match('/^(?=.*[a-z])(?=.*[A-Z])(?=.*\d).{8,}$/', $password);
    }
    
    public static function sanitize($data) {
        if (is_array($data)) {
            return array_map([self::class, 'sanitize'], $data);
        }
        return htmlspecialchars(strip_tags(trim($data)), ENT_QUOTES, 'UTF-8');
    }
    
    public static function validateYear($year) {
        $currentYear = date('Y');
        return is_numeric($year) && $year >= 1900 && $year <= $currentYear;
    }
}
```

#### `/src/Helpers/SecurityHelper.php`
```php
<?php
namespace App\Helpers;

class SecurityHelper {
    public static function generateToken() {
        return bin2hex(random_bytes(32));
    }
    
    public static function generateCsrfToken() {
        if (!isset($_SESSION[CSRF_TOKEN_NAME])) {
            $_SESSION[CSRF_TOKEN_NAME] = self::generateToken();
        }
        return $_SESSION[CSRF_TOKEN_NAME];
    }
    
    public static function verifyCsrfToken($token) {
        return isset($_SESSION[CSRF_TOKEN_NAME]) && 
               hash_equals($_SESSION[CSRF_TOKEN_NAME], $token);
    }
    
    public static function hashPassword($password) {
        return password_hash($password, PASSWORD_HASH_ALGO);
    }
    
    public static function verifyPassword($password, $hash) {
        return password_verify($password, $hash);
    }
}
```

---

### Services

#### `/src/Services/AuthService.php`
```php
<?php
namespace App\Services;

use App\Models\User;
use App\Helpers\SecurityHelper;

class AuthService {
    private $userModel;
    
    public function __construct() {
        $this->userModel = new User();
    }
    
    public function authenticate($email, $password) {
        $user = $this->userModel->findByEmail($email);
        
        if (!$user) {
            return ['success' => false, 'message' => 'Invalid credentials'];
        }
        
        if (!SecurityHelper::verifyPassword($password, $user['password'])) {
            return ['success' => false, 'message' => 'Invalid credentials'];
        }
        
        return [
            'success' => true,
            'user' => [
                'id' => $user['id'],
                'email' => $user['email'],
                'full_name' => $user['full_name'],
                'role' => $user['role']
            ]
        ];
    }
    
    public function getCurrentUser() {
        if (!isset($_SESSION['user_id'])) {
            return null;
        }
        
        return $this->userModel->findById($_SESSION['user_id']);
    }
}
```

---

### View Templates

#### `/src/Views/layouts/header.php`
```php
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title><?php echo $pageTitle ?? 'Project Seal'; ?></title>
    <link href="<?php echo BASE_URL; ?>/assets/css/tailwind.css" rel="stylesheet">
    <link href="<?php echo BASE_URL; ?>/assets/css/main.css" rel="stylesheet">
    <?php if(isset($additionalCSS)): ?>
        <?php foreach($additionalCSS as $css): ?>
            <link href="<?php echo BASE_URL . $css; ?>" rel="stylesheet">
        <?php endforeach; ?>
    <?php endif; ?>
</head>
<body>
    <?php include __DIR__ . '/navbar.php'; ?>
    <main class="container mx-auto px-4 py-8">
```

#### `/src/Views/layouts/footer.php`
```php
    </main>
    
    <footer class="bg-gray-800 text-white py-8 mt-12">
        <div class="container mx-auto px-4 text-center">
            <p>&copy; <?php echo date('Y'); ?> Project Seal. All rights reserved.</p>
            <p class="mt-2">
                <a href="/about" class="hover:underline">About</a> | 
                <a href="/contact" class="hover:underline">Contact</a> | 
                <a href="/privacy" class="hover:underline">Privacy Policy</a>
            </p>
        </div>
    </footer>
    
    <script src="<?php echo BASE_URL; ?>/assets/js/main.js"></script>
    <?php if(isset($additionalJS)): ?>
        <?php foreach($additionalJS as $js): ?>
            <script src="<?php echo BASE_URL . $js; ?>"></script>
        <?php endforeach; ?>
    <?php endif; ?>
</body>
</html>
```

---

## Database Schema Reference

See `/database/schema.sql` for complete schema. Key tables:
- `users` - User authentication and profiles
- `alumni` - Alumni-specific information
- `content` - CMS content (news, updates)
- `opportunities` - Internships and scholarships
- `roadmaps` - Learning roadmaps
- `roadmap_stages` - Roadmap progression stages

## Autoloading with Composer

```json
{
    "autoload": {
        "psr-4": {
            "App\\": "src/"
        }
    }
}
```

Run `composer dump-autoload` after adding new classes.

## Error Handling Pattern

```php
try {
    // Database operation
} catch(\PDOException $e) {
    error_log($e->getMessage());
    if (ENVIRONMENT === 'development') {
        throw $e;
    }
    return ['success' => false, 'message' => 'Operation failed'];
}
```

## Security Best Practices Implemented

1. **Password Hashing**: Using PHP's `password_hash()` with bcrypt
2. **Prepared Statements**: All database queries use PDO prepared statements
3. **CSRF Protection**: Token validation on forms
4. **Input Sanitization**: Using `htmlspecialchars()` and validation helpers
5. **Session Security**: Secure session configuration
6. **SQL Injection Prevention**: Parameterized queries only
7. **XSS Prevention**: Output escaping in views

---

This structure provides a solid, scalable foundation for Project Seal using PHP and MySQL with MVC architecture.
