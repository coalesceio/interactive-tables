# Interactive Table Package

The Coalesce Interactive Table Package helps you build and deploy Snowflake Interactive Tables for low-latency, high-performance analytical queries.

## Package Includes

* [Interactive Table](#interactive-table)
  
---
## Interactive Table

The Coalesce Interactive Table UDN allows you to build and deploy Snowflake Interactive Tables, which are optimized for low-latency, high-performance analytical queries.

Interactive Tables refresh automatically based on a SQL definition and maintain optimized storage for low-latency, high-concurrency queries. This makes them ideal for powering live dashboards, APIs, and sub-second analytical loads while reducing operational complexity and maintaining strong price-performance.
 
### Note

* Interactive Tables can only be created at the end of a data pipeline. They cannot be used as a source for other tables.
* The Interactive Table feature is only available in selected AWS regions. For more information, refer to the [Snowflake documentation on Interactive Tables](https://docs.snowflake.com/en/user-guide/interactive).

### Interactive Table Node Configuration

The Interactive Table Node has 3 configuration groups:

* [Node Properties](#node-properties)
* [General Options](#general-options)
* [Dynamic Interactive Table Options](#dynamic-interactive-table-options)

### Node Properties

| **Property** | **Description** |
|-------------|-----------------|
| **Storage Location** | (Required) Storage Location where the Interactive Table will be created |
| **Node Type** | (Required) Name of template used to create node objects |
| **Description** | A description of the Node's purpose |
| **Deploy Enabled** | If TRUE the Node will be deployed or redeployed when changes are detected<br/>If FALSE the Node will not be deployed or will be dropped during redeployment |

### General Options

 <img width="357" height="656" alt="image_inttbl_go" src="https://github.com/user-attachments/assets/cdbd9d96-28f3-44ca-becb-361957bbf4d7" />


| **Property** | **Description** |
|---------|-------------|
| **Multi Source** | Toggle: True/False<br/>Implementation of SQL UNIONs<br/>**True**: Combine multiple sources in a single Node<br/>True Options:<br/>- **UNION**: Combines with duplicate elimination<br/>- **UNION ALL**: Combines without duplicate elimination |
| **Enable tests** | Toggle: True/False<br/>Determines if tests are enabled |
| **Distinct** | Toggle: True/False<br/>**True**: Group by All is invisible. DISTINCT data is chosen for processing<br/>**False**: Group by All is visible |
| **Group by All** | Toggle: True/False<br/>**True**: DISTINCT is invisible. Data is grouped by all columns for processing<br/>**False**: DISTINCT is visible |
| **Cluster key** | True/False toggle for clustering:<br/>- **True**: Specify clustering column and optional expressions<br/>- **False**: No clustering |
| **Allow Expressions Cluster Key**| When cluster key is set to true. Allows to add an expression to the specified cluster key| 
| **Auto Refresh** | Toggle: True/False <br/>**True** Dynamic Interactive Table options visible.<br/>-**False**: Creates Static Interactive table.|


### Dynamic Interactive Table Options

<img width="383" height="395" alt="image" src="https://github.com/user-attachments/assets/1917d5d7-9ef3-4b7b-b2a9-dbd7b71343d5" />



| **Option** | **Description** |
|------------|----------------|
| **Lag Specification** |  **Time Value**: Frequency of the refresh<br/>- **Time Period**: Seconds/Minutes/Hours/Days |
| **Warehouse** | (Required) Name of warehouse used to refresh the Interactive Table |	

## Interactive Table Deployment

### Interactive Table Initial Deployment Parameters

The Interactive Table includes an environment parameter that allows you to specify a different warehouse to refresh an Interactive Table in different Environments.

The parameter name is `targetInteractiveTableWarehouse` and the default value is `DEV ENVIRONMENT`.

When set to `DEV ENVIRONMENT`, the value entered in the Interactive Table Options config "Warehouse on which to execute Interactive Table" is used when creating the Interactive Table.

```json
{
    "targetInteractiveTableWarehouse": "DEV ENVIRONMENT"
}
```

When set to any value other than `DEV ENVIRONMENT`, the Node attempts to create the Interactive Table using a Snowflake warehouse with the specified value.

For example, the Interactive Table refreshes using a warehouse named `compute_wh`.

```json
{
    "targetInteractiveTableWarehouse": "compute_wh"
}
```
> 📘 **Deployment of Nodes without adding parameters**
>
> This results in a WARNING stage that prompts you to execute the Node after adding parameters.

### Interactive Table Initial Deployment

When deployed for the first time into an Environment, the Interactive Table Node executes the following stage:

| **Stage** | **Description** |
|-----------|----------------|
| **Create Interactive Table** | This stage executes a `CREATE OR REPLACE` statement and creates an Interactive Table in the target Environment. |


#### Interactive Table Redeployment

After initial deployment, subsequent deployments may recreate the Interactive Table.

#### Recreating the Interactive Table 

The following config changes trigger REPLACE statements:

1. Warehouse name
2. Auto refresh setting  
3. Lag specification

This executes a stage:

| **Stage** | **Description** |
|-----------|----------------|
| **CREATE or REPLACE Interactive Table** | Executes CREATE or REPLACE to modify parameters |

Changes to the Node location, Node name, column-level description, or table-level description also result in a `CREATE OR REPLACE` statement. A table name change results in an `ALTER` statement.

### Redeployment with no changes 

If the Nodes are redeployed with no changes compared to the previous deployment, then no stages are executed.

### Interactive Table Undeployment

A table is dropped if all of these are true:

* The Interactive Table Node is deleted from a Workspace.
* The Workspace is committed to Git.
* The Workspace committed to Git is deployed to a higher-level Environment.

| **Stage** | **Description** |
|-----------|----------------|
| **Drop Interactive Table** | Removes table from target Environment |


---

## Code

### Interactive Table Code
* [Node definition](https://github.com/coalesceio/interactive-tables/blob/main/nodeTypes/InteractiveTable-602/definition.yml)
* [Create Template](https://github.com/coalesceio/interactive-tables/blob/main/nodeTypes/InteractiveTable-602/create.sql.j2)
* [Run Template](https://github.com/coalesceio/interactive-tables/blob/main/nodeTypes/InteractiveTable-602/run.sql.j2) 

