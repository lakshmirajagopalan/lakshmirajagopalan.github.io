---
layout: post
title: "AI, Data Teams, and the New Silos: A Lesson From Cost Optimization"
categories: [Blogging, Data Engineering]
tags: [ai, silo, dataengineering, cost]
seo:
  date_modified: 2025-12-05 18:00:00 +0530
---


Over the last decade, the data ecosystem has evolved faster than anyone expected. Databricks, AWS, and the modern lakehouse stack have made it easier than ever for teams to move independently. And with the rise of AI code assistance, the barrier to building pipelines, writing SQL, or transforming data has practically disappeared.

You no longer need a Spark expert to build a feature pipeline.  
You no longer need a veteran SQL engineer to write a dashboard query.  
You can ask your AI assistant to generate your transformations, joins, or aggregations, and it will produce something that works.

It is empowering — and it also creates a new kind of problem.

Recently, while working on cost optimization across Databricks and AWS, I stumbled upon something that made me rethink how AI is changing team dynamics.

## The Discovery

As part of a platform-wide audit, I analyzed DBU consumption across clusters, pipelines, and dashboards. What I found was interesting, but also concerning:

- Data analysts were creating their own SQL transformations to power business dashboards.
- Data scientists were building their own pipelines inside notebooks to prepare datasets for experiments.
- AI assist made it easy for both groups to build independent data flows without involving data engineering at all.

And for a moment, it made me pause.  
I wondered if years of engineering experience — understanding distributed systems, medallion architecture, cost optimization — was becoming less relevant.

So I stepped out for a walk.

As I walked around the neighborhood — past the usual tea stall, the supermarket, the small restaurants — I caught myself noticing things I normally overlook. My mind wandered to all sorts of questions: Has the industry shifted under my feet? Should I consider doing something different? How far am I from my FIRE number? Will inflation affect my kids' future in ways I'm not prepared for?

At some point, a line I’ve always found calming came to mind — a reminder that uncertainty is natural and things eventually fall into place. It helped me reset.  
I returned to my desk with a clearer head and went back to the cost numbers.

## The Pattern: Parallel Work, Parallel Costs

With a calmer mind, the patterns became obvious.

### Duplicate Pipelines
A data scientist had built a pipeline to compute a dataset for experimentation.  
The same dataset already existed in another engineering pipeline.  
It simply went unnoticed because the teams weren’t talking to each other.

### Inefficient Dashboard SQL
Several dashboard queries were extremely expensive.  
Yes, Databricks SQL caches results, but the underlying logic was heavy.  
Many of those transformations should have been part of a DLT pipeline and materialized as Gold tables.

### Underutilized Spark Capabilities
Some notebooks rebuilt the same joins and transformations repeatedly.  
Not because the authors lacked skill — but because they weren’t thinking in terms of distributed execution or platform optimization.

AI assist made it easy to generate working pipelines, but not optimized pipelines.

## The Missing Ingredient: Interaction

The surprising part was how preventable all of this was.

If a data engineer had been involved earlier, we could have:

- avoided duplicated datasets,
- built shared, incremental pipelines,
- eliminated repeated transformations,
- reduced cluster hours,
- and standardized KPIs across dashboards.

When I started collaborating more closely with the analytics team, the improvements were immediate:

- We reviewed the slowest queries and added them to our daily DLT pipelines.
- Dashboards began loading faster.
- Costs dropped.
- Analysts gained visibility into data modelling and transformations.
- We gained visibility into the business metrics analysts cared about.

The interaction itself created alignment — something that no AI tool could replace.

And yet, even after this, a deeper silo remained.

## The Real Data Silos

The gap between data engineering, analytics, and data science teams isn’t caused by people or skill.

It is caused by workflow divergence.

### Analysts work backwards from business questions.
They need KPIs, dashboards, and answers.

### Data scientists work forwards from raw data.
They need maximum flexibility, full data access, and the ability to iterate quickly.

### Data engineers sit in the center.
We care about correctness, lineage, scale, incremental processing, and cost efficiency.

With AI assist, both analysts and scientists can build pipelines instantly. This accelerates individual productivity but also accelerates **individual silos**.

Because when work becomes easier to do alone, collaboration becomes optional.

## AI Might Deepen These Silos

In smaller startups especially, this pattern is becoming common:

1. Analyst writes SQL with AI assist.  
2. Scientist builds a pipeline with AI assist.  
3. Both turn notebooks into scheduled jobs.  
4. Data appears to be "handled."  
5. Engineering enters later when costs spike or pipelines become unmanageable.

The tools are powerful, but they don’t create shared architecture or shared ownership.

Without alignment, teams drift.

## Where Engineering Still Matters

AI can generate code.  
AI can accelerate exploration.  
But AI cannot (yet):

- design medallion architectures,
- enforce incremental data quality,
- decide grain and modeling for Gold tables,
- plan for performance at scale,
- eliminate duplicated pipelines,
- consider cluster cost implications,
- unify metric definitions,
- maintain governance and lineage,
- reason about end-to-end workflows,
- or build systems that teams can rely on long term.

These still require engineering judgment.

## Breaking the Silo

The answer is not more meetings or more documentation.

Teams need **shared surfaces**, such as:

- A common DLT pipeline for domain-level datasets.
- A unified metrics layer so KPIs aren’t redefined repeatedly.
- Shared lineage (Unity Catalog helps here).
- Regularly scheduled syncs that focus on architecture, not status.
- Data engineers being looped in early, not after costs have exploded.

The goal isn’t control — it’s alignment.

## Final Thoughts: AI Is Not the Problem — Isolation Is

AI code generation is an incredible accelerant.  
It empowers everyone to build faster than before.

But it also enables independent workflows that drift from each other unless teams intentionally converge.

Data engineering doesn’t become irrelevant in this new world.  
If anything, it becomes more important — not for writing code, but for designing systems that scale, remain consistent, and avoid duplication.

Teams can move fast with AI, but building data that lasts still requires collaboration, shared architecture, and thoughtful engineering.

