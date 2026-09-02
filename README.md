# HabitSphere – MySQL Database Management System

## 📌 Project Overview

HabitSphere is a habit tracking and management system that uses **MySQL as its primary database** for storing and managing user information, habits, habit completion records, analytics, and reminder history.

The main purpose of the database is to receive information entered through the application, store that information in properly structured tables, retrieve it when required, modify existing records when the user makes changes, and remove records when they are deleted.

The application follows a structured database approach in which related information is separated into different tables and connected using primary keys and foreign keys. This avoids unnecessary duplication of data and makes it easier to maintain, search, update, and analyse habit information.

The database is named **`habit_tracker`** and is accessed from Python using the **`mysql-connector-python`** library. The application performs database operations such as `INSERT`, `SELECT`, `UPDATE`, and `DELETE` according to the user's actions.

---

# 🗄️ Database Technology

| Component                    | Technology               |
| ---------------------------- | ------------------------ |
| Database Management System   | MySQL                    |
| Database Name                | `habit_tracker`          |
| Database Connector           | `mysql-connector-python` |
| Backend Language             | Python                   |
| Database Communication       | SQL Queries              |
| Data Format from Application | JSON                     |
| Analytics                    | Pandas / Python          |
| Database Schema              | SQL                      |

The project requirements include `mysql-connector-python`, which is used by the Python application to establish a connection with MySQL and execute database queries.

---

# 🎯 Purpose of the Database

The MySQL database is responsible for permanently storing application data.

The database handles:

* User account information
* User authentication information
* Habit information
* Habit categories
* Habit goals
* Habit target counts
* Habit start dates
* Habit status
* Daily habit completion
* Completion counts
* Completion notes
* Habit analytics
* Reminder history

Instead of keeping the application's main data only in temporary Python variables, the information is stored in MySQL so that it can be retrieved whenever the user returns to the application.

---

# 🏗️ Database Architecture

The database follows a relational structure.

```text
                         ┌──────────────────┐
                         │      USERS       │
                         │──────────────────│
                         │ user_id (PK)     │
                         │ full_name        │
                         │ email            │
                         │ password_hash    │
                         └────────┬─────────┘
                                  │
                                  │ 1 : Many
                                  ▼
                         ┌──────────────────┐
                         │      HABITS      │
                         │──────────────────│
                         │ habit_id (PK)    │
                         │ user_id (FK)     │
                         │ habit_name       │
                         │ category         │
                         │ goal_type        │
                         │ target_count     │
                         │ start_date       │
                         │ status           │
                         └────────┬─────────┘
                                  │
                 ┌────────────────┼────────────────┐
                 │                │                │
                 ▼                ▼                ▼
       ┌────────────────┐ ┌────────────────┐ ┌─────────────────┐
       │ HABIT_         │ │ HABIT_         │ │ HABIT_          │
       │ COMPLETION     │ │ ANALYTICS      │ │ REMINDERS       │
       │────────────────│ │────────────────│ │─────────────────│
       │ completion_id  │ │ analytics_id   │ │ reminder_id     │
       │ habit_id (FK)  │ │ habit_id (FK)  │ │ habit_id (FK)   │
       │ completion_date│ │ performance    │ │ user_id (FK)    │
       │ completion_count││ metrics        │ │ reminder_type   │
       │ completed      │ │                │ │ reminder_date   │
       │ notes          │ │                │ │                 │
       └────────────────┘ └────────────────┘ └─────────────────┘
```

The project documentation identifies `USERS`, `HABITS`, `HABIT_COMPLETION`, and `HABIT_ANALYTICS` as the core database tables.

The reminder service also works with a `HABIT_REMINDERS` table to prevent duplicate reminders and record successfully sent reminders.

---

# 📋 Main Database Tables

## 1. USERS

The `USERS` table stores information about people registered in HabitSphere.

### Main purpose

It identifies each user and stores the information required for authentication and for associating habits with the correct user.

### Typical information stored

```text
user_id
full_name
email
password_hash
```

### Relationship

One user can have multiple habits.

```text
USERS
  │
  └───< HABITS
```

The `user_id` acts as the primary identifier for a user and is referenced by other tables.

---

# 2. HABITS

The `HABITS` table stores the actual habits created by users.

### Information managed

```text
habit_id
user_id
habit_name
category
goal_type
target_count
start_date
status
```

A habit can represent activities such as:

```text
Reading
Exercise
Meditation
Drinking Water
Coding
Walking
Studying
```

The `goal_type` determines whether the habit is:

```text
daily
weekly
monthly
```

The `target_count` stores how many times the user wants to complete the habit during the applicable period.

For example:

```text
Habit Name: Reading
Goal Type: daily
Target Count: 2
```

This means the user wants to complete the reading habit two times per day.

---

# 3. HABIT_COMPLETION

The `HABIT_COMPLETION` table stores the user's actual progress.

This is one of the most important tables in the system because it records what the user has actually completed.

### Information stored includes

```text
completion_id
habit_id
completion_date
completion_count
completed
notes
```

For example:

```text
Habit: Reading
Date: 2026-09-02
Completion Count: 1
Completed: Yes
Notes: Read for 30 minutes
```

The application can then use these records to calculate:

* Completion percentage
* Current streak
* Longest streak
* Missed days
* Weekly progress
* Monthly progress
* Consistency
* Success rate

---

# 4. HABIT_ANALYTICS

The `HABIT_ANALYTICS` table is used for habit performance information.

Analytics can be derived from the completion records stored in `HABIT_COMPLETION`.

The system can use this information to determine how consistently a user follows a habit.

Examples of calculated information include:

```text
Completion Percentage
Current Streak
Longest Streak
Consistency Score
Success Rate
Missed Days
```

The project documentation identifies `HABIT_ANALYTICS` as a database table associated with habits.

---

# 5. HABIT_REMINDERS

The reminder functionality maintains reminder history in the `HABIT_REMINDERS` table.

The table is used to determine whether a reminder for a particular habit and date has already been sent.

Relevant information includes:

```text
reminder_id
habit_id
user_id
reminder_type
reminder_date
```

The reminder service checks this information before sending another reminder.

For example:

```text
Habit ID: 15
Reminder Type: daily
Reminder Date: 2026-09-02
```

If such a record already exists, the application skips sending the same reminder again.

The reminder service records the reminder only after successful email delivery.

---

# 🔗 Database Relationships

The database uses relationships between tables.

## USERS → HABITS

```text
One User
   ↓
Many Habits
```

A single user can create multiple habits.

Example:

```text
User: Dinesh

Habits:
    Reading
    Exercise
    Coding
    Meditation
```

Each habit stores the user's `user_id`.

---

## HABITS → HABIT_COMPLETION

```text
One Habit
   ↓
Many Completion Records
```

For example:

```text
Reading

2026-08-30 → Completed
2026-08-31 → Completed
2026-09-01 → Missed
2026-09-02 → Completed
```

Each completion record is associated with the corresponding `habit_id`.

---

## HABITS → HABIT_ANALYTICS

Analytics are associated with the corresponding habit.

```text
Habit
  ↓
Analytics
  ├── Completion %
  ├── Current Streak
  ├── Best Streak
  └── Consistency
```

---

## HABITS → HABIT_REMINDERS

A habit can have multiple reminder-history records.

```text
Habit
  ↓
Reminder History
  ├── Daily
  ├── Weekly
  └── Monthly
```

---

# 🔄 CRUD Operations

The database performs the four fundamental CRUD operations.

```text
C → Create
R → Read
U → Update
D → Delete
```

---

# ➕ CREATE – INSERT DATA

When a user creates a new account, a record is inserted into `USERS`.

When a user creates a new habit, a record is inserted into `HABITS`.

Conceptually:

```sql
INSERT INTO HABITS
(
    user_id,
    habit_name,
    category,
    goal_type,
    target_count,
    start_date,
    status
)
VALUES
(
    ?,
    ?,
    ?,
    ?,
    ?,
    ?,
    ?
);
```

The actual values are supplied by the Python application.

For example:

```text
Habit Name   → Reading
Category     → Education
Goal Type    → daily
Target Count → 1
```

The application sends these values to MySQL.

---

# 🔍 READ – SELECT DATA

Whenever the application needs information, it retrieves records from MySQL using `SELECT`.

Examples:

### Get user's habits

```sql
SELECT *
FROM HABITS
WHERE user_id = %s;
```

### Get a specific habit

```sql
SELECT *
FROM HABITS
WHERE habit_id = %s
AND user_id = %s;
```

### Get completion records

```sql
SELECT *
FROM HABIT_COMPLETION
WHERE habit_id = %s;
```

The application uses retrieved records to display current habits, progress, analytics, reports, and dashboard information.

---

# ✏️ UPDATE – MODIFY DATA

When a user edits a habit, the existing MySQL record is updated rather than creating another habit.

For example:

```sql
UPDATE HABITS
SET
    habit_name = %s,
    category = %s,
    goal_type = %s,
    target_count = %s
WHERE habit_id = %s
AND user_id = %s;
```

This allows the user to change information such as:

```text
Habit Name
Category
Goal Type
Target Count
Status
```

The database therefore maintains the latest version of the habit.

---

# 🗑️ DELETE – REMOVE DATA

When the user deletes a habit, the application removes the corresponding database record.

Conceptually:

```sql
DELETE FROM HABITS
WHERE habit_id = %s
AND user_id = %s;
```

Because related records are connected through foreign keys and cascade rules in the approved schema, deleting a parent habit can also remove its associated child records.

This prevents unnecessary orphaned records.

---

# ✅ Recording Habit Completion

When the user marks a habit as completed, the application does not simply change a screen value.

The completion information is stored in MySQL.

The application records information such as:

```text
Habit ID
Completion Date
Completion Count
Completed Status
Notes
```

Conceptually:

```sql
INSERT INTO HABIT_COMPLETION
(
    habit_id,
    completion_date,
    completion_count,
    completed,
    notes
)
VALUES
(
    %s,
    %s,
    %s,
    %s,
    %s
);
```

After the completion is saved, the analytics can be recalculated.

This creates the following flow:

```text
User clicks Complete
        ↓
Python receives request
        ↓
Validate input
        ↓
INSERT / UPDATE MySQL record
        ↓
Completion stored
        ↓
Analytics recalculated
        ↓
Updated progress displayed
```

The application code explicitly records a completion and then recalculates analytics.

---

# 📊 How Analytics Use MySQL Data

The database does not need to store every possible statistic manually.

The application can retrieve completion records and calculate meaningful statistics from them.

For example:

```text
Stored data:

Day 1 → Completed
Day 2 → Completed
Day 3 → Completed
Day 4 → Missed
Day 5 → Completed
```

The application can calculate:

```text
Completion Rate
Current Streak
Longest Streak
Missed Days
Consistency
```

This means the stored completion data becomes the foundation for the analytics system.

---

# 📈 Completion Percentage

The application compares the user's actual completion count with the expected target.

Conceptually:

```text
Completion Percentage =
Actual Completions / Expected Completions × 100
```

Example:

```text
Expected = 10
Completed = 8

Completion Percentage = 80%
```

The result can then be displayed in the dashboard and analytics section.

---

# 🔥 Streak Calculation

The completion records are also used to calculate streaks.

For example:

```text
Monday     ✓
Tuesday    ✓
Wednesday  ✓
Thursday   ✓
Friday     ✗
```

The system can determine:

```text
Current Streak = 4 days
```

Historical completion dates are therefore important database information.

---

# 📅 Weekly Data

For weekly habits, the application retrieves completion records between the beginning of the current week and the selected date.

The reminder service calculates the week beginning on Monday and sums the completion counts during that period.

Conceptually:

```text
Monday
   ↓
Tuesday
   ↓
Wednesday
   ↓
Thursday
   ↓
Friday
   ↓
Saturday
   ↓
Sunday
```

The collected data can be compared against the weekly target.

---

# 🗓️ Monthly Data

For monthly habits, the system calculates progress from the first day of the month through the selected date.

For example:

```text
September 1
     ↓
September 2
     ↓
September 3
     ↓
...
     ↓
Current Date
```

The completion records within that range are summed and compared with the monthly target.

The reminder service uses the first day of the current month as the beginning of the monthly calculation period.

---

# 📧 Database and Reminder System

The reminder system also depends on MySQL.

Before sending a reminder, the application checks:

1. Which habits are active?
2. Which habits are daily/weekly/monthly?
3. Has the habit reached its target?
4. Has a reminder already been sent?
5. Which user owns the habit?
6. What is the user's email address?

For example, the reminder query joins:

```text
HABITS
   +
USERS
   +
HABIT_COMPLETION
   +
HABIT_REMINDERS
```

This allows the application to determine which habits still require attention.

The reminder service specifically checks for an existing reminder record and skips the reminder if one has already been recorded.

---

# 🔐 Data Integrity

The database uses relational constraints to maintain consistent data.

Important relationships include:

```text
USERS.user_id
       ↓
HABITS.user_id
```

and:

```text
HABITS.habit_id
       ↓
HABIT_COMPLETION.habit_id
```

and:

```text
HABITS.habit_id
       ↓
HABIT_ANALYTICS.habit_id
```

and:

```text
HABITS.habit_id
       ↓
HABIT_REMINDERS.habit_id
```

Foreign keys ensure that related records refer to valid parent records.

The project documentation states that the schema uses foreign keys and cascade deletion rules for the related records.

---

# 🔒 SQL Security

The application communicates with MySQL using the Python MySQL connector.

Database values are passed as parameters rather than directly concatenating user input into SQL statements.

For example:

```python
cursor.execute(
    """
    SELECT *
    FROM HABITS
    WHERE habit_id = %s
    AND user_id = %s
    """,
    (habit_id, user_id)
)
```

This parameterized-query approach helps protect the database against SQL injection.

The project documentation specifically identifies parameterized queries through `mysql-connector-python` as part of its security approach.

---

# 🔄 Complete Database Data Flow

The overall database process can be represented as:

```text
                 USER INPUT
                     │
                     ▼
             Python Application
                     │
                     ▼
              Input Validation
                     │
                     ▼
              SQL Operation
                     │
          ┌──────────┼──────────┐
          │          │          │
          ▼          ▼          ▼
        INSERT     SELECT     UPDATE
          │          │          │
          └──────────┼──────────┘
                     │
                     ▼
                 MySQL
                     │
                     ▼
              Stored Records
                     │
                     ▼
            Analytics / Reports
                     │
                     ▼
               User Interface
```

For deletion:

```text
User deletes habit
       ↓
Python receives request
       ↓
Validate user + habit ID
       ↓
DELETE from HABITS
       ↓
Related records handled by
foreign-key cascade rules
       ↓
Updated database
```

---

# 🧩 Database Operation Examples

## Creating a user

```text
Registration Form
       ↓
User Name
Email
Password
       ↓
Python validation
       ↓
Password hashing
       ↓
INSERT INTO USERS
       ↓
User stored in MySQL
```

## Creating a habit

```text
Create Habit Form
       ↓
Habit information
       ↓
Validation
       ↓
INSERT INTO HABITS
       ↓
Habit stored
```

## Completing a habit

```text
Complete button
       ↓
Completion information
       ↓
INSERT/UPDATE HABIT_COMPLETION
       ↓
Analytics calculation
       ↓
Updated progress
```

## Editing a habit

```text
Edit Habit
       ↓
New information
       ↓
UPDATE HABITS
       ↓
Updated habit displayed
```

## Deleting a habit

```text
Delete Habit
       ↓
DELETE FROM HABITS
       ↓
Related records removed according
to database constraints
```

---

# 🛠️ Database Setup

## Requirements

Install:

* MySQL Server
* Python
* `mysql-connector-python`

The project's `requirements.txt` includes:

```text
mysql-connector-python==9.7.0
```

as well as Pandas and Matplotlib for analytics and visualization.

---

# 🗃️ Create the Database

Start your MySQL server and open MySQL Workbench or the MySQL command-line client.

Create the database:

```sql
CREATE DATABASE habit_tracker;
```

Select it:

```sql
USE habit_tracker;
```

Then execute the project's database schema.

The schema is responsible for creating the required tables and relationships.

---

# ⚙️ Database Configuration

The application reads its configuration through its settings/environment configuration.

The configuration contains information such as:

```text
MySQL Host
MySQL Port
MySQL Username
MySQL Password
Database Name
```

A typical local MySQL configuration is:

```text
Host: localhost
Port: 3306
Database: habit_tracker
```

The exact username and password depend on the local MySQL installation.

Database credentials should not be exposed publicly or committed to a public repository.

---

# 🔌 Python–MySQL Connection

The Python application uses:

```text
mysql.connector
```

to connect to MySQL.

The basic flow is:

```text
Python
   ↓
mysql-connector-python
   ↓
MySQL Server
   ↓
habit_tracker database
```

The application opens a connection, creates a cursor, executes SQL, processes the result, and closes the database resources.

---

# 📥 Data Entered Into the Database

The database receives information from different application operations.

### User information

```text
Full Name
Email
Password Hash
```

### Habit information

```text
Habit Name
Category
Goal Type
Target Count
Start Date
Status
User ID
```

### Completion information

```text
Habit ID
Completion Date
Completion Count
Completed Status
Notes
```

### Reminder information

```text
Habit ID
User ID
Reminder Type
Reminder Date
```

---

# 📤 Data Retrieved From the Database

The application retrieves information for:

* Dashboard
* Habit list
* Habit details
* Daily tracker
* Analytics
* Weekly progress
* Monthly progress
* Reports
* Charts
* Reminder processing
* User-specific data

This means the database acts as the central persistent storage layer of the application.

---

# 🧹 Data Modification and Deletion

The database is continuously updated as the user interacts with the application.

For example:

```text
Create Habit
    ↓
INSERT

Edit Habit
    ↓
UPDATE

Complete Habit
    ↓
INSERT/UPDATE completion record

Delete Habit
    ↓
DELETE

View Habit
    ↓
SELECT
```

Therefore, the database is not simply used for storing information once. It continuously reflects the current state of the user's habits and progress.

---

# 📊 Why MySQL Was Used

MySQL is suitable for HabitSphere because the project contains structured and related data.

For example:

```text
One user
   ↓
Many habits
   ↓
Many completion records
```

A relational database makes these relationships easy to manage.

MySQL also provides:

* Structured tables
* Primary keys
* Foreign keys
* Constraints
* Transactions
* Querying
* Filtering
* Aggregation
* Reliable persistent storage
* Relationships between entities

These capabilities make MySQL suitable for storing habit tracking data.

---

# 🧪 Example Database Scenario

Suppose a user creates:

```text
Habit Name: Reading
Category: Education
Goal: Daily
Target: 1
```

The application stores the habit in `HABITS`.

Then the user completes it on:

```text
2026-09-02
```

A completion record is stored in `HABIT_COMPLETION`.

Later, when the dashboard is opened:

```text
MySQL
  ↓
Retrieve habit
  ↓
Retrieve completion records
  ↓
Calculate progress
  ↓
Calculate streak
  ↓
Calculate consistency
  ↓
Display result
```

If the user deletes the habit:

```text
DELETE HABITS record
        ↓
Related records handled
by database relationships
        ↓
Habit no longer appears
```

This demonstrates the complete database lifecycle.

---

# 🔁 Database Lifecycle

The overall lifecycle of data in HabitSphere is:

```text
CREATE
  ↓
STORE
  ↓
READ
  ↓
USE FOR ANALYTICS
  ↓
UPDATE
  ↓
READ AGAIN
  ↓
DELETE WHEN REQUIRED
```

The database therefore acts as the permanent source of truth for the application's main habit-related information.

---

# 📁 Database-Related Project Components

The database functionality is primarily connected to:

```text
app.py
    ↓
API requests
    ↓
habit_tracker.py
    ↓
DatabaseManager
    ↓
MySQL
```

The project also contains:

```text
database/
    └── schema.sql
```

which contains the database schema.

The application uses the database layer for authentication, habits, tracking, analytics, reports, and dashboard information.

---

# ⚠️ Important Note About the Earlier JSON Version

The project also contains an earlier terminal-based `habitsphere.py` implementation that uses:

```text
habits_data.json
```

instead of MySQL.

That version stores habits in JSON and contains operations such as:

```text
load()
save()
add_habit()
delete_habit()
complete_habit()
```

The JSON file contains habit information and completion dates.

However, **the current web application is MySQL-based**. Therefore, the MySQL database described in this README should be considered the database architecture of the main HabitSphere application, while the JSON implementation is an earlier prototype.

---

# 🚀 Running the Database-Connected Application

After installing Python dependencies and starting MySQL:

```powershell
python app.py
```

The Python application starts the HTTP server and communicates with the MySQL database when API requests require database operations.

The application reports its local server address when it starts.

---

# 📝 Summary

HabitSphere uses MySQL as the central database for managing structured habit-tracking information. User accounts are stored in `USERS`, habits are stored in `HABITS`, progress is stored in `HABIT_COMPLETION`, analytics are associated with `HABIT_ANALYTICS`, and reminder history is maintained through `HABIT_REMINDERS`.

The application performs the complete CRUD lifecycle:

```text
INSERT → Create data
SELECT → Read data
UPDATE → Modify data
DELETE → Remove data
```

User input is validated by the Python application before being sent to MySQL through parameterized queries. Relationships between tables are maintained through primary keys and foreign keys.

The stored data is then used to calculate habit progress, streaks, consistency, reports, charts, and reminder requirements.

In simple terms:

```text
User enters data
       ↓
Python processes the data
       ↓
SQL query is executed
       ↓
MySQL stores/updates/deletes the data
       ↓
Application retrieves the data
       ↓
Analytics and reports are generated
       ↓
Results are shown to the user
```

Therefore, MySQL forms the **core persistent data-management layer of HabitSphere**, ensuring that user information, habits, completion records, analytics, and reminder history are organized, connected, and available whenever the application needs them.

---

# 👨‍💻 Author

**P. Dinesh Ajay**

**Project:** HabitSphere
**Database:** MySQL
**Backend:** Python
**Database Connector:** MySQL Connector/Python
