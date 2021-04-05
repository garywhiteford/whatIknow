# The Complete Hands-On Introduction to Apache Airflow

## Description

Learn to author, schedule and monitor data pipelines through practical examples using Apache Airflow. 

## Notes

### 2. Getting Started with Airflow

#### 7. What is Airflow?

Timestamp    |  Description
------------ | --------------
[0:34][001]  |  Marc's definition: Airflow is an **orchestrator** allowing you to execute your tasks at the right time, in the right way, in the right order.
[1:04][002]	 |  Airflow data pipelines are coded in **Python** and everything you can do in Python, you can do in your data pipelines.
[2:30][003]	 |  Other benefits: 1) **Dynamic** (Python code); 2) **Scalability** (limited only by system resources); 3) **UI** (beautiful and useful); 4) **Extensible** (customizable)
[2:35][004]	 |  Core components: 1) **Web server** (Flask server with Gunicorn serving the UI)
[2:48][005]	 |  Core components: 2) [Most important] **Scheduler** (Daemon in charge of scheduling workflows [your tasks])
[3:04][006]  |  Core components: 3) **Metastore** (Database where metadata are stored)
[3:32][007]  |  Core components: 4) **Executor** (Class defining how your tasks should be executed -- Kubernetes, Celery or locally)
[4:17][008]  |  Core components: 5) **Worker** (Process/sub-process executing your task)
[4:52][009]  |  **DAG (Directed Acyclic Graph)** is a [graph](https://en.wikipedia.org/wiki/Graph_(discrete_mathematics)) with *directed edges* and *no loops* (in Airflow, a DAG is a data pipeline).<br>https://airflow.apache.org/docs/apache-airflow/stable/concepts.html
[6:17][010]  |  An **Operator** is a wrapper around the task or thing that you want to achieve.
[6:33][011]  |  Three types of operators: 1) **Action Operators** (execute functions or commands).
[6:50][012]  |  Three types of operators: 2) **Transfer Operators** (transfer data between a source and destination).
[7:03][013]  |  Three types of operators: 3) **Sensor Operators** (wait for something to happen before moving to the next task).
[7:26][014]  |  Three other concepts: 1) **Task** (an operator) and 2) **Task Instance** (a triggered task).
[7:47][015]  |  Three other concepts: 3) **Workflow** (combination of all of the concepts -- a DAG with operators with tasks with dependencies).
[8:05][016]  |  What Airflow is not? Airflow is **NOT a data streaming solution, NEITHER a data processing framework**. Don't try to process something every second. It is not Spark. Don't try to process terabytes of data. Be careful of Pandas in data pipelines (processing terabytes of data will not work -- memory overflow errors). Can use the [Spark Submit operator](https://airflow.apache.org/docs/apache-airflow-providers-apache-spark/stable/operators.html#sparksubmitoperator).

[001]: https://www.udemy.com/course/the-complete-hands-on-course-to-master-apache-airflow/learn/lecture/11918638?start=34#notes
[002]: https://www.udemy.com/course/the-complete-hands-on-course-to-master-apache-airflow/learn/lecture/11918638?start=64#notes
[003]: https://www.udemy.com/course/the-complete-hands-on-course-to-master-apache-airflow/learn/lecture/11918638?start=150#notes
[004]: https://www.udemy.com/course/the-complete-hands-on-course-to-master-apache-airflow/learn/lecture/11918638?start=155#notes
[005]: https://www.udemy.com/course/the-complete-hands-on-course-to-master-apache-airflow/learn/lecture/11918638?start=168#notes
[006]: https://www.udemy.com/course/the-complete-hands-on-course-to-master-apache-airflow/learn/lecture/11918638?start=184#notes
[007]: https://www.udemy.com/course/the-complete-hands-on-course-to-master-apache-airflow/learn/lecture/11918638?start=212#notes
[008]: https://www.udemy.com/course/the-complete-hands-on-course-to-master-apache-airflow/learn/lecture/11918638?start=257#notes
[009]: https://www.udemy.com/course/the-complete-hands-on-course-to-master-apache-airflow/learn/lecture/11918638?start=292#notes
[010]: https://www.udemy.com/course/the-complete-hands-on-course-to-master-apache-airflow/learn/lecture/11918638?start=377#notes
[011]: https://www.udemy.com/course/the-complete-hands-on-course-to-master-apache-airflow/learn/lecture/11918638?start=393#notes
[012]: https://www.udemy.com/course/the-complete-hands-on-course-to-master-apache-airflow/learn/lecture/11918638?start=410#notes
[013]: https://www.udemy.com/course/the-complete-hands-on-course-to-master-apache-airflow/learn/lecture/11918638?start=423#notes
[014]: https://www.udemy.com/course/the-complete-hands-on-course-to-master-apache-airflow/learn/lecture/11918638?start=446#notes
[015]: https://www.udemy.com/course/the-complete-hands-on-course-to-master-apache-airflow/learn/lecture/11918638?start=467#notes
[016]: https://www.udemy.com/course/the-complete-hands-on-course-to-master-apache-airflow/learn/lecture/11918638?start=485#notes
