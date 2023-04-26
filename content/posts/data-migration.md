---
title: "Data Migration"
date: 2023-04-17T16:15:19+01:00
draft: false
----

## Overview

I've worked on many system migration projects over the years in the arena of traditional consumer subscription and billing e.g. cable and satellite and streaming TV, broadband, IP telephony.
I wanted to write down some observations on one key aspect of these projects, that of data migration.  I will describe a technical approach that has worked well for me, as it differs from the more common approaches I have seen and used; although all approaches have pros and cons and no one project is like another.

### Scope

If I'm referencing anything specific in what follows, I have in my mind a business support system for a consumer subscription service for e.g. pay TV.  That is a system or systems handling contracts, products, prices, discounts, service usage, invoicing, accounts receivable, collections, financial reporting, contact centre and technical support, sales pipelines, hardware logistics and asset management.  However, any situation where the business meaning of the data is the key focus should be partially relevant to what follows.

Contrast this to a task of migrating large quantities of logs, event data, transactional data where the focus is more likely to be on technical performance and dealing with synchronisation and consistency.  Here my points are likely to have little value.

### Legacy System

It was common to find the main system supporting such a business to be rather old in software terms, custom built or highly configured, possibly developed organically in-house.  A typical example being a system built using a 4GL tool with the database growing out of the incremental development to support new business, rather than being built on top of a well designed data model.  The data model may also pre-date the era of de facto relational database use.  In these cases, there is also unlikely to be good documentation of the data model available and maybe only one subject matter expert (SME).

It is less likely to find cases like this these days, but what I discuss is still applicable to some degree for migrations from systems that were developed in the last decades, you can still often see an archaeological trail in these systems (and in the greater portfolio of systems surrounding them) as they have been developed and heavily customised over the years.  The business case to migrate from these systems may well come from business accusation and mergers, rather than a need to migrate away from legacy technology and the business risk of in-house development.

## High Level Good Practice

Here I list some high level bullet points of what I believe to be good practice.  Obviously the relevance of these points depends on the size and complexity of the project, they become more relevant within a serious of migration projects e.g. multi company, multi country or multiple systems.

Most of these points will be fairly standard.  However the last few of these points cover a technical approach that differs from the more common batch ETL approach used for data migrations.  I'll go into more detail in all the points below in future posts, especially on the technical approach where I'll cover pros and cons.

### General 

* Set expectations on scope of migration from the start
* Negotiate from position of taking minimal set of data representing the current state of the root business entity (i.e. customer)
* Separate core requirements from 'nice to have'
* Use other systems, datastores and temporary business processes to create workarounds for non-core business requirements e.g. reporting, access to historical data, pending transactions
* Involve the business in a hands-on way
* Publish and consume mapping sheets.  Make business own these and other assets
* Enumerate and publish migration rules
* Create an automated build process
* Apply good software development and delivery practices
* Release early, release often
* Identify large business processes to use for parallel validation of migrated data, e.g. billing run
* Expect migration development to be part of continuous discovery process
* Expect continuous refinement of migration logic
* Optimise for a fast feedback loop to enable the above
* Throw out deviations on an error list, don't migrate bad data
* Automate data cleaning lists and bad data reports and create a simple process for enhancing them

### Technical Approach

* Create a business model on top of the source system
* Create a business model on top of the destination system
* Separate concerns
* Develop a migration process that will migrate one root business entity at a time
* Scale this process into batches
* Output and publish a detailed structure log file

## Detailed Good Practice

### General

#### Set expectations on scope of migration from the start
#### Negotiate from position of taking minimal set of data representing the current state of the root business entity
#### Separate core requirements from 'nice to have'
#### Use other systems, datastores and temporary business processes to create workarounds

If you ask various parts of the business what they need to be migrated into the destination system they will likely say 'everything'.  This is clearly not in anyone's interest.  The more data (broad and deep) that you take across the more bad data you will move, the more data anomalies you will have, the more mapping and implementation in the destination system you will need to do, the more instances of having to 'fake' something that just couldn't exist in the destination system due to concepts in the source and destination being wildly different.

 e.g. systems may represent a set of active subscription products in very different ways, one may be bundled, the other components.  Mapping and migrating the current set of active products will be a hard enough task, how much value is there in trying to also map products the customer previous had and how the transitions between those products within the source system would have looked like if an equivalent had existed in the destination system at that time?
 i.e. to try and make the customer look as if they have always existed in the destination system?

 Very little value I would argue.  In which case the question to the business is to ask what purpose they are trying to achieve.  If this turns out to be historical reporting, or trend analysis or business intelligence, then the reporting systems that are currently used can continue to be used for this and allowances will have to be taken to 'merge' this data if required, or to accept some loss of fidelity for certain segments of customers for a certain time period.

 Another request may be for financial data.  It's important to keep financial data around for both customer service and regulatory reasons, but this does not imply that it needs to be available in a single core system.  In fact trying to move financial records created by a system that no longer exists and pretend that it was created by a system that didn't create it is not something that will be looked upon favourably in a financial audit.

 Another request is to handle pending transactions, requests for payments that have been made and not yet been processed, orders for new services that have been made and sent out to external systems, but have not yet been closed.
 In these cases it is necessary to be more accommodating, as this represents normal ongoing core business.  Although for more niche business processes it would be better to try and close open transactions in the source system before migration. e.g. by pausing the business process that creates them, creating new orders directly in the destination system, paying out or writing off certain types of pending financial transactions.  In other cases and in the case of a big bang migration, it could be that a date is chosen for the migration to minimise the number of pending transactions, e.g. if the invoices are created in one batch in the middle of the month, and most money is received by the end of the month, it may make sense to migrate early in the month when majority of the customers have no open invoices (although you can argue here that if you are going to migrate one open invoice, then you may as well migrate a million)

 All of these decisions take time and require by in and work from the business, so it is important to start early.  This is why I believe the correct approach is to be clear on a minimal set of data to take from the start, and be willing to negotiate to increase that set if and when the business has a good argument on why it cannot be handled in any other way.

 A key thing to bear in mind when holding this minimalist position, is that much of the data is useful only for a short period after the migration, it's usefulness quickly fades with time.  No matter what is done in the migration, all parts of the business will need to be aware for the first 3, 6, 12 months that a migration has taken place, and will anyway need to make allowances in their normal processes.

#### Involve the business in a hands-on way
#### Publish and consume mapping sheets.  Make business own these and other assets
#### Enumerate and publish migration rules

It's important for the business to play an active role in the migration, there are ways to encourage this through delivery process (which I will cover below) and through publishing and ownership of migration assets.

In many cases migration mapping of master data (products, statues, types etc) can be encapsulated in Excel sheets.  These should be created and hosted on a document management system (or even just a shared file system) that the business has access to.
* It should be made clear that the data in these sheets is owned by the business and they should be the ones making changes to them (or you making changes that they have requested).
* They should be created in a user friendly format, i.e. from business name to business name rather than (or in addition to) ID to ID.
* They should be ingested directly into the migration tool / code, to avoid manual error, and to be able to demonstrate the version of assets used for a test migration.

Many mappings will not be 1-1, so they may also need to represent:
* multidimensional mappings
* mappings from an ignored value (I use the text 'ANY')
* mappings from and to a NULL value (I use the text 'NULL')
* mappings indicating the entity should not be migrated (I use the text 'DNM' for Do Not Migrate)
* mappings indicating the mapping is not yet decided (I use the text 'TBD')
* constraint based mappings (rare, but some product set mappings may be described in this way)

The format and dimensionality of the mappings may well change as the project progresses and more things are discovered.  I often find that merging or separating mappings happens during this phase.

There will inevitably be mappings that are special cases.  Depending on how 'special' these are, it may be necessary to create a more complicated mapping, or more often than not just create a separate migration rule which can be written in text in the relevant place in the mapping sheet.

General migration rules, special cases, data quality rules etc, should ideally be enumerated and published.  In the past I've tried to use a tool like Excel or Jira to capture and maintain these.  However, I rarely get to the end of a project without this approach having been abandoned and migration rules just being accepted directly into the migration logic with no hard link back to decisions / requirements etc.   There is always an issue of process over progress, and that is especially true in a 'disposable' project like a data migration.  Multiple migrations, phased migrations, on going realtime migrations, complex 3rd party relationships and data sources; all these push towards enforcing a process, but even then the process should be light-weight as possible or else it will break and may as well not exist.

#### Create an automated build process
#### Apply good software development and delivery practices
#### Release early, release often
#### Identify large business processes to use for parallel validation of migrated data, e.g. billing run
#### Expect migration development to be part of continuous discovery process
#### Expect continuous refinement of migration logic
#### Optimise for a fast feedback loop to enable the above

Data migration development will understandably be seen as temporary and disposable, in the case of a single big bang migration then it will be developed to only be executed once.  However, this does not mean that it will not benefit greatly from the application of good software development and deployment practices.  Knowledge, requirements and even source and destination systems will evolve throughout the project, so having processes that can control and measure constant changes will pay back massively. 

It's important to execute data migration to test environments as soon as possible, and as often as possible.  With large amounts of data, difficult to provision destination environments etc, it is not always possible to have a completely smooth automated deployment pipeline. However this should be the goal.  Work with the infrastructure teams to get the best possible setup and automate everything that is practical to do so.

Work towards an ideal of a single build script that will:
* take a static copy of source data
* provision a temporary copy of a destination environment
* execute any pre-migration steps on the source data
* run data validation checks on source data
* migrate the entire set of data or a subset of data
* execute automated tests on the destination environment using any system APIs available
* execute business reports on the destination environment
* execute system batch processes on the destination environment
* report the output of above against the same reports and batch processes on the source system
* publish the reports to testers and stakeholders

In reality you will compromise on many of these for practical reasons.   Many of these will remain as manual steps, but should still be written down in a single list that can be executed in order, therefore reducing the possibility of manual steps breaking the idea of a repeatable build.

Other software development practices you should aim for where possible:
* version control of migration scripts
* separate development and deployment environments
* unit tests of migration logic
* integration tests of migration logic

In terms of releasing early and releasing often.   This is to:
* motivate stakeholders to have hands on involvement in the migration
* produce KPIs to measure progress and gain confidence
* fast feedback loop to expose issues and questions and new requirements through discovery


#### Throw out deviations on an error list, don't migrate bad data
#### Automate data cleaning lists and bad data reports and create a simple process for enhancing them

I have worked on data migration projects where all data is migrated in some shape or form, no matter of its relevance or quality.   The job is then to identify deviations in the destination system (both relational and business deviations), and to clear them up or more likely ignore them.

I find this is the wrong approach to begin a migration project with.   I will look for deviations, no matter how small, both in an automated validation step as part of the migration, or through assumptions made in the migration logic, and throw these entities out onto an exception list.   I then publish this exception list to the business and put the ownership on them to deal with the issue through cleaning, new requirements etc.  Often this process will uncover new information about the data / business processes.  

In practical terms it is necessary to be flexible with this approach later on in the project.  I maintain a separate level of exception as a warning list, which I will demote some business errors to if the business has agreed a process to deal with the outcome in the destination system.   If it comes to the date of the actual migration and some of the exceptions have not been resolved, then I will push data through no matter what, but rejecting bad data as a first approach minimises the risk of getting to this point.

Data cleaning process should be flexible and able to be inspected and added to with ease.  A heavy process of report writing and publishing is not ideal.   In the past I have created an ad-hoc reporting system driven by structured pages on a wiki containing SQL, which is run every night and published in excels emailed to the relevant groups.  This means that when a data quality issue is identified, then there is no overhead to adding a control to clean it and discuss how it can be prevented in the future.


## Technical Approach

### Overview

Typical approach for data migration is to work on a table (or collection of tables) by table level and use a ETL tool or batch SQL (insert into, update where).   This approach has plenty of advantages, not least in terms of performance, but also in terms of skill sets both within the business, and in terms of bringing people in to work on a migration project.

An ETL tool will often also bring many other advantages through its inbuilt feature set including reporting, version control, ingestion of multiple data sources including Excel mapping tables, repeatable and scalable execution environment.  Things that will need to be developed separately if using SQL / procedural SQL.

The issue I have always had with these approaches is the 'impedance mismatch' between migration rules which are described at the business level against the root entity, vs a table by table implementation.  i.e. the way that migration rules are obscured within the transformation logic.

To give a simple example.  A customer with a credit balance should have a different customer status.  i.e. the balance of the customer should be a dimension in determining the customer status.   In a table by table approach then:
 1. the customer table will be migrated to an equivalent customer table with a default status
 2. the account will be migrated to an equivalent table
 3. the customer table in the destination system will be updated based on the account table

This works for simple mapping rules, but quickly falls apart for more complex rules, where rules overlap each other, where frequent changes are requirement to mapping rules, or in terms of clarity and traceability i.e. 'why did the customer get end up with this status?'

Another approach to the above would be to include a join to the account table in the customer table migration, but then this starts to fall apart when migration logic is applied to the account table, as the logic needs to be duplicated and maintained.

Another simple example would be that migrating the account table needs to take into account the fact that the customer record was migrated in the first place to avoid orphaned data.  Again this manageable in the simple case with a join to the already migrated destination customer table or a post migration step of deleting orphaned records, but can quickly spiral out of control in terms of maintainability and traceability. 

A further disadvantage is that at any point in time during the migration, the destination system is in an inconsistent state.  Any crash in the migration will need to be manually corrected, and the flexibility to repurpose the migration to run on top of a live environment, or to be used to migrate batches or single entities is not there.


### Hierarchical migration of single root entity

I try not to use a table by table approach, but instead take a single root entity (in my example a customer) and all the whole hierarchy of entities sitting below that root entity, extract, transform and load that entity into the destination system in a single atomic step using procedural migration code.

There are many advantages to this approach, but also some disadvantages.

#### Performance

I used to worry about this more than I think I needed to.  Clearly querying a hierarchical set of data and inserting it is slower than a batch operation that can be optimised on the server, but how fast does it need to be?  The overall migration time including downtime on destination system, dealing with post migration clean-up when re-enabling constraints and building indexes, ease of testing and auditing, quality of migration etc. plays a role in this equation.

I have found that some simple optimisations to create a batch of root entities to be migrated in one go, and then paralleling these batches has given acceptable overall migration times.  This is especially true with advances in hardware over the years with SSDs and improved network speeds.  In addition some pre migration steps to batch exclude non-migratable entities, possibly some custom indexes, an efficient batch insert process, and possibly a hybrid approach for migrating a few large but simple non-core tables; all have been enough to give confidence that this approach can work for systems with a few million root entities.

#### Development effort

Comparing to writing raw batch SQL, then the issues are similar.  Everything you need in terms of development and execution environment needs to be hand-built, or adapted from a previous project.
This differs compared to an ETL tool, where many of these things will be included for free.

Another issue is in house or contracted knowledge, ETL tools and even batch SQL are more likely to be familiar to more people than code developed in a specific programming language often by a single person.  There is also likely to be a push from the business to use tools that they have invested in for other parts of the business.











