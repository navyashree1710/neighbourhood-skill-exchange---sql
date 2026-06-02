# 🔄 Neighborhood Skill Exchange — SQL Project

A relational database project that models a hyperlocal skill-swapping platform where people exchange skills instead of money. Built entirely in SQLite using DB Browser.

---

## 💡 Project Idea

Instead of paying for services, people in a neighborhood offer what they know and request what they need. For example:
- Priya teaches Python → gets Yoga lessons from Karan
- Rahul teaches Guitar → gets South Indian cooking from Sneha
- Rohan shares his scooty for an hour → gets woodworking help from Nikhil

No money changes hands — just skills.

---

## 🗄️ Database Structure

The project has 5 tables:

### `users`
Stores people registered on the platform.
| Column | Type | Description |
|---|---|---|
| id | INTEGER | Primary key |
| name | TEXT | Full name |
| location | TEXT | Neighbourhood |
| bio | TEXT | Short description |

### `skills`
Stores all available skills across categories.
| Column | Type | Description |
|---|---|---|
| id | INTEGER | Primary key |
| name | TEXT | Skill name |
| category | TEXT | Tech, Music, Fitness, Household etc. |
| difficulty_level | TEXT | Beginner / Intermediate / Advanced |

### `user_skills`
Many-to-many table linking users to skills — whether they offer or want them.
| Column | Type | Description |
|---|---|---|
| id | INTEGER | Primary key |
| user_id | INTEGER | Foreign key → users |
| skill_id | INTEGER | Foreign key → skills |
| type | TEXT | 'offer' or 'want' |

### `swap_requests`
Tracks requests made between users for a skill swap.
| Column | Type | Description |
|---|---|---|
| id | INTEGER | Primary key |
| requester_id | INTEGER | User who asked |
| provider_id | INTEGER | User who provides the skill |
| skill_id | INTEGER | Skill being swapped |
| status | TEXT | pending / completed / expired |
| requested_date | TEXT | Date of request |

### `reviews`
Ratings left after a completed swap.
| Column | Type | Description |
|---|---|---|
| id | INTEGER | Primary key |
| swap_id | INTEGER | The swap being reviewed |
| reviewer_id | INTEGER | Who left the review |
| reviewee_id | INTEGER | Who was reviewed |
| rating | INTEGER | 1 to 5 |
| comment | TEXT | Optional feedback |
| review_date | TEXT | Date of review |

---

## 📊 Sample Data

| Table | Records |
|---|---|
| users | 13 |
| skills | 31 |
| user_skills | 65 |
| swap_requests | 15 |
| reviews | 8 |

Skills span 9 categories: Tech, Music, Design, Fitness, Cooking, Photography, Language, Household, Electrical, Transport, Physical, Craft.

---

## 🔍 Key Queries

### Find perfect swap matches
```sql
SELECT 
  u1.name AS person_1,
  s.name AS skill,
  u2.name AS person_2_wants
FROM user_skills us1
JOIN user_skills us2 ON us1.skill_id = us2.skill_id
JOIN users u1 ON us1.user_id = u1.id
JOIN users u2 ON us2.user_id = u2.id
JOIN skills s ON us1.skill_id = s.id
WHERE us1.type = 'offer' AND us2.type = 'want'
AND us1.user_id != us2.user_id;
```

### Top rated users
```sql
SELECT users.name, ROUND(AVG(reviews.rating), 2) AS avg_rating
FROM reviews
JOIN users ON reviews.reviewee_id = users.id
GROUP BY users.name
ORDER BY avg_rating DESC;
```

### Most offered skills
```sql
SELECT skills.name, COUNT(*) AS total_offers
FROM user_skills
JOIN skills ON user_skills.skill_id = skills.id
WHERE user_skills.type = 'offer'
GROUP BY skills.name
ORDER BY total_offers DESC;
```

### Users who have never been reviewed
```sql
SELECT users.name
FROM users
LEFT JOIN reviews ON users.id = reviews.reviewee_id
WHERE reviews.reviewee_id IS NULL;
```

### All pending swaps with names
```sql
SELECT 
  u1.name AS requester,
  u2.name AS provider,
  skills.name AS skill,
  swap_requests.requested_date
FROM swap_requests
JOIN users u1 ON swap_requests.requester_id = u1.id
JOIN users u2 ON swap_requests.provider_id = u2.id
JOIN skills ON swap_requests.skill_id = skills.id
WHERE swap_requests.status = 'pending';
```

---

## ⚙️ SQL Concepts Used

- `CREATE TABLE` with primary and foreign keys
- `INSERT` and CSV bulk import
- `SELECT`, `WHERE`, `ORDER BY`, `LIMIT`
- `INNER JOIN` across multiple tables
- `LEFT JOIN` to find missing relationships
- `GROUP BY` with `COUNT`, `AVG`, `ROUND`
- `HAVING` to filter grouped results
- `CREATE VIEW` for reusable queries
- `CREATE TRIGGER` for automatic date insertion

---

## 🗂️ Files in this Repository

| File | Description |
|---|---|
| `skill_exchange.sql` | Full database export — run this to recreate everything |
| `queries.sql` | All queries written during the project |
| `users.csv` | Sample user data |
| `skills.csv` | All 31 skills |
| `user_skills.csv` | Who offers and wants what |
| `swap_requests.csv` | Swap request records |
| `reviews.csv` | Review and rating data |

---

## 🛠️ How to Run

1. Download and install [DB Browser for SQLite](https://sqlitebrowser.org/dl/) (free, 17MB)
2. Open DB Browser → File → Import → Database from SQL file → select `skill_exchange.sql`
3. Go to the Execute SQL tab and run any query from `queries.sql`

---

## 👤 About

Built as a beginner SQL portfolio project covering database design, querying, views, and triggers.
