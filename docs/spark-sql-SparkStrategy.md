title: SparkStrategy

# SparkStrategy -- Base for Execution Planning Strategies

`SparkStrategy` is a Catalyst link:spark-sql-catalyst-GenericStrategy.adoc[GenericStrategy] that converts a link:spark-sql-LogicalPlan.adoc[logical plan] into zero or more link:SparkPlan.md[physical plans].

`SparkStrategy` marks link:spark-sql-LogicalPlan.adoc[logical plans] (i.e. `LogicalPlan`) to be planned later (by some other `SparkStrategy` or after other `SparkStrategy` strategies have finished) using <<PlanLater, PlanLater>> physical operator.

[[planLater]]
[source, scala]
----
planLater(plan: LogicalPlan): SparkPlan = PlanLater(plan)
----

[NOTE]
====
`SparkStrategy` is used as `Strategy` type alias (aka _type synonym_) in Spark's code base that is defined in https://github.com/apache/spark/blob/master/sql/core/src/main/scala/org/apache/spark/sql/package.scala#L44[org.apache.spark.sql] package object, i.e.

[source, scala]
----
type Strategy = SparkStrategy
----

---
====

=== [[PlanLater]] PlanLater Physical Operator

CAUTION: FIXME
