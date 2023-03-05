---
title: "Data Migration Strategy"
date: 2023-02-24T16:47:15Z
draft: false
---

# Overview

Some practical and technical thoughts on planning and executing a data migration project.\
Focused on consumer data from a legacy system as part of a big bang or phased migration,
where the discovery, data quality, migration rules, business rules and configuration of the target system is ongoing throughout the project.\
Sutiable for an agile, iterative and practical approach.\
\
These ideas will hopefully have some relevance for other data migration projects.\

## Consumer data

For the purposes of example, I'll refer to a business support system for traditional subscription billing.
Something supporting mobile, internet and TV billing.\
I'll refer to a 'customer' as a representation of a consumer or business having a contract for hardware and ongoing subscription fees or installment plan.\
When I refer to 'customer' below I mostly imply the top level data / business entity, plus all entities belonging to them.
e.g.
* contact information
  * name
  * addresses
  * email and phone numbers
  * contact preferences / restrictions
* contracts
  * ongoing and new orders
  * contract conditions
* subscriptions
  * price points and discounts
  * data allowances
  * up/download speed
  * tv packages
* hardware
  * phones
  * sim cards
  * set top boxes
  * routers and cables
* finance and collection
  * invoices
  * collection status
  * payment information e.g. direct debit
  * current balance

In reality there's more to it than this, but you get the idea.

## Legacy system

As with many business support systems in traditional businesses, you'll find they have been around a long time and have an interesting archeology.  Looking at the datamodel, they are unlikely to resemble the consistant / standard patterns you'd expect from a modern application developed against an opinionated ORM.\

Some examples 

* Lack of documentation
  * Often just one key resource knows the database, or part of the database
* Developed by a single key individual who has since moved on
* Developed by multiple individuals under different technology and methodology
  * 4GL with a file based datastore
  * Original developer from mainframe environment
  * Converted to RDBMS
  * New developer favours stored procedures
  * Additional functionality added using web framework de-jour
* Functional rewrites called 'NEW_FUNCTION' e.g. 'NEW_ORDER_SYSTEM'
* Table / column name limitations
  * 8 characters
  * inconsistent abbreviations
  * non-native language which has then been abbreviated
* Denormalisation
  * Related to pre-RDMS heritage, where all parent keys needed to be present to query
  * Poor understanding of database design
  * Ongoing / incomplete domain modeling of business
* Orphaned data
* God tables.
  * Former limitation on number of tables / files leading to tables with multiple uses
  * Creative implementations of many to many relationships
* Custom tables / fields
  * Implemnentation of a generic application extention mechanism using a static set of tables
  * static set of 'customfield001', 'customfield002' columns on main tables
* Lack of constraints
  * If not now then in the past
  * Orphaned data
* Inconsistent datatypes
  * Boolen represented by 'Y' / 'N' or 0 / 1 or NULL / 0 / 1
* Inconsistent use of NULLs
* Inconsistent implementation of soft delete
  * Not consistent with related tables
* Single character based keys for configuration items e.g. types and statuses
  * Has long out grown standard alpabet and now using puncutation









# Notes
* GDPR





# Setting Expectations

## Scope

It's imporant to limit the data migrated to the minumum required to properly represent the current state of the customer at time of migrtation.  Two other areas then fall out of this.

a) What happend to the customer to get them to that state.
b) What long running processes have been triggered on that customer in the source system that would result in an eventual change of state, should the customer be otherwise left alone in the source system.

Reporting system

# Traditional approaches

Two common approaches 


# Model the legacy database

## Challanges


## Approach

Use an ORM to incrementally model the legacy database.  The ORM will need to be feature rich enough to handle inconsistencies, so not one that can only deal with modern approach of surrogate id key on every table.

This allows you to encapsulate complexity, deal with inconsistancies in data types, augment naming etc.

The goal is to create a hirerchical business layer on top of a mess.

The model can enforce basic data quality rules that have been agreed with the subject matter expert.  but it's a grey area where the boundary between errors are thrown by the model, and where the layer of business validations build on top of the model takes over.   There's no hard and fast rule here, but normally I would not model inconsistencies in data that the source application itself would treat in an undefined way.  

e.g. customer with multiple addresses of the same type 'main', 'billing', 'delivery'.  





# Data validation on the legacy data

Where possible get the business to clean the data, rather than add extra migration rules to handle bad data / edge cases.\
In some cases it may only be practical to batch clean limited scenarios as a bulk update as part of a pre-migration step.\
e.g. 0 / 1 / NULL


# Create a business API for the destination database

Include validation of business rules

## Adopting best practice from software development

### Concept of a build
  * Automate repeated tasks
  * Fast feedback loop
### Continuous delivery
### Automated testing
### Source control
  * If it's not in source control then it doesn't exist
### Seperation of concerns
  * Secondary advantage is that it allows other alternative approaches for migration.  Someone else
  to load data into the API layer (SME), output to be queued and bulk loaded. or loaded 'realtime' on an individual basis.

## Performance

### Constraints and Indexes

Assuming you have been able to limit the volume of data e.g. to the customer's current profile and exclude historical and transactional data



## Involving the business

### Value mapping sheets
### Entity mapping
### Share code with migration rules
### Fast feedback loop
### Logging

## Acceptable testing

Billing run comparrison, or other business batch processes 




## Sandbox

{{< highlight python >}}
    class hello_sayer:
        def __init__(self, greeting = "Hello"):
            self.greeting = greeting
        def say_hello(self, name):
            print(f"{self.greeting}, {name}!")

    if __name__ == "__main__":
        h = hello_sayer("Good morning")
        h.say_hello("Jeff")
{{< /highlight >}}
