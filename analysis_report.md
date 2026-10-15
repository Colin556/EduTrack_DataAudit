# EduTrack Data Audit
Initial dataset: 10 students, 7 courses, 17 enrollments.
Q1–Q5 describe the initial dataset. Q9–Q12 describe the dataset after
Q6–Q8, containing 16 enrollments. Students and courses remain unchanged.

## Q1 — Enrollments in 'Intro to Python'

Result: 5 enrollments.

| Student | Email | Completion |
|---|---|---:|
| Emily Watson | emily.watson@student.edutrack.com | 85% |
| Klaus Weber | klaus.weber@student.edutrack.com | 92% |
| Marco Rossi | marco.rossi@student.edutrack.com | 88% |
| James Miller | james.miller@test.com | 30% |
| Priya Sharma | priya.sharma@student.edutrack.com | 55% |

James Miller is a test account removed in Q8.

## Q2 — Completion below 10%

Result: 4 enrollments involving 3 distinct students.

| ID | Student | Course | Completion |
|---|---|---|---:|
| 5 | Lucia Fernandes | Web Design Basics | 5% |
| 6 | Lucia Fernandes | Digital Marketing 101 | 3% |
| 10 | Yuki Nakamura | UI/UX Fundamentals | 0% |
| 11 | Pierre Dubois | UI/UX Fundamentals | 0% |

These are potential disengagement signals, not confirmed dropouts.
Lucia has low progress in two courses. Both zero-progress UI/UX
enrollments also lack an instructor.

## Q3 — Missing instructors

Result: 2 enrollments.

| ID | Student | Course |
|---|---|---|
| 10 | Yuki Nakamura | UI/UX Fundamentals |
| 11 | Pierre Dubois | UI/UX Fundamentals |

Both instructor values are NULL.

## Q4 — Highest completion among enrollments not yet passed

Result: 5 enrollments.

| ID | Student | Course | Completion |
|---|---|---|---:|
| 2 | Emily Watson | Web Design Basics | 60% |
| 15 | Priya Sharma | Intro to Python | 55% |
| 9 | Yuki Nakamura | Data Analysis with SQL | 45% |
| 17 | Emily Watson | Advanced Python | 40% |
| 13 | James Miller | Intro to Python | 30% |

These represent four distinct students because Emily appears twice.
James Miller is subsequently removed as a test account.

## Q5 — Enrollments on or after July 1, 2024

Result: 9 enrollments, ordered newest first.

| ID | Enrollment date | Student | Course |
|---|---|---|---|
| 17 | 2025-03-05 | Emily Watson | Advanced Python |
| 16 | 2025-02-20 | Pierre Dubois | Data Analysis with SQL |
| 15 | 2025-01-10 | Priya Sharma | Intro to Python |
| 12 | 2024-12-01 | Priya Sharma | Digital Marketing 101 |
| 11 | 2024-11-05 | Pierre Dubois | UI/UX Fundamentals |
| 10 | 2024-10-11 | Yuki Nakamura | UI/UX Fundamentals |
| 9 | 2024-09-03 | Yuki Nakamura | Data Analysis with SQL |
| 8 | 2024-08-09 | Marco Rossi | Intro to Python |
| 6 | 2024-07-01 | Lucia Fernandes | Digital Marketing 101 |

The missing enrollment is not included because Q5 precedes Q6.

## Q6 — Missing enrollment

Result: 1 enrollment added; enrollment count becomes 18.

- ID: 18
- Student ID: 3
- Student: Lucia Fernandes
- Email: lucia.fernandes@student.edutrack.com
- Course ID: 5
- Course: Advanced Python
- Category: Programming
- Enrollment date: 2025-04-01
- Completion: 0%
- Passed: false
- Monthly fee paid: 69.99
- Instructor: Carlos Vega

## Q7 — Missing instructor correction

Result: 2 enrollments updated.

| ID | Student | Course | New instructor value |
|---|---|---|---|
| 10 | Yuki Nakamura | UI/UX Fundamentals | Pending assignment |
| 11 | Pierre Dubois | UI/UX Fundamentals | Pending assignment |

No NULL instructor values remain. The placeholder does not mean an
actual instructor has been assigned; operational follow-up is still needed.

## Q8 — Remove imported test accounts

Preview result: 2 enrollments.

| ID | Student | Email | Monthly fee paid |
|---|---|---|---:|
| 13 | James Miller | james.miller@test.com | 49.99 |
| 14 | Alex Chen | alex.chen@test.com | 39.99 |

Deletion result: 2 enrollments removed, leaving 16.

The deleted rows represented 89.98 in recorded fees. No @test.com
enrollments remain. The students table is unchanged.

## Q9 — Enrollments by category

Result:

| Category | Enrollments |
|---|---:|
| Programming | 7 |
| Design | 4 |
| Data | 3 |
| Marketing | 2 |
| **Total** | **16** |

## Q10 — Average completion by course

Result, ordered lowest to highest:

| Course | Average completion |
|---|---:|
| UI/UX Fundamentals | 0.00% |
| Web Design Basics | 32.50% |
| Digital Marketing 101 | 36.50% |
| Advanced Python | 45.00% |
| Data Analysis with SQL | 47.67% |
| Intro to Python | 80.00% |

UI/UX Fundamentals has the lowest progress and still lacks an assigned
instructor. Its two enrollments warrant priority review.

The new zero-progress Advanced Python enrollment lowers that course's
average; this alone does not establish disengagement.

For category-level interpretation, enrollment-weighted completion
averages calculated from the same corrected rows are:

- Design: 16.25%
- Marketing: 36.50%
- Data: 47.67%
- Programming: 65.00%

No formal underperformance threshold was supplied. Design and Marketing
have the lowest category averages and should receive priority review.
Small samples and enrollment age limit conclusions.

## Q11 — Courses with more than three enrollments

Result: 1 course.

| Course | Enrollments |
|---|---:|
| Intro to Python | 4 |

Advanced Python and Data Analysis with SQL each have three enrollments
and therefore do not satisfy HAVING COUNT(*) > 3.

## Q12 — Recorded revenue by category

Result, ordered highest to lowest:

| Category | Total revenue |
|---|---:|
| Programming | 409.93 |
| Data | 179.97 |
| Design | 169.96 |
| Marketing | 59.98 |
| **Total** | **819.84** |

Reconciliation: initial recorded fees of 839.83, plus the missing
enrollment's 69.99, minus test-account fees of 89.98, equals 819.84.

These totals sum monthly_fee_paid once per enrollment. They are not
lifetime subscription revenue, and no currency was specified.

The queries cover the supplied dataset, not a specific Q3 date range,
because no reporting year or quarter filter was requested.