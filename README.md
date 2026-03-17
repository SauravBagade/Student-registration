# Student Registration System

A full-stack web application for student registration with a React frontend and Spring Boot backend.

## 🚀 Current Status

The application is currently configured to run **without database dependencies** for easy development and testing. All data is stored in memory and will be reset when the application restarts.

## 📁 Project Structure

```
EasyCRUD/
├── backend/                    # Spring Boot Backend
│   ├── src/main/java/
│   │   └── com/student/registration/
│   │       └── student_registration_backend/
│   │           ├── controller/     # REST Controllers
│   │           ├── model/          # Data Models
│   │           └── config/         # Configuration
│   ├── src/main/resources/
│   │   └── application.properties  # App Configuration
│   ├── database_schema.sql         # Complete Database Schema
│   ├── simple_schema.sql           # Minimal Database Schema
│   ├── DATABASE_SETUP.md           # Database Setup Guide
│   └── pom.xml                     # Maven Dependencies
└── frontend/                   # React Frontend
    ├── src/
    │   ├── components/         # React Components
    │   ├── api/               # API Service
    │   └── hooks/             # Custom Hooks
    └── package.json           # Node Dependencies
```

## 🛠️ Quick Start

### Prerequisites
- Java 17 or higher
- Node.js 16 or higher
- Maven (or use the included Maven wrapper)

### Backend Setup

1. **Navigate to backend directory:**
   ```bash
   cd backend
   ```

2. **Build the application:**
   ```bash
   # Make Maven wrapper executable (first time only)
   chmod +x mvnw
   
   # Build the project
   ./mvnw clean package
   ```

3. **Run the application:**
   ```bash
   # Using Maven
   ./mvnw spring-boot:run
   
   # Or using the JAR file
   java -jar target/student-registration-backend-0.0.1-SNAPSHOT.jar
   ```

The backend will start on `http://localhost:8080`

### Frontend Setup

1. **Navigate to frontend directory:**
   ```bash
   cd frontend
   ```

2. **Install dependencies:**
   ```bash
   npm install
   ```

3. **Start the development server:**
   ```bash
   npm run dev
   ```

The frontend will start on `http://localhost:5173`

## 📊 API Endpoints

### Student Registration API

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/register` | Register a new student |
| GET | `/api/users` | Get all students |
| DELETE | `/api/users/{id}` | Delete a student by ID |

### Sample API Usage

```bash
# Register a new student
curl -X POST http://localhost:8080/api/register \
  -H "Content-Type: application/json" \
  -d '{
    "name": "John Doe",
    "email": "john@example.com",
    "course": "Computer Science",
    "studentClass": "Final Year",
    "percentage": 85.5,
    "branch": "Computer Engineering",
    "mobileNumber": "+1234567890"
  }'

# Get all students
curl http://localhost:8080/api/users

# Delete a student
curl -X DELETE http://localhost:8080/api/users/1
```

## 🗄️ Database Integration (Optional)

### Current State
- ✅ **No database dependencies** - Application runs independently
- ✅ **In-memory storage** - Data persists during runtime
- ✅ **Ready for database integration** - Schema files provided

### Database Schema Files

The project includes comprehensive database schemas:

1. **`database_schema.sql`** - Complete schema with:
   - Users table with all fields
   - Courses and branches tables
   - User roles and permissions
   - Audit logging
   - Sample data (10 students, 6 courses, 5 branches)

2. **`simple_schema.sql`** - Minimal schema with:
   - Basic users table
   - Sample data (5 students)

3. **`DATABASE_SETUP.md`** - Complete setup guide

### MariaDB Setup (Ubuntu)

1. **Install MariaDB:**
   ```bash
   sudo apt update && sudo apt install mariadb-server -y
   ```

2. **Secure the installation:**
   ```bash
   sudo mysql_secure_installation
   ```

3. **Create database and user:**
   ```bash
   sudo mysql -u root -p
   ```
   ```sql
   CREATE DATABASE student_db;
   GRANT ALL PRIVILEGES ON student_db.* TO 'username'@'localhost' IDENTIFIED BY 'your_password';
   FLUSH PRIVILEGES;
   EXIT;
   ```

4. **Import the schema:**
   ```bash
   mysql -u username -p student_db < database_schema.sql
   ```

### Adding Database Support

When ready to add database support:

1. **Add dependencies to `pom.xml`:**
   ```xml
   <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-data-jpa</artifactId>
   </dependency>
   <dependency>
       <groupId>org.mariadb.jdbc</groupId>
       <artifactId>mariadb-java-client</artifactId>
       <scope>runtime</scope>
   </dependency>
   ```

2. **Update `application.properties`:**
   ```properties
   spring.datasource.url=jdbc:mariadb://localhost:3306/student_db
   spring.datasource.username=your_username
   spring.datasource.password=your_password
   spring.jpa.hibernate.ddl-auto=validate
   spring.jpa.show-sql=true
   ```

3. **Restore JPA annotations in User.java:**
   ```java
   @Entity
   @Data
   public class User {
       @Id
       @GeneratedValue(strategy = GenerationType.IDENTITY)
       private Long id;
       // ... other fields
   }
   ```

4. **Recreate UserRepository.java:**
   ```java
   @Repository
   public interface UserRepository extends JpaRepository<User, Long> {
   }
   ```

## 🔧 Configuration

### Environment Variables

When using database, set these environment variables:

```bash
export DB_HOST=localhost
export DB_USER=your_username
export DB_PASS=your_password
export DB_PORT=3306
export DB_NAME=student_db
```

### Application Properties

Key configuration options in `application.properties`:

```properties
# Server Configuration
server.port=8080

# Database Configuration (when enabled)
spring.datasource.url=jdbc:mariadb://localhost:3306/student_db
spring.datasource.username=${DB_USER:root}
spring.datasource.password=${DB_PASS:}
spring.jpa.hibernate.ddl-auto=validate
spring.jpa.show-sql=true
```

## 🧪 Testing

### Backend Testing
```bash
cd backend
./mvnw test
```

### API Testing
```bash
# Test registration
curl -X POST http://localhost:8080/api/register \
  -H "Content-Type: application/json" \
  -d '{"name":"Test User","email":"test@example.com","course":"CS","studentClass":"First Year","percentage":85.0,"branch":"Computer Engineering","mobileNumber":"+1234567890"}'

# Test retrieval
curl http://localhost:8080/api/users
```

## 📝 Features

### Current Features
- ✅ Student registration form
- ✅ View all registered students
- ✅ Delete students
- ✅ Responsive UI design
- ✅ Form validation
- ✅ In-memory data storage

### Planned Features (with Database)
- 🔄 Persistent data storage
- 🔄 User authentication
- 🔄 Role-based access control
- 🔄 Data export/import
- 🔄 Advanced search and filtering
- 🔄 Audit logging

## 🐛 Troubleshooting

### Common Issues

1. **Maven build fails:**
   - Ensure Java 17+ is installed
   - Check if Maven wrapper is executable: `chmod +x mvnw`

2. **Port already in use:**
   - Change port in `application.properties`: `server.port=8081`

3. **Frontend can't connect to backend:**
   - Check CORS configuration in `WebConfig.java`
   - Verify backend is running on correct port

4. **Database connection issues:**
   - Verify MariaDB service is running: `sudo systemctl status mariadb`
   - Check credentials and permissions
   - Ensure database exists: `SHOW DATABASES;`

## 📚 Documentation

- **`DATABASE_SETUP.md`** - Complete database setup guide
- **`database_schema.sql`** - Full database schema with sample data
- **`simple_schema.sql`** - Minimal database schema for quick setup

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test thoroughly
5. Submit a pull request

## 📄 License

This project is open source and available under the [MIT License](LICENSE).

## 🆘 Support

For issues and questions:
1. Check the troubleshooting section
2. Review the database setup guide
3. Check application logs for error messages
4. Verify all prerequisites are installed
