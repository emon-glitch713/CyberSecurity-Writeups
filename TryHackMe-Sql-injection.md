# TryHackMe: SQL Injection — Task 6 (Blind SQLi - Authentication Bypass)

**Author:**   github.com/emon-glitch713
**Date:** May 2026  
**Category:** Web Application Penetration Testing  
**Room Link:** [TryHackMe - SQL Injection](https://tryhackme.com)

---

## 📋 Executive Summary
This write-up documents the successful exploitation of a **Blind SQL Injection (SQLi)** vulnerability within Task 6 (Level Two) of the TryHackMe SQL Injection room. By manipulating the backend database logic through an unsanitized input field, we bypassed the authentication mechanism and retrieved the hidden flag without possessing valid user credentials.

---

## 🛠️ Tools Used
* **Web Browser:** Mozilla Firefox / Google Chrome (Used for interacting with the web application and testing inputs).
* **Manual Code Injection:** No automated tools (like SQLmap) were required, ensuring a precise and stealthy manual exploitation approach.

---

## 🔍 Vulnerability Analysis & Logic

### 1. The Vulnerable Code
During the source/behavioral analysis of Level Two, the backend database query was identified as:
```sql
SELECT * FROM users WHERE username='%username%' AND password='%password%' LIMIT 1;
```
* **The Flaw:** The application directly concatenates user-supplied input from the login form (`%username%` and `%password%`) into the SQL command without proper sanitization or parameterization.

### 2. Attack Vector Logic
To bypass authentication, we need to inject a payload that forces the entire `WHERE` clause to evaluate to `TRUE`. 

* **Payload Selected:** `' OR 1=1;--`
* **Target Field:** Password Field

When injected into the password field, the backend query breaks its original structure and compiles as follows:
```sql
SELECT * FROM users WHERE username='' AND password='' OR 1=1;
```
* **Logic Breakdown:** The database evaluates `username=''` and `password=''` as false. However, due to the `OR` operator and the universally true condition `1=1`, the final result of the clause becomes **TRUE**. The trailing characters are handled safely, granting administrative or first-row user access.

---

## 🚀 Step-by-Step Exploitation (Reproduction Steps)

1. **Access the Lab:** Navigate to Task 6 (Blind SQLi - Authentication Bypass) and launch the Level Two practical environment.
2. **Input Manipulation:** 
   * Leave the **Username** field blank (or input any dummy data).
   * Enter the malicious payload `' OR 1=1;--` into the **Password** field.
3. **Execution:** Click the **Login** or **Submit** button.
4. **Flag Capture:** The application accepts the authentication bypass, updates the state to successful, and displays the hidden flag on the screen.

### 🏁 Captured Flag
```text
THM{SQL_INJECTION_9581}
```

---

## 🛡️ Remediation & Mitigation Strategies
Direct string concatenation in SQL queries should be strictly avoided. To secure this application against SQL Injection, developers must implement **Parameterized Queries (Prepared Statements)**.

### Secure Code Example (PHP/PDO):
```php
\$stmt = \(pdo->prepare('SELECT * FROM users WHERE username = :username AND password = :password LIMIT 1');\)stmt->execute([
    'username' => \$username,
    'password' => \$password
]);
\(user =\)stmt->fetch();
```
*By using prepared statements, the database treats the user input strictly as data, never as executable SQL code.*
