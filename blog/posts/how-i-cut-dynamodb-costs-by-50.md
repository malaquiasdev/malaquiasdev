---
title: "How I Cut DynamoDB Costs by 50% Using Sparse Indexes and Atomic Patterns"
date: '2025-01-22'
tags:
    - aws dynamodb serverless architecture
description: "Learn how to cut DynamoDB costs by 50% using Sparse Indexes and Atomic Patterns. Optimize your indexing strategy to reduce storage costs and improve performance."
---

## TL;DR

DynamoDB isn't expensive; your indexing strategy is.

* **Sparse Indexes:** Stop replicating every item to a GSI. If it doesn't have the GSI key, it doesn't cost you a write.
* **Item Collections:** Put Headers and Items under the same `PK`. Better latency, stronger consistency.
* **Atomic Check-and-Set:** Use `UpdateItem` with `ReturnValues: "ALL_NEW"` to handle massive parallel processing without extra `GetItem` calls.
* **The Result:** We processed **1 million items** for **$1.25** instead of **$2.50**. Simple math, massive impact.

## The Impact: Scaling the Savings

If you think $1.25 isn't much, look at the scale. When you're doing this every day at high volume, the numbers move the needle for the whole business.

| Metric | Legacy (Full GSI) | Optimized (Sparse Index) | Improvement |
| --- | --- | --- | --- |
| Write Cost (per 1M items) | $2.50 | $1.25 | 50% cheaper |
| Annual Cost (100M items/mo) | $3,000 | $1,500 | $1,500 saved/yr |
| Read Strategy | 2 Requests (GSI + Table) | 1 Request (Item Collection) | 2x Faster |
| Consistency | Eventual (GSI) | Strong (Main Table) | Better Reliability |
| GSI Storage | 100% Replication | &lt;1% (Headers only) | 99% Leaner |

## Aligning with the AWS Calculator

If you open the AWS Pricing Calculator right now:

* It asks for **Average Item Size** (defaulting to 1KB).
* It asks for **Write/Read volume in Millions**.
* It asks for **Consistency**.

Our strategy targets these exact levers. We reduced the volume seen by the GSIs and optimized the read consistency by using Item Collections.

## The "Serverless" Trap

People choose DynamoDB because it "scales to infinity," then treats it like a schema-less dumping ground. They dump 10KB JSON blobs, add 5 GSIs "just in case," and then freak out when the AWS bill looks like a phone number.

As Alex DeBrie famously said: **"Do the math"**. DynamoDB is a database for people who like spreadsheets. If you don't know your item sizes and access patterns, you're not designing—you're gambling with the company's credit card.

## The Case Study: From Relational Mess to Sparse Success

We had a system processing massive batches of operations. Here is how we fixed the "money leak."

In a recent project, we were processing massive CSV uploads. In the old "relational" model, every row was a database entry replicated to a GSI. It was a money pit.

### The Shift to Sparse Indexes

We only needed the GSI to find the **Operation Header**, not the millions of individual data rows. Instead of having a "Headers" table and an "Items" table, we moved to a **Single Table Design**.

* **PK:** `OPERATION#<id>`
* **SK:** `HEADER` (for metadata) or `ITEM#<uuid>` (for the data).
* **The Fix:** We removed the `GSI1PK` attribute from the data items.
* **The Result:** Since DynamoDB only indexes items that actually have the index keys, it ignored the 1,000,000 data rows during replication.
* **Cost dropped from $2.50 to $1.25 per million items** because we stopped paying the "replication tax" for data we never intended to search by that index.

Also by doing this, we can fetch the entire operation details (Header + Items) in a **single request** using a `Query` on the `PK`. No more jumping between GSIs or making multiple network hops. Plus, reading from the main table allows **Strong Consistency**, which we needed for the detail view.

## The Math of Blocks

DynamoDB charges you in fixed blocks. It doesn't care if your item is 100 bytes or 1,000 bytes; a single write is **1 WCU**.

### The Query vs. GetItem Trick

If you need to read 100 small items (100 bytes each):

* **100 `GetItem` calls:** Each rounds up to 4KB. Total: **100 RCUs**.
* **1 `Query` call:** DynamoDB sums them first () then rounds (). Total: **3 RCUs**.

You just saved 97% on your read bill by understanding how the "machine" rounds.

### Concurrency at Scale: Atomic Check-and-Set

When you have SQS workers processing a million rows in parallel, race conditions are your worst nightmare. Most devs try to solve this with a `GetItem` -> `Logic` -> `PutItem` flow. That’s slow, expensive, and it **will** break.

Instead, use **Atomic Patterns**:

* **Atomic Increment:** Use `UpdateItem` with `SET val = val + :inc`. DynamoDB serializes this.
* **No-Cost Verification:** Use `ReturnValues: "ALL_NEW"`. DynamoDB gives you the state **after** the math for free.
* **The Winner Check:** The worker checks in memory: `if (new_processed + new_failed >= total_lines)`. The "winning" worker (the one who processed the last bit) triggers the `COMPLETED` status using a `ConditionExpression` to ensure idempotency.

## Aligning with the AWS Calculator

If you open the AWS Pricing Calculator right now:

* It asks for **Average Item Size** (defaulting to 1KB).
* It asks for **Write/Read volume in Millions**.
* It asks for **Consistency**.

Our strategy targets these exact levers. We reduced the volume seen by the GSIs and optimized the read consistency by using Item Collections.

## Conclusion

By switching to a Sparse Index and leveraging Atomic Counters, we didn't just "fix a bug"—we optimized the unit economics of the entire feature.


Next time you're about to add a GSI, ask yourself: **"Does every item really need to be there, or am I just being lazy with my schema?"**

