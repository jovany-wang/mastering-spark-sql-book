title: UnsafeProjection

# UnsafeProjection -- Generic Function to Encode InternalRows to UnsafeRows

`UnsafeProjection` is a `Projection` function that encodes <<spark-sql-InternalRow.adoc#, InternalRows>> as <<spark-sql-UnsafeRow.adoc#, UnsafeRows>>.

```
UnsafeProjection: InternalRow =[apply]=> UnsafeRow
```

[NOTE]
====
Spark SQL uses `UnsafeProjection` factory object to <<create, create>> concrete _adhoc_ `UnsafeProjection` instances.

The base `UnsafeProjection` has no concrete named implementations and <<create, create>> factory methods delegate all calls to link:spark-sql-GenerateUnsafeProjection.adoc[GenerateUnsafeProjection.generate] in the end.
====

=== [[create]] Creating UnsafeProjection -- `create` Factory Method

[source, scala]
----
create(schema: StructType): UnsafeProjection      // <1>
create(fields: Array[DataType]): UnsafeProjection // <2>
create(expr: Expression): UnsafeProjection        // <3>
create(exprs: Seq[Expression], inputSchema: Seq[Attribute]): UnsafeProjection // <4>
create(exprs: Seq[Expression]): UnsafeProjection  // <5>
create(
  exprs: Seq[Expression],
  inputSchema: Seq[Attribute],
  subexpressionEliminationEnabled: Boolean): UnsafeProjection
----
<1> `create` takes the link:spark-sql-DataType.adoc[DataTypes] from `schema` and calls the 2nd `create`
<2> `create` creates a link:spark-sql-Expression-BoundReference.adoc[BoundReference] per field in `fields` and calls the 5th `create`
<3> `create` calls the 5th `create`
<4> `create` calls the 5th `create`
<5> The main `create` that does the heavy work

`create` transforms all <<spark-sql-Expression-CreateNamedStruct.adoc#, CreateNamedStruct>> expressions to `CreateNamedStructUnsafe` in every link:spark-sql-Expression-BoundReference.adoc[BoundReference] in the input `exprs`.

In the end, `create` requests `GenerateUnsafeProjection` to link:spark-sql-GenerateUnsafeProjection.adoc#generate[generate a UnsafeProjection].

NOTE: A variant of `create` takes `subexpressionEliminationEnabled` flag (that usually is link:SparkPlan.md#subexpressionEliminationEnabled[subexpressionEliminationEnabled] flag of `SparkPlan`).
