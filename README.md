# Thoughts on Segment Registry

We need a system that allows us to define, compute, and manage segments for different entities.

Primary users of such a system include analytics teams, business users, and various components of internal and external systems.

Example use case: a user defines a segment in the discounts module to create personalized discounts. The same segment then appears in the marketing module, where the user can send notifications about those discounts.

This segment system sits between two APIs: one connects it to the external world (systems that interact with it), and the other connects it to the storage/computation layer that evaluates segments. There may be additional APIs for technical purposes, but these two are the main ones.

The computation layer influences the features the segment system can provide. For example, if the computation layer supports fuzzy search, the segment registry can expose fuzzy search; if not, it cannot. Different computation layers may vary in maturity: some offer a wide range of operations, while others only provide core capabilities. Therefore, the segment registry should be able to discover and expose the capabilities of the underlying computation layer to enhance validation and metadata for users.

The registry also needs a metadata catalog. It should inform users which entities are available, their fields and types, and how entities are connected to each other.

Some features may be disabled by configuration even if the underlying system supports them. For example, composing “regular” segments with each other or composing regular segments with materialized ones can lead to very complex segments that are computationally heavy. In systems that handle large data volumes and require relatively fast computations, it may be wise to prohibit such compositions. These restrictions can also be applied to lower-cost tiers of the platform.

At its core, the segment registry is a set of conditions used to filter instances of entities. At a given time or upon a specific signal, a segment defined by these conditions is materialized into a list of identifiers representing entries that satisfy the conditions. Other parts of the system can use this list to analyze data or trigger actions.

Potential flags that enable or disable features based on constraints of the computation layer or user configuration:

* Conditions on linked entities enabled (allow conditions not only on the main entity’s fields but also on fields of entities linked to the main one)
* Composition of segments allowed
* Composition of regular and materialized segments allowed
* Conditions on arrays of linked entities allowed (support conditions on entities that can have multiple entries linked to one main entity)

In a real-world implementation, one or more computation layers will be available alongside the segment registry. A user may select any supported computation layer or implement their own — for example, to use a database not yet supported or to add features the segment registry supports but that are not implemented for the desired database.

## Not covered topics

* Potential optimizations;
* Tracking materialized segments and purging unused ones;
* Metrics and logs: which key metrics should be provided?

