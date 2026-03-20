# bank-account-summary-sql
-- ===========================================================

-- Step 1: Create the Transactions table
CREATE TABLE Transactions (
    AccountNumber     INT,
    AccountHolderName VARCHAR(100),
    TransactionDate   DATE,
    TransactionType   VARCHAR(20),
    TransactionAmount DECIMAL(10, 2)
);

-- Step 2: Insert sample transaction data
INSERT INTO Transactions VALUES
(1001, 'Ravi Sharma',  '2023-07-01', 'Deposit',    5000.00),
(1001, 'Ravi Sharma',  '2023-07-05', 'Withdrawal', 1000.00),
(1001, 'Ravi Sharma',  '2023-07-10', 'Deposit',    2000.00),
(1002, 'Priya Gupta',  '2023-07-02', 'Deposit',    3000.00),
(1002, 'Priya Gupta',  '2023-07-08', 'Withdrawal',  500.00),
(1003, 'Vikram Patel', '2023-07-04', 'Deposit',   10000.00),
(1003, 'Vikram Patel', '2023-07-09', 'Withdrawal',  2000.00);

-- ============================================================
-- Step 3: Bank Account Summary Query
-- Calculates Total Deposits, Total Withdrawals & Closing Balance
-- per account using conditional aggregation
-- ============================================================

SELECT
    AccountNumber,
    AccountHolderName,
    SUM(CASE WHEN TransactionType = 'Deposit'
             THEN TransactionAmount ELSE 0 END)  AS TotalDeposits,
    SUM(CASE WHEN TransactionType = 'Withdrawal'
             THEN TransactionAmount ELSE 0 END)  AS TotalWithdrawals,
    SUM(CASE WHEN TransactionType = 'Deposit'
             THEN TransactionAmount
             ELSE -TransactionAmount END)         AS ClosingBalance
FROM Transactions
GROUP BY AccountNumber, AccountHolderName
ORDER BY AccountNumber;

-- ============================================================
-- Expected Output:
-- +---------------+-------------------+---------------+------------------+----------------+
-- | AccountNumber | AccountHolderName | TotalDeposits | TotalWithdrawals | ClosingBalance |
-- +---------------+-------------------+---------------+------------------+----------------+
-- |          1001 | Ravi Sharma       |       7000.00 |          1000.00 |        6000.00 |
-- |          1002 | Priya Gupta       |       3000.00 |           500.00 |        2500.00 |
-- |          1003 | Vikram Patel      |      10000.00 |          2000.00 |        8000.00 |
-- +---------------+-------------------+---------------+------------------+----------------+

-- ============================================================
-- Alternative: Using CTE for better readability
-- ============================================================

WITH TransactionSummary AS (
    SELECT
        AccountNumber,
        AccountHolderName,
        TransactionType,
        SUM(TransactionAmount) AS TotalAmount
    FROM Transactions
    GROUP BY AccountNumber, AccountHolderName, TransactionType
)
SELECT
    AccountNumber,
    AccountHolderName,
    MAX(CASE WHEN TransactionType = 'Deposit'    THEN TotalAmount ELSE 0 END) AS TotalDeposits,
    MAX(CASE WHEN TransactionType = 'Withdrawal' THEN TotalAmount ELSE 0 END) AS TotalWithdrawals,
    SUM(CASE WHEN TransactionType = 'Deposit'    THEN TotalAmount
             WHEN TransactionType = 'Withdrawal' THEN -TotalAmount END)        AS ClosingBalance
FROM TransactionSummary
GROUP BY AccountNumber, AccountHolderName
ORDER BY AccountNumber;
