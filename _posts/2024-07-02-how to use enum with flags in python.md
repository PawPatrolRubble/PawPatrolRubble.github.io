---
title: "How to use enum with flags in Python"
date: 2024-07-02 12:00:00 +0800
categories: [python]
tags: [python, enum, flags, intFlag]
---

In Python, we can use the `enum` module to create enumerations and use them in our code. Enums can be used to represent a set of related values, and each value can have a name and a value. Enums can also be used to define flags, which are bitwise combinations of values.


## Creating an Enum

To create an enum, we can use the `Enum` class from the `enum` module. Here's an example:

```python
from enum import IntFlag


class TaskStatus(IntFlag):
    draft = 1
    submitted = 2
    accepted_by_target_dept = 4
    accepted_by_production_line = 8
    rejected_by_target_dept = 16
    rejected_by_production_line = 32
    discarded = 64
    
    # Combined statuses
    # accepted status is when it is accepted by both dept and line
    accepted = accepted_by_target_dept | accepted_by_production_line
    # a rejected status is when it is rejected by either dept or line
    rejected = rejected_by_target_dept | rejected_by_production_line
```

### how to check status that either of them is true

```python

current_status = TaskStatus.rejected_by_target_dept
# check if current status is rejected, it should return True when any of the rejected flags are set
print((current_status & TaskStatus.rejected) >0)

```

### how to check status that both of them are true

note that in the if condition，we compare the result to the status itself.

```python
current_status = TaskStatus.accepted_by_target_dept
# check if current status is accepted, it should return True when both accepted flags are set
print((current_status & TaskStatus.accepted) == TaskStatus.accepted)

```