# Concepts {#concept_bnp_ydp_r2b .concept}

## Business flow {#section_udp_k4y_s2b .section}

Business flows: for business entities, the concept of business flows is abstract, enable users to organize data code development from a business perspective to improve task management efficiency. Business flows can be used repeatedly in different solutions.

Advantages:

-   Helps organize codes from business perspectives in a clearer way. Supports the code organization based on task types and multi-level sub-directories \(no more than levels\).
-   Views the overall work flows from business perspectives for optimization.
-   Provides dashboards of business flows for efficient development.
-   Organize the release and maintenance according to business flows.

## Solution {#section_srl_n4y_s2b .section}

Solution: You can customize and combine some business flows into a solution in a self-defining manner.

Advantages:

-   Multiple business flows
-   Reusable business flows in different solutions
-   Complete solutions for immersive development

## Component {#section_hgj_p4y_s2b .section}

A component is a SQL code procedure template with multiple input and output parameters. SQL code procedures are generally handled by introducing one or more source data tables through filtering, connect, aggregate, and other operations to process target tables for new business needs. The common logic in SQL can be abstract into components to enhance code reuse.

## Task {#section_pdk_12p_r2b .section}

A task is used to perform various operations on data. The following describes the uses of various tasks:

-   A data synchronization node task is used to copy data from RDS to MaxCompute.
-   A MaxCompute SQL node task is used to run MaxCompute SQL for data conversion.
-   A flow task is used to perform a series of data conversions among several inner SQL nodes.

Each task uses zero or more data tables \(data sets\) as an input, and generates one or more data tables \(data sets\) as the output.

Tasks are divided into node tasks, flow tasks, and inner nodes. See the relationships between these tasks in the following figure:

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16168/15366509968911_en-US.png)

-   A node task is an operation performed on data. It can be configured to be dependent on other node tasks and flow tasks to form a Directed Acyclic Graph \(DAG\).
-   A flow task is formed by a group of inner nodes with limited scope. We recommend using less than 10 flow tasks. Inner nodes of a flow task cannot depend on by other flow or node tasks. A flow task can be configured to be dependent on other flow and node tasks to form a DAG.
-   An inner node is a node inside a flow task. It basically provides the same capabilities as a node task. Its scheduling frequency is inherited from the scheduling frequency of the flow task, and cannot be configured independently.

Data execution can be selected as an operation type, see [Introduction of Node Type](../../../../intl.en-US/User Guide/Data development/Node type/Node type overview.md#).

For details about task scheduling parameter configuration, see [Scheduling configuration](../../../../intl.en-US/User Guide/Data development/Scheduling Configuration/Basic attributes.md#).

## Instance {#section_dg5_w2p_r2b .section}

When a task is scheduled by the system or triggered manually, an instance is generated. An instance represents an execution unit of the task at a certain moment. The instance contains the task operating time, operating status, operating logs, and other information. For example:

Assume that Task 1 is configured to run at 02:00 each day. In this case, the scheduling system automatically generates an execution unit at the time predefined by the periodic node task at 23:30 each day. That is, the instance of Task 1 to be run at 02:00 the next day. When it is detected that the upstream task is complete, the system automatically runs the Task 1 instance at 02:00 the next day.

**Note:** 

You can query task instance information on the **O&M Center** \> **Task O&M**page.

## Submit {#section_un1_tfp_r2b .section}

Commit refers to the process of publishing node and workflow tasks from developing environments to scheduling systems. After a task is submitted, its code and scheduling configuration are synchronized to the scheduling system, which schedules the task according to the configuration.

**Note:** Node tasks and flow tasks that are not submitted do not enter the scheduling system.

## Script {#section_z14_wfp_r2b .section}

A script is a code storage space that is provided for data analysis. The script code cannot be released to the scheduling system, and its scheduling parameters cannot be configured. It can only be used for data query and analysis.

## Resources and functions {#section_lj3_ggp_r2b .section}

Resources and functions are both MaxCompute concepts. For details, see [MaxCompute resources](https://www.alibabacloud.com/help/doc-detail/27822.htm) and [MaxCompute functions](https://www.alibabacloud.com/help/doc-detail/27823.htm).

In DataWorks, you can use interfaces for resource and function management. Resources and functions that are managed through other MaxCompute methods, cannot be queried in DataWorks.

## Output name {#section_phs_cnh_y2b .section}

The output name is the name of each task's output point, which is used when a user sets dependencies within a single tenant \(Alibaba Cloud account\). It represents a virtual entity that connects two tasks upstream and downstream.

When the user is setting up a task to form upstream-downstream dependencies with other tasks, the setting must be done based on the output name \(not the node name or node ID\). The output name of the task is also used as the input name for its downstream node.

**Note:** Similar to the ID of the task, the output name can also be a unique conceptual object for a task that is different from other tasks in the same tenant. Users can also add custom output names to a task, note, however, that the output node name is not allowed to repeat within the tenant.

