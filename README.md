# Project Seal

A departmental web platform connecting students with alumni, providing centralized information, and offering learning roadmaps for Software Engineering students.

##  Project Overview

Project Seal serves as:
- A networking bridge between current students and department alumni
- A central hub for department news, achievements, and opportunities
- A resource for internship and scholarship listings
- A guide providing structured learning roadmaps for software engineering tracks

##  Features (MVP)

### Information Hub
- Department overview and structure
- Student accomplishments showcase
- Internship and scholarship listings
- Association and school news
- Clearance process information
- Department and association structure

### Alumni-Student Networking
- Alumni registration and verification system
- Searchable alumni directory with filters
- Alumni profiles (graduation year, specialization, contact info)

### Learning Roadmaps
- Structured learning paths for various tracks:
  - Web Development
  - Backend Development
  - Mobile Development
  - DevOps
  - Data Science/Engineering
- Stage-based progression with recommended resources

### Authentication System
- Student login with email verification
- Alumni registration and approval workflow
- Admin dashboard for content management

## 🛠️ Tech Stack

- **Frontend**: HTML5, CSS3, JavaScript, TailwindCSS
- **Backend**: PHP 8.0+
- **Database**: MySQL 8.0+
- **Server**: Apache/Nginx
- **Authentication**: PHP Sessions with password hashing

## 📋 Requirements

- PHP 8.0 or higher
- MySQL 8.0 or higher
- Apache/Nginx web server
- Composer (for dependency management)
- SSL certificate (recommended for production)

## 🔧 Installation

### 1. Clone the Repository
```bash
git clone https://github.com/thetruesammyjay/project-seal.git
cd project-seal
```

### 2. Install Dependencies
```bash
composer install
```

### 3. Database Setup
```bash
# Create database
mysql -u root -p
CREATE DATABASE project_seal;
exit

# Import database schema
mysql -u root -p project_seal < database/schema.sql
```

### 4. Configuration
```bash
# Copy environment configuration
cp config/config.example.php config/config.php

# Edit config.php with your settings
nano config/config.php
```

Update the following in `config/config.php`:
- Database credentials
- Site URL
- Email settings
- Admin credentials

### 5. Set Permissions
```bash
chmod -R 755 public/
chmod -R 777 uploads/
chmod 644 config/config.php
```

### 6. Start Development Server
```bash
php -S localhost:8000 -t public/
```

Visit: `http://localhost:8000`

## 🗂️ Project Structure

See [FILE_STRUCTURE.md](FILE_STRUCTURE.md) and [FILE_STRUCTURE_PHP.md](FILE_STRUCTURE_PHP.md) for detailed architecture.

## 👥 User Roles

### Students
- View departmental information
- Browse alumni directory
- Access learning roadmaps
- View opportunities (internships/scholarships)

### Alumni
- Register and create profile
- Update professional information
- Be discoverable to students

### Admin
- Manage all content (CRUD operations)
- Approve/reject alumni registrations
- Publish news and updates
- Edit learning roadmaps
- Manage user accounts

## 🔐 Default Admin Credentials

**Important**: Change these immediately after first login!

- Email: `admin@projectseal.edu`
- Password: `ChangeMe123!`

## 📱 Mobile Responsive

The platform is fully responsive and optimized for:
- Desktop (1920px+)
- Tablet (768px - 1024px)
- Mobile (320px - 767px)

## 🧪 Testing

```bash
# Run PHP unit tests
./vendor/bin/phpunit tests/

# Run database tests
php tests/database_test.php
```

## 🚀 Deployment

### Production Checklist
- [ ] Update database credentials in config
- [ ] Set `ENVIRONMENT = 'production'` in config
- [ ] Enable HTTPS/SSL
- [ ] Change default admin password
- [ ] Set proper file permissions
- [ ] Configure backup system
- [ ] Set up error logging
- [ ] Test all features thoroughly

### Recommended Hosting
- Shared hosting with PHP/MySQL support
- VPS (DigitalOcean, Linode, AWS EC2)
- Managed hosting (SiteGround, Bluehost)

##  Success Metrics (KPIs)

- Number of registered alumni
- Number of verified student users
- Monthly site visits
- Average session duration
- Pages viewed per session
- Content update frequency

##  Future Features (Post-MVP)

- Direct messaging system
- Job application portal
- Discussion forums
- Events calendar with RSVP
- AI chatbot for guidance
- Gamification and achievements
- Mobile application
- Mentorship matching system

##  Contributing

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

## 📝 License

This project is licensed under the MIT License - see the LICENSE file for details.

## 👨‍💻 Author

**Sammy Jay**
- GitHub: [@thetruesammyjay](https://github.com/thetruesammyjay)

##  Support

For issues and questions:
- Create an issue on GitHub
- Email: support@projectseal.edu

##  Acknowledgments

- Department of Software Engineering
- Student Association
- All contributing alumni and students

---

**Version**: 1.0.0 (MVP)  
**Last Updated**: December 2025
