# PFAMS — Penalty / Fine Management System

A Java desktop application for managing penalties and fines — with both **GUI (Swing)** and **Terminal (CLI)** interfaces. Built using **layered architecture** with **MySQL** as the database.

---

## Features

### Three User Roles

| Role | Capabilities |
|------|-------------|
| **Admin** | System report, view all fines, pending fines, overdue fines, total fine per user, lookup user total fine, search fines, audit log, search history, view all users |
| **Authority** | Issue fines/penalties to users, view fines issued by authority, view all users, view violation types, search fines, system report |
| **User** | View profile, view fines, pending fines, total fine, pay fines, search fines, search history |

### Key Functionalities
- **User Signup & Login** — Register new accounts, login with email/password
- **Fine Issuance** — Authority can issue fines with violation type, location, amount, due date, and daily penalty
- **Fine Payment** — Users can pay fines via UPI, Card, NetBanking, or Cash
- **Search** — Search fines by violation name, location, or user name (logged in SearchRequest table)
- **Audit Log** — All payments, fine issuances, and signups are auto-tracked by database triggers
- **Dual Interface** — Full feature parity between GUI and Terminal modes

### SQL Features Used
- **6 Triggers** — Auto-update fine status on payment, update total fine, track fine history, audit logging for payments/fines/signups
- **3 Views** — PendingFines, TotalFinePerUser, OverdueFines
- **1 Stored Procedure** — GetUserTotalFine
- **Indexes** — On email, fine status, due date
- **CHECK constraints, ENUM types, Foreign Keys, CASCADE deletes**

---

## Tech Stack

| Component | Technology |
|-----------|-----------|
| Language | Java 8+ |
| GUI | Java Swing (Nimbus L&F) |
| Database | MySQL |
| Connectivity | JDBC (MySQL Connector/J) |
| Architecture | Layered (Model → DAO → Service → Controller → GUI/CLI) |

---

## Project Structure

```
PFAMS/
├── pfams_setup.sql          # Database setup script (tables, data, triggers, views, procedure)
├── run.bat                  # Build & run script (Windows)
├── lib/
│   └── mysql-connector-j-9.6.0.jar   # MySQL JDBC driver
├── src/
│   ├── model/               # Entity classes (User, Account, Fine, Violation, etc.)
│   ├── util/                # Database connection utility (DBConnection.java)
│   ├── dao/                 # Data Access Objects (SQL queries)
│   ├── service/             # Business logic layer
│   ├── controller/          # Controllers (bridge between UI and service)
│   ├── gui/                 # Swing GUI screens
│   └── main/                # Entry point (Main.java, TerminalApp.java)
└── out/                     # Compiled .class files (auto-generated)
```

---

## ⚠️ Important Setup Requirements

Before running the application, you **MUST** configure your database connection and set up the MySQL database. Follow these steps carefully:

### Step 1: Clone the Repository

```bash
git clone https://github.com/Krish-Korat/PFAMS.git
cd PFAMS
```

### Step 2: Setup the Database

1. Open MySQL terminal or MySQL Workbench
2. Run the setup script:

```sql
source /path/to/PFAMS/pfams_setup.sql;
```

> This creates the `PFAMS` database with all tables, sample data, triggers, views, and stored procedure.

### Step 3: Configure Database Connection (CRITICAL)

Open `src/util/DBConnection.java` and update the credentials to match your local MySQL configuration:

```java
private static final String URL = "jdbc:mysql://localhost:3306/<YOUR_DATABASE_NAME>"; // Usually PFAMS
private static final String USER = "<YOUR_MYSQL_USERNAME>"; // Usually root
private static final String PASSWORD = "<YOUR_MYSQL_PASSWORD>";  // Change this to your password
```

### Step 4: Compile and Run the Application

You can use the provided batch script to run the application automatically on Windows, or compile the Java source files manually.

**Windows (Automated):**
You can simply double-click the `run.bat` file in the project root directory, or run it from the command line:
```cmd
run.bat
```

**Windows (Manual):**
```cmd
# 1. Create the output directory (if it doesn't exist)
mkdir out

# 2. Compile all source files into the out directory
javac -d out -cp "lib\mysql-connector-j-9.6.0.jar" src\model\*.java src\util\*.java src\dao\*.java src\service\*.java src\controller\*.java src\gui\*.java src\main\*.java

# 3. Run the application
java -cp "out;lib\mysql-connector-j-9.6.0.jar" main.Main
```

**Linux / macOS:**
```bash
# 1. Create the output directory
mkdir out

# 2. Compile all source files
javac -d out -cp "lib/mysql-connector-j-9.6.0.jar" src/model/*.java src/util/*.java src/dao/*.java src/service/*.java src/controller/*.java src/gui/*.java src/main/*.java

# 3. Run the application (Note the colon ':' instead of semicolon ';')
java -cp "out:lib/mysql-connector-j-9.6.0.jar" main.Main
```

### Step 5: Choose Mode

```
==========================================
 PFAMS - Penalty/Fine Management System
==========================================
 Select mode:
   1. GUI Mode (Graphical Interface)
   2. Terminal Mode (CLI)
   3. Exit
==========================================
```

---

## Default Login Credentials

| Role | Email | Password |
|------|-------|----------|
| Admin | `admin@pfams.com` | `admin` |
| Authority | `officer@pfams.com` | `officer` |
| User | `rahul@gmail.com` | `pass1` |
| User | `amit@gmail.com` | `pass2` |
| User | `sneha@gmail.com` | `pass3` |
| ... | ... | `pass4` to `pass10` |

---

## Database Schema (ER Diagram)

```
Users (1) ──── (1) Account ──── (1) Role
                    │
         ┌──────────┼──────────┐
         ▼          ▼          ▼
   SearchRequest  Violation   (TotalFine)
                    │
                    ▼
                   Fine ──── Authority
                    │
              ┌─────┼─────┐
              ▼     ▼     ▼
          Penalty Payment FineHistory
                    │
                    ▼
                 AuditLog (via triggers)
```

### Tables
1. **Role** — Admin, User, Authority
2. **Users** — User personal information
3. **Account** — Login credentials, role, total fine
4. **SearchRequest** — Logged searches
5. **ViolationType** — Types of violations with base fines
6. **Violation** — Violation records linked to accounts
7. **Authority** — Issuing authorities
8. **Fine** — Fine records with status (Paid/Unpaid)
9. **Penalty** — Daily penalty for overdue fines
10. **Payment** — Payment records
11. **AuditLog** — System activity trail (auto-populated by triggers)
12. **FineHistory** — Tracks fine amount changes

---

## License

This project is for educational purposes.
