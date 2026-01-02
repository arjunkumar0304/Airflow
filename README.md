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

