# Dependencies {#concept_gbk_lxx_p2b .concept}

The scheduling dependency configuration page is shown in the following figure:

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16303/15367330187925_en-US.png)

Overall scheduling logic: The downstream scheduling can be started only when the upstream scheduling is successfully implemented. Therefore, all workflow nodes must have at least one parent node. Scheduling dependency is used to set the parent-child relationship.

## Differences between automatic resolution and custom dependency {#section_nv5_p1y_p2b .section}

Automatic resolution resolves input and output of the current node based on the kinship in code.

A custom dependency is an additional upstream dependency added when the kinship is inaccurate. We recommend that you ensure the code kinship accuracy and reduce the number of custom dependencies. The following describes how to configure the correct node input and output.

Automatic Resolution: Select the option to automatically resolve scheduling dependencies from the code.

For example, the code of an ODPS SQL node is as follows:

```
insert overwrite table table_a as select * from project_b_name.table_b;
```

The system determines that the node depends on the node generating table\_b and finally generates table\_a . Therefore, it resolves that the output name of the parent node is project\_b\_name.table\_b, and the output name of the current node is project\_name.table\_a.

-   If you do not require dependencies by code resolution, select No.

-   If the code contains multiple temporary tables, whose names start with t\_, these tables are not resolved for scheduling dependency. You can configure the application attributes to define the first characters of temporary tables.

-   If a table in the code is both the output table and referenced table \(depended table\), it is resolved only as the output table.

-   If a table in the code is referenced or output for multiple times, only one scheduling dependency is resolved.


**Note:** By default, a table with a name starting with "t" is recognized as temporary tables. Automatic resolution is not used for temporary tables. If your table with a name starting with "t" is not a temporary table, contact your application owner to configure the temporary table signs in application attributes.

## Depended upstream node {#section_xs5_z3y_p2b .section}

-   Depended Upstream Node: Specifies the parent node of the current node.

    Parent Node Output Name: Specifies the output name of the parent node to be associated to schedule the parent-child relationship. It can be manually added or resolved from the code. After you enter the node output name, the parent node name and ID are automatically resolved. If they are not resolved, the output name does not exist and cannot be submitted because the task cannot depend on a nonexistent node.

-   Depended upstream node: You need enter the output name or table name of the parent node.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16303/15367330187926_en-US.png)

-   What measures should be taken to attach a task to a depended upstream node if the task does not have any upstream dependency? Generally, if a task does not have any upstream dependency, it can be attached to a virtual project node, such as project name\_root.

**Note:** All tasks that do not have any upstream dependency under the project can be attached to a virtual project node. If a project does not have any virtual project node, contact your project administrator to create one. We recommend that you name the virtual project node \{project name\}\_root. For example, the virtual project node for the project "test" can be named test\_root.

-   What measures should be taken if no depended upstream node exists? No parent node output name exists because data in the table is not generated by periodic tasks and may be manually uploaded.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16303/15367330187927_en-US.png)


## Current node output {#section_cfq_cty_p2b .section}

-   Current node output: Output of the current node.
-   Output Name: Scheduled output name of the current node, which is globally unique. This means that two tasks in a workflow cannot have the same output name.

    Generally, if a node generates a MaxCompute table, the output name is projectname.tablename. If other nodes depend on this node, set Parent Node Output Name to the same value as Output Name of the current node. The current node output name can be manually added or resolved by code. If the node has downstream nodes, their names are automatically resolved without manual entering.

    **Note:** The upstream dependency resolved by code and the current node output are displayed in red, which cannot be directly deleted from node configuration. In this case, right-click the beginning of the node code and choose to delete an input or exclude an output. Enter the name of the referenced or generated table in the code next to the equal sign. If the table name in the code does not have a prefix, the output to be excluded cannot have a prefix.


![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16303/15367330187928_en-US.png)

How should I enter the name of a table synchronized from IDB to MaxCompute if I do not know the current node output name? You need to first understand the concepts of the node name, output table name, and current node output name.

Node name: Name of a node, which may be duplicated. Nodes with the same name can be used in different business flows.

Output table name: Name of an output table of the current node.

Current node output name: It is generally the same as the output table name of the current name.

-   What measures should be taken to quickly develop SQL and configure tasks?

    DataWorks provides the xxx\_xxxx\_out node for the current node output by default. This node enables business flow visualization and helps you quickly set the dependencies between business nodes in a business flow. You can drag the nodes to directly set their upstream and downstream nodes.

-   How does the drag-and-drop upstream and downstream setting ensure the input and output of nodes and identify the dependencies of the downstream nodes?

    The system automatically resolves the input and output based on the kinship. You can select the output name and output table name of the current node based on the output kinship, rather than manually adding them one by one.


![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16303/15367330187929_en-US.png)

You can directly select the output table name of the current node. If XXX\_out is the output of the current node, the output table name of the current node must be configured. Otherwise, it is difficult for downstream tasks to depend on the current node.

**Note:** The current node output ended with "\_out" is the default output and cannot be deleted.

How are the downstream task dependencies configured if the current node output ended with "\_out" is used?

If XXX\_out is used as the current node output, how are the dependencies configured for the downstream nodes? Enter the depended table name in the Depended Upstream Node search box. The output names and output table names of the parent node are displayed.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16303/15367330187930_en-US.png)

You can directly select an output table name of the parent node. Code for modifying a dependency by adding comments besides by right-clicking the node.

```
For example, right-click an ODPS SQL task to add or delete an input or output:
 --@extra_input=table name --Add an input
--@extra_output=table name --Add an output
--@exclude_input=table name --Delete an input
 --@exclude_output=table name --Delete an output
```

Write this statement at the forefront of the comment, save the code, and click Resolve Input and Output on the scheduling page.

## FAQ {#section_sg1_kxy_p2b .section}

Q: I have resolved the output name of the parent node by code, but the node name and ID are empty and cannot be submitted. What should I do?

A: The reason for this problem is that no node output name is the current name. Therefore, you must right-click the code and delete an input to delete the table name dependency \(which does not affect the use of table data in the code\). Then, add an existing parent node output name in node configuration.

Q: What should I do if the name and ID of the downstream node in the current node output are empty and cannot be entered?

A: If the current node has subnodes, it automatically resolves the downstream node names and IDs without manual entering. In addition, a new task may not have subnodes.

Q: What is the "output name" of the current node used for?

A: The "output name" of the current node is used to establish dependencies between nodes. If the output name of node A is "ABC" and node B takes "ABC" as its input, the upstream and downstream relationship is established between nodes A and B.

Q: How is the "output name" of a node named?

A: Generally, you do not need to manually specify the "output name". The system automatically resolves the code and uses the table output from the code as the "output name". The table referenced in the code is used as the "input name".  The dependencies are automatically established.

Q: Can a node have multiple "output names"?

A: Yes. If a downstream node references an output name from the current node \(as the "parent node output name" of the downstream node\), it establishes a dependency with the current node.

Q: Can multiple nodes have the same "output name"?

A: No. The "output name" of each node must be globally unique. If multiple nodes output the same MaxCompute table, we recommend that you use "table name\_partition ID" as the output of these nodes.

