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

RULE: Decoupled Server-Client Model with WordPress Plugin Server and PWA Client

The application server operates as a WordPress plugin installed on a standard WordPress host, handling backend logic, mySQL interactions, and exposing APIs via WP REST API extensions. The client is a Progressive Web App (PWA) built with React, which connects to the WordPress host over HTTPS for data fetching, updates, and authentication. This architecture promotes separation of concerns, enables offline functionality, and supports seamless installation on user devices. Specific: Ensure plugin exposes all necessary endpoints under /wp-json/augment/v1/; PWA must register service worker on load. General: Audit API compatibility quarterly; document connection flows in README; test PWA install prompts across browsers.

#### Key Principles
- Server as Plugin: Leverage WP core for authentication (JWT or WP sessions), scheduling (WP cron), and security (nonces). Prefix all custom tables, hooks, and endpoints with 'augment_'; migrate schemas on plugin activation; handle WP updates gracefully with version checks.
- Client as PWA: Use React for UI, Workbox for service workers, and IndexedDB for local storage. Manifest.json defines app metadata; support add-to-homescreen; offline-first design with sync queues. Connect via fetch to WP REST; use WP API client libraries like @wordpress/api-fetch.
- Connection Mechanism: Secure HTTPS only; OAuth2 or WP application passwords for auth; retry logic on network failures; heartbeat pings for session validity; compress payloads with GZIP.
- Avoid: Tight coupling like embedding WP scripts in PWA; direct mySQL access from client; unversioned APIs; ignoring PWA lighthouse scores.
- Benefits: Offline access reduces latency; PWA install boosts engagement; plugin modularity eases WP theme/plugin conflicts; scalable to multiple WP hosts.
- Add: Implement webhook endpoints in plugin for push updates to PWA; use Push API for notifications; cache API responses in service worker; sync data on reconnect with conflict resolution.

GUIDELINE: Offline-First Data Synchronization. Queue mutations in IndexedDB during offline; sync on reconnect via WP REST POST/PUT; resolve conflicts with last-write-wins or server-authoritative; log sync states; test with Chrome DevTools network throttling.

- Plugin Server Layer: Extend WP REST controllers for custom routes; validate requests with WP capabilities; sanitize inputs server-side; log API calls with user context; rate-limit endpoints per IP/user.
- PWA Client Layer: Register service worker for caching strategies (stale-while-revalidate for reads, network-first for writes); handle install/activate events; prompt for permissions (notifications, storage); lazy-load components for initial load speed.
- Integration Layer: Use WP nonces in API calls from PWA; refresh tokens automatically; fallback to cached data on 5xx errors; monitor connection with navigator.onLine and beacon API for unsent analytics.
- Security Layer: Enforce CORS headers in plugin (allow PWA origin only); validate origins in AJAX; encrypt sensitive local data; audit PWA for XSS via DOMPurify.
- Performance Layer: Pre-cache critical assets in service worker; optimize WP API responses with fields param; use React Query for client-side caching; aim for PWA score >90 on Lighthouse.

ENFORCE: Validate architecture in CI: Run WP-CLI to test plugin endpoints; simulate PWA offline with Puppeteer; fail build if sync fails >5%; document API schema in OpenAPI. Specific: PRs must include PWA connection tests. General: Train on PWA debugging; architecture diagram in repo with draw.io.

VS Code: Use Live Server extension for PWA preview; Lighthouse panel for audits; WP-CLI tasks for server sim; Network tab for connection traces; service worker debugger.

RULE: Progressive Enhancement for PWA Features. Ensure core functionality works without PWA traits (e.g., as SPA); add offline/install progressively; test graceful degradation; support legacy browsers via polyfills; measure adoption metrics.

---

## Core Development Rules

### 1. Architecture and Code Organization

RULE: Follow Layered Architecture with Separation of Concerns

Structure plugins modularly for reusability. Layers have defined roles. Apply SOLID to WP hooks, actions, filters, React components. Limit: 3-5 layers/plugin. Review layers quarterly; doc layer map.

#### Key Principles
- Modularity: Independent modules. Separate PHP services, React components, mySQL; prefix with plugin name; <=500 lines/module; subfolders for large.
- Dependency Management: WP patterns/Composer for PHP. Enqueue React via wp_enqueue_script; declare deps; version scripts; conditional load by role.
- Avoid: WP global coupling sans abstraction, god classes, circular deps; no direct $post; no hardcoded paths; no cross-layer statics.
- Benefits: Boosts readability, debugging, multisite; theme compat; updates; load time.
- Add: Namespace PHP classes; admin-only React scripts; conditional enqueue; dequeue on uninstall; lazy-load services.

GUIDELINE: Event-Driven for Loose Coupling. WP actions/filters as events; React custom events via EventEmitter; subscribe in control layer; avoid direct calls; log flows.

- Data/Logic Layer: Business logic, validation, mySQL via $wpdb. DDD entities/repos; index tables; prefixed custom tables; backup on activate; migrate on update.
- Presentation Layer: React admin, PHP public templates. Hooks/Redux state; separate style enqueues; React CSS modules; minify CSS; optimize images.
- Control Layer: WP hooks, AJAX. Nonces, validate reqs, log; add_action entry, do_action exit; hook priorities; remove on deactivate; cron if needed.
- Services: Stateless PHP for externals. Transients, WP-versioned; cache API in options; timeouts; retries; validate resp.
- Utilities: Pure funcs for sanitization etc.; test sans WP; export/reuse; inline small; memoize pure.
- Types/Interfaces: PHP interfaces for hooks, TS for React. Contracts, generics, runtime val; type-hint PHP params; React PropTypes; defaults; validate.

ENFORCE: Maintain boundaries; delegate queries to services. PHPCS checks, custom layer sniffs; build fail on violation. Audit/PR; log violations. Train SOLID; repo layer diagram.

VS Code: Outline (Ctrl+Shift+O) nav. Error Lens warnings. Dep Cruiser rules; WP hook snippets; dep graph; hover docs; layer colors. Madge for circ deps; tasks.json scans.

RULE: Micro-Frontends for React Scalability. Break admin into feature React apps; dynamic imports; share core utils only; version each; isolate tests.

---

### 2. File Naming and Structure

RULE: Consistent Naming Patterns

Intent-showing names. Review in CR. Doc in README; lint names; yearly update.

- Services: camelCaseService.php /includes/services/; slug prefix; abstract suffix if; singleton global; doc purpose.
- Components/UI: PascalCase.jsx /admin/js/components/; snake_case.php /admin/partials/; feature folders; index exports; stories; adjacent tests.
- Utilities: camelCase.php/js /includes/utils/; 1 func/file complex; JS barrel exports; types alongside; version public.
- Models/Types: PascalCase.php/ts /includes/models/; sep interfaces/classes; DTOs; validate; schema file.
- Tests: .test.php/jsx mirror src /tests/; per-file cov; shared setup.

GUIDELINE: Standardize Config/Log Files. plugin-slug-config.php /includes/config/; plugin-slug-logs.sql /logs/; timestamps in logs; .json API configs.

ENFORCE: No generics. WP struct: /includes/, /admin/, /public/, /assets/, /languages/; /templates/ views; /build/ minified; /migrations/ DB. CI struct check. Folder perms; archive old.

VS Code: Compact Folders. Ctrl+P search. Prettier/ESLint/PHPCS save; hook search; exclude node_modules; globs; icons. File Nesting for grouping.

RULE: Assets by Type/Version. /assets/ sub: /css/, /js/, /images/; version hashes in names; /src/ unminified.

---

### 3. Language and Framework Standards

RULE: Strict Configurations

Strict typing/validation. CI enforce. Monthly audit; standards train.

- Type-check: PHP declare(strict_types=1), strict TS, prepared $wpdb; WP cap checks; ? nullable; enums statuses; JS strict.
- Declare returns: PHP/TS; WP error unions; void no-ret; mixed last; annotate all.
- No untyped: Avoid mixed/raw queries; escape outputs; no var_dump/die/eval.
- Error handling: Try-catch WP_Error; error_log; custom exc; throw early; rethrow if.
- Validate: WP sanitize, Zod props; AJAX nonces; rate-limit inputs; CSRF; schema val.

GUIDELINE: Functional Paradigms. Pure utils funcs; array map/filter/reduce; no side-effects React hooks; compose complex; test composable.

#### PHP/WP Style Rules

Variables: camelCase, const pref, no globals but WP; local prefix if; $this_ class; _ private; ctor init.

Functions: Hook closures, destructure params, 20-30 lines, private props, pure, immutable args; early returns fails; SRP; PHP8 named args; doc params.

Strings: Double interp, single literals, esc_html out; __() trans; plural gettext; sprintf; . concat.

Conditionals: ===, early ret, cap checks; WP_DEBUG checks; role switch; simple ternary; PHP8 match.

Async: WP AJAX hooks, try-catch, wp_send_json_error, JS AbortController; limit concurrent; poll long; web workers heavy; promise chains.

Modules: Composer autoload, grouped imports, .apply spread; require_once; conditional/lazy load; rare dynamic.

Safety: wp_verify_nonce, esc_attr, ?? defaults; apply_filters inputs; email/URL/IP val.

Arrays: map/filter/reduce, foreach, array_pad; array_key_exists checks; consistent sort; unique keys; diff/merge.

PHP: Hook interfaces, annotate params/returns; traits shared; abstract classes; final methods; namespace use.

Debugging: JSON error_log, no echo, WP_DEBUG; !WP_DEBUG conditional; levels; stack; slow profiler.

Data: readonly props, WP_Query > arrays; get_transient cache; safe serialize/unserialize; val unserialize.

Input: sanitize_*, type guards; upload size/type/MIME; virus scan; file hash.

Documentation: PHPDoc/JSDoc, wp_date; @since vers; @param types; @return desc; @example.

Frameworks: WP std, React hooks, mySQL index; monthly PHPCS/ESLint update; React memo; SQL explain; table opt.

#### React/mySQL
- React: State hooks, WP REST fetch, sanitize props; useCallback handlers; error boundaries; suspense; context.
- mySQL: $wpdb->prepare, esc_sql; multi-query trans; fail error; last err log; vacuum.

RULE: Optimize PHP OOP. Composition > inheritance; class depth <=3; DI services; val injected; profile inst times.

VS Code: Intelephense, TS ext, Problems, formatOnSave, ESLint/PHPCS; WP doc keybind; WP colors theme; folding; snippets. PHP DocBlockr; Ctrl+Shift+D bind.

GUIDELINE: Promise Async. React API chain promises; PHP8+ async/await WP cron; global rejections; 30s timeouts; exp retry fails.

---

## Testing Requirements

### 4. Test Coverage Standards

RULE: 98% Coverage Across 18 Categories

98% line/branch/path for WP flows, edges, multisite, threats. Branch min 95%. Trend up; monthly report.

Cover 18 categories.

1. Unit Tests
   - Isolate PHP/React/mySQL.
   - Edges/boundaries/precision. Mock $wpdb/WP.
   - Ex: calculateTax mocked options.
   - Include: pure funcs first; parametrize; seed random; assert types.

GUIDELINE: Property-Based Units. Random inputs via Hypothesis PHP; assert invariants; auto rare edges; CI regression.

2. Integration Tests
   - PHP/React/WP REST/mySQL flows.
   - Hooks/DB/third-party.
   - Ex: Payment hook to mySQL.
   - Include: nonce calls; mock third; env setup; data cleanup.

3. E2E Tests
   - Full stack React-PHP-mySQL.
   - Multisite/workflows.
   - Ex: React checkout to email.
   - Include: WP-CLI setup; video; journeys; assert email. Utilize Playwright for headless browser automation to simulate realistic user interactions, navigate WP admin dashboards, interact with React components, and verify end-to-end data flow from PWA client to PHP backend and mySQL storage, ensuring cross-browser consistency in Chrome, Firefox, and Safari.

4. System Tests
   - Plugin WP env, health/resources.
   - PHP-mySQL interplay.
   - Ex: Staging activate.
   - Include: themes; mem limit; conflicts; server load.

5. Regression Tests
   - Post-change re-run.
   - Impact analysis.
   - Ex: Auth re-run post-update.
   - Include: WP ver change; file-selective; baseline diff.

6. Acceptance Tests
   - WP reqs, BDD.
   - Scenarios.
   - Ex: Settings <5s.
   - Include: stories; Gherkin; stakeholder sign; criteria.

7. Performance Tests
   - Benchmark time/throughput.
   - Ex: JMeter AJAX <200ms.
   - Include: mySQL locks; 50 concurrent; flame graph; mem peak.

8. Load/Stress Tests
   - Surges, bottlenecks.
   - Ex: 10x users, leak monitor.
   - Include: cron sim; ramp load; break/recovery.

9. Security Tests
   - Vuln scans, pen, WP compliance.
   - OWASP/nonces.
   - Ex: ZAP SQL/XSS/CSRF.
   - Include: cap checks; fuzz; Burp; auth bypass.

10. Compatibility Tests
    - WP vers/themes/plugins/browsers/devices.
    - Backward/forward.
    - Ex: React WP 6.0+ browsers.
    - Include: Gutenberg; mobile; plugin matrix; theme switch. Leverage Playwright's multi-browser support for automated compatibility testing across desktop and mobile viewports, simulating device-specific interactions and verifying PWA responsiveness without manual intervention.

11. Accessibility Tests
    - WCAG/ADA React/PHP.
    - Scans/audits.
    - Ex: Screen readers forms.
    - Include: focus order; contrast; WAVE; keyboard nav. Integrate Playwright with accessibility plugins like axe-core to perform automated a11y audits during E2E tests, checking for ARIA compliance, keyboard navigation paths, and screen reader compatibility in headless mode.

12. Exploratory Tests
    - Unstructured probes.
    - Heuristics/SBTM.
    - Ex: Unicode search.
    - Include: rapid clicks; edge devices; personas; notes.

13. Smoke Tests
    - Basic ops checks.
    - Post-build.
    - Ex: Activate/load admin.
    - Include: shortcodes; plugin list; menu; console errs.

14. Sanity Tests
    - High-impact post-change.
    - Fixes.
    - Ex: Nonce post-patch.
    - Include: roles; user switch; cache clear; perms.

15. Usability Tests
    - Intuitiveness, SUS.
    - Tasks/errors.
    - Ex: Settings config.
    - Include: task time; heatmap; A/B; survey.

16. L10n/I18n Tests
    - Multi/RTL/formats.
    - Pseudo-loc.
    - Ex: Spanish dates/curr.
    - Include: .po; string len; locale switch; RTL.

17. Recovery Tests
    - Disruptions/recovery.
    - Rollback/chaos.
    - Ex: DB mid-query term.
    - Include: WP cache clear; failover; backup; data loss.

18. Data Integrity Tests
    - Persistence/val.
    - ACID/restores.
    - Ex: Malformed import reject.
    - Include: backup/restore; charset; dup detect; constraints.

RULE: Expand to 20 Cats w/ Visual/Network. Visual reg Percy; net intercept Cypress; UI theme consistency; slow net sim. Incorporate Playwright for visual regression testing by capturing screenshots at key UI states during E2E flows and comparing against baselines to detect unintended changes in React admin interfaces across WP themes.

ENFORCE:
- Merge Gates: No int w/o 98% peer tests. SonarQube/Codecov; block low; approve delta.
- Critical: BVT/E2E/Reg/Sec + Perf/Rec for auth/trans; manual critical; signoff; risk.
- CI/CD: GitHub Actions/WP-CLI auto, parallel, flaky alerts, approvals; 3x retry; artifacts; parallel envs.
- Vigilance: Q audits, TDD/BDD, dashes; gap reports; train; goal 99.
- Add: PHP 8.1+; BrowserStack; device lab; WP nightly. Configure Playwright in CI pipelines to run parallel browser instances for comprehensive E2E and compatibility test coverage, integrating with WP-CLI for automated site provisioning and teardown.

VS Code: Test Explorer (PHPUnit/Jest). tasks.json coverage. Codecov badges; save run; debug; sidebar; inline. Test Runner UI visuals; coverage gutters. Install Playwright VS Code extension for seamless test authoring, debugging, and execution within the editor, enabling trace viewer for failed E2E scenarios and codegen for rapid test script creation.

---

### 5. Test Organization

RULE: Mirror Structure, Readable

Tests like src. AAA: Arrange, Act, Assert. AAA all. Colocate; naming conv.

- Unit: /tests/unit/ mirror (userService.test.php); WP mocks; per-file cov; group asserts; types.
- Integration: /tests/integration/ subsystems (ajax/); shared DB; env vars; DB teardown; cleanup.
- E2E: /tests/e2e/ journeys (admin-flow/); page objs selectors; wait load; screenshot fail; email assert. Employ Playwright for scripting these tests, defining page object models for reusable locators in React components, handling dynamic WP nonces via network interception, and asserting on API responses and UI states in headless or headed modes as needed for different modules like admin dashboards and PWA install flows.
- Utilities: /tests/utils/ mocks/factories; fake gens; seeder; config fakes; shared setup.
- Fixtures: /tests/fixtures/ (sample-users.sql); schema vers; import/clean scripts; data gen.

PATTERN: Per-file tests. Names: shouldReturnProfileValidId; describe/it; data tables; nested desc; skip known; tags.

Practices:
- Naming: <module>.test.<ext>, describe/it; data tables; nested desc; skip known; tags.
- Data: Param, afterEach cleanup; DB reset; teardown hooks; global setup; factory.
- Mocking: External only. afterEach reset (jest.resetAllMocks); Mocha sinon.restore; hook spies; once/partial.
- CI: Parallel, artifacts; flaky labels; HTML report; email fail; global timeout.
- Pitfalls: Over-mock, brittle selectors/timeouts; ignore slow; 5s timeout; mock time; stale.
- Benefits: Fast debug, TDD; CI rate; cov trend; isolation.

GUIDELINE: Test Data Builders Fixtures. Builder classes complex objs; fluent custom; default quick; schema vers.

Cache Clear Process:
Clear Jest cache pre-runs consistency. Clear before suite. Doc process.

1. Location: node_modules/.cache/jest/; config; backup; multi-env.
2. Command: npx jest --clearCache; watch; selective; force.
3. Workflows: pkg "test:clean", tasks.json, CI prepend, Husky; pre-test; log time; hook.
4. Verify: --no-cache; warn log; results diff; full re-run.
5. Edge: Per-pkg, invalidate configs; monorepo; custom trans; clear time log; disk check.

ENFORCE: README doc. Merge gate; pkg script; warn not; lint enforce. Daily clear. Train; monitor size.

VS Code: Test search/symbols. Debugger bps. Testing watch. Clear tasks; keybind run; cov view; config; groups. Watch toggle iterative. Use Playwright Test extension to run and debug E2E tests directly in VS Code, with integrated trace inspection and browser launch for interactive troubleshooting of module-specific failures.

RULE: Contract Testing APIs. Consumer contracts; Pact val; indep WP REST; backward compat; CI auto.

---

## Documentation Standards

### 6. Automatic Updates

RULE: Update Docs w/ Changes

Docs as code: ver/test/review. Auto. Per-commit update. Q audit; 90% cov.

- VERSION: Inc header/notes. semantic-release Composer; "Tested up to"; changelog link; PHP req; min WP.
- CHANGELOG: Files/feats/bugs detail. Format: Added/Changed/etc; WP Trac link; breaking; migration; credits.
- README: Instr/ex/badges; quickstart code; FAQ; screenshots; install vid.
- API: PHPDoc/JSDoc, Swagger REST; hook ex; endpoint params; resp codes; auth.

Guidelines:
- Templates: MD notes/endpoints; nonce ex; migration; before/after; vars.
- Auto: phpDocumentor/JSDoc/Docusaurus; CI build/deploy; search; auto-link.
- Validation: Vale/Alex; links/spell/grammar; readability.
- Accessibility: Headings/alt; plain alt; screen notes; table headers; contrast.

GUIDELINE: Interactive API Docs. Swagger UI REST; admin embed; annot auto-update; try-it; schema val.

VS Code: MD Preview. Husky CHANGELOG; PHPDoc snippet; doc lint; link val; toc gen. MD All in One preview/auto-toc.

---

### 7. Version Management

RULE: Semantic Versioning

- MAJOR: Breaking API/mySQL; WP req update; dep list; major changelog; calver year bump.
- MINOR: Compat adds; React feats; flags; opt-in; compat note.
- PATCH: Fixes; sec patches; hotfix tag; urgent note; stable backport.

Guidelines:
- Pre: -alpha beta; staging test; beta users; feedback; pre-changelog.
- Changelogs: Tags/issues links; WP.org notes; prev diff; size/impact.
- Tools: Composer/npm, opt calver; commit locks; post-audit; compat test; diff.
- Deprecation: Notice/guides; admin/console warn; next major remove; migrate script.

VS Code: Version Lens bumps; changes preview; auto changelog; tag push. Merge bump. Monthly review; policy doc.

RULE: Release Candidates Testing. Pre-final RC tags; private WP.org dist; beta feedback; auto RC builds; deprec 2wks.

GUIDELINE: Dep Metrics Track. Log deprecated usage; admin warns; migrate script; removal deadlines; email notify.

---

### 8. Code/Process Docs

RULE: Inline/External Docs

Doc public funcs/classes/APIs/logic; why decisions; ex configs/workflows; WP roles; err/success paths; perf notes.

Guidelines:
- Inline: PHPDoc/JSDoc params/returns/thrown/ex/@see; @wp-hook filters; @version; @access public; @deprecated.
- Architecture: /docs/adrs/ per decision; ADR template; status col; review.
- Process: MD workflows; onboard; diagrams; flowcharts; checklists.
- Diagrams: MD Mermaid/PlantUML; hook flows; PNG export; alt; interactive.
- Review: PR updates/checklists; CI doc test; link check; cov; doc PR.

VS Code: Auto JSDoc stubs. Thunder API; hook preview; gen all; sync. PR template doc. Sprint reviews.

GUIDELINE: User-Facing Guides. /docs/user/; troubleshooting FAQ; vid tuts; printable checklists; release update; localize.

RULE: Terms Glossary. WP terms 'nonce'/'transient'; /docs/glossary.md update; inline links; annual review; WP Codex contrib.

---

## Security and Privacy Rules

### 9. Sensitive Data

RULE: No Secrets Exposed/Committed

- Storage: WP options/env managers; encrypt if; salt keys; periodic rotate; access audit.
- Templates: .env.example; var list; placeholders; comment use; ex vals.
- No commit: .env/keys; .gitignore; pre-commit/CI scan; git hist clean.

VS Code: dotenv load. Git .gitignore view; secrets alert; GitGuardian ext; push scan. Weekly scan. Policy; rotation plan.

RULE: Encryption Enforce. WP salts encrypt; AES-256 options; WP config key mgmt; demand decrypt; usage audit.

GUIDELINE: Access Logging. Log reads/writes sens data; IP anonymize; 90d retain; admin query; anomalous alerts.

---

### 10. Input Validation

RULE: Validate/Sanitize Inputs

- Payloads/uploads/queries; size/type; max len; min val; format.
- Sanitize text/JS; DOMPurify; HTML strip; entity decode.
- Param DB; bind vars; escape raw; safe quote; input log.

VS Code: JSON schema previews; inline hints; VSCode schema; type val. Input test. Val code review; lib.

GUIDELINE: Schema Libs. Yup/Joi complex; recursive val; detailed errs; schema evo test; all layers enforce.

RULE: Endpoint Rate Limit. Token bucket; transient store; admin IP exempt; config thresh; throttle log.

---

### 11. Error Handling

RULE: Robust Error Management

Sec/maint/UX approach. 100% branch cov. Taxonomy doc.

- No Internal Exposure: No user traces/debug. Internal log; WP_Error; prod suppress; dev/user mode; mask details.
- Structured Logging: JSON timestamp/code/level/meta; WP cron alert; size rotate; searchable; aggregate.
- User Messages: Desc/actions/plain; admin notices; dismiss; support link; localize.
- Fallbacks: Cache/alts/queue; transient fail; offline; grace; retry.
- Auto Reporting: Test fails GitHub issue sum/steps/logs/tags; E2E screenshots/vid; auto label; triage bot.

VS Code: Debugger bps. Output channels; log filter/export/search. Weekly review. Playbook; post-mortem template.

RULE: Error Type Classify. Custom classes val/net/auth; extend WP_Error; codes; user msg map; freq track.

GUIDELINE: Graceful Degradation. Early detect; cached fallback; partial fail notify; retry queue; degrado metrics.

---

## Performance and Optimization

### 12. Resource Management

RULE: Monitor Resources

- Release handles/conns; close cursors; GC hints; unset vars; alloc profile.
- Caching/pooling; obj cache; Memcached/Redis opt; invalidate.
- Mem/CPU/net monitor; query times; flame graph; profile; high alert.

VS Code: Process Explorer. tasks.json profile; load run; thresh alert; chart. Monthly profile. Perf budget; opt guide.

GUIDELINE: Obj Pooling Heavy. Pool DB conns; React comp reuse; size <=10; health monitor; evict idle.

RULE: mySQL Queries Opt. EXPLAIN all; no SELECT *; limit 100; batch inserts; composite indexes; weekly vacuum.

---

### 13. Fair Use/Rate Limiting

RULE: Respect Services

- Config limits; WP cron track; IP/user hash; token bucket.
- Retries backoff; max 3; jitter; circuit break; status track.
- Quota policies; doc usage; user/admin adjust; exceed report.

VS Code: REST Client sims; mock resp; throttle test; delay. Limit test. Policy update; quota dash.

GUIDELINE: Circuit Breakers Externals. Open fails; half-open timeout; fail rate track; config thresh; state log.

RULE: Aggressive Cache/Invalidation. WP obj cache reads; tag invalid; stale-while-reval; React client cache; update purge.

---

## Version Control and Collaboration

### 14. Commit Standards

RULE: Conventional Commits

Format: type(scope): desc
Types: feat/fix/docs/style/refactor/test/chore; scope wp-admin/db/react; body details; footer issues; breaking!.

VS Code: GitLens templates/val; hook enforce; amend invalid; co-author. Msg review. Train conv; commitlint.

GUIDELINE: Metadata Enrich. Perf impact notes; benchmark links; affected tests; co-authored-by; commitlint val.

RULE: Pre-Merge Squash. Logical units combine; conv format preserve; squash template; hist review; no over-squash.

---

### 15. Errors as Issues

RULE: Track Errors/Bugs Issues

GitHub log all visibility. Test fails first. Issue/error. Weekly triage; stale close.

Guidelines:
- Title: Sum ex "AJAX 500 empty"; WP/PHP/React ver; env tag.
- Desc: Context/steps/exp/act/screenshots/logs; mySQL dump; env; repro repo/code.
- Labels: bug/sec/perf/docs; wp-compat; area-php/react; sev; module.
- Priority: crit/high/med/low; auto impact.
- Assignees: Dev/team; area-based; auto/notify/rotate.
- Linkage: Commits/PRs/disc/WP Trac; related/dup/blocks.
- Lifecycle: Create/triage/assign/resolve/close; weekly rev; criteria; verified; reopen pol.

ENFORCE:
- All doc; no private; close ref; issue template; req fields; bot val; auto dup close.

VS Code: GitHub ext create/manage; inline link; bulk edit; search. Template load. Bot; label pol.

RULE: Bot Auto Triage. Probot labeling; keyword sev; module assign; sim dup close; Slack crit notify.

GUIDELINE: Root Cause Analysis. 5 Whys desc; fishbone diags; prev measures; retro rev; RCA rate track.

---

### 16. Branch Management

RULE: Structured Branching

- main: Prod; release tags; sign; protect push/status req.
- develop: Int; weekly merge; hotfix too; nightly; test branch.
- feature/*: New; from develop; ticket name; WIP prefix; post-merge delete.
- hotfix/*: Urgent; to main; develop backport; urgent test; short life.
- release/*: Prep; final tests; cherry fixes; RC tag; code freeze.

VS Code: SC switch. Git Graph viz; branch colors; conflict tool; create cmd. Delete merged. Pol doc; prune old.

GUIDELINE: Rebase Clean Hist. Feature rebase pre-PR; local conflicts; no shared force-push; doc workflow; train rebase -i.

RULE: Branch Lifespan Limit. 7d max features; auto-close stale PRs; owner notify; archive long; exc review.

---

### 17. Pre-commit

RULE: Automated Checks

- Linting PHPCS/ESLint; custom sniffs; auto fix; format; style.
- Type-check; PHPStan lv5; TS strict; no errs; warn only.
- Tests pass; min cov; unit pre; smoke quick; int light.
- Sec scans; composer/npm audit; secrets; vuln fix.

VS Code: Husky/lint-staged. Ctrl+Shift+P Task; status ind; pass/fail icon; quick fix. Silent run. Hook update; log check.

RULE: Dep Diff Checks. Breaking changes scan; major bump block; migration notes; post-update test pre-commit.

GUIDELINE: Early Static Analysis. Psalm PHP types; SonarLint smells; high sev fail; auto-fix; trend report.

---

### 18. Commit/Merge to main

RULE: Commit/Merge w/ Notes

Follow mergeGitHub.md. Notes req. Merge train; post-hook.

Guidelines:
- Commit: SC stage. Conv msgs detail/rationale/files/test, no emojis; slug prefix; sign/verify; lint amend.
- Merge: PR feat to main. Notes scope/details/cov/impacts. 1+ rev; reviewer assign; auto req; thread comm; conflict res.
- Format: Sum/Changes/Test/Rationale/Next; risks; related PRs; dep PR; story.
- ENFORCE: No direct push. Approvals. Actions checks. Checklists ref; rebase opt; squash def; msg; PR timeout.

VS Code: GitLens hist/blame. GitHub PR/merge; inline rev; thread res; emoji; suggest. Checklist. Metrics; peer swap.

GUIDELINE: Merge Queues Safety. GitHub queue PRs; queue CI; fail rollback; delay notify; size <=5.

RULE: Post-Merge Verif. Smoke post-merge; prod metrics mon; anomaly revert; doc steps; alert auto.

---

## Development Workflow

### 19. Feature Process

WORKFLOW: Git Flow WP. 1w max feat. Retro end; sprint plan.

1. Branch develop: feature/<id>-desc. Latest pull; submod update; stash; local; git flow init.
2. Implement/tests: TDD, 98% cov. WP mock; often commit; branch tests; per-commit lint; daily build.
3. Docs: Inline/README/API. Gen; link check; pot update; sample trans; doc test.
4. Checks: Lint/test/build WP-CLI; cov report; WP ver/theme compat; sec scan.
5. PR: Checklist/tests/docs/screenshots. Issue link; WIP draft; auto labels; size est; auto lint.
6. Review: 1+ approval. Feedback address; stall ping; re-rev changes; add unit; conflict-free.
7. Merge develop: Squash/conflicts. CI int; channel notify; changelog update; dev tag; back merge.
8. Staging: CI/CD deploy. Flows verify; multisite/load/user accept; smoke prod.
9. Main/prod: Approval. Tag/notify; WP.org sub; notes; announce; day1 mon.

Guidelines:
- Roles: Dev impl/rev std/QA WP; rotate rev; mentor; pair; QA gate.
- Tools: GitHub Proj/Slack; templates; bot reminders; int bot; time track.
- Escalation: Issues/standups; daily sync; blockers; matrix; root cause.
- Metrics: <3d cycle; velocity; burn/lead time; defect rate.

VS Code: Terminal Ctrl+` WP-CLI. Git keybinds; full flow task; PR template; workflow ext. Auto task. Doc; sprint board.

GUIDELINE: Pair Prog Sessions. Complex feats pair; weekly rotate; dec doc; opt record; PR pair rev.

RULE: Feat Flags Safe Rollouts. WP options flags; user/role toggle; A/B int; usage audit; post-val remove.

---

### 20. Code Review

RULE: Mandatory Merges

Standards/know/issues checkpoints. <24h constructive. 2 rev complex. Train; metrics.

Check:
- Correctness: Logic/edges WP; manual test; console; input vary; repro.
- Security: OWASP/nonces; static scan; secrets; role; auth flow.
- Coverage: 98%/qual; mock rev; edges; mutation; int link.
- Docs: Complete; ex work; trans; links; guide.
- Consistency: Layers/naming/WP; std check; perf note; bundle; i18n.

Guidelines:
- Template: PR sections; req fields; checkbox; auto fill; vid demo.
- Practices: Impact/alts/conf approve; pos notes; code suggest; diff; crit block.
- Escalation: >48h lead; log; auto timeout; lead notify; mediation.
- Learning: Monthly retros; feedback form; anon survey; tips doc.

VS Code: Live Share annotate. GitHub comm/approvals; thread feedback; emoji; suggest. Checklist. Metrics; peer swap.

RULE: LGTM Threshold. 2 sec changes; 1 docs; module auto-req; balance track; 48h timeout.

GUIDELINE: Feedback Templates. Sandwich; spec ex; alts suggest; impact focus; pos end; skills train.

---

## Environment and Dependencies

### 21. Environment Config

RULE: Isolated Environments

- Dev/test/prod WP configs; local Docker; compose; vol mount; net isolate.
- Local overrides no commit; per-env .env; gitignore; template copy; var val.
- Schema val; JSON lint; config test; diff; backup.

VS Code: Multi-root. .vscode/settings overrides; env task; reload; workspace. Secure vars. Doc; cred rotate.

GUIDELINE: IaC. Docker Compose all envs; ver infra; change test; auto prov; setup scripts doc.

RULE: Team Config Sync. Shared repo; clone pull; diff val; change notify; rev enforce.

---

### 22. Dependency Management

RULE: Secure Practices

- Weekly composer/npm audit; vuln alert/patch; summary report.
- Pin locks; no ^/~; exact semver; range test; dep tree.
- Trusted srcs; WP.org first; checksum/sig; source audit.
- Auto updates; change PR; post-test; rollback; crit freeze.

VS Code: npm Intellisense complete/warn; install audit; dep graph/tree. Lock commit. Pol; Q review.

RULE: Dep Health Scores. Score sec/maint/pop; low block; monthly rev; deprec migrate.

GUIDELINE: Deprec Handling Auto. Scan deprec; migration tickets; alt test; gradual phase; paths doc.

---

## Monitoring and Maintenance

### 23. Health Monitoring

RULE: Automated Monitoring

- REST health /wp-json/myplugin/health; JSON metrics; status/uptime ping.
- DB/API mon; cron ping; uptime/resp time/err count.
- Track queries/render/err rate/sessions.
- Real-time alert; email/Slack; thresh; esc chain.

VS Code: Endpoint task test; mock server; resp view; curl. Daily ping. Dash; alert tune.

GUIDELINE: APM Tools. New Relic PHP trace; Sentry errs; Grafana dash; trace-log corr; 99.9% SLOs.

RULE: UX Metrics Mon. Page loads; user err rates; sessions; A/B impacts; drop alerts.

---

### 24. Logging

RULE: Structured Logging

- Levels err/warn/info/debug WP; custom; user filter; context; rotate.
- Corr IDs reqs/AJAX; trace ID prop; span log.
- Sec explicit; nonce fails; logins; IP; audit.
- Prod JSON; size rotate; backup; search/export.

VS Code: Log Viewer parse/filter; keyword search; CSV export; tail. Weekly rev. Retention; audit.

RULE: ELK Central Logs. Elasticsearch ship; Kibana viz; corr query; 30d retain; auto rotate.

GUIDELINE: Sens Log Anonymize. PII mask/token; GDPR comply; content audit; user export.

---

## Deployment and Production

### 25. Readiness

RULE: Pass Checklist

- BVT post-build; smoke; ver/dep check; build verify.
- Tests/E2E pass; parallel; WP matrix; full cov.
- Scans complete; zero vuln; static/smell; dep vuln.
- Benchmarks; load test; thresh pass; perf budget; scale sim.
- Docs update; WP.org ready; zip; changelog; license.
- Mon op; alert test; prod config; env/rollback test.

VS Code: tasks.json Build; checklist task; report; gate pass. Sign. Deploy doc; readiness gate.

GUIDELINE: Auto Readiness Gates. GitHub Actions verif; fail block; reports; signoffs; pass rate track.

RULE: Dry-Run Deploys. Sim no changes; config val; hook test; log outputs; pre-live fix.

---

### 26. Rollback

RULE: Safe Deploy/Rollback

- Mechs: CLI deactivate; DB revert; file backup; snapshot.
- Staged/blue-green; ver deploys; canary/percent rollout; mon.
- Post metrics; baseline comp; anomaly detect; trigger; log.
- Runbooks: scenarios/contacts; test; Q drill.

VS Code: MD outline; runbook search; PDF print; external link. Drill. Incident plan; post-rev.

RULE: Staging Rollback Tests. Weekly practice; <5min time; data int post; fail doc; mech improve.

GUIDELINE: Flags for Rollbacks. Feat toggle off vs full; gradual; impact mon; easier DB; hist audit.

---

## VS Code Guidelines

RULE: Optimize Environment

- Exts: ESLint/Prettier/GitLens/Thunder/Jest/Error Lens/Version Lens/GitHub PRs/Markdown/Intelephense/WP-CLI; WP Snippets; Docker/Remote SSH/Bracket Pair.
- Settings: .vscode/json formatOnSave true, single quote, autoSave delay, fixAll eslint/phpcs; WP debug true; indent/tab 2; bracket color.
- Tasks: tasks.json test/npm/wp-lint/shell; WP activate; build zip; deploy; cache clean.
- Launch: launch.json Node React/Xdebug PHP; mySQL bp; WP debug port; React hot; multi.
- Keybinds: json Ctrl+Shift+T test, Ctrl+G Git, Ctrl+Alt+W WP-CLI; save all; format doc; toggle comm; symbol find.
- Workspace: extensions.json rec. Remote-Cont WP; Docker compose; port fwd; vol map; devcontainer.
- ENFORCE: Shared .vscode no secrets. Open config; sync settings; backup; migrate guide; val setup.

Q ext update. Team share; policy.

GUIDELINE: WP Themes Customize. WP color schemes; comment hook highlight; PHP/JS icons; team sync.

RULE: Deep WP-CLI Tasks. Tasks wp plugin activate/db exp/imp/theme switch; F5 bind; panel log.

---

## AI Task Completion

RULE: Tasklist Reinforcement

Adhere/complete for deliverables. Checklist. Per-resp check. Weekly audit; user share.

Principles:
- Holistic: Pre-final verify. MD notes; done mark; share/ver checklist; archive.
- No Skip: Mand. Delay issue; high pri; due; esc/notify user.
- Iterative: Post-step audit. Query rev; log/metric; improve/adjust.
- Prompts: "Review: Arch complete, Test 98% etc." Commit notes; ts; signoff; peer/user confirm.

Checklist:
- [ ] Arch: Layers/hooks sep; no direct q; SOLID; namespace; map.
- [ ] Naming: WP/PHP/React pat; slug prefix; case cons; no abbr; lint.
- [ ] Lang: Strict/types/styles; async nonces; escapes; cap check; val lib.
- [ ] Testing: 98%/18 org; cache clear; flaky fix; device; trend.
- [ ] Docs: VER/CHANGELOG/README/inline; ex run; links; trans ready; 90 cov.
- [ ] Vers: Sem WP; dep notice; lock commit; audit clean; pol doc.
- [ ] Sec: Secrets/val/errs; OWASP; scan pass; role test; rotate plan.
- [ ] Perf: Res/limits; transients; index q; bundle min; budget.
- [ ] VC: Commits/issues/branches; templates; signed; protect main; prune.
- [ ] Pre-commit: Lint/types/tests/scans; cov gate; audit zero; no secrets; hook update.
- [ ] Workflow: Branch/PR/rev/merge; staging ver; prod prep; tag; retro.
- [ ] Review: Checks; feedback addr; 1+ app; changes test; metrics.
- [ ] Env: Isolated val; Docker local; config diff; backup; cred rotate.
- [ ] Deps: Audit/pin; weekly; update PR; compat test; src audit.
- [ ] Mon: Endpoints/log; alert test; sample; dash; tune.
- [ ] Deploy: Checklist/rollback; blue-green; canary; post test; incident.
- [ ] VS Code: Exts/settings/tasks; keybinds; sync; theme; pol share.
- [ ] Overall: Enh cov, err fallbacks; multisite; user notice; perf base; user test.

ENFORCE: Resp integrate. User guide. 100% loop; flag inc; progress bar; auto comp; audit share.

GUIDELINE: Task Metrics Track. Time log/task; eff calc; bottlenecks ID; Q adjust; team dash share.

RULE: Checklist Val Auto. Scripts verify items; PR int; flag inc; reports; 100% pre-merge enforce.

---

## Necessity of Tasks

RULE: Exhaustive Completion

Req for qual/reli/prof. Partial undercuts cons/maint/sec; fails/debt/trust loss. 100%/PR. Annual audit; new train.

Why Essential:
- Risk: Skips vuln/reg/bottlenecks; breaches/downtime/loss. Completion pre-prod ID/res; mySQL inj; nonce bypass; XSS; audit.
- Scalability: Frag code compl enh. Full mod/self-doc; onboarding red; WP update easy; theme compat; multisite; API extend.
- Compliance: Viol gates/rev/audits. Full acc/trace/ver; prod-ready; WP.org app; license/GPL; legal.
- User: Expects seamless/sec/perf. Neglect frust/gaps. Full 18 cats vet; robust; admin ease; mobile; i18n; feedback.
- Efficiency: Upfront min rework/debug/inc. Tools/habit auto; CI save; bug down; vel up; ROI.

ENFORCE: Contract. Deviate issue/plan. Q metrics success. 100% or mediocrity; adherence track; full reward; partial pen; milestones celeb.

GUIDELINE: Monthly Task Audits. Adherence rev; team interview; feedback adjust; improve doc; 95% aim.

RULE: Tasks to Biz Value. ROI assign/task; high pri; value track; Q report; user align.

---

## Usage Guidelines

### Changes:
- Lint/tests/type check; WP-CLI run; cov check; secrets scan; build verify.
- Docs imm; inline first; pot update; doc commit; ex add.
- Naming/struct; prefix check; case ver; len limit; folder org.
- Tests cov; WP mock; local run; edges; mutate.
- Commits conv; WP scope; body lines; footer notes; sign ver.

GUIDELINE: Batch Changes Eff. Related group; batch test; unit rev; PR red; context maint.

### Submitting:
- Tests pass; 98 cov; no flaky; all cats; parallel env.
- No sens; commit scan; git secret; diff check; hist clean.
- Sem vers; header update; lock bump; changelogs; compat note.
- Docs comp; links work; build; FAQ add; vid guide.
- Staging val; multisite; theme test; user sim; load sim.

RULE: E2E Val Submissions. Full E2E submit; screenshot diffs; no reg assert; fail block; auto retry.

### Projects:
- Domain adapt; WP spec; post type; block supp; shortcode opt.
- Sec/test strict; always nonces; sanitize all; rate; audit log.
- Docs cons; all PHPDoc/JSDoc props; ADR update; glossary.
- Errs/log struct; WP_Error; JSON log; alert chain; retention pol.

GUIDELINE: New Proj Scaffold. Yeoman WP gen; all folders; VS Code pre-config; val scaffold; domain custom.

### Times:
- Rules follow; section check; daily read; team quiz; rules update.
- Plan acts; steps list; timeline; risks ID; milestone.
- Logical proc; layer seq; dep order; parallel if; rev plan.
- Instances cov; multisite; edge users; role vary; scale sim.
- Errs/fallbacks; user notices; retry btn; user act log; full trace.
- Ref reinforce conclude; checklist mark; signoff; archive; learn share.

RULE: Time-Box Dev Tasks. 2h unit limits; overrun esc; time sinks track; proc opt; speed/qual balance.

### Debugging/Script

RULE: Debug/Resolve Scripts

Gen WP debug scripts. Analyze bugs/common; code-complete solve. No script manual. Include WP-CLI; DB q script; hook list; log analyzer.

Force Scripts Prevent ?. Assignment

TASK: Fix React ?. assignments.

REQUIREMENTS:
1. Search JS/TS /assets/js/; grep pat; file list; line hl.
2. ?. access pat; regex; line num; context.
3. Read/write non-dist; AST parse; node insp; type infer.
4. Exclude assigns; node type check; code comm; branch cov.
5. Val no ?. LHS =/+=; AST check; unit fix test; lint rule; build test.

PATTERNS:
- obj.prop = val to obj?.prop = val (INCORRECT)
- obj.prop to obj?.prop read (CORRECT)

DELIVERABLES:
1. Prevent fixes; ESLint rule; doc update; test case; old migrate.

Focus: Future prevent; CI lint; PR check; scan tool.

GUIDELINE: Debug Scripts Lib. /scripts/debug/; q analyzers; hook tracers; perf prof; usage doc; vers scripts.

RULE: Auto Bug Repro. Min repro scripts; WP-CLI setup; vid record; repro repos share; fix verify repros.

---

# Method for Augment AI VS Code WP

Rules VS Code WP repo interact. Thoughtful solve, clarify assumps, user collab, best prac. Adhere: qual/und > speed.

## Principles
- Clarity: Context pre-changes und. State/ver assumps; num list; WP spec; file cite; new info update.
- Question: Target uncerts; no guess; max 1-3; follow; term clarify; pri Qs.
- Incremental: Small/testable; code move/ref; 1 file first; per commit; easy revert; each test.
- Collaboration: Partner; pause dec; no brute; plan confirm; alt prop; user lead; co-dec.
- Reflection: Blocker ID; Qs/alts prop; opt list; pri; time box; lesson doc.

GUIDELINE: Assumps Doc Explicit. assumptions.md ded; per-int update; pre-code rev; user val share.

## Rules
1. Analysis:
   - Pre-suggest scan struct/deps/mods/pats; WP dirs note; dep tree; size est; compat check.
   - Unclear? Ask: "React/PHP WP files; framework clarify?" Key files list; purpose Q; goal align; constraints.

2. Movement/Refactor:
   - Impact outline: "Hook move 3 files affect; verify?" Files list; risk; cost/ben; test plan.
   - No large sans app. Plan ask: "Goals align?" Num steps; time est; fallback; milestone.

3. Assumption:
   - Resp start list: "Assumps: (1) /v1/users; (2) errorHandler." Bold key; cite src; conf level; wrong impact.
   - Q each: "(1) acc, config check?" Ver suggest; no default; list update; all confirm.

4. Questioning:
   - Amb: Pause 1-3 Qs; WP spec; closed/open mix; why/how; user impact.
   - Ex: "Perf/read goal?" "5 files; compat?" "Type resolve; hook details?" mySQL edge; role; scale; alt cost.
   - Post-ans suggest; no code til clear; ans confirm; sum; next Q chain.

5. Workflow:
   - Assess: Confirm desc: "[task] asking; correct?" WP restate; goal align; scope; success meas.
   - Plan: High risks: "Deps risk; mitigate?" Bullet risks; tools; milestones; res need.
   - Execute: 1 change; term test; feedback: "Diff; npm test?" Cmd run; exp out; diff; user ver.
   - Iterate: Fail explain pivot: "Failed [ ]; explore [ ]?" Why brief; lesson; retry cnt; plan adjust.

6. Pitfalls:
   - Concise thorough; <200w; bullet list; bold key; user focus.
   - Ask > assume; 1+ Q unless clear/trivial; log Qs; cnt; follow.
   - Speed override: Qual > Vel; log override; time balance; user time; effort est.

RULE: Collab Enhance Live Share. VS Code Live real-time pair; term share; co-edit docs; opt record; shared art rev.

## Enforcement/Log
- Log Qs/assumps "Augment AI Log" chan; ts; export opt; filter; date/user search.
- Viol flag: "Pro no full—review"; fix suggest; self corr; learn; incident report.

Rules follow, human-like WP dev VS Code; method iterate; feedback loop; cont improve.

GUIDELINE: Method Eff Q Rev. User survey clarity/speed; rules adjust; changes doc; metrics track; wins celeb.
