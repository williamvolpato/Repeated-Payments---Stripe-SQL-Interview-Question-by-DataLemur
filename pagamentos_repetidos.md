# Pagamentos Repetidos

## Problema

Às vezes, transações de pagamento são repetidas acidentalmente devido a erro do usuário, falhas na API ou problemas de tentativas que causam a cobrança duplicada.

Usando a tabela `transactions`, identifique quaisquer pagamentos repetidos feitos no mesmo comerciante com o mesmo cartão de crédito e para o mesmo valor **dentro de 10 minutos**. Conte tais pagamentos repetidos.

---

## Premissas

- A **primeira transação** de tais pagamentos não deve ser contada como um pagamento repetido.
- Para duas transações realizadas no mesmo comerciante, com o mesmo cartão e para o mesmo valor **dentro de 10 minutos**, haverá apenas **1 pagamento repetido**.

---

## Tabela: transactions

| Nome da Coluna          | Tipo       |
|-------------------------|------------|
| transaction_id          | integer    |
| merchant_id             | integer    |
| credit_card_id          | integer    |
| amount                  | integer    |
| transaction_timestamp   | datetime   |

---

## Entrada de Exemplo

| transaction_id | merchant_id | credit_card_id | amount | transaction_timestamp   |
|----------------|-------------|----------------|--------|-------------------------|
| 1              | 101         | 1              | 100    | 25/09/2022 12:00:00     |
| 2              | 101         | 1              | 100    | 25/09/2022 12:08:00     |
| 3              | 101         | 1              | 100    | 25/09/2022 12:28:00     |
| 4              | 101         | 2              | 200    | 25/09/2022 12:00:00     |
| 5              | 102         | 3              | 300    | 25/09/2022 12:00:00     |
| 6              | 102         | 3              | 400    | 25/09/2022 12:10:00     |

---

## Saída Esperada

| payment_count |
|---------------|
| 1             |

---

## Solução (SQL)

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

##Explicação

## 1.Condição do JOIN:

- Compare cada transação com as demais na mesma tabela (t1 e t2) para identificar pagamentos repetidos.
- Assegure que merchant_id, credit_card_id e amount sejam iguais.
- Use t1.transaction_id < t2.transaction_id para evitar a contagem duplicada dos pares de transações.

## 2.Restrição de Tempo:

- Calcule a diferença em segundos entre os valores de transaction_timestamp.
- Utilize BETWEEN 0 AND 600 para limitar a diferença a 10 minutos.

## 3.Resultado:

- Conte o número de pagamentos repetidos únicos.

