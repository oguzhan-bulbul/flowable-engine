# flowable-engine — flowapp fork

Fork of [flowable/flowable-engine](https://github.com/flowable/flowable-engine) (`origin` =
`oguzhan-bulbul/flowable-engine`, branch `main`, upstream version `8.1.0-SNAPSHOT`). `../backend`
consumes it as locally-built `org.flowable:*:8.1.0-flowapp-SNAPSHOT` from `~/.m2`.
**Last resort:** change code here only when `../backend` has no other path (workspace rule).

## State of the fork
- **No local patches.** Code == upstream at `e948e961a7` (2026-07-22); only `CLAUDE.md` is ours.
  When a patch lands, list it here: commit · module · why · upstream PR/issue if any.
- The `-flowapp` version suffix is **not** in `pom.xml`; it is applied at build time (below).
  Committing a permanent version bump is an open follow-up.
- Engine API facts that shape backend code (child deployments, CMMN task service, `.or()` task leak)
  are documented where they are used: `../backend/CLAUDE.md` → Flowable.

## Commands
Backend imports five modules: `flowable-bom`, `flowable-engine`, `flowable-spring`,
`flowable-cmmn-spring-configurator`, `flowable-dmn-spring-configurator`. `-am` pulls their reactor deps.
Same JDK as backend (Java 25, Oracle GraalVM).

```bash
./mvnw -q versions:set -DnewVersion=8.1.0-flowapp-SNAPSHOT -DgenerateBackupPoms=true
./mvnw -q install -DskipTests -am \
  -pl modules/flowable-bom,modules/flowable-engine,modules/flowable-spring,modules/flowable-cmmn-spring-configurator,modules/flowable-dmn-spring-configurator
./mvnw -q versions:revert    # restores only the poms versions:set touched; never `git checkout -- .` — that would wipe an uncommitted patch
```
Recipe reconstructed 2026-09-13 from the artifacts in `~/.m2` (installed 2026-05-25). Re-verify on the
next rebuild and correct this file if it differs. Quick reactor sanity without a full build:
`./mvnw -q -pl modules/flowable-engine -am validate`.

## Syncing with upstream
`git remote add upstream https://github.com/flowable/flowable-engine.git` (once), then
`git fetch upstream && git merge upstream/main`, rebuild with the recipe above, run `../backend`'s
`./mvnw verify`.

## Commits
Conventional Commits (`fix: … (backend issue #N)`). A patch commit must also
update "State of the fork" above.
