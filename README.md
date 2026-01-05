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

