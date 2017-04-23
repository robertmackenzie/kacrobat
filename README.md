Kacrobat
========

Monitors and balances topics in Kafka using consistent hashing.

Design
------

* We want to ensure a good distribution of load. We should be able to rely on consistent hashing to uniformly distribute topic-partitions across nodes. To take account of usage distribution, we could use a stable marriage algorithm variant, such as the one Akamai use to best match clients and servers. To avoid thrashing with use variance during normal operation, we should do this periodically rather than continuously and use appropriately weighted readings.
* We want to minimise the transfer of topics when a node is added/removed or when the replica or partition count is changed. Again, consistent hashing should have the right properties. If you are launching or removing several nodes at once we should avoid transferring data repeatedly and offer an atomic mechanism.
* We want to avoid re-balancing topics in the case of an ephemeral failure. Decommissioning a node should be a manual step. Adding a node should be a manual step.
* We want to take advantage of Kafka and Zookeeper for any state. Topic metadata state can be read from Kafka. Broker metadata can be read from Zookeeper. State specific to Kacrobat can be stored in a Kafka topic which can be synchronised to in-memory data structures on startup.
* We want to ensure the availability and durability intended by the topic owner. We want to ensure topic replicas r don't end up on the same node. In the case the number of nodes would be less than r, we should provide an error.
