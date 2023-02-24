---
title: "Data Migration Strategy"
date: 2023-02-24T16:47:15Z
draft: true
---

Some practical and technical thoughts on how to execute a data migration project.\
Specifically consumer data from a legacy system as part of a big bang or phased migration,
where the discovery, data quality, migration rules, business rules and configuration of the target system is ongoing throughout the project.\
Some of these approaches may be relevant for other data migrations.

## Adopting best practice from software development

* Continuous delivery
* Concept of a build
  *    Automate repeated tasks
  *    Fast feedback loop
* Automated testing
* Source control
  * If it's not in source control then it doesn't exist
* Seperation of concerns

## Involving the business

* Value mapping sheets
* Shared code for migration logic
* Fast feedback loop
* Logging




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
