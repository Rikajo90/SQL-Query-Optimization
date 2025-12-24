# Case Study #2: Missing Index on WHERE Clause

## 🔴 Problem Statement

**Scenario:** User search by email taking too long  
**Environment:** SQL Server, User management system

### Original Query:
```sql
SELECT user_id, full_name, email, phone, last_login
FROM Users
WHERE email = 'customer@example.com';
```

### ⚠️ Performance (BEFORE):
- **Execution Time:** 2.8 seconds
- **Rows Scanned:** 2,500,000 (full table scan)
- **Logical Reads:** 35,000 pages

---

## 🔍 Analysis

### Problem:
- No index on `email` column
- Full table scan on 2.5M user records
- Happens frequently (login/registration operations)

### Execution Plan:
```
Table Scan on Users (cost=3500.00, rows=2,500,000)
  Filter: email = 'customer@example.com'
```

---

## ✅ Solution

### Create Index on Email Column:
```sql
CREATE UNIQUE INDEX idx_users_email 
ON Users(email);
```

**Why UNIQUE?**
- Email should be unique per user
- Faster lookups
- Enforces business rule

---

## 🎯 Results

### ✅ Performance (AFTER):

- **Execution Time:** 0.003 seconds ⚡
- **Rows Scanned:** 1 (index seek)
- **Logical Reads:** 3 pages

### 📊 Improvement:

| Metric | Before | After | Improvement |
|--------|--------|-------|-------------|
| Execution Time | 2.8s | 0.003s | **99.9%** ↓ |
| Logical Reads | 35K | 3 | **99.99%** ↓ |

### Speed: **933x faster!** 🚀

---

## 💡 Best Practice

**Always index columns used in:**
- WHERE clauses (especially with =)
- JOIN conditions
- ORDER BY clauses
- Unique constraints (email, username, SSN)

---

**Optimized by:** Rika Afriyani  
**Date:** December 2024
