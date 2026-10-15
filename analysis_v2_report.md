# EduTrack Data Audit — Normalized Schema (v2)

## INNER JOIN

### 1. Every enrollment with student, course, and completion

```sql
SELECT s.name AS student, c.title AS course, e.completion_percentage
FROM enrollments e
JOIN students s ON s.id = e.student_id
JOIN courses c ON c.id = e.course_id
ORDER BY e.id;
```

Result: 16 enrollments.

| Student | Course | Completion |
|---|---|---:|
| Emily Watson | Intro to Python | 85% |
| Emily Watson | Web Design Basics | 60% |
| Klaus Weber | Intro to Python | 92% |
| Klaus Weber | Data Analysis with SQL | 78% |
| Lucia Fernandes | Web Design Basics | 5% |
| Lucia Fernandes | Digital Marketing 101 | 3% |
| Marco Rossi | Advanced Python | 95% |
| Marco Rossi | Intro to Python | 88% |
| Yuki Nakamura | Data Analysis with SQL | 45% |
| Yuki Nakamura | UI/UX Fundamentals | 0% |
| Pierre Dubois | UI/UX Fundamentals | 0% |
| Priya Sharma | Digital Marketing 101 | 70% |
| Priya Sharma | Intro to Python | 55% |
| Pierre Dubois | Data Analysis with SQL | 20% |
| Emily Watson | Advanced Python | 40% |
| Lucia Fernandes | Advanced Python | 0% |

### 2. Students and courses passed

```sql
SELECT s.name AS student, s.email, c.title AS course
FROM enrollments e
JOIN students s ON s.id = e.student_id
JOIN courses c ON c.id = e.course_id
WHERE e.passed = TRUE
ORDER BY s.name, c.title;
```

Result: 6 passed enrollments across 4 students.

| Student | Email | Course passed |
|---|---|---|
| Emily Watson | emily.watson@student.edutrack.com | Intro to Python |
| Klaus Weber | klaus.weber@student.edutrack.com | Data Analysis with SQL |
| Klaus Weber | klaus.weber@student.edutrack.com | Intro to Python |
| Marco Rossi | marco.rossi@student.edutrack.com | Advanced Python |
| Marco Rossi | marco.rossi@student.edutrack.com | Intro to Python |
| Priya Sharma | priya.sharma@student.edutrack.com | Digital Marketing 101 |

### 3. Average completion per instructor

```sql
SELECT c.instructor_name AS instructor,
	   ROUND(AVG(e.completion_percentage), 2) AS average_completion
FROM enrollments e
JOIN courses c ON c.id = e.course_id
GROUP BY c.instructor_name
ORDER BY average_completion DESC;
```

| Instructor | Average completion |
|---|---:|
| Marta López | 66.14% |
| Carlos Vega | 40.00% |
| Lucia Prades | 36.50% |
| Pending assignment | 0.00% |

## LEFT JOIN: No Enrollments

### 4. Students with no enrollments

```sql
SELECT s.id, s.name, s.email
FROM students s
LEFT JOIN enrollments e ON e.student_id = s.id
WHERE e.id IS NULL
ORDER BY s.id;
```

| ID | Student | Email |
|---:|---|---|
| 8 | Giulia Romano | giulia.romano@student.edutrack.com |

### 5. Courses with no enrollments

```sql
SELECT c.id, c.title
FROM courses c
LEFT JOIN enrollments e ON e.course_id = c.id
WHERE e.id IS NULL
ORDER BY c.id;
```

| ID | Course |
|---:|---|
| 7 | Email Campaigns |

## Aggregation Across Tables

### 6. Students enrolled in more than one course

```sql
SELECT s.name AS student, COUNT(DISTINCT e.course_id) AS course_count
FROM students s
JOIN enrollments e ON e.student_id = s.id
GROUP BY s.id, s.name
HAVING COUNT(DISTINCT e.course_id) > 1
ORDER BY s.name;
```

Result: 7 students.

| Student | Courses |
|---|---:|
| Emily Watson | 3 |
| Klaus Weber | 2 |
| Lucia Fernandes | 3 |
| Marco Rossi | 2 |
| Pierre Dubois | 2 |
| Priya Sharma | 2 |
| Yuki Nakamura | 2 |

### 7. Revenue per category at current course prices

```sql
SELECT c.category, ROUND(SUM(c.monthly_fee), 2) AS total_revenue
FROM courses c
JOIN enrollments e ON e.course_id = c.id
GROUP BY c.category
ORDER BY total_revenue DESC;
```

| Category | Total revenue |
|---|---:|
| Programming | 409.93 |
| Data | 179.97 |
| Design | 169.96 |
| Marketing | 59.98 |
| **Total** | **819.84** |

Programming brings in the most at 409.93. This sums `courses.monthly_fee` once per enrollment; it is not based on `enrollments.monthly_fee_paid` and is not a lifetime revenue figure.

### 8. Distinct enrolled students per instructor

```sql
SELECT c.instructor_name AS instructor,
	   COUNT(DISTINCT e.student_id) AS student_count
FROM courses c
JOIN enrollments e ON e.course_id = c.id
GROUP BY c.instructor_name
ORDER BY c.instructor_name;
```

| Instructor | Students |
|---|---:|
| Carlos Vega | 3 |
| Lucia Prades | 2 |
| Marta López | 6 |
| Pending assignment | 2 |

Counts are distinct students per instructor, so a student enrolled in multiple courses taught by the same instructor is counted once.

## Data Integrity

### 9. Enrollments with a missing student

```sql
SELECT e.id AS enrollment_id, e.student_id
FROM enrollments e
LEFT JOIN students s ON s.id = e.student_id
WHERE s.id IS NULL;
```

Result: No orphaned student references.

### 10. Enrollments with a missing course

```sql
SELECT e.id AS enrollment_id, e.course_id
FROM enrollments e
LEFT JOIN courses c ON c.id = e.course_id
WHERE c.id IS NULL;
```

Result: No orphaned course references.

Both foreign keys are declared on `enrollments`, which prevents orphaned references during normal database writes; the LEFT JOIN checks return no rows in the supplied seed data.
