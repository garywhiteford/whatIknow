# The Complete Hands-On Introduction to Apache Airflow

## Description

Learn to author, schedule and monitor data pipelines through practical examples using Apache Airflow. 

## Table of Contents

* [7. What is Airflow?](#7-what-is-airflow) (Benefits, Core components, Operators, What Airflow is NOT!)
* [8. How Airflow works?](#8-how-airflow-works) (Types of architecture, Overall process, Installation)
* [10. [Practice] The CLI in Airflow 2.0](#10-practice-the-cli-in-airflow-20) (Create first user, Command overview)
* [11. [Practice] What you need to know about the UI](#11-practice-what-you-need-to-know-about-the-ui) (Views, Logs)

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

#### 8. How Airflow works?

Timestamp    |  Description
------------ | --------------
[0:11][017]	 |  **Single-node architecture**: Web Server, Scheduler, Metastore, Executor (containing Queue). The Web Server fetches metadata from the Metastore. The scheduler talks to the Metastore and the Executor in order to send tasks to be executed. The Executor updates the status of the tasks in the Metastore. The components talk together through the Metastore.
[1:29][018]	 |  **Multi-node architecture:**<br>Node 1: Web Server, Scheduler, Executor<br>Node 2: Metastore, Queue (external to Executor; e.g., RabbitMQ or Redis)<br>Worker Nodes 1-N: Airflow Worker<br><br>Redis is an in-memory data structure store, used as a distributed, in-memory key–value database, cache and message broker, with optional durability.<br>https://redis.io/<br><br>RabbitMQ is an open-source message-broker software that originally implemented the Advanced Message Queuing Protocol and has since been extended with a plug-in architecture to support Streaming Text Oriented Messaging Protocol, MQ Telemetry Transport, and other protocols.<br>https://www.rabbitmq.com/
[3:57][019]	 |  Overall process: 1) You add a new data pipeline (e.g., dag.py) in the DAGS folder; 2) The **Web Server** and the **Scheduler** parse the **DAGS folder** in order to be aware of the available data pipelines; 3) When a data pipeline is ready to be triggered, a **DagRun Object** is created and is given a status of running in the **Metastore**; 4) The first task to run in your data pipeline is scheduled (when a task is running, it creates a **Task Instance**); 5) Task Instance is sent to the **Executor** by the Scheduler; 6) Executor updates the status of the Task Instance object in the Metastore; 7) When Task Instance is complete, the Executor updates the Metastore; 8) Scheduler checks Metastore to see if work DagRun Instance is done and updates the Metastore; 9) Web Server updates UI to reflect completion status of Dag.
[3:40][020]	 |  Start python virtual environment:<br>```python3 -m venv sandbox```<br>```source sandbox/bin/activate```<br>Ref: https://docs.python.org/3/tutorial/venv.html"
[3:30][021]	 |  VisualStudio Code: Terminal > New Terminal
[4:32][022]	 |  Install helper package:<br>```pip install wheel```
[4:45][023]  |  Install Airflow with version constraints file provided by Marc Lamberti:<br>```pip3 install apache-airflow==2.0.0 --constraint https://gist.githubusercontent.com/marclamberti/742efaef5b2d94f44666b0aec020be7c/raw/5da51f9fe99266562723fdfb3e11d3b6ac727711/constraint.txt```<br>Ref: https://airflow.apache.org/docs/apache-airflow/stable/installation.html#constraints-files
[6:36][024]	 |  Initialize the Metastore and generate initial files<br>```airflow db init```
[8:01][025]  |  Start Airflow Web Server to start the user interface<br>```airflow webserver```

#### 10. [Practice] The CLI in Airflow 2.0

Timestamp    |  Description
------------ | --------------
[0:52][026]  |  Review command line help<br>```airflow -h```
[1:16][027]  |  Review db command line help<br>```airflow db -h```
[1:42][028]  |  Review users command line help<br>```airflow users -h```
[1:59][029]  |  Review users create command line help<br>```airflow users create -h```
[2:17][030]  |  Create admin user<br>```airflow users create -u admin -p admin -f Gary -l Whiteford -r Admin -e grwhiteford@msn.com```
[4:08][031]  |  Command overview:<br>```airflow db init```<br>Used only once. Should not be used again.
[4:31][032]  |  Command overview:<br>```airflow db upgrade```<br>Used to upgrade Airflow from one version to the next.
[4:44][033]  |  Command overview:<br>```airflow db reset```<br>**DANGEROUS!!** Use to experiment in DEV. Do not use in production!!
[5:08][034]  |  Command overview:<br>```airflow webserver```<br>Start the Airflow Web Server.
[5:18][035]  |  Command overview:<br>```airflow scheduler```<br>Start the Airflow Scheduler.
[5:40][036]  |  Start the Airflow Scheduler:<br>In a new terminal session<br>```source sandbox/bin/activate```<br>```airflow scheduler```
[6:11][037]  |  Review list of dags<br>```airflow dags list```
[6:37][038]  |  To review list of tasks, select a DAG ID from the DAGS list<br>```airflow tasks list <dag_id>```<br>Can be used to check that your DAG has no errors (if a task does not show up, there may be a parsing error in the DAG).
[7:16][039]  |  Trigger a DAG run on a specific date<br>```airflow dags trigger -e 2022-01-01 example_xcom_args```<br><br>Help<br>```(sandbox) airflow@airflowvm:~/airflow$ airflow dags trigger -h```<br>```usage: airflow dags trigger [-h] [-c CONF] [-e EXEC_DATE] [-r RUN_ID] [-S SUBDIR] dag_id```<br><br>```Trigger a DAG run```<br><br>```positional arguments:```<br>```  dag_id                The id of the dag```<br><br>```optional arguments:```<br>```  -h, --help            show this help message and exit```<br>```  -c CONF, --conf CONF  JSON string that gets pickled into the DagRun's conf attribute```<br>```  -e EXEC_DATE, --exec-date EXEC_DATE```<br>```                        The execution date of the DAG```<br>```  -r RUN_ID, --run-id RUN_ID```<br>```                        Helps to identify this run```<br>```  -S SUBDIR, --subdir SUBDIR```<br>```                        File location or directory from which to look for the dag. Defaults to '[AIRFLOW_HOME]/dags' where [AIRFLOW_HOME] is the value you set for 'AIRFLOW_HOME' config you set in 'airflow.cfg' ```

#### 11. [Practice] What you need to know about the UI

Timestamp    |  Description
------------ | --------------
[0:49][040]  |  **DAGs View** is the default view in Airflow. It shows all the DAGs available.
[1:11][041]  |  The **Pause/Unpause toggle** is important as it allows you to pause or unpause a DAG. A DAG will not work if it is paused.
[1:33][042]  |  Tags (Categories) can be useful for organizing many DAGs.<br>Ref: [Add tags to DAGs and use it for filtering in the UI](https://airflow.apache.org/docs/apache-airflow/stable/howto/add-dag-tags.html?highlight=tags)
[2:55][043]  |  **Delete** DAG in the UI does not delete the files, only the metadata in the Metastore.
[5:46][044]  |  With **Tree View** you get the history of your diagrams. With **Graph View** you get only the current (latest) diagram.
[5:55][045]  |  In Graph View, **color of the boxes** indicates which operator was used.
[6:07][046]  |  **Gantt View**. Useful for spotting bottlenecks.
[7:33][047]  |  Check **logs** by clicking on a task in any view (brings up Model view for that task) and then click on the logs button at the top.
[7:54][048]  |  Model View. To re-run tasks, you must Clear them first (using the **Clear button** in the Model View).
[8:00][049]  |  Model View. Use **Mark Failed** or **Mark Success** to test behavior of subsequent tasks

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
[017]: https://www.udemy.com/course/the-complete-hands-on-course-to-master-apache-airflow/learn/lecture/11940460?start=11#notes
[018]: https://www.udemy.com/course/the-complete-hands-on-course-to-master-apache-airflow/learn/lecture/11940460?start=89#notes
[019]: https://www.udemy.com/course/the-complete-hands-on-course-to-master-apache-airflow/learn/lecture/11940460?start=237#notes
[020]: https://www.udemy.com/course/the-complete-hands-on-course-to-master-apache-airflow/learn/lecture/11918674?start=220#notes
[021]: https://www.udemy.com/course/the-complete-hands-on-course-to-master-apache-airflow/learn/lecture/11918674?start=210#notes
[022]: https://www.udemy.com/course/the-complete-hands-on-course-to-master-apache-airflow/learn/lecture/11918674?start=272#notes
[023]: https://www.udemy.com/course/the-complete-hands-on-course-to-master-apache-airflow/learn/lecture/11918674?start=285#notes
[024]: https://www.udemy.com/course/the-complete-hands-on-course-to-master-apache-airflow/learn/lecture/11918674?start=396#notes
[025]: https://www.udemy.com/course/the-complete-hands-on-course-to-master-apache-airflow/learn/lecture/11918674?start=481#notes
[026]: https://www.udemy.com/course/the-complete-hands-on-course-to-master-apache-airflow/learn/lecture/11941200?start=52#notes
[027]: https://www.udemy.com/course/the-complete-hands-on-course-to-master-apache-airflow/learn/lecture/11941200?start=76#notes
[028]: https://www.udemy.com/course/the-complete-hands-on-course-to-master-apache-airflow/learn/lecture/11941200?start=102#notes
[029]: https://www.udemy.com/course/the-complete-hands-on-course-to-master-apache-airflow/learn/lecture/11941200?start=119#notes
[030]: https://www.udemy.com/course/the-complete-hands-on-course-to-master-apache-airflow/learn/lecture/11941200?start=137#notes
[031]: https://www.udemy.com/course/the-complete-hands-on-course-to-master-apache-airflow/learn/lecture/11941200?start=248#notes
[032]: https://www.udemy.com/course/the-complete-hands-on-course-to-master-apache-airflow/learn/lecture/11941200?start=271#notes
[033]: https://www.udemy.com/course/the-complete-hands-on-course-to-master-apache-airflow/learn/lecture/11941200?start=284#notes
[034]: https://www.udemy.com/course/the-complete-hands-on-course-to-master-apache-airflow/learn/lecture/11941200?start=308#notes
[035]: https://www.udemy.com/course/the-complete-hands-on-course-to-master-apache-airflow/learn/lecture/11941200?start=318#notes
[036]: https://www.udemy.com/course/the-complete-hands-on-course-to-master-apache-airflow/learn/lecture/11941200?start=340#notes
[037]: https://www.udemy.com/course/the-complete-hands-on-course-to-master-apache-airflow/learn/lecture/11941200?start=371#notes
[038]: https://www.udemy.com/course/the-complete-hands-on-course-to-master-apache-airflow/learn/lecture/11941200?start=397#notes
[039]: https://www.udemy.com/course/the-complete-hands-on-course-to-master-apache-airflow/learn/lecture/11941200?start=436#notes
[040]: https://www.udemy.com/course/the-complete-hands-on-course-to-master-apache-airflow/learn/lecture/11941194?start=49#notes
[041]: https://www.udemy.com/course/the-complete-hands-on-course-to-master-apache-airflow/learn/lecture/11941194?start=71#notes
[042]: https://www.udemy.com/course/the-complete-hands-on-course-to-master-apache-airflow/learn/lecture/11941194?start=93#notes
[043]: https://www.udemy.com/course/the-complete-hands-on-course-to-master-apache-airflow/learn/lecture/11941194?start=175#notes
[044]: https://www.udemy.com/course/the-complete-hands-on-course-to-master-apache-airflow/learn/lecture/11941194?start=346#notes
[045]: https://www.udemy.com/course/the-complete-hands-on-course-to-master-apache-airflow/learn/lecture/11941194?start=355#notes
[046]: https://www.udemy.com/course/the-complete-hands-on-course-to-master-apache-airflow/learn/lecture/11941194?start=367#notes
[047]: https://www.udemy.com/course/the-complete-hands-on-course-to-master-apache-airflow/learn/lecture/11941194?start=453#notes
[048]: https://www.udemy.com/course/the-complete-hands-on-course-to-master-apache-airflow/learn/lecture/11941194?start=474#notes
[049]: https://www.udemy.com/course/the-complete-hands-on-course-to-master-apache-airflow/learn/lecture/11941194?start=480#notes
