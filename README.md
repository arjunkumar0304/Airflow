# Airflow 



## 1. Introduction to Apache Airflow

Apache Airflow is an **open-source workflow orchestration tool** used to **author, schedule, and monitor data pipelines**. Pipelines are defined as code, making them version-controlled, testable, and maintainable.

Airflow is widely used in **data engineering**, **ETL/ELT pipelines**, **machine learning workflows**, and **batch processing**.

---

## 1.1 Core Concepts

### 1.1.1 DAG Definition

**DAG** stands for **Directed Acyclic Graph**.

* A DAG represents a **workflow** in Airflow
* It is a collection of tasks with **dependencies**
* Tasks flow in **one direction** (no loops allowed)

**Key Points:**

* Each DAG has a **unique DAG ID**
* DAGs are written in **Python**
* DAGs define **what to run** and **in what order**

**Example Use Case:**

* Extract data → Transform data → Load data

---

### 1.1.2 Operators

**Operators** define **what kind of work** needs to be done.

They are templates for tasks.

**Common Types of Operators:**

* **BashOperator** – Run shell commands
* **PythonOperator** – Run Python functions
* **DummyOperator / EmptyOperator** – No operation (used for flow control)
* **BranchPythonOperator** – Conditional branching
* **TriggerDagRunOperator** – Trigger another DAG

**Key Point:**

> An operator becomes a **task** only when it is assigned to a DAG.

---

### 1.1.3 Tasks

A **Task** is a **single unit of execution** in a DAG.

* Created when an operator is instantiated
* Tasks are executed independently
* Each task has a **task_id**

**Task Lifecycle:**

* Scheduled
* Queued
* Running
* Success / Failed / Skipped

**Example:**

* Task 1: Read file
* Task 2: Clean data
* Task 3: Load to database

---

### 1.1.4 DAG to DAG Trigger

Airflow allows one DAG to **trigger another DAG**.

**Why use DAG-to-DAG triggering?**

* Break complex pipelines into smaller DAGs
* Improve reusability
* Better failure isolation

**How it works:**

* Parent DAG finishes execution
* Child DAG is triggered using `TriggerDagRunOperator`

**Example Scenario:**

* DAG 1: Ingest raw data
* DAG 2: Process transformed data (triggered after DAG 1)

---

## 1.2 Scheduler

The **Scheduler** is the heart of Airflow.

**Responsibilities:**

* Monitors DAGs and tasks
* Decides **when** tasks should run
* Submits tasks to the executor

**How Scheduler Works:**

1. Reads DAG definitions
2. Checks task dependencies
3. Identifies runnable tasks
4. Sends tasks to executor

**Key Points:**

* Scheduler runs continuously
* Works based on DAG schedule interval
* Handles retries and failures

---



# Apache Airflow – Executors & Operators

This README explains **Airflow Executors** and commonly used **Operators** with simple explanations and examples. This is suitable for **freshers** and **interview preparation**.

---

## 1.3 Executors in Airflow

### What is an Executor?

An **Executor** defines **how and where Airflow tasks are executed**. It controls task parallelism and execution mode.

In simple words:

> Executor decides **"who will run my task and how"**.

### Why Executor is Important?

* Controls scalability
* Controls parallel task execution
* Decides whether tasks run locally, in Docker, or on a cluster

### Common Types of Executors

| Executor           | Description                            | Use Case                |
| ------------------ | -------------------------------------- | ----------------------- |
| SequentialExecutor | Runs one task at a time                | Local testing, learning |
| LocalExecutor      | Runs tasks in parallel on same machine | Small production setups |
| CeleryExecutor     | Distributed execution using workers    | Large-scale production  |
| KubernetesExecutor | Runs each task in a Kubernetes Pod     | Cloud-native workloads  |

---

## 1.4 Operators in Airflow

### What is an Operator?

An **Operator** defines **what work needs to be done**.

> One operator = One task

Operators are used **inside DAGs** to perform actions.

---

### 1.4.1 BashOperator

#### What it does

Executes **Linux / shell commands**.

#### When to use

* Run shell scripts
* Trigger curl commands
* File operations

#### Example

```python
from airflow.operators.bash import BashOperator

bash_task = BashOperator(
    task_id='print_date',
    bash_command='date'
)
```

---

### 1.4.2 PythonOperator

#### What it does

Runs a **Python function**.

#### When to use

* Data processing
* API calls
* Custom logic

#### Example

```python
from airflow.operators.python import PythonOperator

def my_function():
    print("Hello Airflow")

python_task = PythonOperator(
    task_id='python_task',
    python_callable=my_function
)
```

---

### 1.4.3 Sensor Operator

#### What it does

**Waits for a condition** to be met.

> Sensors keep checking until condition is true.

#### Common Sensors

* FileSensor
* ExternalTaskSensor
* TimeSensor

#### Example (FileSensor)

```python
from airflow.sensors.filesystem import FileSensor

wait_for_file = FileSensor(
    task_id='wait_for_file',
    filepath='/tmp/data.csv'
)
```

---

### 1.4.4 SubDagOperator

#### What it does

Runs a **DAG inside another DAG**.

> Used to group tasks logically.

#### Important Note ⚠️

* **SubDagOperator is deprecated**
* Use **TaskGroup** instead (recommended)

#### Example (Old style)

```python
from airflow.operators.subdag import SubDagOperator

subdag_task = SubDagOperator(
    task_id='subdag_task',
    subdag=subdag
)
```

---

### 1.4.5 TriggerDagRunOperator

#### What it does

Triggers **another DAG**.

> Used for DAG-to-DAG orchestration.

#### When to use

* Upstream DAG completes → trigger downstream DAG
* Modular pipeline design

#### Example

```python
from airflow.operators.trigger_dagrun import TriggerDagRunOperator

trigger_dag = TriggerDagRunOperator(
    task_id='trigger_next_dag',
    trigger_dag_id='target_dag_id'
)
```

---





* **Executor**: Defines how tasks are executed
* **Operator**: Defines what task does
* **BashOperator**: Runs shell commands
* **PythonOperator**: Runs Python functions
* **Sensor**: Waits for condition
* **SubDagOperator**: DAG inside DAG (deprecated)
* **TriggerDagRunOperator**: Triggers another DAG

---






## 1.5 Workflow Management in Airflow

Workflow Management in Airflow means **defining, scheduling, and controlling task execution order** inside a DAG.

> Airflow manages workflows using DAGs (Directed Acyclic Graphs).

---

## 1.5.1 Default Arguments (default_args)

### What are Default Arguments?

`default_args` is a **dictionary** used to define **common parameters** for all tasks in a DAG.

### Why use default_args?

* Avoid repeating code
* Centralized configuration
* Cleaner DAGs

### Common Parameters

* `owner`
* `start_date`
* `retries`
* `retry_delay`
* `email_on_failure`

### Example

```python
default_args = {
    'owner': 'airflow',
    'start_date': datetime(2024, 1, 1),
    'retries': 1,
    'retry_delay': timedelta(minutes=5)
}
```

---

## 1.5.2 Schedule Interval

### What is Schedule Interval?

Defines **how often a DAG should run**.

### Common Schedule Values

| Schedule        | Meaning             |
| --------------- | ------------------- |
| `@daily`        | Once every day      |
| `@hourly`       | Every hour          |
| `@weekly`       | Once a week         |
| Cron Expression | Custom schedule     |
| `None`          | Manual trigger only |

### Example

```python
schedule_interval='@daily'
```

---

## 1.5.3 Catchup

### What is Catchup?

Catchup decides whether **past scheduled DAG runs** should be executed when Airflow starts.

### Values

* `catchup=True` → Runs all missed DAG runs
* `catchup=False` → Runs only latest DAG run

### Recommended

For production DAGs:

```python
catchup=False
```

---

## 1.5.4 Task Dependencies

### What are Task Dependencies?

Task dependencies define **execution order** between tasks.

> A task can start only after its upstream task finishes.

### Methods to Define Dependencies

* `>>` (right shift)
* `<<` (left shift)
* `set_upstream()`
* `set_downstream()`

---

## 1.5.4.1 set_upstream() / set_downstream()

### set_upstream()

Defines a task that must run **before** the current task.

### set_downstream()

Defines a task that must run **after** the current task.

### Example

```python
task1.set_downstream(task2)
# OR
task2.set_upstream(task1)
```

Equivalent shorthand:

```python
task1 >> task2
```

---

## 1.5.5 Scheduled Trigger

### What is Scheduled Trigger?

A **Scheduled Trigger** starts a DAG automatically based on the `schedule_interval`.

### Types of Triggers

* Scheduled trigger (time-based)
* Manual trigger (UI / CLI)
* External trigger (TriggerDagRunOperator)

### Example

```python
schedule_interval='0 2 * * *'  # Runs daily at 2 AM
```

---







## 1. Monitoring & Performance in Airflow

Monitoring helps you **track the health, performance, and reliability** of your Airflow pipelines (DAGs and tasks).

### Why Monitoring is Important

* Detect DAG failures early
* Identify slow or stuck tasks
* Track SLA misses
* Improve system performance
* Ensure stable production pipelines

---

## 1.1 Monitoring & Observability

### What is Observability?

Observability means **understanding what is happening inside Airflow** by looking at:

* Metrics (numbers)
* Logs (events)
* DAG & task states (visual view)

### How to Monitor Airflow Practically

#### 1. Airflow Web UI

* DAG View → See DAG status (Success, Failed, Running)
* Graph View → Task dependencies and failures
* Tree View → Daily execution status
* Gantt View → Task execution time and bottlenecks

#### 2. Metrics Monitoring

Airflow exposes metrics such as:

* Task duration
* DAG run duration
* Scheduler heartbeat

Can be integrated with:

* Prometheus
* Grafana
* StatsD

#### 3. SLA Monitoring

* SLA (Service Level Agreement) defines time limits for tasks
* Airflow alerts when SLA is missed
  nExample:

```python
sla=timedelta(minutes=30)
```

#### 4. Alerts & Notifications

* Email alerts on task failure
* Custom alerts using callbacks

---

## 1.2 Logging & Debugging

Logging helps you **understand what happened inside a task**.

### Airflow Logging

* Each task instance generates logs
* Logs are stored locally or in cloud storage (S3, Azure Blob, GCS)

### Where to See Logs

* Airflow UI → Click Task → View Log
* Server path:

  ```
  AIRFLOW_HOME/logs/
  ```

### Log Levels

* INFO → Normal execution messages
* WARNING → Potential issues
* ERROR → Task failure

Example:

```python
import logging
logging.info("Task started")
logging.error("Something went wrong")
```

### Debugging Techniques

* Re-run failed tasks
* Check logs for stack traces
* Use task retries
* Use `airflow tasks test` command

---

## 2. Trigger Rules & DAG Scheduling

---

## 2.1 Trigger Rules

Trigger rules define **when a task should run based on upstream task status**.

### Default Trigger Rule

* `all_success` (task runs only if all upstream tasks succeed)

### Common Trigger Rules

| Trigger Rule | Description                         |
| ------------ | ----------------------------------- |
| all_success  | All upstream tasks must succeed     |
| one_success  | At least one upstream task succeeds |
| all_failed   | All upstream tasks must fail        |
| one_failed   | At least one upstream task fails    |
| all_done     | Runs regardless of upstream result  |

Example:

```python
trigger_rule="all_done"
```

---

## 2.2 DAG Scheduling

DAG scheduling defines **when and how often a DAG runs**.

### Key Scheduling Parameters

#### 1. start_date

* The date from which DAG starts scheduling

#### 2. schedule_interval

Defines execution frequency:

* `@daily`
* `@hourly`
* `@once`
* Cron expression (`0 6 * * *`)

#### 3. catchup

* `True` → runs all missed DAG runs
* `False` → runs only latest schedule

Example:

```python
default_args = {
    'start_date': datetime(2025, 1, 1)
}

DAG(
    dag_id='example_dag',
    schedule_interval='@daily',
    catchup=False
)
```

---

## 2.3 DAG Run Types

* Scheduled Run → Triggered by scheduler
* Manual Run → Triggered from UI or CLI
* Backfill Run → Past execution runs

---
