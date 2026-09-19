# Operating notes

Failure analyses from running the system this repository describes. Each note takes a set of incidents that looked unrelated, finds the mechanism underneath them, and states what would prove the conclusion wrong.

They are notes from operating a workflow, not lessons from finishing one. The first two cover the pipeline — upstream, then downstream; the third covers how the agents coordinate around it.

| # | Note | What it covers |
|---|---|---|
| 01 | [When Your Agent Crosses Two Records: Identity in Parallel Tool Calls](./01-parallel-fetch-identity.md) | Records acquiring each other's source pointers, and duplicate detection that could not read most of its own store. Why written rules failed where mechanical checks held. |
| 02 | [The Document Passed. It Was Still Wrong](./02-generated-document-integrity.md) | Documents that pass familiar checks and are still wrong: evidence crossing record boundaries, metrics detaching from their source claims, and correct text breaking on render or regeneration. |
| 03 | [The Human Was the Message Bus: Moving Agent Coordination onto the Blackboard](./03-coordination-inbox.md) | A human relaying handoffs between two agents, the shared inbox that replaced it, and three first-day failures — a missed instruction load, ID collisions, and a lost update — that turned remembered rules into an enforcing tool. |

All three notes end with falsification criteria and a commitment to report the result.
