# The Umbrella Framework

An **Umbrella Workspace** is an independent Git repository that brings other independent Git repositories together for a shared system or purpose. Its root provides a starting point for contributors, records which repositories belong together, and owns the work that crosses repository boundaries.

[Workspace Steward](https://github.com/d1ys3nk0/ws) uses that inventory to populate child checkouts and coordinate work across them. A **Managed Workspace** is the set of Git worktrees that Workspace Steward creates or adopts for a particular task.

The [Kerbmark Guidelines](https://github.com/d1ys3nk0/kerbmark) defer to Umbrella for multi-repository workspace layout, repository ownership, and documentation placement.

## Choose a workspace type

| Type | Use it when |
| --- | --- |
| System-oriented | The repositories contribute to one system. The root documents how the components interact and how to operate or change them together. |
| Purpose-oriented | The repositories serve a shared activity, such as maintaining open-source projects or applying upgrades across a portfolio. |

Both types place child checkouts at `repositories/<name>/` and ignore `/repositories/` in the root repository.

```text
open-source/
├── README.md
├── AGENTS.md
├── .gitignore
├── workspace.yml
└── repositories/           # ignored by the root
    ├── formatter/
    └── release-tool/
```

Commit the root configuration and the ignore rule. Register each child in `workspace.yml` and keep its checkout outside the root index. The registry records repository names, checkout paths, remotes, and trunk branches. Each child is an independent Git repository with its own history and remote.

## Repository ownership

- Use the root `README.md` to explain the workspace's purpose and help contributors find repositories, setup instructions, and shared documentation. Put cross-repository decisions, integration procedures, and coordination tooling in the umbrella. Add workspace agent guidance in the root `AGENTS.md` when applicable and route contributors and agents to the relevant rules there.
- Keep each child's source, setup, configuration, tests, CI, release process, and component documentation in that repository. A fresh standalone clone must support its normal development and delivery workflow. Keep umbrella-specific agent instructions and skill bundles at the umbrella root. A standalone project can maintain its own agent instructions.
- Declare inter-repository dependencies explicitly. Use published packages, checked-in snapshots of versioned contracts, or caller-supplied paths as appropriate. Avoid assuming that a parent workspace, sibling checkout, or umbrella configuration exists.

## Documentation lifecycle

Create these directories at the root of the repository that owns the material, as each becomes useful. The umbrella owns shared material and each child owns material specific to that child.

| Location | What belongs there |
| --- | --- |
| `docs/` | Current architecture, supported usage, operating procedures, and decisions that still apply. |
| `backlog/` | Proposals, candidates, and active plans for work that remains unimplemented. |
| `archive/` | Retained historical material, such as closed plans, superseded decisions, incident records, and retired artifacts. |

Use these suggested subdirectories as material accumulates. Use `README.md` for directory indexes and lowercase, hyphenated names for additional topics.

| Location | What belongs there |
| --- | --- |
| `docs/product/` | Supported product behavior and workflows |
| `docs/engineering/` | Current architecture, engineering guidance, and decisions in effect |
| `docs/operations/` | Current runbooks and operating procedures |
| `backlog/product/` | Product proposals and candidate workflows |
| `backlog/engineering/` | Engineering proposals and open investigations |
| `backlog/plans/` | Active standalone implementation plans |
| `archive/plans/` | Completed, abandoned, and superseded plans |
| `archive/incidents/` | Historical incident records and reviews |
| `archive/artifacts/` | Retained evidence and historical deliverables |

Classify a document by its current role. An old recovery procedure that still applies belongs in `docs/`. A closed rollout report belongs in `archive/`. Record approval, implementation, and deployment status separately.

Put standalone implementation plans in `backlog/plans/YYMMDDHHMMSS-slug.md`. Use the creation time in UTC and a lowercase, hyphen-separated slug. Keep the filename through revisions and move completed, abandoned, or superseded plans to `archive/plans/` with the same name. Leave existing historical filenames intact when adopting the framework.

When closing a plan, update the owning `docs/` with guidance that now applies, carry any unfinished work into `backlog/`, and archive the execution record worth retaining. Preserve provenance and update incoming and relative links when files move.

A workspace can document shared terminology in a single glossary such as `docs/LANGUAGE.md`. For distinct topics, another option is to keep a `LANGUAGE.md` in each relevant documentation section and link it from that section's `README.md`. Keep one canonical definition per term and cross-reference it where other sections use it.

Root entrypoints and conventional legal notices can stay in their usual locations. Preserve paths owned by tools. In particular, keep OpenSpec specifications, active changes, and the native `openspec/changes/archive/` in the OpenSpec tree.

## Seasoned workspace example

This system-oriented workspace uses all the suggested documentation folders. It also shows `.agents/skills/` and `openspec/` for workspaces that use agent skills and OpenSpec.

```text
product-system/
├── README.md
├── AGENTS.md
├── LICENSE
├── .gitignore
├── workspace.yml
├── .agents/
│   └── skills/
│       └── workspace-operations/
│           └── SKILL.md
├── docs/
│   ├── README.md
│   ├── LANGUAGE.md
│   ├── product/
│   │   └── account-workflows.md
│   ├── engineering/
│   │   └── architecture.md
│   └── operations/
│       └── recovery.md
├── backlog/
│   ├── README.md
│   ├── product/
│   │   └── account-export.md
│   ├── engineering/
│   │   └── event-delivery.md
│   └── plans/
│       └── 261110090000-add-export-worker.md
├── archive/
│   ├── README.md
│   ├── plans/
│   │   └── 261001090000-migrate-queue.md
│   ├── incidents/
│   │   └── 2026-10-12-queue-outage.md
│   └── artifacts/
│       └── queue-migration-report.md
├── openspec/
│   ├── specs/
│   └── changes/
│       └── archive/
└── repositories/           # ignored by the root
    ├── aux-devtools/
    ├── doc-wiki/
    ├── iac-ansible/
    ├── iac-cicd/
    ├── iac-common/
    ├── svc-core/
    └── svc-web/
```

## Use Workspace Steward

For an existing umbrella, clone its root and registered children together. Replace the example URL with the root remote. Prefer SSH remotes for workspaces where contributors push using SSH keys.

```sh
ws clone git@example.com:team/product-system.git
```

To adopt the layout in an existing root Git checkout, run `ws init` if the registry is absent. Add the root ignore rule and declare the child repositories in `workspace.yml`. The following entry registers `svc-core` from the example workspace.

```yaml
---

version: 1
root:
  trunk: main

children:
  - name: svc-core
    path: repositories/svc-core
    url: git@example.com:team/svc-core.git
    trunk: main
```

Set each trunk and remote to match the actual repository. Run `ws check --config-only` to validate the registry, then `ws bootstrap` to populate missing checkouts and `ws check` to audit the result. Review and commit the root setup before creating a Managed Workspace for cross-repository work.

See the [Workspace Steward configuration guide](https://github.com/d1ys3nk0/ws/blob/main/docs/configuration.md) for the registry schema, dependency declarations, and lifecycle hooks.
