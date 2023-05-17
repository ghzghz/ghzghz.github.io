---
title: "System Migration Projects"
date: 2023-05-17T21:18:39+01:00
draft: false
---

Some note and strategy on running complex system migration project.


1) Don't underestimate the complexity of the project

A legacy business will be complicated in many different dimensions both process and organisational.  It will not be possible to comprehend this complexity up front, the complexity will reveal itself during the project.  Therefore what matters more is that you have the processes, tools and decision making structure in place to quickly reveal, describe and manage this complexity.

2) Simplicity

Set some overriding goals / concepts / principals to abide by.   These will help the decision making process.  Simplicity should be a key one of these.  Whenever possible argue for a simple and clean solution and force anyone arguing for a complex solution with edge cases to justify a real business or compliance need behind it.

3) Convergence

Another overriding goal.  Wherever possible the goal should be convergence with other projects.  To facilitate this this means the project team understanding the existing solutions and buying into the advantages of convergence.  They should have a stake in this by working with existing teams and gaining confidence from a proven solution.

4) Ownership

It's critical that the resources on the project feel ownership of the solution.  Outsourcing critical parts of the project will break this and cause problems in quality, attention to detail throughout the project and cause problems with handover to BAU.  The implementation and configuration of the system is a key part of this.  The project team should be empowered to make decisions that affect their own roles in BAU, and should build up confidence with the wider business so that they can promote their decisions for areas that are with other roles in BAU.

5) Isolate the critical path of the project from commercials

It's important not to get stuck and not to have to make unnecessary compromises due to push back and appeals to commercial relationships .  Wherever possible the critical path delivery of the project should be under local governance.  This means assigning roles and resources between local team and supplier in a way that you can not be held up by the supplier.  If you are using the supplier for anything in the critical path, then it should something you could theoretically take ownership of.  If you are relying on the supplier for something that is part of the main iteration and workflow of the project (configuration or migration) then make sure there is a clear SLA with them and that they are aligned with the project velocity.

5) Focus on BAU

Throughout a project the solution will be sliced and diced up in unfamiliar and unhelpful ways.  Possibly for technical reasons, possibly because the supplier will influence how the solution is presented according to how they see the product.  It's important always to keep working towards the end goal, which is running business processes end to end in a live environment with the resources who will be responsible for ordering, running and receiving the output from these processes.

A starting point for should be draft work instructions that can be continuously improved throughout the project.

6) Definition of "Done"

It's important to agree on what 'done' means for a project task.  Leaving things 90% done will mean ending up with a lot of lose ends and difficult reporting structure.  Better to complete a task and create another task for parts that cannot be closed than leave a task open, or close a task without nailing it down.  The definition will change depending on the context, so this will be a working definition.

7) Quality from the supplier

There are cases where the supplier is sloppy in their delivery or their product.  The 'broken window' theory can help here where even minor things are documented and reported so that the expectation is such that you will not let anything pass and they will adjust their behaviour to match.

8) Privacy by design

Privacy and GDPR in general can often not seem important in projects like this.  Often because it is too difficult and complex to trace this throughout the project.  Doing things right from the start normally solves all issues automatically.  e.g. the only data that can be seen by the development and test migrations is data anonymised data without any PII.  Getting this right from the start also leads to respect and good practice for BAU.

9) Automation

To handle a complex project, you need to build processes and tools that allow you to iterate fast and iterate safely.  The idea of making a change and having to wait days or weeks to agree with the supplier to plan and then deliver another migration, then having an environment delivered where you cannot for sure guarantee the source and ancestry of configuration and data and software, or have to make manual changes to before you can use it.  This is a big smell.   Software development practices of continuous delivery, continuous integration, continuous testing, source control, build pipelines should be followed and tools and scripts required to deliver this should be put in place from the start (they can of course be improved as the project develops).   The eventual goal should be that you can clean data in the source system, make a configuration change in an excel sheet, update a migration rule, press a button, go to lunch and when you come back a new environment has been provisioned, migrated to, validated (including invoice runs and reports) and tested.

10) Recording of issues and tasks

All issues and tasks that are open shall be recorded in a ticketing system (e.g. Jira).  No email chains with incomplete threads and no clear ownership or resolution.  If an issue breaks into multiple threads then multiple tickets shall be created.  It's important to be quite militant on this from the start and insist on issues not being recorded in this way do not exist (you can start to cut and paste email chains into ticket comments to judge the correct behaviour).  It's important that all streams of the project are using the same system.  This system will form part of a project members' main workflow, and will also be used for reporting.

11) Automatic integration with supplier's ticketing system

If supplier isn't able to 100% commit to use project ticketing system, then set up automation to push tickets back and forward.  Thinking you can do this manually is dangerous and as detail and immediacy will be lost and then other means (email) will be used to short circuit the system.  It's also important that all parties have a common and agreed view of what is open and what is to be worked on.

12) Favour lightweight working documents

Documentation should be light, should not be wasteful and should be alive.   Having the perfect documentation should not be the enemy of having no documentation.  A placeholder is better than nothing.  Most documents should be seen as working documents that are improved / corrected as part of normal workflow and part of definition of done.  Confluence is a good tool for this.  Emailing word documents around should be discouraged.  Refuse to accept it, or nudge to good behaviour by putting it in confluence and returning the link.  Documents for future stages of the project (e.g. rollout) should be opened early and built up and refined over time.  Often having such documents facilitates a home for information gathered during the project and makes sure it isn't lost.

13) Shed light on all aspects of the project

Publishing on confluence and jira is important way to get some measurement of progress and quality.   Nudge people to good practice.  Create dashboards on Jira that help show something meaningful and encourage momentum through gamification, while not deliberately leading stakeholders to focus on things that are not important.

14) Focus on Test cases as the basis of the project

Following on from focus on BAU.  The end to end BAU routines should form test cases and test cases should form work instructions.  Test cases should be exposed outside a core test team including the supplier.  They should also be logged in Jira as tickets and follow a test case workflow so that progress can be monitored and issues can be linked.  Test cases should be executed by the project team, and then executed by the business.  This execution by the business forms an important part of handover and of ownership.  Test cases should be seen as working documents (it should be possible to correct or clarify the steps), and it should be possible (indeed enforced as part of definition of done) to create new test cases throughout the project.  Any extra test cases may or may not form part of formal UAT.  Wherever possible test cases should be automated and added to the pipeline, but must always be manually runnable.

15) Parallel Run as part of UAT

A critical part of the UAT should be a parallel run between the existing live system and the new system.  The period of a month shall be mapped out in terms of important business processes.  The tools for validating (and comparing to old system) should be developed as these will form part of stabilisation.

16) Don't get stuck

Always try and move forward.  Don't make decisions too early - wait until you have more knowledge, but don't kick them forward due to lack of decision.  Always have a MVP in mind.

17) Have an Exit plan for any MVPs

Any workarounds / scripts etc should not end up as normal BAU.  If you have to make compromises to move forward, then have the correct solution planned.  Possibly for a future phase.

18) Minimise environments

Aim to have as few test / dev environments as possible.  Possibly only one.  Integrate early and often to this environment.  This minimises environments deviating.  It also helps keep environments 'alive' which helps with integration testing and development and helps with a sense of momentum and confidence.
Minimising environments poses some challenges e.g. creation of test data, validation of financial reports or invoice runs.  But these can be solved with some careful planning upfront and the benefits can be far greater than any initial discipline required.

19) Identify and secure key resources from the business

System migration can be a unsettling moment in the careers of many people.  People have an opportunity to assess their sometimes long standing positions and take the opportunity of having to give up what they have always know to look elsewhere.   It's important to secure any critical people from the business.  This can be related to some project level bonus if this is appropriate, personal development goals, something for their organisation to suggest.  Key resources MUST be taken out of their normal BAU roles and backfilled.  This demand is also an important part of having the local business take the project seriously.   Identify at least one resource from the call centre operation and arrange to second them for the project.  It's critical to have CS input through the project, and in addition this person shall be responsible for training material and delivery as part of rollout.

20) Focus on Rollout

rollout plan covering the months before and after migration should be created as a working document early in the project.
more detailed plan covering activities from all streams over the migration weeks should also be created.
the aim should be to rehearse this plan at least once under strict execution.  more informal execution should be part of the parallel run.

21) Set the tone from the start

Convey the goals and importance of the project with presentation to stakeholder and all other critical business functions.  All business functions should be aware that project is going on and should be involved wherever possible.  They should understand the concept of freezing new business, of the channel the can use to report their own projects so they can be assessed for risk and impact.

22) Start the cleanup of the source system from the start

Automate this process wherever possible.  A set of cleanup lists should be set to run every night.  This list should be published, easy to refine and add to.

23) Partition the data migration process

Even if the same person is ultimately given the role of migrating from source to destination, it still make sense to see this as two separate tasks.
Creating a staging schema that represents a business definition of entities.  Customers, products etc.  And the tools to : validate this schema against all configured business rules, migrate from this schema into the destination schema.. including all the technical specifics unrelated to business.
This staging environment and migration tool can then be reused for different source systems.
This then allows partitioning of the data migration into a role that understands the source system and how to map it to a business level, and a role that understands how a business level is represented in the destination system.  It's critical that the execution of both these processes can be automated.

24) Product migration error lists

Rather than put bad data into the destination system and then try to identify it (through broken integrity rules, broken business rules or incorrect billing) then fail early and only push compliant data.  This helps focus the effort to clean and fix data / rules as the 'unable to migrate customers' category is a metric that can be understood throughout the project (other metrics like 'number of broken foreign keys' or 'percentage of bills differing by over 1 euro' are not easy to track or explain)



