# Idempotency

Idempotency is the property of an operation that ensures performing the same action multiple times produces the same outcome as doing it once. 

An idempotent API ensures that making the same request multiple times produces the exact same system state as making it a single time. 
This property is crucial for building reliable distributed systems where network failures, timeouts, or accidental double-clicks cause clients to retry requests. 
Without idempotency, a retried request could result in duplicate operations, such as double-charging a credit card or creating multiple database entries.

```
{http}
POST /resource HTTP/1.1
Host: example.com
Idempotency-Key: unique-id-123
Content-Type: application/json
{
    "name": "New Resource",
    "value": "New Value"
}
```

## Tutorials
1. https://www.geeksforgeeks.org/javascript/what-is-an-idempotent-rest-api/
2. https://medium.com/@reetesh043/rest-api-design-what-is-idempotency-18218e1ff73c
3. https://blog.bytebytego.com/p/mastering-idempotency-building-reliable

<img src="img/Idempotency in payment APIs.png" width="100%" />

## 🚀 "The customer clicked 'Pay' three times. How do you ensure they're charged only once?"

This is one of the most common backend and system design interview questions.

Many candidates know what idempotency is. Very few can explain why it's needed and how it's implemented.

Spend 5 minutes with this diagram and you'll never forget this concept.

Save this before your next interview.

━━━━━━━━━━━━━━━━━━

## 🤔 The Problem

Imagine you're building a payment system.

The user clicks "Pay." The request reaches the server, but the response never reaches the user because of a network timeout.

What does the user do?

👉 Clicks "Pay" again.

Without idempotency, your API may create:
🔹 Multiple orders
🔹 Duplicate payments
🔹 Incorrect inventory
🔹 Angry customers

━━━━━━━━━━━━━━━━━━

## 💡 The Solution

Generate a unique Idempotency Key for each logical request.

🔹 First request → Process it and store the response with the key.

🔹 Retry with the same key → Return the stored response.

🔹 No duplicate processing.

✅ Same request + Same key = Same result.

━━━━━━━━━━━━━━━━━━

## 🌍 Where is it used?
🔹 Payment APIs
🔹 Order creation
🔹 Money transfers
🔹 Ticket booking
🔹 Inventory updates
🔹 Any operation where duplicates are unacceptable

━━━━━━━━━━━━━━━━━━

## 🎯 Common Interview Questions
🔹 What is idempotency?
🔹 Why is POST not idempotent by default?
🔹 How do idempotency keys work?
🔹 Where should idempotency keys be stored?
🔹 How long should they be retained?
🔹 Which HTTP methods are idempotent?

━━━━━━━━━━━━━━━━━━

## 💡 Interview Tip

Don't say: ❌ "Idempotency prevents duplicate requests."

Say: 
```
✅ "Idempotency ensures the same request produces the same outcome. A common implementation uses an Idempotency Key to identify retries and return the original response instead of processing the request again."
```
That shows you understand both the problem and the solution.

━━━━━━━━━━━━━━━━━━

# 💬 Interview Question: If your payment API stores idempotency keys in Redis and Redis crashes, how would you prevent duplicate payments?

> If Redis crashes, I would not rely solely on Redis for idempotency because duplicate payments are unacceptable.
> Instead, I'd make the database the source of truth.
> The payment request would include an Idempotency-Key, and before processing I'd check a persistent database table with a unique constraint on that key.
> If the key already exists, I'd return the previously stored response.
> Redis can be used as a fast cache to reduce database lookups, but if Redis is unavailable, the system falls back to the database.
> This guarantees exactly one payment is processed even during Redis failures.

Suppose Redis stores:
```
Key: abc123
Response: Payment Successful
```
Then Redis crashes.

Redis Memory
-------------
abc123 ❌ Lost

The client retries:
```
POST /payments
Idempotency-Key: abc123
```
Redis says:
```
Key not found
```
Server processes payment again.

Result:
```
Payment #1 ✅
Payment #2 ❌ Duplicate
```
This is why Redis should never be the only protection for financial transactions.

## Enterprise Architecture
```
                  Client
                     |
                     |
      POST /payments
      Idempotency-Key: abc123
                     |
                     v
             API Gateway
                     |
                     v
             Payment Service
                     |
        +------------+-------------+
        |                          |
        | Check Redis              |
        |                          |
        | Hit?                     |
        |                          |
       Yes                        No
        |                          |
Return Cached Response             |
                                   v
                       Check Payment DB
                     (Unique Idempotency Key)
                                   |
                 +-----------------+----------------+
                 |                                  |
           Key Exists                          Key Missing
                 |                                  |
     Return Stored Response             Start Transaction
                                                 |
                                       Process Payment
                                                 |
                                    Save Payment Record
                                    Save Idempotency Key
                                                 |
                                 Update Redis Cache (optional)
                                                 |
                                        Return Response
```

## Best Practice
Redis = Cache
```
Fast lookup

Redis

abc123
↓

Payment Success
```
TTL:
```
24 hours
48 hours
7 days
```

**Database = Source of Truth**
payments

---------------------------------------------
payment_id
amount
status
idempotency_key (UNIQUE)
response
---------------------------------------------

Unique constraint:
```
ALTER TABLE payments
ADD CONSTRAINT uk_idempotency
UNIQUE(idempotency_key);
```
Even if 100 identical requests arrive,

Database allows only
```
abc123
```
once.

## Processing Flow

**Request 1**
```
Key = abc123

Redis?
↓

Not Found

↓

Database?
↓

Not Found

↓

Charge Card

↓

Insert

Payment
Key=abc123

↓

Return Success
```

**Retry**
```
Key=abc123

Redis?

↓

Found

↓

Return Same Response

No payment processing
```

**Redis Crash**
```
Key=abc123

Redis

↓

Unavailable

↓

Database Lookup

↓

Found

↓

Return Existing Payment

Done
```
No duplicate payment.

# What if Two Requests Arrive at Exactly the Same Time?

Example
```
Request A
Request B

Both have

Idempotency-Key = abc123
```

**Without locking:**
```
A checks DB
No record

B checks DB
No record

A charges

B charges

Duplicate
```
Bad.

**Solution 1 (Recommended)**

Database Unique Constraint
```
BEGIN;

INSERT INTO payments
(idempotency_key)

VALUES('abc123');
```
Only one succeeds.

Second request gets
```
Duplicate Key Error
```
Then
```
SELECT payment

Return Existing Response
```

**Solution 2**

Distributed Lock

Example
```
SETNX payment:abc123
```
Only one server gets the lock.

Others wait.

Useful in distributed systems.

**Solution 3**

Pessimistic Lock
```
SELECT ...

FOR UPDATE
```
Useful when updating existing rows.

**Payment Flow with Transaction**
```
BEGIN TRANSACTION

↓

Insert idempotency key

↓

Charge Payment Gateway

↓

Save Payment

↓

Commit

↓

Return Response
```
If anything fails:
```
Rollback
```
No partial payment.

# What if Payment Gateway Charged but DB Commit Failed?

This is another common interview question.

Example
```
DB
↓

Insert

↓

Payment Gateway

↓

Charged

↓

DB crashes
```
Now customer is charged but DB doesn't know.

Solutions include:
- Outbox Pattern to reliably publish payment events after commit.
- Saga Pattern to orchestrate long-running transactions and compensating actions.
- Reconciliation Jobs that periodically compare gateway transactions with the database and repair inconsistencies.
- Gateway transaction reference (or gateway-side idempotency key) so retries return the original payment instead of charging again.

Large payment providers (Stripe, PayPal, Adyen, Razorpay) rely on combinations of these techniques rather than a single database transaction.


