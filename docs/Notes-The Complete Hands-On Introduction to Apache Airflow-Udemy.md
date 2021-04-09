# The Complete Hands-On Introduction to Apache Airflow

## Description

Learn to author, schedule and monitor data pipelines through practical examples using Apache Airflow. 

## Table of Contents

### 2. Getting Started with Airflow

* [7. What is Airflow?](#7-what-is-airflow) (Benefits, Core components, Operators, What Airflow is NOT!)
* [8. How Airflow works?](#8-how-airflow-works) (Types of architecture, Overall process, Installation)
* [10. [Practice] The CLI in Airflow 2.0](#10-practice-the-cli-in-airflow-20) (Create first user, Command overview)
* [11. [Practice] What you need to know about the UI](#11-practice-what-you-need-to-know-about-the-ui) (Views, Logs)
* [Section 2: Key Takeaways](#section-2-key-takeaways)

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

#### Section 2: Key Takeaways

* Airflow is an orchestrator, not a processing framework, process your gigabytes of data outside of Airflow (i.e. You have a Spark cluster, you use an operator to execute a Spark job, the data is processed in Spark).
* A DAG is a data pipeline, an Operator is a task.
* An Executor defines how your tasks are execute whereas a worker is a process executing your task
* The scheduler schedules your tasks, the web server serves the UI, the database stores the metadata of Airflow.
* ```airflow db init``` is the first command to execute to initialise Airflow
* If a task fails, check the logs by clicking on the task from the UI and "Logs"
* The Gantt view is super useful to sport bottlenecks and tasks are too long to execute

### 3. Coding Your First Data Pipeline with Airflow

#### 13. Introduction

Timestamp    |  Description
------------ | --------------
0:21         | Questions to be answered<br>1. How to create a data pipeline?<br>2. How to interact with a third-party tool from a data pipeline?<br>3. How to check for a file to exist before moving to the next task?

#### 14. What is a DAG?

Timestamp    |  Description
------------ | --------------
0:54         | A DAG is a directed acyclic graph with directed dependencies, tasks corresponding to the nodes and no loops.

#### 15. [Practice] Time to code your first DAG

Timestamp    |  Description
------------ | --------------
0:33         | Overall Process<br>1. Creating_table (SQLite operator)<br>2. Is_API_available (HTTP sensor)<br>3. Extracting_user (HTTP operator)<br>4. Processing_user (Python operator)<br>5. Storing_user (Bash operator)<br> <br>References<br>https://airflow.apache.org/docs/apache-airflow-providers-sqlite/stable/operators.html<br>https://airflow.apache.org/docs/apache-airflow-providers-http/stable/operators.html#httpsensor<br>https://airflow.apache.org/docs/apache-airflow-providers-http/stable/operators.html<br>https://airflow.apache.org/docs/apache-airflow/stable/howto/operator/python.html<br>https://airflow.apache.org/docs/apache-airflow/stable/howto/operator/bash.html

#### 16. [Practice] DAG Skeleton

Timestamp    |  Description
------------ | --------------
0:07         | Initial steps<br>1. Start Oracle VM VirtualBox<br>2. Start AirflowVM<br>3. Start Visual Studio Code<br>4. Connect to VM (in VSC: F1 > remote-ssh: Connect > localhost > password)<br>5. In VSC: Terminal > New Terminal<br>6. Start Python Virtual Environment: source sandbox/bin/activate
1:05         | 7. Navigate to airflow subdirectory and open in Explorer in VSC (may need to type password again)
1:47         | 8. Create folder: dags
2:13         | 9. In dags folder, create file: user_processing.py
2:45         | 10. Add line:<br>```from airflow.models import DAG```<br>11. On my own: Add Microsoft Python extension for Visual Studio Code<br>11a. Install pylint (per Python extension)
3:10         | 12. Add line (import Python module for defining a date):<br>```from datetime import datetime```
3:37         | 13. Add line (instantiate the DAG):<br>```with DAG()```<br>Ref: https://airflow.apache.org/docs/apache-airflow/stable/tutorial.html?highlight=dag_id#instantiate-a-dag
3:50         | 14. Define a DAG ID (unique among all DAGs in the system):<br>```'user_processing'```
4:13         | 15. Define the schedule interval (how frequently the data pipeline will be triggered):<br>```schedule_interval='@daily'```
4:37         | 16. Define the start date (when your data pipeline will start being scheduled):<br>```start_date=datetime(2020, 1, 1)```
5:02         | 17. Define the catchup (to be explained):<br>```catchup=False```
5:15         | 18. Close out the instantiation (with target / variable / object name):<br>```as dag:```
5:40         | 19. Create default arguments as a dictionary:<br>```default_args = {```<br>``` ```<br>```}```<br>Ref: https://airflow.apache.org/docs/apache-airflow/stable/tutorial.html?highlight=dag_id#default-arguments
6:19         | 20. Move start_date out of the DAG instantiation and into the default arguments dictionary:<br>```'start_date': datetime(2020, 1, 1)```<br>Ref: https://docs.python.org/3/reference/expressions.html#dictionary-displays
6:36         | 21. In the DAG instantiation, add a reference to the default arguments dictionary:<br>```default_args=default_args```

#### 17. What is an Operator?

Timestamp    |  Description
------------ | --------------
0:08         | 22. Definition: An operator defines one task in your data pipeline.
0:39         | 23. Principle: Segregate tasks into repeatable / fail-able elements (e.g., separate the cleaning data task from the processing data task).
1:21         | 24a. Definition: Three types of operators: Action operators (execute an action).<br>Ref: https://airflow.apache.org/docs/apache-airflow/stable/concepts.html#operators
1:34         | 24b. Definition: Three types of operators: Transfer operators (transfer data).
1:47         | 24c. Definition: Three types of operators: Sensors (wait for a condition to be met).<br>Ref: https://airflow.apache.org/docs/apache-airflow/stable/concepts.html#sensors

#### 18. [Practice] Creating Table

Timestamp    |  Description
------------ | --------------
0:18         | 25. Task 1: Create table: Add line (import SqliteOperator):<br>```from airflow.providers.sqlite.operators.sqlite import SqliteOperator```<br>Ref: https://airflow.apache.org/docs/apache-airflow-providers-sqlite/stable/operators.html
1:21         | 26. Task 1: Create table: Add line (define variable for SqliteOperator):<br>```creating_table = SqliteOperator()```
1:34         | 27. Task 1: Create table: Add line (create a Task ID unique in the context of the data pipeline):<br>```task_id='creating_table'```
1:57         | 28. Task 1: Create table: Add line (define the connection to be used, created later):<br>```sqlite_conn_id='db_sqlite'```
2:16         | 29. Task 1: Create table: Add line (provide the SQL to be used):<br>```sql='''```<br>```    CREATE TABLE users (```<br>```        -- user_id INTEGER PRIMARY KEY AUTOINCREMENT, --Originally included, but later deleted and replaced by email as primary key below```<br>```        firstname TEXT NOT NULL, ```<br>```        lastname TEXT NOT NULL, ```<br>```        country TEXT NOT NULL, ```<br>```        username TEXT NOT NULL, ```<br>```        password TEXT NOT NULL, ```<br>```        email TEXT NOT NULL PRIMARY KEY```<br>```    );```<br>```    '''```<br>Ref: https://docs.python.org/3/glossary.html?highlight=triple%20quoted%20string#term-triple-quoted-string<br>Ref: [SQLite: SQL Comment Syntax](https://sqlite.org/lang_comment.html)
3:56         | Task 1: Create table:<br>33. Edit line (change Primary Key in SQL CREATE TABLE statement to be email instead)<br> <br>5:15 PM 4/6/2021<br>34. Unpause DAG to see results<br>```DAG: user_processing```<br>```Task Instance: creating_table```<br>```Log by attempts```<br>```-----------------------------```<br>```..```<br>```[2021-04-06 22:15:35,758] {taskinstance.py:1396} ERROR - The conn_id `db_sqlite` isn't defined```<br>```..```
4:13         | Task 1: Create table: Create connection:<br>34. Start Airflow Web Server and Airflow Scheduler<br>In Terminal 1:<br>```source sandbox/bin/activate```<br>```airflow webserver```<br>In Terminal 2:<br>```source sandbox/bin/activate```<br>```airflow scheduler```
5:35         | 35b. Check for SQLite package ("Requirement already satisfied: apache-airflow-providers-sqlite in ./sandbox/lib/python3.8/site-packages (1.0.2)")<br>```pip install 'apache-airflow-providers-sqlite'```<br>Ref: https://airflow.apache.org/docs/apache-airflow-providers/packages-ref.html#apache-airflow-providers-sqlite
7:43         | Task 1: Create table: Create connection:<br>36. In Airflow UI, go to Admin (menu) and click on Connections
7:55         | Task 1: Create table: Create connection:<br>37. Click [Add a new record] (button)
8:00         | Task 1: Create table: Create connection:<br>38. Complete connection details<br>```A. Conn Id:     db_sqlite [same as value in sqlite_conn_id at step 28.]```<br>```B. Conn Type:   Sqlite```<br>```C. Description: SQLITE connection to the DB```<br>```D. Host:        /home/airflow/airflow/airflow.db```<br>39. Save connection and find in list of connections

#### 19. [Practice] The secret weapon!

Timestamp    |  Description
------------ | --------------
0:00         | Task 1: Create table: Test task:<br>40. View command line help<br>```airflow tasks test -h```<br>41. Run command line statement<br>```# airflow tasks test dag_id task_id execution_date```<br>```airflow tasks test user_processing creating_table 2020-01-01```
1:13         | Task 1: Create table: Test task:<br>42. Verify new table exists<br>A. Start SQLite with the underlying Airflow SQLite database<br>```sqlite3 airflow.db```<br>B. List tables<br>```sqlite> .tables```<br>--> Existence of table [users]<br>Ref: [Command Line Shell for SQLite](https://sqlite.org/cli.html)<br>C. Note: The table is empty<br>```sqlite> SELECT * FROM users;```<br>--> Empty result

#### 20. [Practice] Is API available?

Timestamp    |  Description
------------ | --------------
0:45         | Task 2: Is API Available?<br>43. Add import statement<br>```from airflow.providers.http.sensors.http import HttpSensor```<br>Ref: [apache-airflow-providers-http](https://airflow.apache.org/docs/apache-airflow-providers-http/stable/index.html)<br>Ref: [HTTP Operators](https://airflow.apache.org/docs/apache-airflow-providers-http/stable/operators.html)
1:13         | Task 2: Is API Available?: Define task<br>44. Define task<br>A. Add variable<br>```is_api_available = HttpSensor(```<br>``` ```<br>```)```<br>B. Add Task ID<br>```task_id='is_api_available'```<br>C. Add Connection ID<br>```# To be defined in the UI and point to the URL of an API```<br>```http_conn_id='user_api'```<br>D. Add End Point<br>```# The page to be checked```<br>```endpoint='api/'```
2:14         | Task 2: Is API Available?: Create connection<br>45. In Airflow UI, go to Admin (menu) and click on Connections<br>46. Click [Add a new record] (button)<br>47. Complete connection details<br>```A. Conn Id:     user_api```<br>```B. Conn Type:   HTTP```<br>```C. Description: API for getting users```<br>```D. Host:        https://randomuser.me/```<br>48. Save connection and find in list of connections<br>Ref: [Documentation for the Random User Generator API](https://randomuser.me/documentation)
3:36         | Task 2: Is API Available?: Check provider installation<br>49. Check that the provider has been installed (Requirement already satisfied: apache-airflow-providers-http in ./sandbox/lib/python3.8/site-packages (1.1.1))<br>```pip install apache-airflow-providers-http```<br>Ref: [apache-airflow-providers-http](https://airflow.apache.org/docs/apache-airflow-providers-http/stable/index.html#installation)
4:13         | Task 2: Is API Available?: Test task<br>50. Run command line statement<br>```# airflow tasks test dag_id task_id execution_date```<br>```airflow tasks test user_processing is_api_available 2020-01-01```

#### 21. [Practice] Extracting users

Timestamp    |  Description
------------ | --------------
0:47         | Task 3: Extracting Users: Import Operator<br>51. Add import statement<br>```from airflow.providers.http.operators.http import SimpleHttpOperator```<br>Ref: [apache-airflow-providers-http](https://airflow.apache.org/docs/apache-airflow-providers-http/stable/index.html)<br>Ref: [HTTP Operators: SimpleHttpOperator](https://airflow.apache.org/docs/apache-airflow-providers-http/stable/operators.html#simplehttpoperator)
1:17         | Task 3: Extracting Users: Define task<br>52. Define task<br>Ref: [Documentation: class airflow.providers.http.operators.http.SimpleHttpOperator](https://airflow.apache.org/docs/apache-airflow-providers-http/stable/_api/airflow/providers/http/operators/http/index.html)<br>A. Add variable<br>```extracting_user = SimpleHttpOperator(```<br>``` ```<br>```)```<br>B. Add Task ID<br>```task_id='extracting_user'```<br>C. Add Connection ID<br>```# As previously defined```<br>```http_conn_id='user_api'```<br>D. Add End Point<br>```# The page to be checked```<br>```endpoint='api/'```
2:11         | Task 3: Extracting Users: Define task<br>52. Define task (continued)<br>E. Add Method<br>```# Use GET since no data is sent to the URL```<br>```method='GET'```<br>F. Add Response Filter with a Lambda function<br>```# A formula to manipulate the response text```<br>```response_filter=lambda response: json.loads(response.text)```<br>Ref: [Python Reference: Lambda Expressions](https://docs.python.org/3/reference/expressions.html#lambda)<br>Ref: [Python Tutorial: Lambda Expressions](https://docs.python.org/3/tutorial/controlflow.html#lambda-expressions)<br>Ref: [Python Reference: json: json.loads](https://docs.python.org/3/library/json.html#json.loads)<br>G. Set Log Response to true<br>```# So that response text is included in the logs```<br>```log_response=True```
3:17         | Task 3: Extracting Users: Import json<br>53. Import the Python json package<br>```import json```<br>Ref: [Python Standard Library: JSON Encoder and Decoder module](https://docs.python.org/3/library/json.html)<br>Ref: [Introducing JSON (JavaScript Object Notation)](https://www.json.org/json-en.html)<br>Ref: [Python Language Reference: 5. The Import System](https://docs.python.org/3/reference/import.html)
3:36         | Task 3: Extracting Users: Test task<br>54. Run command line statement<br>```# airflow tasks test dag_id task_id execution_date```<br>```airflow tasks test user_processing extracting_user 2020-01-01```


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
