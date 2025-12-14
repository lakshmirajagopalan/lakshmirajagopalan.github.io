---
layout: post
title: "AI, Data Teams, and the New Silos: A Lesson From Cost Optimization"
categories: [Blogging, Data Engineering]
tags: [ai, silo, dataengineering, cost]
seo:
  date_modified: 2025-12-05 18:00:00 +0530
---

Over the last decade, the data ecosystem has evolved faster than anyone expected. Databricks, AWS, and the modern lakehouse stack have made it easier than ever for teams to move independently. With the rise of AI code assistance, the barrier to building pipelines, writing SQL, or transforming data has practically disappeared.

You no longer need a Spark expert to build a feature pipeline nor a SQL engineer to write a dashboard query.  Just ask an AI assistant to generate transformations, joins, or aggregations, and it will produce something that works.

It is empowering, but it also creates a new kind of problem.

Recently, while working on cost optimization across Databricks and AWS, I stumbled upon something that made me rethink how AI is changing team dynamics.

## The Discovery

As part of a platform-wide audit, I analyzed DBU consumption across clusters, pipelines, and dashboards. What I found was interesting, but also concerning.

- Data analysts were creating their own SQL transformations to power business dashboards.
- Data scientists were building their own pipelines inside notebooks to prepare datasets for experiments.
- AI assistance made it easy for both groups to build independent data flows without involving data engineering.

For a moment, this made me pause. I wondered if years of engineering experience, especially around distributed systems and cost optimization, was becoming less relevant.

I stepped out for a walk.

As I walked around the neighborhood, past the usual tea stall, supermarket, and small restaurants, I caught myself noticing things I normally overlook. My mind wandered to questions about the future: Has the industry shifted under my feet? Should I consider doing something different? How far am I from my FIRE number? Will inflation affect my children’s education in ways I am not prepared for?

At some point, a line I have always found calming came to mind, a reminder that uncertainty is natural and things eventually fall into place. It helped me reset. I returned to my desk with a clearer head and went back to the cost numbers.

## The Pattern: Parallel Work, Parallel Costs

With a calmer mind, the patterns became obvious.

### Duplicate Pipelines  
A data scientist had built a pipeline to compute a dataset for experimentation. The same dataset already existed in another engineering pipeline. This went unnoticed because the teams were not talking to each other.

### Inefficient Dashboard SQL  
Several dashboard queries were extremely expensive.  
Yes, Databricks SQL caches results, but the underlying logic was heavy.  
Many of these transformations should have been part of a DLT pipeline and materialized as Gold tables.

### Underutilized Spark Capabilities  
Some notebooks rebuilt the same joins and transformations repeatedly.  
Not because the authors lacked skill, but because they were not thinking in terms of distributed execution or platform optimization.

AI assistance made it easy to generate working pipelines, but not optimized pipelines.

## The Missing Ingredient: Interaction

The surprising part was how preventable all of this was. If a data engineer had been involved earlier, we could have avoided duplicated datasets, built shared incremental pipelines, eliminated repeated transformations, reduced cluster hours, and standardized KPIs across dashboards.

When I started working more closely with the analytics team, the improvements were immediate.

- We reviewed the slowest queries and added them to our daily DLT pipelines.
- Dashboards began loading faster.
- Costs dropped.
- Analysts gained visibility into data modeling and transformations.
- We gained visibility into the business metrics analysts cared about.

The interaction itself created alignment, something that no AI tool could replace. Even after this, a deeper silo remained.

## The Real Data Silos

The gap between data engineering, analytics, and data science teams is not caused by people or skill but by workflow divergence.

### Analysts work backwards from business questions.  
They need KPIs, dashboards, and answers.

### Data scientists work forwards from raw data.  
They need maximum flexibility, full data access, and the ability to iterate quickly.

### Data engineers sit in the center.  
We care about correctness, lineage, scale, incremental processing, and cost efficiency.

With AI assistance, both analysts and scientists can build pipelines instantly. This accelerates individual productivity, but also accelerates individual silos.

As work gets easier to be done alone, collaboration becomes optional.

## AI Might Deepen These Silos

In smaller startups especially like those I usually work for, this pattern is becoming common.

- Analysts write SQL with AI assistance.  
- Scientists build pipelines with AI assistance.  
- Both turn notebooks into scheduled jobs.  
- Data appears to be handled.  
- Engineering enters later when costs spike or pipelines become unmanageable.

The tools are powerful, but they do not create shared architecture or shared ownership. Without alignment, teams drift.

## Where Engineering Still Matters

AI can generate code and also accelerate exploration. But it cannot yet,

- design medallion architectures,
- enforce incremental data quality,
- choose the correct grain for Gold tables,
- plan for performance at scale,
- eliminate duplicated pipelines,
- understand cluster cost trade-offs,
- unify business metric definitions,
- maintain governance and lineage,
- reason about end-to-end workflows,
- or build systems that remain stable over time.

These require engineering judgment and architectural thinking.

## Breaking the Silo

The answer is not more meetings or more documentation. Teams need shared surfaces, such as:

- A common DLT pipeline for domain-level datasets.
- A unified metrics layer so KPIs are not redefined repeatedly.
- Shared lineage and cataloging.
- Regular conversations focused on architecture, not status.
- Data engineers being involved early, not after cost issues appear.

The goal here is alignment, not control.

## Final Thoughts: Isolation Is a Problem and AI seems to exacerbate it

AI code generation is an incredible accelerant. It has helped me build faster than before. It also enables independent workflows that drift unless teams intentionally converge.

Data engineering does not become irrelevant in this new world.  
It becomes more important, not for writing code, but for shaping systems that scale, remain consistent, and avoid duplication.

Teams can move fast with AI, but building data that lasts still requires collaboration, shared architecture, and thoughtful engineering.