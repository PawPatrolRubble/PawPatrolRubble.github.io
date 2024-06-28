---
title: how to use apsscheduler in fastapi
date: 2024-06-28 12:00:00 +0800
categories: [halcon,fastapi]
tags: [python, apscheduler, fastapi]
---


### Introduction

In this article, we will learn how to use apscheduler in fastapi.

### Prerequisites

Before we start, make sure you have the following:  

- Python 3.6 or higher
- FastAPI
- APScheduler

### Installing APScheduler  

To install APScheduler, run the following command in your terminal:

```
pip install apscheduler
```

### use sqlite database for scheduler

``` python
from apscheduler.schedulers.background import BackgroundScheduler
from apscheduler.jobstores.sqlalchemy import SQLAlchemyJobStore

jobstores = {
    'default': SQLAlchemyJobStore(url='sqlite:///jobs.sqlite')
}

```

### start scheduler on app startup


``` python
@asynccontextmanager
async def lifespan(app:FastAPI):
    try:
        scheduler = BackgroundScheduler()
        scheduler.configure(jobstores = jobstores)
        scheduler.add_job(task_router.create_default_task_for_every_active_user,"interval",days = 7, id='add_default_task_for_every_active_user',jitter= 8000, replace_existing=True)
        scheduler.start()
        logging.info('scheduler started')
        yield
    finally:
        logging.info('scheduler failed to start')
      
```

### parameters explanations

`
replace_existing=True
` this will replace the existing job with the same id if it already exists, use it if you already have a job with the same id in database.

