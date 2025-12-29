# Project Seal - File Structure Documentation

## Overview
This document outlines the complete file and directory structure for Project Seal, a PHP/MySQL web platform connecting students with alumni.

## Directory Tree

```
project-seal/
├── config/
│   ├── config.php                 # Main configuration file
│   ├── config.example.php         # Example configuration template
│   ├── database.php               # Database connection handler
│   └── constants.php              # Global constants
│
├── public/                        # Web root directory
│   ├── index.php                  # Main entry point
│   ├── .htaccess                  # Apache URL rewriting rules
│   │
│   ├── assets/
│   │   ├── css/
│   │   │   ├── main.css           # Main stylesheet
│   │   │   ├── tailwind.css       # TailwindCSS
│   │   │   ├── admin.css          # Admin panel styles
│   │   │   └── responsive.css     # Mobile responsive styles
│   │   │
│   │   ├── js/
│   │   │   ├── main.js            # Main JavaScript
│   │   │   ├── search.js          # Alumni search functionality
│   │   │   ├── forms.js           # Form validation
│   │   │   └── admin.js           # Admin panel scripts
│   │   │
│   │   └── images/
│   │       ├── logo.png           # Site logo
│   │       ├── default-avatar.png # Default user avatar
│   │       └── icons/             # Various icons
│   │
│   ├── pages/
│   │   ├── home.php               # Homepage
│   │   ├── about.php              # Department overview
│   │   ├── alumni-directory.php   # Alumni listing
│   │   ├── alumni-profile.php     # Individual alumni profile
│   │   ├── opportunities.php      # Internships & scholarships
│   │   ├── roadmaps.php           # Learning roadmaps listing
│   │   ├── roadmap-detail.php     # Individual roadmap view
│   │   ├── news.php               # News & updates
│   │   ├── clearance.php          # Clearance process info
│   │   ├── structure.php          # Dept & association structure
│   │   ├── login.php              # Login page
│   │   ├── register.php           # Student registration
│   │   ├── alumni-register.php    # Alumni registration
│   │   └── 404.php                # Error page
│   │
│   └── uploads/                   # User uploaded files
│       ├── profiles/              # Profile pictures
│       ├── documents/             # Documents
│       └── news/                  # News images
│
├── src/
│   ├── Controllers/               # Business logic controllers
│   │   ├── AuthController.php
│   │   ├── UserController.php
│   │   ├── AlumniController.php
│   │   ├── ContentController.php
│   │   ├── OpportunityController.php
│   │   ├── RoadmapController.php
│   │   └── AdminController.php
│   │
│   ├── Models/                    # Database models
│   │   ├── User.php
│   │   ├── Alumni.php
│   │   ├── Student.php
│   │   ├── News.php
│   │   ├── Opportunity.php
│   │   ├── Roadmap.php
│   │   └── Content.php
│   │
│   ├── Views/                     # View templates
│   │   ├── layouts/
│   │   │   ├── header.php         # Site header
│   │   │   ├── footer.php         # Site footer
│   │   │   ├── navbar.php         # Navigation bar
│   │   │   └── sidebar.php        # Sidebar (admin)
│   │   │
│   │   ├── components/
│   │   │   ├── alumni-card.php    # Alumni display card
│   │   │   ├── news-card.php      # News article card
│   │   │   ├── opportunity-card.php
│   │   │   ├── roadmap-card.php
│   │   │   └── pagination.php     # Pagination component
│   │   │
│   │   └── admin/
│   │       ├── dashboard.php      # Admin dashboard
│   │       ├── manage-users.php
│   │       ├── manage-alumni.php
│   │       ├── manage-content.php
│   │       ├── manage-opportunities.php
│   │       ├── manage-roadmaps.php
│   │       └── settings.php
│   │
│   ├── Middleware/                # Request middleware
│   │   ├── AuthMiddleware.php     # Authentication check
│   │   ├── AdminMiddleware.php    # Admin authorization
│   │   └── CsrfMiddleware.php     # CSRF protection
│   │
│   ├── Helpers/                   # Helper functions
│   │   ├── ValidationHelper.php   # Input validation
│   │   ├── SecurityHelper.php     # Security functions
│   │   ├── FileHelper.php         # File upload handling
│   │   ├── EmailHelper.php        # Email sending
│   │   └── DateHelper.php         # Date formatting
│   │
│   └── Services/                  # Business services
│       ├── AuthService.php        # Authentication logic
│       ├── AlumniService.php      # Alumni operations
│       ├── SearchService.php      # Search functionality
│       └── NotificationService.php # Notifications
│
├── database/
│   ├── schema.sql                 # Database schema
│   ├── seeds.sql                  # Sample data
│   ├── migrations/                # Database migrations
│   │   ├── 001_create_users.sql
│   │   ├── 002_create_alumni.sql
│   │   ├── 003_create_content.sql
│   │   └── 004_create_opportunities.sql
│   └── backups/                   # Database backups
│
├── admin/                         # Admin panel (protected)
│   ├── index.php                  # Admin dashboard
│   ├── .htaccess                  # Admin access control
│   │
│   ├── users/
│   │   ├── index.php              # User management
│   │   ├── edit.php               # Edit user
│   │   └── delete.php             # Delete user
│   │
│   ├── alumni/
│   │   ├── index.php              # Alumni management
│   │   ├── approve.php            # Approve alumni
│   │   ├── edit.php               # Edit alumni profile
│   │   └── reject.php             # Reject application
│   │
│   ├── content/
│   │   ├── index.php              # Content management
│   │   ├── create.php             # Create content
│   │   ├── edit.php               # Edit content
│   │   └── delete.php             # Delete content
│   │
│   ├── opportunities/
│   │   ├── index.php              # Opportunity management
│   │   ├── create.php             # Create opportunity
│   │   ├── edit.php               # Edit opportunity
│   │   └── delete.php             # Delete opportunity
│   │
│   ├── roadmaps/
│   │   ├── index.php              # Roadmap management
│   │   ├── create.php             # Create roadmap
│   │   ├── edit.php               # Edit roadmap
│   │   └── delete.php             # Delete roadmap
│   │
│   └── settings/
│       ├── index.php              # General settings
│       ├── profile.php            # Admin profile
│       └── security.php           # Security settings
│
├── api/                           # API endpoints (future)
│   ├── v1/
│   │   ├── auth.php
│   │   ├── alumni.php
│   │   ├── opportunities.php
│   │   └── roadmaps.php
│   └── .htaccess                  # API access rules
│
├── tests/                         # Test files
│   ├── Unit/
│   │   ├── AuthTest.php
│   │   ├── UserTest.php
│   │   └── AlumniTest.php
│   │
│   ├── Integration/
│   │   ├── RegistrationTest.php
│   │   └── SearchTest.php
│   │
│   └── database_test.php          # Database connection test
│
├── logs/                          # Application logs
│   ├── error.log                  # Error logs
│   ├── access.log                 # Access logs
│   └── admin.log                  # Admin activity logs
│
├── vendor/                        # Composer dependencies
│
├── docs/                          # Documentation
│   ├── API.md                     # API documentation
│   ├── DATABASE.md                # Database schema docs
│   ├── DEPLOYMENT.md              # Deployment guide
│   └── CONTRIBUTING.md            # Contribution guidelines
│
├── .gitignore                     # Git ignore rules
├── composer.json                  # Composer dependencies
├── composer.lock                  # Composer lock file
├── LICENSE                        # License file
├── README.md                      # Project readme
├── FILE_STRUCTURE.md              # This file
└── FILE_STRUCTURE_PHP.md          # PHP-specific structure

```

## Key Directory Purposes

### `/config`
Contains all configuration files including database credentials, site settings, and environment variables. These files should never be committed to version control.

### `/public`
The web-accessible root directory. All HTTP requests are routed through `index.php`. Contains static assets (CSS, JS, images) and public-facing pages.

### `/src`
Core application code organized by architectural pattern (MVC):
- **Controllers**: Handle HTTP requests and responses
- **Models**: Database interaction and business entities
- **Views**: HTML templates and UI components
- **Middleware**: Request/response processing
- **Helpers**: Reusable utility functions
- **Services**: Business logic services

### `/database`
Database-related files including schema definitions, migrations, seeds, and backup storage.

### `/admin`
Protected administrative interface with its own authentication layer. Handles content management, user administration, and system settings.

### `/uploads`
Stores user-uploaded files with proper permission settings. Subdirectories organize different file types.

### `/tests`
Automated testing suite with unit and integration tests.

### `/logs`
Application logging for debugging, security auditing, and monitoring.

## Security Considerations

1. **Protected Directories**: Use `.htaccess` to restrict access to `/admin`, `/config`, and `/logs`
2. **File Permissions**: Set appropriate chmod permissions (755 for directories, 644 for files)
3. **Upload Validation**: Validate and sanitize all uploaded files
4. **Environment Files**: Never commit `config.php` or files containing credentials

## Naming Conventions

- **Files**: PascalCase for classes (`UserController.php`), kebab-case for pages (`alumni-directory.php`)
- **Directories**: lowercase with hyphens for multi-word names
- **Classes**: PascalCase
- **Functions**: camelCase
- **Constants**: UPPER_SNAKE_CASE

## Scalability Notes

The structure supports future expansion:
- API directory ready for RESTful endpoints
- Modular component system for reusability
- Service layer for complex business logic
- Clear separation of concerns for maintainability
