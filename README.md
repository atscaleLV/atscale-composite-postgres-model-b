# AtScale Composite PostgreSQL - Model B

This repository owns the Budget component of the three-repository composite
model lab. Its default branch is the deployable single-connection control:

- `lab_fact_budget_control` reads `src_b.fact_budget` through the AtScale data
  warehouse `pg_composite_control`;
- `lab_budget_control` exposes the `Budget Amount` metric; and
- the relationship uses the canonical packaged dimension `Lab Date Control`
  from the Model A repository.

Model B deliberately does **not** duplicate Date on `main`. `package.yml`
imports Model A at an immutable commit so Model A remains the owner of the
conformed dimension.

`package.yml` pins Model A to the full commit SHA that this revision was tested
against. Keep package pins immutable and advance them through normal pull
requests.

## Branches

- `main`: package-based control mapped to `pg_composite_control`. Use this with
  the Composite repository to compile and exercise cross-repository SML.
- `isolated-service-account`: standalone Budget model mapped only to
  `pg_model_b`. It owns a branch-local Date dimension so the regular model
  stays within one AtScale data warehouse and can compile independently.

The isolated branch proves the PostgreSQL service-account boundary. It is not
the branch consumed by the deployable composite because AtScale C2026.7.1
requires every regular component model to resolve to one data-warehouse ID.

## Prerequisites

Create the AtScale data warehouse used by the selected branch before linking
this repository in Design Center:

| Branch | AtScale external ID | PostgreSQL user | Source schemas | Aggregate schema |
|---|---|---|---|---|
| `main` | `pg_composite_control` | `svc_composite_control` | `shared`, `src_b` | `agg_control` |
| `isolated-service-account` | `pg_model_b` | `svc_b` | `shared`, `src_b` | `agg_b` |

The shareable PostgreSQL fixture is maintained with the Composite repository.
Its defaults use `host.docker.internal:54329`, database `atscale_lab`, and the
disposable local-only credentials shown in `.env.example`.

## Design Center

Link this repository as a separate project and make both branch names visible.
On `main`, Design Center must also be able to read the private Model A URL in
`package.yml` using the same GitHub credential. Deploy Model A first, then
Model B, then the Composite repository.

