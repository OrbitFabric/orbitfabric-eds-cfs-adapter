# Getting Started

## Install the current published release

Core [`v1.4.0`](https://github.com/OrbitFabric/orbitfabric/releases/tag/v1.4.0), GitHub Release Source [`v0.1.0`](https://github.com/OrbitFabric/orbitfabric-github-release-source/releases/tag/v0.1.0), and this adapter [`v0.1.1`](https://github.com/OrbitFabric/orbitfabric-eds-cfs-adapter/releases/tag/v0.1.1) were publicly released on 2026-09-25. The adapter's exact published Release Descriptor is indexed in the canonical Catalog. Product publication does not promote Core-owned adapter lifecycle or integration contracts from candidate maturity; no PyPI publication is assumed.

Use the published Core installation manifest and exact adapter version:

```bash
python3 -m venv .venv
. .venv/bin/activate
python -m pip install -r https://github.com/OrbitFabric/orbitfabric/releases/download/v1.4.0/orbitfabric-install.txt
orbitfabric adapter install eds-cfs --version 0.1.1
orbitfabric adapter list
orbitfabric adapter verify <instance-id>
```

The installed identity is `github.com/OrbitFabric:orbitfabric/eds-cfs@0.1.1`. The Catalog/provider path requires no repository clone, manual adapter download or Project Lock. Use `--catalog-revision <40-character-commit-sha>` to pin the Catalog, or `--catalog ./catalog.json` for a local snapshot. For the reference application's input files and native proof scripts, use the matching `v0.1.1` tagged source. Publication alone does not establish a new external greenfield end-to-end acceptance of this coordinated version.

## Historical manual installation reference

The following explicit-source instructions preserve the historical `v0.1.0` reference baseline and FAROTECH Source Coordinate. They are not the current `v0.1.1` Catalog-based onboarding path. Historical release identities, assets and native evidence are not rewritten.

This guide describes the normal consumer path for the `v0.1.0` OrbitFabric EDS-cFS Adapter release.

`v0.1.0` was published on 2026-09-07; its exact release assets and tagged source remain available for this historical procedure. Repository source bytes alone are not a substitute for published release artifacts.

## Validated baseline

The first release is deliberately narrow and evidence-backed.

| System | Validated baseline |
| --- | --- |
| OrbitFabric Core | `v1.3.0`, commit `a25917e81c90396df2b189834e83cf852fa4da5f` |
| NASA cFS | `v7.0.1`, commit `088b2fa828db9ff7e00733f1908e0eeb59f66ce3` |
| NASA EdsLib | `v7.0.1`, commit `2acc963b34f77692c6396555dcfb10ef43eb1046` |
| NASA cFE | commit `c5fb2b4d540bd55eb6c3707da7dd13eee679d4dd` |

No broader cFS, EdsLib or generic CCSDS EDS compatibility range is claimed by `v0.1.0`.

## Historical explicit-source installation and execution

The numbered steps below use historical `v0.1.0` identities, exact Core validation and target-runtime observations. Do not silently mix these historical assets with the current `v0.1.1` Catalog installation above.

## 1. Create a clean consumer environment

```bash
python3 -m venv .venv
source .venv/bin/activate
python -m pip install --upgrade pip
```

Optionally isolate Adapter Manager state:

```bash
export ORBITFABRIC_STATE_DIR="$PWD/.orbitfabric-state"
```

## 2. Install OrbitFabric Core

Install OrbitFabric Core `v1.3.0` or the exact validated commit above.

Check the Adapter Manager surface:

```bash
orbitfabric --help
orbitfabric adapter list
```

## 3. Obtain the published adapter assets

For `v0.1.0`, the GitHub Release contains publisher-owned assets only:

```text
orbitfabric_eds_cfs_adapter-0.1.0-py3-none-any.whl
adapter-release.json
SHA256SUMS
```

Keep them together and verify the downloaded bytes:

```bash
sha256sum -c SHA256SUMS
```

The canonical Adapter Source Coordinate is:

```text
github.com/FAROTECH:orbitfabric/eds-cfs
```

A Project Lock is project-owned consumer state and is not published as an adapter release asset.

## 4. Install through Adapter Manager

```bash
orbitfabric adapter install \
  adapter-release.json \
  --artifact orbitfabric_eds_cfs_adapter-0.1.0-py3-none-any.whl
```

Then inspect and verify the installed instance:

```bash
orbitfabric adapter list --json
orbitfabric adapter inspect <instance-id>
orbitfabric adapter verify <instance-id>
```

## 5. Produce a Core Integration Input Set

The adapter does not parse Mission Model YAML as a private API. It consumes the public Core Integration Input Set.

```bash
orbitfabric export integration-input-set \
  <mission-directory> \
  --output-dir <core-input-directory>
```

The handoff manifest is:

```text
<core-input-directory>/integration_input_manifest.json
```

## 6. Provide the EDS-cFS Projection Profile

The Profile owns explicit target binding intent. For cFS message allocation it binds OrbitFabric interfaces to mission-owned symbolic `CFE_MISSION` identities rather than universal absolute TopicId numbers.

Representative bindings are:

```text
CFE_MISSION/OF_DEMO_CMD_TOPICID
CFE_MISSION/OF_DEMO_STATUS_TLM_TOPICID
```

The selected cFS mission remains authoritative for concrete allocation values and MissionLib realization.

See [Target allocation](target-allocation.md).

## 7. Execute the adapter

```bash
orbitfabric adapter execute <instance-id> \
  --operation eds_cfs_projection \
  --input-set-manifest <core-input-directory>/integration_input_manifest.json \
  --profile <eds-cfs-profile.yaml> \
  --output-dir <output-directory>
```

The retained product output is:

```text
eds/mission.xml
traceability.json
integration_result.json
```

The EDS XML is an explicit interoperability boundary. The adapter does not bypass it with a hidden OrbitFabric-to-cFS direct path.

## Important runtime boundaries

`v0.1.0` records two target-runtime characterizations that consumers should understand:

1. EDS `ValidRange` metadata is projected faithfully, but the validated native lane does not automatically enforce every projected range at runtime.
2. On the frozen native lane, an undefined Function Code in a derived OF_DEMO command family can reach a valid generated typed handler. The adapter therefore does not claim automatic unknown-Function-Code rejection.

No adapter-local runtime guards are inserted to manufacture stronger target behavior than the validated lane provides.

See [Integration Coverage](../coverage/integration-coverage.md) for exact claim boundaries.
