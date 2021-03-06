title: BroadcastNestedLoopJoinExec

# BroadcastNestedLoopJoinExec Binary Physical Operator

`BroadcastNestedLoopJoinExec` is a link:SparkPlan.md#BinaryExecNode[binary physical operator] (with two child <<left, left>> and <<right, right>> physical operators) that is <<creating-instance, created>> (and converted to) when link:spark-sql-SparkStrategy-JoinSelection.adoc[JoinSelection] physical plan strategy finds a link:spark-sql-LogicalPlan-Join.adoc[Join] logical operator that meets either case:

* link:spark-sql-SparkStrategy-JoinSelection.adoc#canBuildRight[canBuildRight] join type and `right` physical operator link:spark-sql-SparkStrategy-JoinSelection.adoc#canBroadcast[broadcastable]

* link:spark-sql-SparkStrategy-JoinSelection.adoc#canBuildLeft[canBuildLeft] join type and `left` link:spark-sql-SparkStrategy-JoinSelection.adoc#canBroadcast[broadcastable]

* non-``InnerLike`` join type

NOTE: `BroadcastNestedLoopJoinExec` is the default physical operator when no other operators have matched link:spark-sql-SparkStrategy-JoinSelection.adoc#join-selection-requirements[selection requirements].

[NOTE]
====
link:spark-sql-SparkStrategy-JoinSelection.adoc#canBuildRight[canBuildRight] join types are:

* CROSS, INNER, LEFT ANTI, LEFT OUTER, LEFT SEMI or Existence

link:spark-sql-SparkStrategy-JoinSelection.adoc#canBuildLeft[canBuildLeft] join types are:

* CROSS, INNER, RIGHT OUTER
====

[source, scala]
----
val nums = spark.range(2)
val letters = ('a' to 'c').map(_.toString).toDF("letter")
val q = nums.crossJoin(letters)

scala> q.explain
== Physical Plan ==
BroadcastNestedLoopJoin BuildRight, Cross
:- *Range (0, 2, step=1, splits=Some(8))
+- BroadcastExchange IdentityBroadcastMode
   +- LocalTableScan [letter#69]
----

[[metrics]]
.BroadcastNestedLoopJoinExec's Performance Metrics
[cols="1,2,2",options="header",width="100%"]
|===
| Key
| Name (in web UI)
| Description

| [[numOutputRows]] `numOutputRows`
| number of output rows
|
|===

.BroadcastNestedLoopJoinExec in web UI (Details for Query)
image::images/spark-sql-BroadcastNestedLoopJoinExec-webui-details-for-query.png[align="center"]

[[requiredChildDistribution]]
.BroadcastNestedLoopJoinExec's Required Child Output Distributions
[cols="1m,2,2",options="header",width="100%"]
|===
| BuildSide
| Left Child
| Right Child

| BuildLeft
| link:spark-sql-Distribution-BroadcastDistribution.adoc[BroadcastDistribution] (uses `IdentityBroadcastMode` broadcast mode)
| link:spark-sql-Distribution-UnspecifiedDistribution.adoc[UnspecifiedDistribution]

| BuildRight
| link:spark-sql-Distribution-UnspecifiedDistribution.adoc[UnspecifiedDistribution]
| link:spark-sql-Distribution-BroadcastDistribution.adoc[BroadcastDistribution] (uses `IdentityBroadcastMode` broadcast mode)
|===

=== [[creating-instance]] Creating BroadcastNestedLoopJoinExec Instance

`BroadcastNestedLoopJoinExec` takes the following when created:

* [[left]] Left link:SparkPlan.md[physical operator]
* [[right]] Right link:SparkPlan.md[physical operator]
* [[buildSide]] `BuildSide`
* [[joinType]] link:spark-sql-joins.adoc#join-types[Join type]
* [[condition]] Optional join condition link:spark-sql-Expression.adoc[expressions]
