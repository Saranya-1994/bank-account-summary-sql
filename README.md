
# Bank Account Summary – SQL Analysis

**Tools:** PostgreSQL · SQL · Conditional Aggregation · CTEs

---

## Problem Statement

Generate a bank account summary by processing transaction history for multiple accounts — calculating total deposits, total withdrawals, and the final closing balance per account holder.

## Transaction Data

| AccountNumber | AccountHolderName | TransactionDate | TransactionType | TransactionAmount |
|:---:|:---|:---:|:---:|---:|
| 1001 | Ravi Sharma | 2023-07-01 | Deposit | 5000.00 |
| 1001 | Ravi Sharma | 2023-07-05 | Withdrawal | 1000.00 |
| 1001 | Ravi Sharma | 2023-07-10 | Deposit | 2000.00 |
| 1002 | Priya Gupta | 2023-07-02 | Deposit | 3000.00 |
| 1002 | Priya Gupta | 2023-07-08 | Withdrawal | 500.00 |
| 1003 | Vikram Patel | 2023-07-04 | Deposit | 10000.00 |
| 1003 | Vikram Patel | 2023-07-09 | Withdrawal | 2000.00 |

## Solution

Used **conditional aggregation** with `CASE WHEN` inside `SUM()` to separate deposits from withdrawals and compute the net closing balance per account.

```sql
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
```

## Expected Output

| AccountNumber | AccountHolderName | TotalDeposits | TotalWithdrawals | ClosingBalance |
|:---:|:---|---:|---:|---:|
| 1001 | Ravi Sharma | 7000.00 | 1000.00 | 6000.00 |
| 1002 | Priya Gupta | 3000.00 | 500.00 | 2500.00 |
| 1003 | Vikram Patel | 10000.00 | 2000.00 | 8000.00 |

## Key SQL Concepts Used

- CASE WHEN inside SUM() for conditional aggregation
- GROUP BY to summarize per account
- ORDER BY for clean output
- CTE alternative version included for readability

---

## Connect with Me

- LinkedIn: [linkedin.com/in/saranya-cj](https://www.linkedin.com/in/saranya-cj)
- GitHub: [github.com/Saranya-1994](https://github.com/Saranya-1994)
- Email: saranyasharu03@gmail.com
