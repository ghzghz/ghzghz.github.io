+++ 
draft = false
date = 2023-04-14T10:43:41+02:00
title = "Data Migration"
description = ""
slug = ""
authors = []
tags = []
categories = []
externalLink = ""
series = []
+++


# Overview

Here are some practical and technical thoughts on planning and executing a data migration project.\
They are focused on consumer data from a legacy system as part of a big bang or phased migration;
where the discovery, data quality, migration rules, business rules and configuration of the target system is ongoing throughout the project.\
ie. sutiable for an agile, iterative and practical approach.\
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

Use an ORM to incrementally model the legacy database.  The ORM will need to be feature rich enough to handle inconsistencies, so not one that assumes a surrogate id key on every table. 

This allows you to encapsulate complexity, deal with inconsistancies in data types, augment naming etc.

The goal is to create a hirerchical business layer on top of what at first looks like a mess.


NOTE: you only need to configure the model to handle reading data, so massive short cuts can be taken.
e.g. my model is littered with one read only properties like this which will only ever work in one direction.

{{< highlight python >}}
    @property
    def is_special_type(self):
        return self.product_id in config.SPECIAL_TYPE_PRODUCT_IDS
{{< /highlight >}}

The model can enforce basic data quality rules that have been agreed with the SME.  However it's a grey area where the boundary between errors thrown by the model, and where the layer of business validations build on top of the model takes over.   There's no hard and fast rule here, but normally I would not model inconsistencies in data that the source application itself would treat in an undefined way.  

Some illustrative examples.

Customers have a collection of addresses.  each address has a 'type' eg. 'MAIN', 'BILLING', 'DELIVERY'

the source application requires every customer to have a 'main' address, so it is reasonably to model this as implied

{{< highlight python >}}
    @property
    def main_address(self):
        return one(address for address in self.addresses if address.type_id == 'MAIN')
{{< /highlight >}}

NOTE: I'm making use of ```more_itertools``` package here, specifically the 'one' and 'only' functions which implement a 'one and only one' and 'one or none' rule.  They can take an extra argument of a custom error message, which can assist in clarity in logging errors.

the source data may have other addresses that we have agreed with the project are not to be used in the migration project.  there could also be bad data of address types that are not valid.   We could exclude both of these cases by simply not modeling either of them.

{{< highlight python >}}
    t_addresses = select([address]).where(
        address.c.type_id.in_(
	    ('MAIN', 'BILLING', 'DELIVERY')
	)
    ).alias()
{{< /highlight >}}

NOTE: This technique for simply 'not seeing' certain data in the source system can be very useful, but in a hierarcical structure also risks missing uncovering important data due to data inconsistencies.  e.g. you may exclude all customers with a status 'ANONYMISED', so you simply don't see those customers, but this implies that there has been no bug or data issue marking customers as 'ANONYMISED' even though they still have items or data that should be migrated.
It's imporant to think about these things and produce data cleaning reports to cover these cases, i.e. data controls to cover any assumptions made in the modeling.


The system allows a customer to have multiple billing addresses.  However it's unclear from a business point of view what the meaning of this is, and there is no business mapping for this into the destination system at the point you are working on the model.   In this case could be ok to implement this restriction in the model, or could be ok to model multiple addresses and handle the issue in a separate validation layer, or as part of the main migration logic.

e.g. either

{{< highlight python >}}
    @property
    def billing_address(self):
        return only(address for address in self.addresses if address.type_id == 'BILLING')
{{< /highlight >}}

or

{{< highlight python >}}
    #
    # model
    #
    @property
    def billing_addresses(self):
        return [address for address in self.addresses if address.type_id == 'BILLING']

    #
    # validation
    #
    def only_one_billing_address(customer):
        billing_address = only(customer.billing_addresses, 'multiple billing addresses found')

{{< /highlight >}}








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


## Misc

* a good log file can be used for post migration tasks.  useful when migration code has stabailised and last minute changes are introduced.
