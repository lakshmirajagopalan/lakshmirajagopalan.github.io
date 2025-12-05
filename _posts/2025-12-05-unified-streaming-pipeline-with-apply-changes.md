---
layout: post
title: Unified Streaming Pipeline: Intelligent Multi-Source Deduplication with APPLY CHANGES
categories: [Blogging, Data Engineering]
tags: [databricks, dlt, spark, performance]
seo:
  date_modified: 2025-12-04 20:00:00 +0530
---


One of the most challenging problems I’ve solved recently was figuring out how to **collate events arriving from multiple source systems**, each with its own delivery pattern, format, and reliability model — *without losing streaming semantics*.  

In theory, a Lakehouse should help unify everything. In practice, when an event can originate from:

- a Kafka topic  
- application logs  
- MongoDB change streams  
- Mixpanel analytics  
- vendor callbacks  
- async responses over HTTP  
- or even partner system batch drops  

…you quickly realize that nothing in the real world arrives consistently, and nothing is guaranteed to be “the one true source.”

For my "workflow event" pipeline, an event could come from literally **any** of these source systems. And to power downstream analytics, user KPIs, vendor performance insights, and business dashboards, I needed to build a **single, unified, deduped stream**.

This turned into a fascinating engineering challenge.

---

## 1. The Core Challenge: You Cannot Collate Multi-System Events in Batch Without Losing Streaming Features

At first, the naive approach seems obvious:

> “Just read all the sources in batch mode, join them, dedupe them, and output one clean table.”

But the moment you treat any source as batch instead of streaming, you immediately lose what makes streaming valuable:

- incremental processing  
- event-time handling  
- watermarks  
- microbatch-level incremental transformation  
- low latency  
- backpressure handling  
- exactly-once semantics  

Once the pipeline becomes “batch-first,” everything downstream becomes batch too — which meant:

❌ no more real-time analytics  
❌ no more near-instant vendor monitoring  
❌ no more user behavior freshness  
❌ no more true incremental transformations

So the design constraint became:

# **Every input MUST remain a streaming source all the way through Silver.**

Only then can I guarantee that transformations, enrichment, metadata tagging, and joins remained incremental.

---

## 2. The Architecture: Treat Every Input Source as a Stream

It doesn’t matter if the input system *is* streaming — I transform it *into* a stream.

For every input:

- Kafka → streaming  
- MongoDB CDC → streaming  
- Mixpanel → streaming  
- Vendor callback logs → streaming  
- Application logs → converted to streaming with Autoloader  
- Async vendor responses → treated as streaming microbatches  

The moment they enter the Lakehouse, they’re handled with **Stream()** in DLT or Structured Streaming.

This allows me to:

- normalize schemas  
- enrich with lookup tables  
- add metadata  
- perform type inference  
- unify timestamps  
- use event-time logic everywhere  
- retain incremental semantics  

Everything until Silver is a **stream**, full stop.

---

## 3. Apply All Transformations in Streaming Mode (Bronze → Silver)

Upstream systems are unpredictable, but my Silver layer must not be.

So in Silver, I do all the heavy lifting:

- cleaning  
- normalizing fields  
- linking user/vendor IDs  
- flattening JSON  
- enriching from dimension tables  
- classifying events  
- correcting timestamps  
- adding context from other streams  
- tagging events with internal metadata  

At this stage, the events still look like:

( user_id, vendor_id, source_system, timestamp, payload, success_flag, raw_ids, … )


But the key is:

> **All this happens in streaming mode.**  
> Each stream flows independently through DLT transformations, but ultimately lands in a unified schema.

Now I have a “multi-source unlock stream,” but with duplicates, retries, partial failures, and conflicting event-level truth.

This leads to the next problem.

---

## 4. The Hardest Problem: How Do You Deduplicate Multi-Source Events?

When a single business event can be produced by:

- the user service  
- the vendor service  
- internal job logs  
- vendor webhooks  
- Mixpanel  
- app analytics  
- retry mechanisms  

…you don’t have a single “truth.”  
You have **multiple versions** of the same event clustered around the same logical key.

Deduplication gets tricky:

- Event A is successful  
- Event B is “maybe”  
- Event C is an error  
- Event D comes from a retry job  
- Event E comes from vendor after 60 seconds  

Which one is correct?  
Which one should dashboards show?  
Which one should metrics compute?

And how do you dedupe this **without dropping streaming semantics**?

Traditional methods such as windowed dedupe or batch dedupe simply don’t work reliably, especially when late-arriving data from vendors can appear long after the original event.

---

## 5. The Solution: CDC-Style Apply Changes (With a Twist)

This is where **Delta’s `APPLY CHANGES INTO`** became my secret weapon.

I treat all incoming events like CDC merges.

The trick:

### **Primary key = the event’s unique business key**  
(e.g., request_date, user_id, entity_type, entity_id)

### **Sequence column = “confidence score”**

Examples:

| event_type        |  confidence_score    |
|------------------ |----------------------|
| high_confidence   | 1.0                  |
| medium_confidence | 0.75                 |
| low_confidence    | 0.5                  |
| fallback          | 0.25                 |
| noise             | 0.10                 |

Whichever event has the **highest sequence value** becomes the “official truth.”

This gives me:

- deterministic dedupe  
- correct event selection  
- full streaming semantics preserved  
- late arrivals handled gracefully  
- updates applied incrementally  
- one unified event per key  

### How `APPLY CHANGES` Works (Intuitively)

It acts like a MERGE, but incrementally:

APPLY CHANGES INTO unified_events
FROM silver_multi_source_stream
KEY (event_id)
SEQUENCE BY confidence_sequence


The row with the highest `confidence_score` wins.

No massive joins.  
No full-table rewrites.  
No destroying streaming.  

Just incremental updates.

---

## 6. Once Deduplication Is Done → Streaming Phase Ends

After `APPLY CHANGES` produces the final unified event table, the streaming portion of the pipeline is complete.

From here:

- data is stable  
- each event exists once  
- truth is deterministic  
- all retries have been consolidated  
- late vendor messages have been resolved  
- downstream is safe  

This is the point where the pipeline “switches modes”:

- **Bronze → Silver: streaming**  
- **Silver → Apply changes: streaming**  
- **Gold: batch aggregations**  

This separation is intentional — streaming is perfect for event formation, while batch is ideal for analytics.

---

## 7. Now We Build the Gold Layer (Dashboards Love This)

With a clean, unified deduped event table: unlock_events_unified


…we can build fast, stable, trusted Gold tables.

### User Metrics  
- daily unlock count  
- success rate  
- average latency  
- error classifications  

### Vendor Metrics  
- throughput  
- vendor-side error patterns  
- SLA breaches  
- latency trends  

### Organization-Level KPIs  
- overall success rate  
- volume by region  
- anomaly detection  
- conversion funnel analysis  

These Gold tables:

- are optimized for dashboards  
- are denormalized or pre-aggregated  
- use predictable naming conventions  
- load instantly in Databricks SQL  
- stay consistent across teams  

---

## 8. Lessons Learned

Here are the principles that made the pipeline stable and scalable:

✔ Treat all inputs as **streams**, even if they aren’t  
✔ Perform cleaning and enrichment before merging anything  
✔ Normalize schemas so events can be collated reliably  
✔ Use `APPLY CHANGES` with confidence scoring for dedupe  
✔ End streaming where business events stabilize  
✔ Build Gold tables in batch — it’s simpler and faster  
✔ Align Gold tables to business questions, not raw schemas  

This design lets me plug in new upstream systems without major rewrites, and guarantees that dashboards always reflect consistent truth.

---
