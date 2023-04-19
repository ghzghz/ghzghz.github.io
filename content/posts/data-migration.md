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
* Throw out deviations on an error list, don't migrate bad data
* Automate data cleaning lists and bad data reports and create a simple process for enhancing them
* Identify large business processes to use for parallel validation of migrated data, e.g. billing run
* Expect migration development to be part of continuous discovery process
* Expect continuous refinement of migration logic
* Optimise for a tight feedback loop to enable the above

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

If you ask various parts of the business what they need to be migrated into the destination system they will likely say 'everything'.  This is clearly not in anyone's interest.  The more data (broad and deep) that you take across the more bad data you will move, the more data anomalies you will have, the more modelling you will need to do, the more instances of having to 'fake' something that just couldn't exist in the destination system due to concepts in the source and destination being wildly different.

 e.g. systems may represent a set of active subscription products in very different ways, one may be bundled, the other components.  Mapping and migrating the current set of active products will be a hard enough task, how much value is there in trying to also map products the customer previous had and how the transitions between those products within the source system would have looked like if an equivalent had existed in the destination system at that time?
 i.e. to try and make the customer look as if they have always existed in the destination system.

 Very little value I would argue.  In which case the question to the business is to ask what purpose they are trying to achieve.  If this turns out to be historical reporting, or trend analysis or business intelligence, then the reporting systems that are currently used can continue to be used for this and allowances will have to be taken to 'merge' this data if required, or to accept some loss of fidelity for certain segments of customers for a certain time period.

 Another request may be for financial data.  It's important to keep financial data around for both customer service and regulatory reasons, but this does not imply that it needs to be available in a single core system.  In fact trying to move financial records created by a system that no longer exists and pretend that it was created by a system that didn't create it is not something that will be looked upon favourably in a financial audit.

 Another request is to handle pending transactions, requests for payments that have been made and not yet been processed, orders for new services that have been made and sent out to external systems, but have not yet been closed.
 In these cases it is necessary to be more accommodating, as this represents normal ongoing business.  Although for more niche business processes it would be better to try and close open transactions in the source system before migration. e.g. by pausing the business process that creates them, creating new orders directly in the destination system, paying out or writing off certain types of pending financial transactions.  In other cases and in the case of a big bang migration, it could be that the date is chosen for the migration to minimise the number of pending transactions, e.g. if the invoices are created in one batch in the middle of the month, and most money is received by the end of the month, it may make sense to migrate early in the month when majority of the customers have no open invoices (although you can argue here that if you are going to migrate one open invoice, then you may as well migrate a million)

 All of these decisions take time and require by in and work from the business, so it is important to start early.  This is why I believe the correct approach is to be clear on a minimal set of data to take from the start, and be willing to negotiate to increase that set if and when the business has a good argument on why it cannot be handled in any other way.

 Here is typically where I would start:
 * Former customers who are no longer customers, they have no active products and no pending finance, will not be migrated
   * Typically these customers will have been anonymised, or will be shortly anonymised
   * If there is a key sales process of trying to 'win back' some of these customers, then there may be push back



## Technical Approach

### Overview