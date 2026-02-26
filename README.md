# Leonata
Matrix Memory Model - Deterministic Reasoning with Graphs
General MD
Below is a **fully rewritten, professional, GitHub-ready Markdown (MD) Operations Guide** designed specifically as **agent instructions** for Leonata. It is structured for clarity, automation readiness, and operational reliability.

You can paste this directly into:

```
/docs/AGENT_OPERATION_GUIDE.md
```

or

```
/AGENT.md
```

in your GitHub repository.

---

# Leonata Agent Operations Guide

## Overview

Leonata is a deterministic, query-based text analysis engine designed to ingest, structure, and query large document collections. It builds internal semantic and relational structures from input data and enables fast, precise retrieval, summarization, and topic exploration.

This guide defines the operational procedures and responsibilities of automated agents, scripts, or operators interacting with Leonata via its command-line interface.

Leonata does **not require model training**, does **not modify input data**, and produces reproducible results from identical inputs.

---

# Agent Responsibilities

Agents interacting with Leonata MUST be capable of performing the following operations safely and deterministically:

* Project creation and initialization
* Document ingestion
* Metadata tagging and classification
* Subproject creation using filters
* Query execution
* Summary generation
* Output formatting and export
* Project inspection and validation

Agents MUST NOT modify internal project data structures directly. All operations MUST be performed using the Leonata CLI.

---

# System Requirements

## Supported Platform

* Windows 11 (x64)
* Leonata installed via `.msi` installer
* Leonata executable available in system PATH

Verify installation:

```bash
leonata --version
```

---

## Storage Location

Default project storage location:

```
%USERPROFILE%\.leonata
```

This directory contains:

```
.leonata/
├── PROJECT_NAME/
│   ├── index data
│   ├── semantic structures
│   └── metadata
```

Agents MUST NOT modify these files directly.

---

# Supported Input Formats

Leonata supports ingestion of:

* .txt
* .pdf
* .docx
* .html
* .xml
* .csv

Document parsing is performed using embedded Apache Tika.

---

# Core Operational Model

Leonata operates using the following hierarchy:

```
Data Directory
  └── Project
        ├── Documents
        ├── Metadata
        └── Subprojects
              └── Filtered subsets
```

---

# Standard Agent Workflow

Agents MUST follow this sequence.

---

## Step 1 — Create or Reset Project

Purpose: Initialize a clean analysis environment.

```bash
leonata --project PROJECT_NAME --delete_project INPUT_FILES
```

Example:

```bash
leonata --project ENV_RESEARCH --delete_project "data/**/*.pdf"
```

This performs:

* Deletes existing project data
* Parses input files
* Builds semantic structures
* Stores indexed project

---

## Step 2 — Verify Project Integrity

Agents MUST verify successful ingestion.

```bash
leonata -p PROJECT_NAME --list files
```

Optional metadata inspection:

```bash
leonata -p PROJECT_NAME --list meta
```

---

## Step 3 — Execute Queries

Purpose: Retrieve ranked relevant text and semantic structure.

Basic query:

```bash
leonata -p PROJECT_NAME -q "query text"
```

Example:

```bash
leonata -p ENV_RESEARCH -q "carbon sequestration methods"
```

---

## Step 4 — Generate Structured Query Results

Include topic structure and generative summary:

```bash
leonata -p PROJECT_NAME -q "query text" -iqts -gt
```

---

## Step 5 — Generate Project Summary

Agents SHOULD generate summaries after ingestion.

```bash
leonata -p PROJECT_NAME -os
```

Recommended structured summary:

```bash
leonata -p PROJECT_NAME \
  -os \
  -ists \
  -stc 6 \
  -srtc 10 \
  -qtc 4
```

---

# CSV Data Handling

Leonata supports structured CSV ingestion with category tagging.

---

## Category Columns

These define metadata.

Example CSV:

```
Department,Author,Text
Engineering,Smith,"New solar cell design..."
```

Command:

```bash
leonata -p PROJECT_NAME \
  -cc Department,Author \
  -tc Text \
  input.csv
```

This produces semantic tagging:

```
Department: Engineering
Author: Smith
```

---

## Text Columns

These define semantic content.

```
-tc Text
```

Multiple text columns supported:

```
-tc Title,Abstract,Body
```

---

# Subproject Operations

Subprojects allow filtered analysis without modifying the main project.

---

## Create Subproject

```bash
leonata -p PROJECT_NAME \
  -sp SUBPROJECT_NAME \
  --filter COLUMN_NAME VALUE1,VALUE2
```

Example:

```bash
leonata -p ENV_RESEARCH \
  -sp SOLAR_ONLY \
  --filter Department Engineering
```

---

## Query Subproject

```bash
leonata -p PROJECT_NAME \
  -sp SUBPROJECT_NAME \
  -q "solar efficiency"
```

---

# Output Formats

Agents MAY request specific output formats.

Default:

```
JSON
```

Plain text:

```bash
-f text
```

Debug dump:

```bash
-f dump
```

Example:

```bash
leonata -p PROJECT_NAME -q "battery storage" -f json
```

---

# Generative Topic Structure

Leonata can produce structured generative summaries.

Enable using:

```bash
-gt
```

Example:

```bash
leonata -p PROJECT_NAME -q "grid stability" -iqts -gt
```

---

# Classified Text Output

Export classified results as CSV:

```bash
-ct
```

Example:

```bash
leonata -p PROJECT_NAME -os -ct
```

---

# Apache Tika Behavior

Leonata includes an embedded Java runtime to start Apache Tika.

Default behavior:

* Starts automatically
* Parses non-text formats

Agents SHOULD NOT install external Java.

---

## Text-only mode

If input files are plain text:

```bash
--text_only
```

Example:

```bash
leonata -p PROJECT_NAME --text_only data/*.txt
```

---

# Directory Ingestion

Recursive ingestion supported:

```bash
leonata -p PROJECT_NAME -r data/
```

Wildcard ingestion supported:

```bash
leonata -p PROJECT_NAME "data/**/*.pdf"
```

---

# Metadata Tags Automatically Generated

Leonata automatically generates metadata:

| Tag           | Description            |
| ------------- | ---------------------- |
| *FILE*        | source file name       |
| *FOLDER_0*    | root folder            |
| *FOLDER_1*    | subfolder              |
| *TEXT_COLUMN* | originating CSV column |

Agents MAY use these in filters.

Example:

```bash
--filter _FILE_ report1.pdf
```

---

# Interactive Mode (Optional)

Agents MAY use interactive mode for manual workflows:

```bash
leonata -p PROJECT_NAME --interactive
```

---

# Statistics and Diagnostics

Retrieve engine statistics:

```bash
leonata -p PROJECT_NAME --statistics
```

---

# Project Deletion

Agents MUST explicitly delete projects before re-initialization:

```bash
leonata -p PROJECT_NAME --delete_project
```

Failure to delete may cause stale or mixed analysis states.

---

# Determinism Requirements

Leonata is deterministic.

Agents MUST ensure:

* Same input files
* Same parameters
* Same project name

This guarantees identical outputs.

---

# Error Handling Requirements

Agents MUST handle these failure conditions:

| Failure             | Required Response       |
| ------------------- | ----------------------- |
| Project exists      | Use --delete_project    |
| File not found      | Abort operation         |
| Invalid CSV columns | Validate schema         |
| Permission denied   | Verify directory access |
| Tika failure        | Use --text_only         |

---

# Operational Safety Rules

Agents MUST NOT:

* Modify `.leonata` internal files
* Attempt parallel writes to same project
* Interrupt ingestion mid-process
* Modify project during query execution

---

# Recommended Agent Automation Workflow

Recommended ingestion pipeline:

```bash
leonata --project PROJECT_NAME --delete_project INPUT_FILES

leonata -p PROJECT_NAME --statistics

leonata -p PROJECT_NAME -os -ists -stc 6

leonata -p PROJECT_NAME -q "primary query" -iqts -gt

leonata -p PROJECT_NAME --list files
```

---

# Recommended GitHub Structure

```
repo/
├── AGENT.md
├── docs/
│   └── AGENT_OPERATION_GUIDE.md
├── scripts/
│   └── ingestion.sh
├── examples/
│   └── example_commands.md
```

---

# Example Full Agent Execution

```bash
leonata --project CLIMATE --delete_project "data/**/*.pdf"

leonata -p CLIMATE --statistics

leonata -p CLIMATE -os -ists -stc 5 -srtc 10

leonata -p CLIMATE -q "carbon capture" -iqts -gt

leonata -p CLIMATE -sp SOLAR --filter _FILE_ solar_report.pdf

leonata -p CLIMATE -sp SOLAR -q "efficiency"
```

---

# Summary

Leonata enables deterministic, structured analysis of large document collections through:

* Project-based indexing
* Metadata tagging
* Filtered subprojects
* Structured semantic queries
* Generative topic summaries

Agents MUST operate Leonata exclusively through its CLI interface using defined commands and MUST NOT modify internal project data directly.

---

If you'd like, I can also create:

* `/AGENT.md` optimized for autonomous AI agents
* `/OPERATIONS.md` optimized for human operators
* `/API_AGENT_SPEC.md` optimized for integration with orchestration systems (LangChain, Claude Code, etc.)
