# Case Study #3: N+1 Query Problem

## 🔴 Problem Statement

**Scenario:** Product listing page loading very slow  
**Issue:** Executing 1 query + N additional queries in a loop

### Problematic Code Pattern:
```sql
-- Query 1: Get all products
SELECT product_id, product_name, category_id
FROM Products
WHERE is_active = 1;

-- Then in application loop (N times):
-- Query 2, 3, 4... N+1:
SELECT category_name 
FROM Categories 
WHERE category_id = ?;
```

**Result:** If 100 products → 101 database calls!

### ⚠️ Performance (BEFORE):
- **Total Execution Time:** 8.5 seconds
- **Number of Queries:** 101 queries
- **Database Round Trips:** 101

---

## 🔍 Problem Analysis

**The N+1 Problem:**
1. One query to fetch main data (Products)
2. N queries to fetch related data (Categories)
3. Total = 1 + N queries

**Impact:**
- Multiple database round trips
- Network latency multiplied
- Increased database load
- Poor application performance

---

## ✅ Solution: Use JOIN

### Single Optimized Query:
```sql
SELECT 
    p.product_id,
    p.product_name,
    p.price,
    c.category_name,
    c.category_id
FROM Products p
INNER JOIN Categories c ON p.category_id = c.category_id
WHERE p.is_active = 1
ORDER BY p.product_name;
```

**Result:** 1 query instead of 101!

---

## 🎯 Results

### ✅ Performance (AFTER):

- **Total Execution Time:** 0.15 seconds ⚡
- **Number of Queries:** 1 query
- **Database Round Trips:** 1

### 📊 Improvement:

| Metric | Before | After | Improvement |
|--------|--------|-------|-------------|
| Execution Time | 8.5s | 0.15s | **98.2%** ↓ |
| DB Queries | 101 | 1 | **99%** ↓ |

### Speed: **56x faster!** 🚀

---

## 💡 How to Avoid N+1

**1. Use JOINs:**
```sql
SELECT ... FROM Products p
JOIN Categories c ON p.category_id = c.category_id;
```

**2. Use Subqueries (if needed):**
```sql
SELECT 
    p.*,
    (SELECT category_name FROM Categories 
     WHERE category_id = p.category_id) as category_name
FROM Products p;
```

**3. In ORM (Entity Framework, Hibernate):**
```csharp
// C# Entity Framework - Eager Loading
var products = context.Products
    .Include(p => p.Category)  // Prevent N+1
    .ToList();
```

---

## 🚨 Detection Tips

**How to spot N+1 in your application:**

1. Enable SQL query logging
2. Look for repeated similar queries
3. Use database profiler
4. Monitor query count per page load

**Warning signs:**
- Query count = Number of records + 1
- Same query pattern with different parameters
- Performance degrades with more data

---

**Optimized by:** Rika Afriyani  
**Date:** December 2024
