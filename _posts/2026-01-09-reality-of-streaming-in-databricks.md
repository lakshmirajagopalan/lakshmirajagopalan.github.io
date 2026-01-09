---
layout: post
title: "Streaming in Databricks: Where Expectations Meet Reality"
categories: [Blogging, Data Engineering]
tags: [databricks, structured streaming, dlt, dataengineering]
seo:
  date_modified: 2026-01-09 19:00:00 +0530
---


Databricks has done something genuinely impressive. It brings **batch, streaming, ML**, and now even agents into a single platform. For many teams, that consolidation is the reason Databricks exists at all. One system, fewer moving parts, less glue code.

But, **streaming in Databricks often required more trade-offs than expected** in my projects.

This post isn’t a critique of Databricks as a platform. I have spent more than 7 years working extensively with Databricks, and it is easily one of the best data platforms. This is a reflection on a few **recurring friction points I’ve noticed specifically around streaming**, after building and operating real pipelines over several years.


## Everyone wants data *now*

Executives don’t think in schedules.  
Product teams don’t want yesterday’s numbers.  
Engineers don’t want to wait hours to see if something they built shows up in the final dashboards.

They want:
- insights that are **always fresh**
- dashboards to reflect reality **immediately**
- data to flow as and when it happens

Streaming isn’t optional anymore but it’s the expectation.

---

## But Databricks does have streaming right?

Yes, Databricks supports streaming. And yes, it’s micro-batch but that isn't the only problem part.

But this issue shows up with **Delta Live Tables (DLT)** and that is not what we would have expected.

If you want:
- always-on ingestion
- near-real-time visibility

you definitely need **Continuous DLT**.

But the moment you need correctness:
- CDC
- deduplication
- `APPLY CHANGES`
- `MERGE INTO`

Continuous DLT simply doesn’t support it. Those features only work in **Triggered DLT**, which means:
- bounded runs
- scheduled execution
- waiting for the next refresh

So, you can only have either **fresh data OR correct state** but not both. And that's a real bummer.

---

## Why this happens (in simple terms)

Continuous DLT optimizes for **flow**:
- tables advance independently
- no global commit boundary

That’s great for append-only pipelines.

But `MERGE` and CDC are **state mutations**. They need:
- coordinated progress
- all-or-nothing semantics
- deterministic retries

DLT only provides that in **Triggered mode**, not Continuous.

---


## The inevitable fallback: Structured Streaming

### Delta Live Tables: declarative and intent-driven

{% highlight sql %}
CREATE STREAMING LIVE TABLE user_updates AS
SELECT *
FROM STREAM(kafka.user_updates);

APPLY CHANGES INTO LIVE.users
FROM STREAM(user_updates)
KEYS (user_id)
SEQUENCE BY updated_at
APPLY AS DELETE WHEN operation = 'DELETE';
{% endhighlight %}


Compare that with the same logic in raw Structured Streaming:

{% highlight scala %}
streamingDF.writeStream
  .foreachBatch { (batchDF, _) =>
    batchDF.createOrReplaceTempView("updates")
    spark.sql("""
      MERGE INTO target t
      USING updates s
      ON t.id = s.id
      WHEN MATCHED THEN UPDATE SET *
      WHEN NOT MATCHED THEN INSERT *
    """)
  }
  .start()
{% endhighlight %}


You lost much of what made DLT appealing:

- no declarative pipelines

- no automatic dependency handling

- no built-in data quality expectations

- weaker lineage and observability

- more operational overhead


---


## This gap is starting to matter more

Databricks is now pushing into:

- agents

- decision-making systems

These systems are inherently incremental and reactive an don’t fit well with scheduled refreshes.


Even ML is shifting:

- features are increasingly streaming

- feedback loops are tighter

- models evolve more frequently

Yet streaming in Databricks still feels secondary compared to batch and ML.


---


## Where this leaves us

I genuinely like Databricks a lot. For batch analytics, incremental processing, feature engineering, and ML, it’s one of the best platforms available today.

But **streaming still feels like the hardest compromise**.

People don’t want *scheduled truth* anymore. They want to see their systems as they are **now**.

Databricks moves fast and consistently delivers. My hope is that streaming eventually gets the same first-class treatment that batch and ML already enjoy, because as data use cases become more real-time and reactive, these gaps become harder to ignore.

