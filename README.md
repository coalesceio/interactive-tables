### Interactive Table Package

### Package Includes
* [Interactive Table](#interactive-table)
  
---
## Interactive Table

The Coalesce Interactive Table UDN allows you to build and deploy Snowflake Interactive Table, which are optimized for low-latency, high-performance analytical queries.

Interactive Table refresh automatically based on a SQL definition and maintain optimized storage for low-latency, high-concurrency queries. This makes them ideal for powering live dashboards, APIs, and sub-second analytical loads—while reducing operational complexity and maintaining strong price-performance.
 
**NOTE**: Interactive Table can be only created at the end of a data pipeline. It cannot be used as a source for other table.

### Interactive Table Node Configuration
* [Node properties](interactive-table-node-properties)
* [General Options](interactive-table-general-options)
* [Dynamic Interactive Table Options](interactive-table-dynamic-interactive-table-options)

### Node properties
| **Property** | **Description** |
|-------------|-----------------|
| **Storage Location** | (Required) Storage Location where the Interactive Table will be created |
| **Node Type** | (Required) Name of template used to create node objects |
| **Description** | A description of the node's purpose |
| **Deploy Enabled** | If TRUE the node will be deployed/redeployed when changes are detected<br/>If FALSE the node will not be deployed or will be dropped during redeployment |
					
					
### General Options 
 <img width="357" height="656" alt="image_inttbl_go" src="https://github.com/user-attachments/assets/cdbd9d96-28f3-44ca-becb-361957bbf4d7" />


| **Property** | **Description** |
|---------|-------------|
| **Multi Source** | Toggle: True/False<br/>Implementation of SQL UNIONs<br/>**True**: Combine multiple sources in a single node<br/>True Options:<br/>- **UNION**: Combines with duplicate elimination<br/>- **UNION ALL**: Combines without duplicate elimination |
| **Enable tests** | Toggle: True/False<br/>Determines if tests are enabled |
| **Distinct** | Toggle: True/False<br/>**True**: Group by All is invisible. DISTINCT data is chosen for processing<br/>**False**: Group by All is visible |
| **Group by All** | Toggle: True/False<br/>**True**: DISTINCT is invisible. Data is grouped by all columns for processing<br/>**False**: DISTINCT is visible |
| **Cluster key** | True/False toggle for clustering:<br/>- **True**: Specify clustering column and optional expressions<br/>- **False**: No clustering |
| **Allow Expressions Cluster Key**| When cluster key is set to true. Allows to add an expression to the specified cluster key| 
| **Auto Refresh** | Toggle: True/False <br>**True** Dynamic Interactive Table options visible.<br>-**False**: Creates Static Interactive table.|


### Dynamic Interactive Table Options
<img width="383" height="395" alt="image" src="https://github.com/user-attachments/assets/1917d5d7-9ef3-4b7b-b2a9-dbd7b71343d5" />



| **Option** | **Description** |
|------------|----------------|
| **Lag Specification** |  **Time Value**: Frequency of the refresh<br/>- **Time Period**: Seconds/Minutes/Hours/Days |
| **Warehouse** | (Required) Name of warehouse used to refresh the Interactive Table |	

### Interactive Table  Deployment

#### Interactive Table  Initial Deployment Parameters

The Interactive Table  includes an environment parameter that allows you to specify a different warehouse to refresh a Interactive Table in different environments.

The parameter name is `targetInteractiveTableWarehouse` and the default value is `DEV ENVIRONMENT`.

When set to `DEV ENVIRONMENT`, the value entered in the Interactive Table Options config "Warehouse on which to execute Interactive Table" will be used when creating the Interactive Table.

```json
{
    "targetInteractiveTableWarehouse": "DEV ENVIRONMENT"
}
```

When set to any value other than `DEV ENVIRONMENT` the node will attempt to create the Interactive Table using a Snowflake warehouse with the specified value.

For example, the Interactive Table will refresh using a warehouse named `compute_wh`.

```json
{
    "targetInteractiveTableWarehouse": "compute_wh"
}
```
> 📘 **Deployment of nodes without adding parameters**
>
> This results in a WARNING stage getting executed insisting to execute the node after adding parameters 

### Interactive Table  Initial Deployment

When deployed for the first time into an environment the Interactive Table  node will execute the following stage:

| **Stage** | **Description** |
|-----------|----------------|
| **Create  Interactive Table | This stage will execute a `CREATE OR REPLACE` statement and create a Interactive Table in the target environment. |


#### Interactive Table  Redeployment

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

Also if the location of the node, node name, column level description, and table level description results in an `CREATE or REPLACE` statement, whereas table name change result in a `ALTER` statement.

### Redeployment with no changes 

If the nodes are redeployed with no changes compared to previous deployment,then no stages are executed

### Interactive Table  Undeployment

A table will be dropped if all of these are true:

* The Interactive  Node is deleted from a space.
* The space is committed to Git.
* The space committed to Git is deployed to a higher level environment.

| **Stage** | **Description** |
|-----------|----------------|
| **Drop Interactive Table** | Removes table from target environment |


---

## Code

### Interactive Table Code
* [Node definition](https://github.com/coalesceio/interactive-tables/blob/main/nodeTypes/InteractiveTable-602/definition.yml)
* [Create Template](https://github.com/coalesceio/interactive-tables/blob/main/nodeTypes/InteractiveTable-602/create.sql.j2)
* [Run Template](https://github.com/coalesceio/interactive-tables/blob/main/nodeTypes/InteractiveTable-602/run.sql.j2) 

