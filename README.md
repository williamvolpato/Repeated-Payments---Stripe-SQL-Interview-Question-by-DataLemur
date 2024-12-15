# Repeated Payments Challenge

This repository contains the solution for the **"Repeated Payments"** SQL challenge from [DataLemur](https://datalemur.com/questions/repeated-payments).

## Problem

Identify repeated payments made:
- At the same merchant
- Using the same credit card
- For the same amount
- Within **10 minutes** of each other

Count such repeated payments.

---

## Solution

We use a **self-join** to compare transactions and identify pairs that meet the criteria.

### SQL Code:
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

## 1.Self-join: Compare each transaction to all others.

## 2.Conditions:

- Same merchant, card, and amount.
- Transactions within 10 minutes.
- Avoid double-counting pairs.

## 3.Output: Number of unique repeated payments.

For more details, visit the challenge on DataLemur -> https://datalemur.com/questions/repeated-payments

---



### **Versão em Português: README.md**

# Desafio Pagamentos Repetidos

Este repositório contém a solução para o desafio **"Pagamentos Repetidos"** do [DataLemur](https://datalemur.com/questions/repeated-payments).

## Problema

Identifique pagamentos repetidos realizados:
- No mesmo comerciante
- Usando o mesmo cartão de crédito
- Para o mesmo valor
- Dentro de **10 minutos** entre si

Conte tais pagamentos repetidos.

---

## Solução

Usamos um **self-join** para comparar transações e identificar os pares que atendem aos critérios.

### Código SQL:
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

## Explicação

## 1.Self-join: Compare cada transação com as demais.

## 2.Condições:

- Mesmo comerciante, cartão e valor.
- Transações dentro de 10 minutos.
- Evite contar pares duplicados.
  
## 3.Saída: Número de pagamentos repetidos únicos.

- Para mais detalhes, acesse o desafio no DataLemur.
