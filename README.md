# The Umbrella Framework

An **Umbrella Workspace** is an independent Git repository that brings other independent Git repositories together for a shared system or purpose. Its root provides a starting point for contributors, records which repositories belong together, and owns the work that crosses repository boundaries.

[Workspace Steward](https://github.com/d1ys3nk0/ws) uses that inventory to populate child checkouts and coordinate work across them. A **Managed Workspace** is the set of Git worktrees that Workspace Steward creates or adopts for a particular task.

## Choose a workspace type

| Type | Use it when | Child checkouts | Root `.gitignore` entry |
| --- | --- | --- | --- |
| System-oriented | The repositories contribute to one system. The root documents how the components interact and how to operate or change them together. | `components/<name>/` | `/components/` |
| Purpose-oriented | The repositories serve a shared activity, such as maintaining open-source projects or applying upgrades across a portfolio. | `repositories/<name>/` | `/repositories/` |

```text
product-system/
├── README.md
├── AGENTS.md
├── .gitignore
├── workspace.yml
└── components/             # ignored by the root
    ├── api/                # independent Git repository
    └── web/                # independent Git repository

open-source/
├── README.md
├── AGENTS.md
├── .gitignore
├── workspace.yml
└── repositories/           # ignored by the root
    ├── formatter/          # independent Git repository
    └── release-tool/       # independent Git repository
```

Commit the root configuration and the ignore rule. Register each child in `workspace.yml` and keep its checkout outside the root index. The registry records repository names, checkout paths, remotes, and trunk branches. Each child retains its own Git history and remote.

### Optional prefixes for system-oriented repositories

Use a short prefix when it helps readers recognize a repository's role in a system. The prefix is a naming convention. Workspace Steward uses the paths declared in `workspace.yml`.

| Prefix | Role |
| --- | --- |
| `aux-` | Supporting repositories, such as the umbrella itself or shared developer tooling. |
| `svc-` | Application services, including backends and web frontends. |
| `iac-` | Infrastructure and delivery automation, including provisioning and CI/CD. |
| `doc-` | Repositories dedicated to documentation, such as a wiki. |

For example, an umbrella repository named `aux-meta` could organize its children like this:

```text
aux-meta/
└── components/
    ├── aux-devtools/
    ├── doc-wiki/
    ├── iac-ansible/
    ├── iac-cicd/
    ├── iac-common/
    ├── svc-core/
    └── svc-web/
```

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

Classify a document by its current role. An old recovery procedure that still applies belongs in `docs/`. A closed rollout report belongs in `archive/`. Record approval, implementation, and deployment status separately.

Put standalone implementation plans in `backlog/plans/YYMMDDHHMMSS-slug.md`. Use the creation time in UTC and a lowercase, hyphen-separated slug. Keep the filename through revisions and move completed, abandoned, or superseded plans to `archive/plans/` with the same name. Leave existing historical filenames intact when adopting the framework.

When closing a plan, update the owning `docs/` with guidance that now applies, carry any unfinished work into `backlog/`, and archive the execution record worth retaining. Preserve provenance and update incoming and relative links when files move. Use `README.md` for directory indexes. Add topical subdirectories where they help readers find material, such as `docs/operations/`, `backlog/product/`, or `archive/incidents/`.

Root entrypoints and conventional legal notices can stay in their usual locations. Preserve paths owned by tools. In particular, keep OpenSpec specifications, active changes, and the native `openspec/changes/archive/` in the OpenSpec tree.

## Use Workspace Steward

For an existing umbrella, clone its root and registered children together. Replace the example URL with the root remote.

```sh
ws clone https://example.com/team/product-system.git
```

To adopt the layout in an existing root Git checkout, run `ws init` if the registry is absent. Add the root ignore rule and declare the child repositories in `workspace.yml`:

```yaml
---

version: 1
root:
  trunk: main

children:
  - name: api
    path: components/api
    url: https://example.com/team/api.git
    trunk: main
```

For a purpose-oriented workspace, use the corresponding `repositories/<name>` paths. Set each trunk and remote to match the actual repository. Run `ws check --config-only` to validate the registry, then `ws bootstrap` to populate missing checkouts and `ws check` to audit the result. Review and commit the root setup before creating a Managed Workspace for cross-repository work.

See the [Workspace Steward configuration guide](https://github.com/d1ys3nk0/ws/blob/main/docs/configuration.md) for the registry schema, dependency declarations, and lifecycle hooks.

## License

MIT License

Copyright (c) 2026 Denis Lysenko

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
