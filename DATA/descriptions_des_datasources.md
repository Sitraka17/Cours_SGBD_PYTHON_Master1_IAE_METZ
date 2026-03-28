Ici se trouve les datas et les liens vers les sources des données. 


KAGGLE : https://www.kaggle.com/datasets



Possible de jouer avec SQL : 

To truly play and learn with this financial schema, you need a progression from basic data retrieval to advanced, production-level SQL concepts like ACID transactions and Window Functions. 

Here is a complete sandbox suite written in standard SQL (PostgreSQL dialect recommended for UUID and Decimal support). 

### 1. The Sandbox Setup (DDL & Seeding)
First, you need the actual tables and some dummy data to play with.

```sql
-- Enable UUID extension (PostgreSQL specific)
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";

-- Create Tables (Simplified DDL from the DBML)
CREATE TABLE customers (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  first_name VARCHAR(100) NOT NULL,
  last_name VARCHAR(100) NOT NULL,
  email VARCHAR(255) UNIQUE NOT NULL,
  kyc_verified BOOLEAN DEFAULT FALSE
);

CREATE TABLE accounts (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  customer_id UUID REFERENCES customers(id),
  account_number VARCHAR(30) UNIQUE NOT NULL,
  type VARCHAR(50) NOT NULL,
  balance DECIMAL(19, 4) DEFAULT 0.0000,
  status VARCHAR(20) DEFAULT 'active'
);

CREATE TABLE transactions (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  account_id UUID REFERENCES accounts(id),
  type VARCHAR(20) NOT NULL,
  amount DECIMAL(19, 4) NOT NULL,
  running_balance DECIMAL(19, 4) NOT NULL,
  reference_id UUID,
  executed_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Insert Test Data
INSERT INTO customers (id, first_name, last_name, email, kyc_verified) 
VALUES ('c1b2a345-1234-1234-1234-123456789012', 'Ada', 'Lovelace', 'ada@example.com', true);

INSERT INTO accounts (id, customer_id, account_number, type, balance) 
VALUES 
  ('a1b2a345-1234-1234-1234-123456789012', 'c1b2a345-1234-1234-1234-123456789012', 'CHK-001', 'Checking', 1500.0000),
  ('a1b2a345-1234-1234-1234-123456789013', 'c1b2a345-1234-1234-1234-123456789012', 'SAV-001', 'Savings', 5000.0000);

-- Insert Initial Deposits
INSERT INTO transactions (account_id, type, amount, running_balance) 
VALUES 
  ('a1b2a345-1234-1234-1234-123456789012', 'deposit', 1500.0000, 1500.0000),
  ('a1b2a345-1234-1234-1234-123456789013', 'deposit', 5000.0000, 5000.0000);
```

---

### 2. Intermediate Queries: Joins and Aggregations
These queries explore relationships and calculate financial metrics.

**Get a full customer portfolio (Total Wealth)**
Calculates the total liquid assets of a customer across all their active accounts.
```sql
SELECT 
    c.first_name, 
    c.last_name, 
    COUNT(a.id) AS total_accounts,
    SUM(a.balance) AS total_portfolio_value
FROM customers c
JOIN accounts a ON c.id = a.customer_id
WHERE a.status = 'active'
GROUP BY c.id, c.first_name, c.last_name;
```

**Generate a Monthly Bank Statement**
Retrieves chronological ledger entries for a specific account, resolving the account ID via the account number.
```sql
SELECT 
    t.executed_at,
    t.type,
    t.amount,
    t.running_balance
FROM transactions t
JOIN accounts a ON t.account_id = a.id
WHERE a.account_number = 'CHK-001'
  AND t.executed_at >= date_trunc('month', CURRENT_DATE)
ORDER BY t.executed_at ASC;
```

---

### 3. Advanced Queries: Window Functions and Data Integrity
Financial databases require strict auditing. These queries test the mathematical integrity of the ledger.

**Recalculate Running Balances (Window Function)**
This query uses a Window Function (`SUM() OVER`) to dynamically recalculate the balance row-by-row based strictly on the transaction history. If this output differs from the stored `running_balance`, you have a data integrity breach.
```sql
SELECT 
    account_id,
    executed_at,
    amount,
    running_balance AS stored_balance,
    SUM(amount) OVER (
        PARTITION BY account_id 
        ORDER BY executed_at ASC
    ) AS calculated_running_balance
FROM transactions
WHERE account_id = 'a1b2a345-1234-1234-1234-123456789012';
```

**Find Orphaned Transfers (Self-Join)**
Every transfer should have a debit leg and a credit leg sharing the same `reference_id`. This query finds incomplete transfers where the sum of the legs does not equal exactly zero.
```sql
SELECT 
    reference_id,
    SUM(amount) as net_transfer_value,
    COUNT(id) as legs_count
FROM transactions
WHERE type = 'transfer'
GROUP BY reference_id
HAVING SUM(amount) != 0 OR COUNT(id) != 2;
```

---

### 4. Expert Execution: The ACID Transaction Block
To simulate real backend code, you should execute a transfer using an explicit database transaction block. This ensures that if the database crashes halfway through, money isn't lost or created out of thin air.

**Simulating a \$200 Transfer from Checking to Savings**
```sql
BEGIN; -- Start ACID block

-- 1. Deduct from sender (Checking)
UPDATE accounts 
SET balance = balance - 200.0000 
WHERE account_number = 'CHK-001' AND balance >= 200.0000; 
-- Note: In a real app, you'd check if rows affected = 1 to prevent overdraft

-- 2. Add to receiver (Savings)
UPDATE accounts 
SET balance = balance + 200.0000 
WHERE account_number = 'SAV-001';

-- 3. Record Debit Ledger Entry (Notice the hardcoded shared UUID for reference_id)
INSERT INTO transactions (account_id, type, amount, running_balance, reference_id)
SELECT id, 'transfer', -200.0000, balance, 'f9e8d7c6-b5a4-3210-9876-543210987654'
FROM accounts WHERE account_number = 'CHK-001';

-- 4. Record Credit Ledger Entry
INSERT INTO transactions (account_id, type, amount, running_balance, reference_id)
SELECT id, 'transfer', 200.0000, balance, 'f9e8d7c6-b5a4-3210-9876-543210987654'
FROM accounts WHERE account_number = 'SAV-001';

COMMIT; -- Persist all changes atomically. Use ROLLBACK here if an error occurs.
```

Would you like to explore how to optimize these specific queries by writing the exact `CREATE INDEX` statements required to prevent slow table scans?
