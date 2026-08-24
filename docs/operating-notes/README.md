# Operating notes

Failure analyses from running the system this repository describes. Each note takes a set of incidents that looked unrelated, finds the mechanism underneath them, and states what would prove the conclusion wrong.

They are notes from operating a workflow, not lessons from finishing one. Read in order — the first covers the upstream half, the second the downstream.

| # | Note | What it covers |
|---|---|---|
| 01 | [When Your Agent Crosses Two Records: Identity in Parallel Tool Calls](./01-parallel-fetch-identity.md) | Records acquiring each other's source pointers, and duplicate detection that could not read most of its own store. Why written rules failed where mechanical checks held. |
| 02 | [The Document Passed. It Was Still Wrong](./02-generated-document-integrity.md) | Documents that pass familiar checks and are still wrong: evidence crossing record boundaries, metrics detaching from their source claims, and correct text breaking on render or regeneration. |

Both notes end with falsification criteria and a commitment to report the result.
