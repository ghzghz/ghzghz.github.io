---
title: "Data Migration Approach"
date: 2023-02-24T16:47:15Z
draft: false
---

# Overview

I've worked on many system migration projects over the years in the arena of traditional consumer subscription and billing: cable and satellite and streaming TV, broadband, IP telephony.
I wanted to write down some observations on one key aspect of these projects, that of data migration.  I intend to describe in detail a technical approach that has worked well for me, as it differs from the more common approaches I have seen and used; although all approaches have pros and cons and no one project is like another.

## Scope

If I'm referencing anything specific in what follows, I have in my mind a business support system for a consumer subscription service for e.g. pay TV.  That is a system or systems handling contracts, products, prices, discounts, service usage, invoicing, accounts receivable, collections, financial reporting, contact centre and technical support, sales pipelines, hardware logistics and asset management.  However, any situation where the business meaning of the data is the key focus should be partially relevant to what follows.

Contrast this to a task of migrating large quantities of logs, event data, transactional data where the focus is more likely to be on technical performance and dealing with synchronization and consistency.  Here my points are likely to have little value.

## Legacy System

It was common to find the main system supporting such a business to be rather old in software terms, custom built or highly configured, possibly developed organically in-house.  A typical example being a system built using a 4GL tool with the database growing out of the incremental development to support new business, rather than being built on top of a well designed data model.  The data model may also pre-date the era of de facto relational database use.  In these cases, there is also unlikely to be good documentation of the data model available and maybe only one subject matter expert (SME).

It is less likely to find cases like this these days, but what I discuss is still applicable to some degree for migrations from systems that were developed in the last decades, you can still often see an archaeological trail in these systems (and in the greater portfolio of systems surrounding them) as they have been developed and heavily customised over the years.  The business case to migrate from these systems may well come from business accusation and mergers, rather than a need to migrate away from legacy technology and the business risk of in-house development.

# High Level Good Practice

Here I list some high level bullet points of what I believe to be good practice.  Most of these will be fairly standard.  However the last few of these points cover a technical approach that differs from the more common batch ETL approach used for data migrations.  I'll go into more detail in all the points below in future posts, especially on the technical approach where I'll cover pros and cons.

## General 

* Set expectations on scope of migration from the start
* Negotiate from position of taking minimal set of data representing the current state of the root business entity
* Separate core requirements from 'nice to have'
* Use other systems, datastores and temporary business processes to create workarounds for non-core business requirements e.g. reporting, access to historical data, pending transactions
* Involve the business in a hands-on way
* Publish and consume mapping sheets.  Make business own these and other assets
* Enumerate and publish migration rules
* Create an automated build process
* Apply good software development and delivery practices
* Release early, release often
* Throw out deviations on an error list, don't migrate bad data
* Automate data cleaning lists and bad data reports and create a simple process for enhancing them
* Identify large business processes to use for parallel validation of migrated data, e.g. billing run
* Expect migration development to be part of continuous discovery process
* Expect continuous refinement of migration logic
* Optimise for a tight feedback loop to enable the above

## Technical Approach

* Create a business model on top of the source system
* Create a business model on top of the destination system
* Separate concerns
* Develop a migration process that will migrate one root business entity at a time
* Scale this process into batches
* Output and publish a detailed and structured log file
