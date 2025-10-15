# Rules_and_Guidelines_react-WordPress-PHP-plugin-dev
Augment AI Rules and Guidelines for WordPress Plugin Development

**Copy and Paste** everything below the line and add as a Rule and Guidelines for your Augment AI Coding project in VS Code.

---
type: "always_apply"
description: "Example description"
---

# Augment AI Rules & User Guidelines – VS Code Development for WordPress Plugins

This document defines standards, workflows, and guidelines for WordPress plugin development using React for admin UI, PHP for backend, and mySQL for database. It enforces consistency, maintainability, security, and professionalism in Augment AI projects. Guidelines fit VS Code workflows.

---

## Application Architecture

RULE: Decoupled Server-Client Model with WordPress Plugin Server and Cordova-Enhanced PWA Client

The server is a WordPress plugin on a standard host, managing backend logic, mySQL, and WP REST API extensions. The client is a PWA with React UI, enhanced by Apache Cordova for native mobile support on iOS/Android, accessing hardware like camera/GPS/notifications while enabling offline use. This separates concerns, supports offline/native features, and allows PWA/app installs. Specific: Expose endpoints at /wp-json/augment/v1/; register service worker; config.xml for Cordova platforms/plugins. General: Quarterly API audits; README connection docs; test PWA prompts/Cordova builds across browsers/devices.

#### Key Principles
- Server as Plugin: Use WP for auth (JWT/sessions), cron, nonces. Prefix '**augment_**' (Use App specific nomenclature); migrate on activation; version-check WP updates.
- Client as Cordova-Enhanced PWA: React UI, Workbox service workers, IndexedDB storage. Manifest.json for metadata; add-to-homescreen; offline sync queues. Wrap in Cordova for hybrid apps, plugins (e.g., camera/geolocation); build iOS/Android; webview PWA compat. Fetch to WP REST; @wordpress/api-fetch.
- Connection: HTTPS only; OAuth2/WP passwords auth; retry on failures; heartbeat pings; GZIP compress; Cordova InAppBrowser for auth.
- Avoid: WP script embeds in PWA/Cordova; client mySQL; unversioned APIs; ignore Lighthouse/Cordova conflicts.
- Benefits: Low latency offline; engagement via installs; native hardware/app stores; modularity vs. WP conflicts; multi-host/device scale.
- Add: Plugin webhooks for PWA/Cordova pushes; Push API w/ cordova-plugin-push; service worker API cache; reconnect sync w/ conflicts.

GUIDELINE: Offline-First Sync. Queue IndexedDB mutations offline; sync WP REST POST/PUT on reconnect; last-write-wins/server-authoritative conflicts; log states; test w/ DevTools throttling/Cordova emulator.

- Plugin Layer: Extend REST controllers; WP capability validate; server sanitize; user-context logs; IP/user rate-limits; Cordova device headers.
- PWA Layer: Service worker caching (stale-while-revalidate reads, network-first writes); install/activate events; permissions (notifs/storage/camera); lazy-load; Cordova hooks for lifecycle (pause/resume).
- Integration: WP nonces in calls; auto token refresh; cached fallback 5xx; navigator.onLine/beacon monitor; Cordova network status.
- Security: CORS (PWA/Cordova origins); AJAX origin validate; encrypt local data; DOMPurify XSS audit; Cordova whitelists.
- Performance: Pre-cache assets; fields-param API opt; React Query cache; >90 Lighthouse; Cordova startup/hardware latency profile.

ENFORCE: CI Validate: WP-CLI endpoints; Puppeteer PWA offline; cordova emulate/build tests; fail >5% sync; OpenAPI schema. PRs: PWA/Cordova tests. Train PWA/Cordova debug; draw.io diagram w/ mobile.

VS Code: Live Server PWA preview; Lighthouse audits; WP-CLI server; Network traces; worker debug; Cordova ext for builds/emulate.

RULE: Progressive Enhancement. Core as SPA sans PWA/Cordova; add offline/install/native progressively; graceful degrade test; polyfills legacy; adoption metrics web/native.

---

## Core Development Rules

### 1. Architecture and Code Organization

RULE: Layered Architecture w/ Separation of Concerns

Modular plugins for reuse. Defined layer roles. SOLID on WP hooks/filters/React. Limit 3-5 layers/plugin. Quarterly layer review; doc map.

#### Key Principles
- Modularity: Independent modules. Sep PHP services/React/mySQL; plugin prefix; <=500 lines/module; subfolders large; /cordova/plugins/.
- Dep Mgmt: WP/Composer PHP. wp_enqueue_script React; deps/version; role-conditional; config.xml Cordova plugins.
- Avoid: Global coupling w/o abstraction; god classes; circular deps; direct $post; hardcoded paths; cross-layer statics; Cordova misuse web-only.
- Benefits: Readability/debug/multisite; theme compat/updates/load; cross-platform mobile.
- Add: PHP namespace; admin React conditional; uninstall dequeue; lazy services; Cordova platform hooks.

GUIDELINE: Event-Driven Coupling. WP actions/filters events; React EventEmitter; control subscribe; no direct calls; log; Cordova deviceready extend.

- Data/Logic: Business/val/mySQL $wpdb. DDD entities/repos; indexes; prefixed tables; activate backup; update migrate.
- Presentation: React admin/PHP templates. Hooks/Redux; style enqueue; CSS modules; minify CSS; opt images; Cordova responsive webview.
- Control: WP hooks/AJAX. Nonces/val/log; add_action/do_action; priorities; deactivate remove; cron; JS bridges Cordova.
- Services: Stateless PHP externals. WP-versioned transients; options API cache; timeouts/retries; resp val.
- Utilities: Pure sanitize funcs; WP-less test; export/reuse; inline small; memoize; Cordova-agnostic.
- Types: PHP hook interfaces; TS React. Contracts/generics/runtime; PHP type-hint; PropTypes; defaults/val; Cordova returns guards.

ENFORCE: Boundary maintain; service delegate queries. PHPCS/layer sniffs; viol build fail. Audit/PR; viol log. SOLID train; diagram w/ Cordova.

VS Code: Outline nav; Error Lens; Dep Cruiser; WP snippets; dep graph; hover docs; layer colors; Madge circ; tasks scans; Cordova tasks.

RULE: React Micro-Frontends. Feature apps; dynamic imports; core utils share; version/isolate tests; Cordova webview lazy compat.

---

### 2. File Naming and Structure

RULE: Consistent Naming

Intent names. CR review. README doc; lint; yearly update.

- Services: camelCaseService.php /includes/services/; slug prefix; abstract suffix; singleton; doc.
- Components/UI: PascalCase.jsx /admin/js/components/; snake_case.php /admin/partials/; features; index exports; stories; adj tests; camelCaseCordovaHook.js /cordova/hooks/.
- Utilities: camelCase.php/js /includes/utils/; 1 func/file complex; barrel exports; types adj; version public.
- Models/Types: PascalCase.php/ts /includes/models/; intf/class sep; DTOs/val; schema.
- Tests: .test.php/jsx mirror /tests/; per-file cov; shared setup; Cordova mocks.

GUIDELINE: Config/Log Std. plugin-slug-config.php /includes/config/; plugin-slug-logs.sql /logs/; ts logs; API .json; Cordova config.xml.

ENFORCE: No generics. WP: /includes/admin/public/assets/languages/templates/build/migrations. CI check. Perms; archive.

VS Code: Compact Folders; Ctrl+P; Prettier/ESLint/PHPCS save; hook search; exclude node; globs/icons; Nesting.

RULE: Assets Type/Version. /assets/css/js/images/; hash names; /src/ unmin; /www/ Cordova React.

---

### 3. Language and Framework Standards

RULE: Strict Configs

Strict type/val. CI enforce. Monthly audit; train.

- Type: PHP strict_types=1; TS strict; prepared $wpdb; WP caps; ? null; enums; JS strict; Cordova callbacks.
- Returns: PHP/TS declare; WP error unions; void no-ret; mixed last; annotate.
- No untyped: Avoid mixed/raw q; escape out; no var_dump/die/eval.
- Errors: Try-catch WP_Error; error_log; custom exc; early throw; rethrow; Cordova plugin errors.
- Val: WP sanitize/Zod; AJAX nonces; rate/CSRF/schema; Cordova perms.

GUIDELINE: Functional. Pure utils; array map/filter/reduce; no side React hooks; compose/test composable; Cordova-agnostic pure.

#### PHP/WP Rules

Vars: camelCase; const pref; no globals but WP; local prefix; $this_; _ private; ctor init.

Funcs: Hook closures; destructure; 20-30 lines; private props; pure/immut args; early ret; SRP; PHP8 named; doc.

Strings: Double interp; single lit; esc_html; __() trans; plural; sprintf; . concat.

Cond: ===; early ret; caps; WP_DEBUG; role switch; ternary; PHP8 match.

Async: WP AJAX; try-catch; wp_send_json_error; AbortController; concurrent limit; poll; workers; chains; Cordova async.

Modules: Composer autoload; grouped imports; spread; require_once; conditional/lazy; dynamic rare; Cordova requires.

Safety: wp_verify_nonce; esc_attr; ?? def; apply_filters in; email/URL/IP val; Cordova whitelists.

Arrays: map/filter/reduce; foreach; pad; key_exists; sort; unique; diff/merge.

PHP: Hook intf; annotate; traits; abstract; final meth; namespace.

Debug: JSON error_log; no echo; WP_DEBUG cond; levels/stack; profiler; Cordova console.

Data: readonly; WP_Query > arr; transient cache; safe ser/unser; val unser.

Input: sanitize_*; guards; upload size/type/MIME; virus/hash; Cordova file val.

Doc: PHPDoc/JSDoc; wp_date; @since; @param/@return/@ex; @cordova-plugin.

Frameworks: WP std; React hooks; mySQL index; monthly PHPCS/ESLint; memo; SQL explain; opt; Cordova CLI.

#### React/mySQL
- React: State hooks; WP REST fetch; sanitize props; useCallback; boundaries; suspense; context; Cordova useEffect lifecycle.
- mySQL: prepare/esc_sql; trans multi-q; error fail/log; vacuum.

RULE: PHP OOP Opt. Comp > inherit; depth <=3; DI services; inj val; inst profile; Cordova wrappers.

VS Code: Intelephense/TS; Problems; formatOnSave; ESLint/PHPCS; WP doc bind; colors; folding/snippets; DocBlockr; Ctrl+Shift+D; Cordova config edit.

GUIDELINE: Promise Async. React chain; PHP8+ await cron; rej global; 30s timeout; exp retry; Promise Cordova.

---

## Testing Requirements

### 4. Test Coverage Standards

RULE: 98% Coverage 18 Categories

98% line/branch/path WP flows/edges/multisite/threats. Branch 95% min. Trend up; monthly report.

Cover 18 cats.

1. Unit: Isolate PHP/React/mySQL. Edges/mock $wpdb/WP. Ex: tax mock opts. Pure first/param/seed/assert types; Cordova mocks.

GUIDELINE: Property Units. Hypothesis random; invariants; auto edges; CI reg; device mocks.

2. Integration: PHP/React/REST/mySQL flows. Hooks/DB/3rd. Ex: pay hook mySQL. Nonce/mock/env/clean; Cordova int.

3. E2E: Full stack React-PHP-mySQL. Multisite/workflows. Ex: checkout email. WP-CLI/video/journeys/email assert. Playwright headless UI/nav WP admin/React; verify PWA/Cordova to backend/mySQL; cross-browser Chrome/FF/Safari; emulator farms.

4. System: Plugin WP env/health/res. PHP-mySQL. Ex: staging act. Themes/mem/conflicts/load; Cordova lifecycle.

5. Regression: Post-change re-run. Impact. Ex: auth update. WP ver/file/baseline; Cordova builds.

6. Acceptance: WP reqs/BDD. Scenarios. Ex: settings <5s. Stories/Gherkin/sign/crit; Cordova mobile.

7. Perf: Benchmark time/thru. Ex: JMeter AJAX <200ms. Locks/50 conc/flame/mem; Cordova hardware.

8. Load/Stress: Surges/bottlenecks. Ex: 10x users/leak. Cron/ramp/break/rec; Cordova mobile.

9. Sec: Vuln/pen/WP. OWASP/nonces. Ex: ZAP SQL/XSS/CSRF. Caps/fuzz/Burp/bypass; Cordova plugins.

10. Compat: WP vers/themes/plugins/browsers/devices. Back/forward. Ex: React WP6+ browsers. Gutenberg/mobile/matrix/theme. Playwright multi-browser/viewports; PWA/Cordova resp; iOS/Android builds.

11. A11y: WCAG/ADA React/PHP. Scans. Ex: screen forms. Focus/contrast/WAVE/keyboard. Playwright axe-core; ARIA/nav/reader; Cordova voiceover.

12. Exploratory: Probes. Heur/SBTM. Ex: unicode search. Clicks/edges/devices/personas; Cordova hardware.

13. Smoke: Basic post-build. Ex: act/load admin. Shortcodes/list/menu/console; Cordova launch.

14. Sanity: High-impact post-change. Ex: nonce patch. Roles/switch/cache/perms; mobile.

15. Usability: Intuit/SUS. Tasks/err. Ex: settings config. Time/heatmap/A/B/survey; Cordova touch.

16. L10n/I18n: Multi/RTL/formats. Pseudo. Ex: es dates. .po/len/switch/RTL; Cordova locale.

17. Recovery: Disrupt/rec. Rollback/chaos. Ex: DB term. Cache/failover/backup/loss; Cordova crash.

18. Data Integrity: Persist/val. ACID/rest. Ex: import reject. Backup/charset/dup/constr; Cordova offline sync.

RULE: Expand 20 w/ Visual/Network. Percy visual reg; Cypress net; UI theme; slow sim. Playwright screenshots key states; compare baselines; React UI changes WP themes/Cordova platforms.

ENFORCE:
- Gates: No merge <98% peer. Sonar/Codecov; block low; delta approve.
- Crit: BVT/E2E/Reg/Sec+Perf/Rec auth/trans; manual/sign/risk.
- CI/CD: GH Actions/WP-CLI auto/parallel/flaky/approv/retry/artifacts/envs; Cordova CI builds.
- Vigil: Q audits/TDD/BDD/dash/gaps/train; goal 99.
- Add: PHP8.1+; BrowserStack/lab; WP nightly. Playwright CI parallel browsers; WP-CLI prov/teardown; Cordova emulate.

VS Code: Test Explorer (PHPUnit/Jest); tasks cov; Codecov badges; save/debug/sidebar/inline; Runner visuals/gutters. Playwright ext; trace/code gen; Cordova tasks.

---

### 5. Test Organization

RULE: Mirror Structure/Readable

Tests mirror src. AAA all. Colocate; naming conv.

- Unit: /tests/unit/ mirror (userService.test.php); WP mocks; per cov; group assert/types.
- Integration: /tests/int/ sub (ajax/); shared DB/env/teardown/clean; Cordova mocks.
- E2E: /tests/e2e/ journeys (admin/); page objs/selectors/wait/screenshot/email. Playwright script; POM locators React; nonce intercept; API/UI assert headless/headed; modules admin/PWA/Cordova flows; native plugins emulate.
- Utils: /tests/utils/ mocks/fact/seeder/config; shared.
- Fixtures: /tests/fix/ (users.sql); schema/import/clean; Cordova config.

PATTERN: Per-file. Names: shouldReturnId; desc/it/tables/nest/skip/tags.

Practices:
- Naming: <mod>.test.<ext>; tables/nest/skip/tags.
- Data: Param/afterEach clean/DB reset/teardown/global/fact.
- Mock: External only; afterEach reset; spies/once/partial; Cordova deviceready.
- CI: Parallel/art/flaky/HTML/email/timeout; Cordova parallel.
- Pitfalls: Over-mock/brittle/select/timeout/slow; mock time/stale; Cordova var.
- Benefits: Debug/TDD/CI cov/iso/cross-platform.

GUIDELINE: Data Builders. Fluent complex objs; default/schema; Cordova device data.

Cache Clear:
Clear Jest pre consistency. Before suite. Doc.

1. Loc: node/.cache/jest/; config/backup/multi.
2. Cmd: npx jest --clearCache; watch/select/force.
3. WF: pkg test:clean; tasks/CI/Husky/pre/log/hook.
4. Ver: --no-cache/warn/diff/full.
5. Edge: Per-pkg/inval/monorepo/trans/time/disk.

ENFORCE: README; gate/script/warn/lint. Daily/train/monitor.

VS Code: Search/symbols; bps; watch; clear/key/cov/config/groups; toggle. Playwright ext run/debug/trace/browser; Cordova emulate.

RULE: Contract APIs. Pact consumer; indep REST; compat/CI; Cordova net cond.

---

## Documentation Standards

### 6. Automatic Updates

RULE: Update Docs Changes

Docs code: ver/test/review. Auto/commit. Q audit; 90% cov.

- VER: Header/notes. sem-release Composer; Tested up; chlog link; PHP/WP min; Cordova reqs.
- CHANGELOG: Files/feats/bugs. Added/Changed/etc; Trac/breaking/mig/credits; Cordova.
- README: Instr/ex/badges/quick/FAQ/screenshots/vid; Cordova build.
- API: PHPDoc/JSDoc/Swagger; hook/ex/params/codes/auth; Cordova plugins.

Guidelines:
- Templates: MD notes/end/nonce/mig/before/after/vars; Cordova ex.
- Auto: phpDoc/JSDoc/Docusaurus; CI build/deploy/search/link.
- Val: Vale/Alex; links/spell/gram/read.
- A11y: Head/alt/screen/table/contrast.

GUIDELINE: Interactive API. Swagger UI/embed/auto/try/schema; Cordova sim.

VS Code: MD Preview; Husky CHLOG; PHPDoc snippet; lint/link/toc; All in One preview/toc.

---

### 7. Version Management

RULE: Semantic Versioning

- MAJOR: Breaking API/mySQL/WP/dep/chlog/calver; Cordova major.
- MINOR: Compat adds/React feats/flags/opt-in/note; Cordova minor.
- PATCH: Fixes/sec/hotfix/note/backport; Cordova bugs.

Guidelines:
- Pre: -alpha/beta; staging/test/feedback/pre-chlog; beta Cordova.
- Chlogs: Tags/issues/WP.org/prev diff/size/impact/mobile.
- Tools: Composer/npm/calver; locks/post-audit/compat/diff; Cordova sync.
- Deprec: Notice/guides/warn/remove/mig script; plugins.

VS Code: Version Lens bumps/preview/auto chlog/tag. Merge bump. Monthly/policy.

RULE: RC Testing. Pre-final tags; private WP.org/beta/auto RC; deprec 2wks; Cordova stores.

GUIDELINE: Dep Metrics. Log usage/warns/mig/deadlines/email; Cordova track.

---

### 8. Code/Process Docs

RULE: Inline/External Docs

Public funcs/classes/APIs/logic; why/ex/configs/WP roles/err paths/perf; Cordova points.

Guidelines:
- Inline: PHPDoc params/ret/thrown/ex/@see; @wp-hook/@ver/@access/@deprec/@cordova-hook.
- Arch: /docs/adrs/ dec/ADR temp/status/review; mobile ADRs.
- Process: MD wf/onboard/diags/flow/check; Cordova build/deploy.
- Diags: Mermaid/PlantUML; flows/PNG/alt/int; device.
- Review: PR up/check/CI test/link/cov/doc PR.

VS Code: Auto JSDoc; Thunder/hook prev/gen/sync. PR temp. Sprint rev.

GUIDELINE: User Guides. /docs/user/; FAQ/vid/check/release/loc; mobile install.

RULE: Glossary. WP 'nonce'/'transient'; /docs/glossary; links/annual/Codex; Cordova 'webview'.

---

## Security and Privacy Rules

### 9. Sensitive Data

RULE: No Secrets Commit

- Store: WP opts/env/enc/salt/rotate/audit; Cordova secure plugins.
- Temp: .env.ex/var/list/phold/comment/ex val.
- No: .env/keys; .gitig/pre/CI scan/git clean; Cordova signing.

VS Code: dotenv; Git ig view/alert/Guardian/push. Weekly/pol/rot.

RULE: Encryption. WP salts/AES-256 opts/config key/demand/dec/audit; Cordova enc storage.

GUIDELINE: Access Log. Reads/writes sens; IP anon/90d/admin query/anom alert; device ID.

---

### 10. Input Validation

RULE: Val/Sanitize Inputs

- Payloads/uploads/q; size/type/len/val/format; Cordova files.
- Sanit text/JS; DOMPurify/HTML strip/decode.
- DB param/bind/esc/quote/log.

VS Code: JSON schema prev/hints/VS schema/type. Input test/rev/lib.

GUIDELINE: Schema Libs. Yup/Joi rec/val/errs/evo test/layers; Cordova sensor schemas.

RULE: Rate Limit. Token/trans/admin exempt/thresh/log; Cordova per-device.

---

### 11. Error Handling

RULE: Robust Mgmt

Sec/maint/UX. 100% branch cov. Taxonomy doc.

- No Exp: No user trace/debug; log WP_Error/prod supp/dev mask; Cordova crash rep.
- Log: JSON ts/code/level/meta; cron alert/rotate/search/agg; Cordova console.
- User Msg: Desc/act/plain; notices/dismiss/link/loc; mobile alerts.
- Fallback: Cache/alt/queue/trans fail/offline/grace/retry; Cordova off.
- Auto Rep: Test fail GH issue sum/steps/logs/tags/screenshots/vid/label/triage; Cordova err plugins.

VS Code: bps; output filter/export/search. Weekly/play/post-mort.

RULE: Error Classify. Custom val/net/auth; extend WP_Error; codes/msg map/freq; mobile.

GUIDELINE: Degrade. Early detect/cache fallback/partial notify/retry queue/metrics; Cordova pause.

---

## Performance and Optimization

### 12. Resource Management

RULE: Monitor Res

- Release handles/conn/close/GC/unset/alloc; Cordova mon.
- Cache/pool/obj/Memc/Redis/inval.
- Mem/CPU/net/q times/flame/profile/alert; mobile battery.

VS Code: Proc Exp; tasks profile/load/thresh/chart. Monthly/budget/opt; Cordova prof.

GUIDELINE: Pool Heavy. DB conn/React reuse <=10/health/evict; Cordova plugin pool.

RULE: mySQL Opt. EXPLAIN/no */limit100/batch/comp index/vacuum wkly; mobile payloads.

---

### 13. Fair Use/Rate Limiting

RULE: Respect Svcs

- Limits/WP cron/IP hash/token; Cordova device ID.
- Retries backoff/3/jitter/circuit/status.
- Quota doc/user adjust/report.

VS Code: REST sim/mock/throttle/delay. Limit test/pol/quota dash.

GUIDELINE: Circuit Ext. Open fail/half timeout/rate thresh/log/state; Cordova net events.

RULE: Cache/Invalid. WP obj/tag/stale-reval; React cache/purge; Cordova local.

---

## Version Control and Collaboration

### 14. Commit Standards

RULE: Conventional Commits

type(scope): desc
Types: feat/fix/docs/style/ref/test/chore; scope wp-admin/db/react/cordova; body/footer/breaking!.

VS Code: GitLens temp/val/hook/amend/co-auth. Rev/train/commitlint.

GUIDELINE: Metadata. Perf notes/bench/affected/co-auth/val; Cordova notes.

RULE: Pre-Merge Squash. Logical/comb conv pres/squ temp/hist/no over.

---

### 15. Errors as Issues

RULE: Track Errors/Bugs

GH log vis. Test first. Issue/err. Wkly triage/stale close.

Guidelines:
- Title: Sum ex AJAX500; WP/PHP/React ver/env; Cordova plat.
- Desc: Context/steps/exp/act/screenshots/logs/mySQL/env/repro/code; device logs.
- Labels: bug/sec/perf/docs/wp-compat/area-php/react/cordova/sev/mod.
- Pri: crit/high/med/low/auto impact.
- Assign: Dev/team/area/auto/notify/rot.
- Link: Comm/PR/disc/Trac/rel/dup/block.
- Life: Create/triage/assign/res/close/wkly/crit/ver/reopen pol.

ENFORCE: All doc/no priv/close ref/temp/req/bot val/auto dup.

VS Code: GH ext create/man/inline/bulk/search. Temp load/bot/label.

RULE: Bot Triage. Probot label/keyword sev/mod assign/sim dup/Slack crit.

GUIDELINE: RCA. 5 Whys/fishbone/prev/retro/rate; mobile RCAs.

---

### 16. Branch Management

RULE: Structured Branching

- main: Prod/tags/sign/protect push/status.
- develop: Int/wkly merge/hot/nightly/test.
- feature/*: New/develop/ticket/WIP/post del.
- hotfix/*: Urgent/main/backport/test/short.
- release/*: Prep/final/cherry/RC/freeze; Cordova RC.

VS Code: SC switch/Graph viz/colors/conflict/create. Del merged/pol/prune.

GUIDELINE: Rebase Hist. Feature pre-PR/local conf/no shared force/doc/train -i.

RULE: Lifespan Limit. 7d feat/auto-close PR/notify/archive/exc rev.

---

### 17. Pre-commit

RULE: Auto Checks

- Lint PHPCS/ESLint/sniff/fix/format/style.
- Type PHPStan lv5/TS strict/no err/warn.
- Tests pass/min cov/unit pre/smoke/int light; Cordova smoke.
- Sec comp/npm audit/secrets/vuln fix; Cordova audit.

VS Code: Husky/lint-staged; Ctrl+Shift+P Task/status/pass/fix/silent/hook/log.

RULE: Dep Diff. Breaking scan/major block/mig notes/post test; Cordova diffs.

GUIDELINE: Early Static. Psalm types/Sonar smells/high fail/fix/trend.

---

### 18. Commit/Merge to main

RULE: Commit/Merge Notes

Follow mergeGH.md. Notes req. Train/post-hook.

Guidelines:
- Commit: SC stage/conv detail/rat/files/test/no emoji/slug/sign/lint amend.
- Merge: PR feat/main/notes scope/cov/imp/1+ rev/assign/auto/thread/conf res.
- Format: Sum/Changes/Test/Rat/Next/risks/rel/dep/story.
- ENFORCE: No direct/approv/Actions/check/ref/rebase opt/squ def/msg/timeout; Cordova pass.

VS Code: GitLens hist/blame/GH PR/merge/inline/rev/thread/emoji/sug/check/metrics/peer.

GUIDELINE: Merge Queues. GH queue/CI/fail rollback/delay/size<=5.

RULE: Post-Merge Verif. Smoke/prod metrics/anom revert/doc/alert; Cordova deploy.

---

## Development Workflow

### 19. Feature Process

WF: Git Flow WP. 1w max. Retro/sprint.

1. Branch dev: feature/<id>-desc/pull/sub/stash/local/init.
2. Impl/tests: TDD/98% cov/WP mock/comm/lint/daily build; Cordova tests.
3. Docs: Inline/README/API/gen/link/pot/trans/test; Cordova guides.
4. Checks: Lint/test/build WP-CLI/cov/WP ver/theme/sec; Cordova compat.
5. PR: Check/tests/docs/screenshots/issue/WIP/auto label/size/lint.
6. Rev: 1+ app/feedback/ping/re-rev/unit/conf-free.
7. Merge dev: Squ/conf/CI notify/chlog/dev tag/back.
8. Staging: CI deploy/flows/multi/load/user/smoke; Cordova mobile.
9. Main/prod: App/tag/notify/WP.org/notes/ann/mon; app store.

Guidelines:
- Roles: Dev impl/rev/QA WP/rot/mentor/pair/QA gate; mobile QA.
- Tools: GH Proj/Slack/temp/bot/int/time.
- Esc: Issues/stand/daily/block/matrix/root.
- Metrics: <3d cycle/vel/burn/lead/defect; mobile adopt.

VS Code: Term Ctrl+` WP-CLI/Git kb/full task/PR temp/wf ext/auto/doc/board; Cordova CLI.

GUIDELINE: Pair Sessions. Complex/wkly rot/dec/record/PR pair; mobile.

RULE: Feat Flags. WP opts/user/role toggle/A/B/audit/post remove; Cordova support.

---

### 20. Code Review

RULE: Mandatory Merges

Std/know/issue check. <24h const. 2 rev complex. Train/metrics.

Check:
- Corr: Logic/edges WP/manual/console/input/repro; Cordova device.
- Sec: OWASP/nonce/static/secrets/role/auth; plugin.
- Cov: 98%/qual/mock/edges/mut/int; mobile.
- Docs: Comp/ex/trans/links/guide; Cordova.
- Cons: Layers/naming/WP/std/perf/bundle/i18n/cross.

Guidelines:
- Temp: PR sec/req/check/auto/vid demo; mobile demo.
- Prac: Impact/alts/conf app/pos/code/diff/crit block.
- Esc: >48h lead/log/timeout/notify/med.
- Learn: Monthly retro/feedback/anon/tips.

VS Code: Live Share anno/GH comm/app/thread/emoji/sug/check/metrics/peer.

RULE: LGTM. 2 sec/1 docs/mod auto/bal/48h timeout.

GUIDELINE: Feedback Temp. Sandwich/spec ex/alts/impact/pos/skills; mobile.

---

## Environment and Dependencies

### 21. Environment Config

RULE: Isolated Envs

- Dev/test/prod WP configs/Docker compose/vol/net; Cordova emus.
- Local over no commit/per .env/ig/temp copy/val.
- Schema/JSON lint/config test/diff/backup.

VS Code: Multi-root/.vscode sett over/env task/reload/ws. Secure/doc/rot; Cordova ws.

GUIDELINE: IaC. Docker all/ver/change test/auto prov/scripts; Cordova prov.

RULE: Team Sync. Shared repo/clone pull/diff val/change notify/rev; Cordova setup.

---

### 22. Dependency Management

RULE: Secure Prac

- Wkly comp/npm audit/vuln alert/patch/rep; Cordova audit.
- Pin locks/no ^~/exact semver/range test/tree.
- Trusted/WP.org/check/sig/audit; Cordova official.
- Auto up/change PR/post test/rollback/freeze crit.

VS Code: npm Intelli/comp/warn/install audit/graph/tree. Lock commit/pol/Q rev.

RULE: Dep Health. Score sec/maint/pop/low block/month rev/deprec mig; Cordova health.

GUIDELINE: Deprec Auto. Scan/mig tickets/alt test/phase/paths; Cordova alts.

---

## Monitoring and Maintenance

### 23. Health Monitoring

RULE: Auto Mon

- REST health /wp-json/health/JSON met/status/ping.
- DB/API/cron/uptime/resp/err/sess; Cordova app.
- Q/render/err rate/sess; mobile met.
- RT alert/email/Slack/thresh/esc.

VS Code: Endpoint task/mock/resp/curl. Daily/dash/alert tune.

GUIDELINE: APM. New Relic PHP/Sentry err/Grafana/trace corr/99.9 SLO; Cordova crashlytics.

RULE: UX Met. Loads/err rates/sess/A/B/drop; native vs web.

---

### 24. Logging

RULE: Structured Log

- Levels err/warn/info/debug WP/custom/filter/context/rotate.
- Corr ID reqs/AJAX/trace/span; Cordova sess.
- Sec nonce fail/login/IP/audit.
- Prod JSON/rotate/backup/search/export.

VS Code: Log View parse/filter/keyword/CSV/tail. Wkly/ret/audit.

RULE: ELK. ES ship/Kibana viz/corr q/30d rot; mobile agg.

GUIDELINE: Sens Anon. PII mask/token/GDPR/audit/export; device anon.

---

## Deployment and Production

### 25. Readiness

RULE: Pass Check

- BVT/smoke/ver/dep/build ver; Cordova builds.
- Tests/E2E pass/parallel/WP matrix/cov; mobile matrix.
- Scans zero vuln/static/smell/dep.
- Bench/load thresh/perf budget/scale.
- Docs/WP.org zip/chlog/lic; app assets.
- Mon op/alert test/prod config/env/rollback.

VS Code: tasks Build/check/report/gate. Sign/deploy doc/read gate.

GUIDELINE: Auto Gates. GH Actions ver/fail block/rep/sign/pass track; Cordova gates.

RULE: Dry Deploys. Sim no change/config val/hook/log/pre fix; dry Cordova.

---

### 26. Rollback

RULE: Safe Deploy/Rollback

- Mech: CLI deact/DB revert/file snap; Cordova ver.
- Staged/blue-green/ver/canary/percent/mon.
- Post met/baseline/anom detect/trig/log.
- Runbooks scen/cont/test/Q drill; mobile.

VS Code: MD outline/search/PDF/link. Drill/inc plan/post-rev.

RULE: Staging Rollback. Wkly prac/<5min/data int/doc/mech imp; Cordova.

GUIDELINE: Flags Rollback. Toggle off/full/grad/impact/DB hist; Cordova support.

---

## VS Code Guidelines

RULE: Optimize Env

- Exts: ESLint/Prettier/GitLens/Thunder/Jest/Error Lens/Version Lens/GH PRs/MD/Intelephense/WP-CLI/WP Snip/Docker/Remote SSH/Bracket; Cordova Tools builds/emus.
- Sett: .vscode formatOnSave true/single/auto delay/fixAll; WP debug; indent2/bracket color.
- Tasks: test/npm/wp-lint/shell/WP act/build zip/deploy/cache; cordova build/emulate.
- Launch: Node React/Xdebug PHP/mySQL bp/WP port/React hot/multi; Cordova device.
- Kb: Ctrl+Shift+T test/G Git/Alt+W WP; save/format/toggle/symbol; cordova.
- Ws: ext rec/Remote WP/Docker/port/vol/devcont; Cordova ws.
- ENFORCE: Shared .vscode no sec/open/sync/backup/mig val.

Q update/team share/pol.

GUIDELINE: WP Themes. Color/sch/comment hook/PHP JS icons/sync; Cordova icons.

RULE: Deep WP-CLI. Tasks wp act/db exp/imp/theme; F5/panel; cordova plat add/test.

---

## AI Task Completion

RULE: Tasklist Reinforce

Adhere/comp deliv. Check/per-resp. Wkly audit/share.

Princ:
- Holistic: Pre-final ver/MD notes/done/share/check/archive.
- No Skip: Mand/delay issue/pri/due/esc/notify.
- Iter: Post-step audit/query/log/metric/improve/adjust.
- Prompts: "Rev: Arch comp/Test 98% etc." Comm ts/sign/peer/user conf.

Check:
- [ ] Arch: Layers/hooks/no q/SOLID/ns/map; Cordova.
- [ ] Naming: WP/PHP/React pat/slug/case/no abbr/lint.
- [ ] Lang: Strict/type/style/async nonce/esc/cap/val lib; Cordova types.
- [ ] Testing: 98%/18/cache/flaky/device/trend; mobile cov.
- [ ] Docs: VER/CHLOG/README/inline/ex/link/trans/90; Cordova guides.
- [ ] Vers: Sem WP/dep notice/lock/audit/pol; plat sync.
- [ ] Sec: Secrets/val/err/OWASP/scan/role/rot; plugin.
- [ ] Perf: Res/lim/trans/index/bundle/budget; mobile opt.
- [ ] VC: Comm/issues/br/temp/sign/prot main/prune.
- [ ] Pre-comm: Lint/type/test/scan/cov gate/audit zero/no sec/hook.
- [ ] WF: Branch/PR/rev/merge/staging/prod/tag/retro; mobile deploy.
- [ ] Rev: Checks/feedback/1+ app/change test/metrics.
- [ ] Env: Iso val/Docker/config diff/backup/rot; emus.
- [ ] Deps: Audit/pin/wkly/up PR/compat/src; plugins.
- [ ] Mon: End/log/alert/sample/dash/tune; mobile met.
- [ ] Deploy: Check/rollback/blue/canary/post/inc; stores.
- [ ] VS: Exts/sett/task/kb/sync/theme/pol; Cordova ext.
- [ ] Overall: Enh cov/err fall/multi/user notice/perf base/user test/cross.

ENFORCE: Resp int/user guide/100% loop/flag/progress/auto/audit share.

GUIDELINE: Task Met. Time log/eff/bot ID/Q adjust/team dash.

RULE: Check Val Auto. Scripts/PR int/flag/rep/100% pre-merge.

---

## Necessity of Tasks

RULE: Exhaustive Comp

Req qual/reli/prof. Partial under cons/maint/sec/fail/debt/loss. 100%/PR. Annual audit/train.

Why Ess:
- Risk: Skip vuln/reg/bot; breach/down/loss. Comp pre ID/res; inj/bypass/XSS/audit/mobile exp.
- Scal: Frag compl enh. Full mod/doc/onboard/WP up/theme/multi/API/device.
- Comp: Viol gates/rev/aud. Full acc/trace/ver/prod/WP.org/GPL/legal/stores.
- User: Seam/sec/perf exp. Neg frust/gaps. 18 cats vet/robust/admin/mobile/i18n/feedback.
- Eff: Upfront min rework/debug/inc. Tools/habit/CI/bug down/vel/ROI.

ENFORCE: Contr/dev issue/plan/Q met/succ/100%/medioc/adh track/full rew/partial pen/mil celeb.

GUIDELINE: Monthly Audits. Adh rev/inter/feedback/adjust/doc/95 aim.

RULE: Tasks Biz Val. ROI/pri/val track/Q rep/user align.

---

## Usage Guidelines

### Changes:
- Lint/test/type/WP-CLI/cov/secret/build; Cordova ver.
- Docs imm/inline/pot/commit/ex.
- Naming/struct/prefix/case/len/folder.
- Tests cov/WP mock/local/edges/mut; mobile.
- Comm conv/WP scope/body/footer/sign.

GUIDELINE: Batch Eff. Rel group/batch test/unit rev/PR red/context.

### Submitting:
- Tests pass/98/no flaky/all/parallel; Cordova pass.
- No sens/scan/secret/diff/hist clean.
- Sem ver/header/lock/chlogs/compat; plat notes.
- Docs comp/link/build/FAQ/vid; mobile guide.
- Staging val/multi/theme/user/load/device.

RULE: E2E Val. Full/screenshot/no reg/fail block/retry; Cordova.

### Projects:
- Domain/WP spec/post/block/short.
- Sec/test strict/nonce/san/rate/audit; mobile.
- Docs cons/PHPDoc/JSDoc/ADR/gloss.
- Err/log struct/WP_Error/JSON/alert/ret pol.

GUIDELINE: New Scaffold. Yeo WP/all folders/VS pre/val/domain; Cordova scaffold.

### Times:
- Rules/sec check/daily read/quiz/update.
- Plan acts/step/timeline/risk/mil.
- Log proc/layer seq/dep/parallel/rev.
- Inst cov/multi/edge role/scale sim/devices.
- Err/fallback/notice/retry btn/act log/trace.
- Ref concl/check mark/sign/archive/learn share.

RULE: Time-Box Tasks. 2h unit/over esc/sink track/proc opt/speed qual bal.

### Debugging/Script

RULE: Debug/Resolve Scripts

Gen WP debug. Analyze bug/common/code-comp solve. No manual. Inc WP-CLI/DB q/hook/log anal; Cordova bridges.

Force Scripts Prevent ?. Assign

TASK: Fix React ?. assigns.

REQ:
1. Search JS/TS /assets/js/grep pat/file/line hl.
2. ?. pat/regex/line/context.
3. Read/write non-dist/AST/node/type.
4. Exclude assign/node check/comm/branch cov.
5. Val no ?. LHS =/+=/AST/unit fix/lint/build; Cordova compat.

PAT:
- obj.prop=val to obj?.prop=val (INC)
- obj.prop to obj?.prop read (COR)

DELIV:
1. Prevent/ESLint rule/doc/test/mig old.

Focus: Future/CI lint/PR scan.

GUIDELINE: Debug Lib. /scripts/debug/q anal/hook trace/perf/usage/vers; Cordova tracers.

RULE: Auto Bug Repro. Min scripts/WP-CLI/vid/repo/fix ver; mobile repros.

---

# Method for Augment AI VS Code WP

Rules VS Code WP repo int. Thought solve/clar assum/user collab/best. Adh: qual/und > speed.

## Principles
- Clarity: Context pre und. State/ver assum/num/WP spec/file cite/new update.
- Q: Target uncert/no guess/max1-3/follow/term/pri.
- Inc: Small/test/code move/ref/1 file/comm/revert/test.
- Collab: Partner/pause/no brute/plan conf/alt prop/user lead/co-dec.
- Refl: Block ID/Qs/alt opt/pri/time box/lesson doc.

GUIDELINE: Assumps Doc. assum.md ded/per int up/pre-code rev/user val share.

## Rules
1. Analysis:
   - Pre-sug scan struct/dep/mod/pat/WP dir/dep tree/size/compat; Cordova configs.
   - Unclear? Ask: "React/PHP WP files/fw clar?" Key list/purp Q/goal/constr.

2. Move/Ref:
   - Impact: "Hook move 3 aff ver?" List/risk/cost ben/test; mobile impact.
   - No large sans app. Plan: "Goals align?" Steps/time/fall/mil.

3. Assum:
   - Resp list: "Ass: (1)/v1/users (2)errHand." Bold/cite/conf/wrong imp.
   - Q each: "(1) acc config?" Ver sug/no def/list up/all conf.

4. Questioning:
   - Amb: Pause1-3 Q/WP spec/close open/why/how/user imp.
   - Ex: "Perf goal?" "5 files compat?" "Type hook?" mySQL edge/role/scale/alt cost/Cordova compat.
   - Post sug/no code til clear/ans conf/sum/next chain.

5. WF:
   - Assess: Conf desc "[task] ask corr?" WP rest/goal/scope/succ meas.
   - Plan: High risk "Deps mit?" Bullet/tools/mil/res.
   - Exec: 1 change/term test/feedback "Diff npm test?" Cmd/exp/diff/user ver.
   - Iter: Fail expl pivot "Fail [ ] exp [ ]?" Why/lesson/retry/plan adj.

6. Pit:
   - Conc thor/<200w/bullet/bold/user.
   - Ask>assum/1+ Q un clear/log cnt/follow.
   - Speed over: Qual>Vel/log/time/user/eff est.

RULE: Collab Live Share. VS Live pair/term/co-edit/opt rec/shared rev; remote device.

## Enforcement/Log
- Log Qs/ass "Aug AI Log" chan/ts/export/filter/date/user.
- Viol flag "Pro no full rev"; fix/self corr/learn/inc rep.

Rules human WP dev VS Code; meth iter/feedback/cont imp.

GUIDELINE: Meth Eff Q Rev. User surv clar/speed/rules adj/change doc/met track/wins celeb.
