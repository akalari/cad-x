# Upstream synchronization and licensing

This document defines downstream engineering policy for `akalari/cad-x`.
cad-x is a maintained fork of FreeCAD and an independent product. It is not a
plugin, and the FreeCAD project does not endorse it.

## Repository configuration

Use these exact remotes:

- `origin`: `https://github.com/akalari/cad-x.git`
- `upstream`: `https://github.com/FreeCAD/FreeCAD.git`

Configure a clone as follows:

```shell
git remote set-url origin https://github.com/akalari/cad-x.git
git remote add upstream https://github.com/FreeCAD/FreeCAD.git
git remote set-url --push upstream DISABLED
git remote -v
```

If `upstream` already exists, use this command instead of `git remote add`:

```shell
git remote set-url upstream https://github.com/FreeCAD/FreeCAD.git
git remote set-url --push upstream DISABLED
```

The expected output must show the upstream fetch URL and `DISABLED` for the
upstream push URL. Never push cad-x branches or tags to `FreeCAD/FreeCAD`.

## Fork baseline

The fork was created from FreeCAD `main` at commit
`bcf8b5156b29bcdd53b1617a8b0c96e46be6d745`. The machine-readable record is
[`upstream-baseline.json`](upstream-baseline.json).

Update the baseline record only after a reviewed upstream synchronization PR
is merged. The record identifies the upstream commit that downstream `main`
contains.

## Upstream synchronization procedure

1. Start from a clean, current downstream `main`.
2. Fetch both remotes and inspect the fetched upstream changes.
3. Create a dedicated branch named `upstream-sync/YYYY-MM-DD`.
4. Merge `upstream/main` explicitly. Create a merge commit.
5. Do not rebase published cad-x product history.
6. Record every conflict and its resolution in the pull request.
7. Review the complete upstream range and the complete resulting diff.
8. Run native validation on Windows, macOS, and Linux.
9. Update `upstream-baseline.json` to the merged upstream commit and date.
10. Open a pull request from the sync branch to downstream `main`.

Example:

```shell
git switch main
git pull --ff-only origin main
git fetch --prune origin
git fetch --prune upstream
git log --oneline --decorate HEAD..upstream/main
git switch -c upstream-sync/YYYY-MM-DD
git merge --no-ff upstream/main
```

Do not use `git push --force` or `git push --force-with-lease` on downstream
`main`. Do not bypass required review or branch protection.

### Sync pull request checklist

- [ ] Record the old and new upstream commit hashes.
- [ ] Review upstream commits and changed dependency metadata.
- [ ] List all merge conflicts, affected files, and chosen resolutions.
- [ ] Confirm that downstream core hooks remain necessary and documented.
- [ ] Confirm that cad-x product behavior did not change unintentionally.
- [ ] Validate native Windows builds and tests.
- [ ] Validate native macOS builds and tests.
- [ ] Validate native Linux builds and tests.
- [ ] Review license, notice, submodule, and third-party inventory changes.
- [ ] Update and parse `upstream-baseline.json`.
- [ ] Confirm that the branch targets downstream `main`.

## Divergence and downstream patches

Keep fork divergence small:

- Put future product work in `src/Mod/CadX` and downstream packaging or
  branding surfaces when possible.
- Track each downstream patch in its pull request. State its purpose, owner,
  affected upstream area, and expected removal or upstreaming condition.
- Document every shared-core hook and why an existing native extension point is
  not sufficient.
- Do not mix a generic FreeCAD fix with cad-x product behavior.
- Put generic fixes in separate commits and separate pull requests. Submit
  suitable fixes to `FreeCAD/FreeCAD` under its contribution and AI policies.
- Remove a downstream patch after the equivalent upstream change is included.

## Releases, branches, and tags

- Downstream `main` is the protected integration branch.
- Use short-lived topic branches for product changes and
  `upstream-sync/YYYY-MM-DD` branches for upstream merges.
- Create a release branch only when a release needs stabilization after
  `main` moves forward. Name it `release/<version>`.
- Create annotated release tags from a reviewed commit on downstream `main` or
  its approved release branch.
- Use a cad-x version tag that cannot be confused with an upstream FreeCAD tag.
- Never move or replace a published release tag. Publish a new corrective tag.
- Never force-push downstream `main`.

## Dependencies, submodules, and assets

Treat dependency, submodule, and asset updates as code changes:

- Pin and review the exact new revision or artifact.
- Review provenance, maintainer status, integrity data, license, notices,
  source availability, and platform impact.
- Inspect submodule commits and their nested dependencies. Do not approve only
  a changed pointer.
- Do not combine an unrelated dependency update with an upstream sync.
- Update the third-party inventory and distribution notices when content or
  license terms change.
- Require native Windows, macOS, and Linux validation for shipped dependency
  changes.

## Recovery from a bad upstream merge

Do not rewrite published downstream history.

1. Stop releases from the affected commit.
2. Create a recovery branch from current downstream `main`.
3. Revert the upstream merge with `git revert -m 1 <merge-commit>`.
4. Resolve and document any revert conflicts.
5. Run the same native validation required for an upstream sync.
6. Open and merge a reviewed recovery pull request.
7. Correct the problem on a new `upstream-sync/YYYY-MM-DD` branch.
8. Update the baseline record only when downstream `main` again contains the
   reviewed upstream baseline.

If the bad merge is not yet on downstream `main`, close the pull request and
replace the branch. Do not force-push a reviewed branch in a way that hides the
previous result.

## Security boundary

Treat all upstream refs, fetched commits, tags, submodules, artifacts, and pull
request branches as untrusted until review is complete. Do not run
write-authorized automation from those refs.

Workflows with repository, package, release, or cloud write credentials must
execute only workflow code from protected downstream `main`. Use read-only
permissions for review and validation of untrusted code. Do not expose secrets
to builds or tests from upstream refs or pull request branches.

## Licensing guidance

This section is engineering guidance, not legal advice. Escalate unclear
licensing questions for legal review before distribution.

### FreeCAD and new cad-x code

- FreeCAD is licensed under LGPL-2.1-or-later according to its
  [contribution guidance](../../CONTRIBUTING.md#4-licensing-ownership-and-credit),
  root [license](../../LICENSE), and existing file headers.
- Preserve all copyright notices, attribution, SPDX identifiers, and
  file-specific licenses.
- Modifications to existing FreeCAD code keep a license compatible with the
  existing file and project license.
- The planned native `src/Mod/CadX` module uses LGPL-2.1-or-later unless a
  reviewed file-specific reason requires another compatible license.
- Review copied or moved files by source file. Do not assume that every file in
  the repository has the root license.

### Open CASCADE Technology

Open CASCADE Technology (OCCT) is licensed under LGPL-2.1 with the Open CASCADE
exception. A distribution must preserve its license and notices. It must also
provide exact corresponding source and build information for the distributed
version and preserve applicable replacement and relinking rights. Review the
[OCCT licensing page](https://dev.opencascade.org/resources/licensing) and the
license files for the exact shipped OCCT version before release.

### Third-party content and cad-x-rust

- Third-party files, submodules, libraries, fonts, translations, and assets
  retain their own licenses. Review them in an inventory before distribution.
- The upstream application links to the
  [FreeCAD third-party library inventory](https://wiki.freecad.org/Third_Party_Libraries).
  The release inventory must also cover downstream additions and exact shipped
  versions.
- Code adapted from the MIT-licensed
  [`akalari/cad-x-rust`](https://github.com/akalari/cad-x-rust) must retain the
  applicable MIT copyright attribution and license notice.
- Prefer an original C++ implementation based on documented and tested
  behavior. Do not mechanically translate Rust source.

### Names and attribution

cad-x branding must not state or imply endorsement by the FreeCAD project or
the FreeCAD Project Association. Preserve required FreeCAD attribution and
third-party notices. Review names, marks, icons, and other assets separately
before a downstream release.
