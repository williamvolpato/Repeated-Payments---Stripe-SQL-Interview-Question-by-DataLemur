# Repeated Payments

## Problem

Sometimes, payment transactions are repeated by accident due to user error, API failures, or retry issues that cause a card to be charged twice.

Using the `transactions` table, identify any repeated payments made at the same merchant with the same credit card for the same amount **within 10 minutes** of each other. Count such repeated payments.

---

## Assumptions

- The first transaction of such repeated payments should not be counted as a repeated payment.
- For two transactions performed at the same merchant, with the same card, and for the same amount **within 10 minutes**, there will only be **1 repeated payment**.

---

## Table: transactions

| Column Name           | Type     |
|-----------------------|----------|
| transaction_id        | integer  |
| merchant_id           | integer  |
| credit_card_id        | integer  |
| amount                | integer  |
| transaction_timestamp | datetime |

---

## Example Input

| transaction_id | merchant_id | credit_card_id | amount | transaction_timestamp   |
|----------------|-------------|----------------|--------|-------------------------|
| 1              | 101         | 1              | 100    | 09/25/2022 12:00:00     |
| 2              | 101         | 1              | 100    | 09/25/2022 12:08:00     |
| 3              | 101         | 1              | 100    | 09/25/2022 12:28:00     |
| 4              | 101         | 2              | 200    | 09/25/2022 12:00:00     |
| 5              | 102         | 3              | 300    | 09/25/2022 12:00:00     |
| 6              | 102         | 3              | 400    | 09/25/2022 12:10:00     |

---

## Expected Output

| payment_count |
|---------------|
| 1             |

---

## Solution (SQL)

```sql
SELECT COUNT(*) AS payment_count
FROM transactions t1
JOIN transactions t2
  ON t1.merchant_id = t2.merchant_id
 AND t1.credit_card_id = t2.credit_card_id
 AND t1.amount = t2.amount
 AND t1.transaction_id < t2.transaction_id
WHERE EXTRACT(EPOCH FROM (t2.transaction_timestamp - t1.transaction_timestamp)) BETWEEN 0 AND 600;
```

---

## Explanation

## 1.	JOIN Condition:

-	Compare each transaction with others in the same table (t1 and t2) to find potential repeated payments.
-	Ensure merchant_id, credit_card_id, and amount match.
-	Use t1.transaction_id < t2.transaction_id to avoid double-counting pairs of transactions.

## 2.	Time Constraint:

-	Calculate the difference in seconds between transaction_timestamp values.
-	Use BETWEEN 0 AND 600 to limit the time gap to 10 minutes.

## 3.	Result:

-	Count the number of unique repeated payments.
