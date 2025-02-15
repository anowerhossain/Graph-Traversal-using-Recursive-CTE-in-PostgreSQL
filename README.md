# Graph-Traversal-using-Recursive-CTE-in-PostgreSQL

Recursive Common Table Expression (CTE) is a powerful feature in PostgreSQL that allows you to query hierarchical or tree-structured data using self-referencing queries. It is useful for solving problems like:

✅ Graph Traversal (Depth-First Search, Breadth-First Search)

✅ Hierarchy Queries (Organization charts, Folder structures, Category trees)

✅ Pathfinding Algorithms (Shortest paths, Route planning)

## 📌 Structure of a Recursive CTE

A Recursive CTE consists of two parts:

`Anchor Query` → This is the base query (starting point).
`Recursive Query` → This refers back to the CTE itself to process hierarchical relationships.

## Base structure of recursive CTE
```sql
WITH RECURSIVE cte_name (column_list) AS (
    --  1. Anchor Query (Base Case)
    SELECT initial_values

    UNION ALL

    --  2. Recursive Query (Calls itself)
    SELECT next_values
    FROM table_name
    JOIN cte_name ON condition
)
SELECT * FROM cte_name;
```

### Organizational Hierarchy 

- Create Table & Insert Data
```sql
CREATE TABLE employees (
    id SERIAL PRIMARY KEY,
    name TEXT NOT NULL,
    manager_id INT REFERENCES employees(id) -- Self-referencing foreign key
);

INSERT INTO employees (name, manager_id) VALUES
    ('CEO', NULL),
    ('CTO', 1), ('CFO', 1), ('CMO', 1),
    ('Engineer1', 2), ('Engineer2', 2),
    ('Accountant', 3),
    ('Marketing Lead', 4);
```

- Recursive Query to Find Employee Hierarchy
```sql
WITH RECURSIVE employee_hierarchy AS (
    --  Start from the CEO (Root)
    SELECT id, name, manager_id, 1 AS level
    FROM employees
    WHERE manager_id IS NULL

    UNION ALL

    --  Recursively find subordinates
    SELECT e.id, e.name, e.manager_id, h.level + 1
    FROM employees e
    JOIN employee_hierarchy h ON e.manager_id = h.id
)
SELECT * FROM employee_hierarchy ORDER BY level, id;
```

 id  |       name        | manager_id  | level
-----|-------------------|-------------|-----
  1  | CEO               | NULL        | 1
  2  | CTO               | 1           | 2
  3  | CFO               | 1           | 2
  4  | CMO               | 1           | 2
  5  | Engineer1         | 2           | 3
  6  | Engineer2         | 2           | 3
  7  | Accountant        | 3           | 3
  8  | Marketing Lead    | 4           | 3

✅ Hierarchy levels are properly displayed.

✅ The CEO is at Level 1, CTO/CFO/CMO at Level 2, and employees at deeper levels.
