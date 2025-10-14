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

## Core Development Rules

### 1. Architecture and Code Organization

RULE: Follow Layered Architecture with Separation of Concerns

Structure plugins for modularity and reusability. Each layer has defined responsibilities. Follow SOLID principles for WordPress hooks, actions, filters, and React components. Specific: Limit layers to 3-5 per plugin. General: Review layers quarterly; document layer map.

#### Key Principles
- Modularity: Use independent modules for development. Separate PHP services from React components and mySQL access; prefix modules with plugin name; limit module size to 500 lines; use subfolders for large modules.
- Dependency Management: Use WP patterns or Composer for PHP. Enqueue React scripts with wp_enqueue_script; declare dependencies in enqueue; version scripts; load conditional on user role.
- Avoid: Coupling to WP globals without abstraction, god classes, circular dependencies; no direct $post access; no hardcode paths; no static calls across layers.
- Benefits: Improves readability, debugging, WP multisite support; enables theme compatibility; supports updates; reduces load time.
- Add: Use namespaces for PHP classes to avoid conflicts; register React scripts in admin only when needed; conditional enqueue on page; dequeue on uninstall; lazy load services.

- Data/Logic Layer: Handle business logic, validation, mySQL via $wpdb. Use DDD entities and repositories; index mySQL tables for queries; use custom tables prefixed; backup tables on activate; migrate on update.
- Presentation Layer: Use React for admin, PHP templates for public. Use hooks or Redux for state; enqueue styles separately from scripts; use CSS modules in React; minify CSS; optimize images.
- Control Layer: Use WP hooks, AJAX handlers. Add nonces, validate requests, log points; use add_action for entry, do_action for exit; priority on hooks; remove on deactivate; schedule cron if needed.
- Services: PHP classes for external calls. Keep stateless, use transients, version for WP; cache API responses in options; timeout on calls; retry logic; validate responses.
- Utilities: Pure functions for tasks like sanitization; test utilities without WP load; export for reuse; inline if small; memoize if pure.
- Types/Interfaces: PHP interfaces for hooks, TS for React. Use contracts, generics, runtime validation; type hint all PHP params; PropTypes in React; default props; validate interfaces.

ENFORCE: Keep layer boundaries. Delegate queries to services. Use PHPCS for checks; custom sniffs for layers; fail build on violate. Specific: Audit boundaries per PR; log violations. General: Train team on SOLID; layer diagram in repo.

VS Code: Use Outline (Ctrl+Shift+O) for navigation. Error Lens for warnings. Dependency Cruiser for rules; set up snippets for WP hooks; graph view for deps; hover docs; color layers.

---

### 2. File Naming and Structure

RULE: Consistent Naming Patterns

Use names that show intent. Specific: Review names in code review. General: Document conventions in README; lint names; update yearly.

- Services: camelCaseService.php in /includes/services/; prefix with plugin slug; abstract suffix if needed; singleton if global; doc purpose.
- Components/UI: PascalCase.jsx in /admin/js/components/; snake_case.php in /admin/partials/; group by feature folder; index file for exports; story for components; test file adjacent.
- Utilities: camelCase.php/js in /includes/utils/; one function per file if complex; barrel exports JS; type file alongside; version if public.
- Models/Types: PascalCase.php/ts in /includes/models/; separate interfaces from classes; DTO pattern; validate models; schema file.
- Tests: .test.php/jsx mirroring source in /tests/; add coverage per file; setup file shared.

ENFORCE: No generic names. Follow WP structure: /includes/, /admin/, /public/, /assets/, /languages/; /templates/ for views; /build/ for minified; /migrations/ for DB. Specific: Structure check in CI. General: Folder permissions; archive old.

VS Code: Compact Folders. Ctrl+P search. Prettier/ESLint/PHPCS on save; use search for hook names; exclude node_modules; glob patterns; file icons.

---

### 3. Language and Framework Standards

RULE: Strict Configurations

Use strict typing and validation. Specific: Enforce in CI. General: Audit compliance monthly; train on standards.

- Type-check: declare(strict_types=1) in PHP, strict TS, prepared $wpdb; check WP capabilities in functions; use ? for nullable; enum for statuses; strict mode JS.
- Declare returns: In PHP/TS; use union types for WP errors; void for no return; mixed only last resort; annotate all.
- No untyped: Avoid mixed, raw queries; escape all outputs; no var_dump; no die; no eval.
- Error handling: Try-catch with WP_Error; log with error_log; custom exceptions; throw early; rethrow if needed.
- Validate: WP sanitize, Zod for props; check nonces in AJAX; rate limit inputs; CSRF tokens; schema validate.

#### PHP/WP Style Rules

Variables: camelCase, const preferred, no globals except WP; prefix locals if needed; $this_ for class; underscore private; init in constructor.

Functions: Closures for hooks, destructure params, 20-30 lines, private props, pure, immutable args; return early on fails; single responsibility; named args PHP8; doc params.

Strings: Double for interp, single literals, esc_html output; use __() for translatable; plural gettext; sprintf format; concat with .

Conditionals: ===, early returns, capability checks; if WP_DEBUG add checks; switch for roles; ternary simple; match PHP8.

Async: WP AJAX hooks, try-catch, wp_send_json_error, AbortController in JS; limit concurrent requests; poll for long tasks; web worker if heavy; promise chain.

Modules: Composer autoload, group imports, spread over .apply; require once for files; conditional load; lazy load; dynamic require if rare.

Safety: wp_verify_nonce, esc_attr, ?? for defaults; filter inputs with apply_filters; validate emails; URL check; IP validate.

Arrays: map/filter/reduce, foreach, array_pad; use array_key_exists for checks; sort consistent; unique keys; diff/merge.

PHP: Interfaces for hooks, annotate params/returns; use traits for shared logic; abstract classes; final methods; namespace use.

Debugging: error_log JSON, no echo, WP_DEBUG; conditional debug if !WP_DEBUG; log levels; stack trace; profiler if slow.

Data: readonly props, WP_Query over arrays; use get_transient for cache; serialize options; unserialize safe; validate unserialize.

Input: sanitize_*, type guards; validate file uploads size/type; MIME check; virus scan if possible; hash files.

Documentation: PHPDoc/JSDoc, wp_date; @since for versions; @param types; @return desc; @example code.

Frameworks: WP standards, React hooks, mySQL index; update PHPCS/ESLint monthly; React memo; SQL explain; optimize table.

#### React/mySQL
- React: Hooks for state, WP REST fetch, sanitize props; use useCallback for handlers; error boundaries; suspense load; context providers.
- mySQL: $wpdb->prepare, esc_sql; use transactions for multi-queries; error on fail; last error log; vacuum tables.

VS Code: PHP Intelephense, TS ext, Problems panel, formatOnSave, ESLint/PHPCS; keybind for WP doc lookup; theme WP colors; folding regions; snippet pack.

---

## Testing Requirements

### 4. Test Coverage Standards

RULE: 98% Coverage Across 18 Categories

Maintain 98% coverage (line/branch/path) for WP flows, edges, multisite, threats. Specific: Min branch 95%. General: Coverage trend up; report monthly.

Cover 18 categories.

1. Unit Tests
   - Isolate PHP/React/mySQL units.
   - Focus: Edges, boundaries, precision. Mock $wpdb/WP.
   - Example: calculateTax with mocked options.
   - Add: Test pure functions first; parametrize inputs; seed random; assert types.

2. Integration Tests
   - Check PHP/React/WP REST/mySQL flows.
   - Focus: Hooks, DB, third-party.
   - Example: Payment hook to mySQL.
   - Add: Test nonce in calls; mock third-party; env setup; cleanup data.

3. E2E Tests
   - Full stack: React to PHP to mySQL.
   - Focus: Multisite, workflows.
   - Example: Checkout from React to email.
   - Add: Use WP-CLI for setup; video record; user journey; assert email.

4. System Tests
   - Plugin in WP env, health/resources.
   - Focus: PHP-mySQL interplay.
   - Example: Activate on staging.
   - Add: Test with themes; memory limit; plugin conflict; server load.

5. Regression Tests
   - Re-run post-changes.
   - Focus: Impact analysis.
   - Example: Re-run auth after update.
   - Add: Run on WP version change; selective by file; baseline compare; diff results.

6. Acceptance Tests
   - Meet WP requirements, BDD.
   - Focus: Scenarios.
   - Example: Settings under 5s.
   - Add: User stories in tests; Gherkin format; stakeholder sign; acceptance criteria.

7. Performance Tests
   - Benchmark times/throughput.
   - Example: JMeter AJAX <200ms.
   - Add: Test mySQL locks; concurrent 50; flame graph; memory peak.

8. Load/Stress Tests
   - Peak surges, bottlenecks.
   - Example: 10x users, monitor leaks.
   - Add: Simulate cron jobs; ramp up load; break point; recovery time.

9. Security Tests
   - Scan vulns, pen tests, WP compliance.
   - Focus: OWASP, nonces.
   - Example: ZAP for SQL/XSS/CSRF.
   - Add: Test capability checks; fuzz inputs; Burp suite; auth bypass.

10. Compatibility Tests
    - WP versions/themes/plugins/browsers/devices.
    - Focus: Backward/forward.
    - Example: React across WP 6.0+, browsers.
    - Add: Test Gutenberg blocks; mobile viewport; plugin matrix; theme switch.

11. Accessibility Tests
    - WCAG/ADA for React/PHP.
    - Focus: Scans/audits.
    - Example: Screen readers on forms.
    - Add: Check focus order; contrast tool; WAVE scan; keyboard nav.

12. Exploratory Tests
    - Unstructured probes.
    - Focus: Heuristics/SBTM.
    - Example: Unicode in search.
    - Add: Test rapid clicks; edge devices; user personas; session notes.

13. Smoke Tests
    - Basic operational checks.
    - Focus: Post-build.
    - Example: Activate, load admin.
    - Add: Check shortcodes; plugin list; menu item; console errors.

14. Sanity Tests
    - High-impact funcs post-change.
    - Focus: Fixes.
    - Example: Nonce after patch.
    - Add: Test roles; user switch; cache clear; perm check.

15. Usability Tests
    - Intuitiveness, SUS.
    - Focus: Tasks/errors.
    - Example: Configure settings.
    - Add: Time on task; heatmap; A/B variants; survey score.

16. L10n/I18n Tests
    - Multilingual, RTL, formats.
    - Focus: Pseudo-loc.
    - Example: Spanish dates/currency.
    - Add: Test .po files; string length; locale switch; RTL layout.

17. Recovery Tests
    - Disruptions, recovery.
    - Focus: Rollback/chaos.
    - Example: DB terminate mid-query.
    - Add: Test WP cache clear; failover sim; backup verify; data loss.

18. Data Integrity Tests
    - Persistence, validation.
    - Focus: ACID/restores.
    - Example: Malformed import reject.
    - Add: Backup/restore options; charset check; duplicate detect; constraint test.

ENFORCE:
- Merge Gates: No integration without 98% peer-reviewed tests. Use SonarQube/Codecov; block low coverage; approve coverage; coverage delta.
- Critical: BVT/E2E/Regression/Security + Perf/Recovery for auth/transactions; manual for critical; sign off; risk assess.
- CI/CD: Automate in GitHub Actions/WP-CLI, parallel, flakiness alerts, approvals; retry 3x; artifact store; parallel envs.
- Vigilance: Quarterly audits, TDD/BDD, dashboards; gap report; team train; coverage goal 99.
- Add: Test on PHP 8.1+; BrowserStack; device lab; WP nightly.

VS Code: Test Explorer (PHPUnit/Jest). tasks.json for coverage. Codecov badges; run on save; debug test; test sidebar; inline coverage.

---

### 5. Test Organization

RULE: Mirror Structure, Keep Readable

Organize tests like source. Use AAA: Arrange mocks, Act, Assert. Specific: AAA in all. General: Colocate always; test naming convention.

- Unit: tests/unit/ mirror (userService.test.php); isolate WP with mocks; coverage per file; group assertions; assert types.
- Integration: tests/integration/ by subsystem (ajax/); shared DB spins; env vars for test; teardown DB; cleanup data.
- E2E: tests/e2e/ by journeys (admin-flow/); page objects for selectors; wait for load; screenshot fail; assert email.
- Utilities: tests/utils/ mocks/factories; fake data generators; seeder class; configurable fakes; setup file shared.
- Fixtures: tests/fixtures/ (sample-users.sql); version with schema; import script; clean script; data gen.

PATTERN: Test per file. Names like shouldReturnProfileValidId; scenario in desc; given/when/then; test ID.

Practices:
- Naming: <module>.test.<ext>, describe/it; use table for data; nested describe; skip if known; tag tests.
- Data: Parameterize, cleanup afterEach; reset DB state; teardown hooks; global setup; data factory.
- Mocking: External only. Reset mocks afterEach (jest.resetAllMocks); restore in Mocha sinon.restore; spy on hooks; mock once; partial mocks.
- CI: Parallel scripts, artifacts; flaky test labels; report HTML; email fail; timeout global.
- Pitfalls: Over-mock, brittle selectors, timeouts; ignore slow tests; timeout 5s; mock time; stale mocks.
- Benefits: Fast debug, TDD; CI pass rate; coverage trend; test isolation.

Cache Clear Process:
Clear Jest cache pre-runs for consistency. Specific: Clear before suite. General: Doc process.

1. Location: node_modules/.cache/jest/; config path; backup cache; multi-env.
2. Command: npx jest --clearCache; --watch mode; selective clear; force flag.
3. Workflows: package.json "test:clean", tasks.json task, CI prepend, Husky; pre-test only; log time; hook trigger.
4. Verify: jest --no-cache; log warnings; diff results; re-run full.
5. Edge: Per-package, invalidate configs; monorepo clear; custom transformer; log clear time; disk space check.

ENFORCE: Document in README. Gate merges; script in package; warn if not; enforce via lint. Specific: Clear daily. General: Train on process; monitor cache size.

VS Code: Search/symbols for tests. Debugger breakpoints. Testing ext watch. Tasks for clear; keybind test run; coverage view; test config; run group.

---

## Documentation Standards

### 6. Automatic Updates

RULE: Update Docs with Changes

Treat docs as code: version, test, review. Automate. Specific: Update per commit. General: Audit docs quarterly; doc coverage 90%.

- VERSION: Increment header, notes. semantic-release Composer; update "Tested up to"; changelog link; requires PHP; min WP.
- CHANGELOG: Detail files/features/bugs. Keep format: Added/Changed/etc; link WP Trac; breaking section; migration steps; contrib credits.
- README: Instructions/examples/badges; quickstart code; FAQ section; screenshots; install video.
- API: PHPDoc/JSDoc, Swagger REST; hook examples; endpoint params; response codes; auth req.

Guidelines:
- Templates: MD for notes/endpoints; include nonces in examples; migration guide; before/after; template vars.
- Auto: phpDocumentor/JSDoc/Docusaurus; build on CI; deploy docs; search index; auto link.
- Validation: Vale/Alex; check links; spell check; grammar; readability score.
- Accessibility: Headings/alt/readable; plain text alternatives; screen reader notes; table headers; color contrast.

VS Code: Markdown Preview. Husky for CHANGELOG; snippet for PHPDoc; lint docs; link validate; toc gen.

---

### 7. Version Management

RULE: Semantic Versioning

- MAJOR: Breaking API/mySQL; update WP requires; dep list; changelog major; bump year if calver.
- MINOR: Compatible adds; new React features; feature flag; opt-in; add compat note.
- PATCH: Fixes; security patches; hotfix tag; urgent note; backport if stable.

Guidelines:
- Pre: -alpha for beta; test in staging; beta users; feedback form; changelog pre.
- Changelogs: Link tags/issues; WP.org notes; diff prev; size change; user impact.
- Tools: Composer/npm, calver optional; lockfile commit; audit post-update; compat test; diff tool.
- Deprecation: Advance notice, guides; admin notices; console warn; remove next major; migrate script.

VS Code: Version Lens for bumps; preview changes; auto changelog; tag push. Specific: Bump on merge. General: Review versions monthly; version policy doc.

---

### 8. Code/Process Docs

RULE: Inline/External Docs

- Document public funcs/classes/APIs/logic; why for decisions; examples for configs/workflows; WP role examples; error cases; success paths; perf notes.

Guidelines:
- Inline: PHPDoc/JSDoc params/returns/thrown/examples/@see; @wp-hook for filters; @version tag; @access public; @deprecated if.
- Architecture: ADRs in docs/adrs/; one per decision; template ADR; status column; review ADR.
- Process: MD for workflows; onboard steps; diagram key; flow chart; checklist process.
- Diagrams: Mermaid/PlantUML in MD; hook flows; export PNG; alt text; interactive if.
- Review: PRs include updates, checklists; doc test in CI; link check; coverage doc; doc PR.

VS Code: Auto stubs (Insert JSDoc). Thunder Client API; preview hooks; generate all; sync doc. Specific: Doc in PR template. General: Doc sprints; doc review.

---

## Security and Privacy Rules

### 9. Sensitive Data

RULE: No Secrets Exposed/Committed

- Storage: WP options/env, managers; encrypt if needed; salt keys; rotate periodic; audit access.
- Templates: .env.example; list vars; default placeholders; comment use; example values.
- No commit: .env/keys; .gitignore rules; pre-commit scan; CI scan; git history clean.

VS Code: dotenv load. Git view .gitignore; alert on secrets; GitGuardian ext; scan push. Specific: Scan weekly. General: Policy doc; secret rotation plan.

---

### 10. Input Validation

RULE: Validate/Sanitize Inputs

- Payloads/uploads/queries; size/type checks; max length; min value; format match.
- Sanitize text; for JS too; DOMPurify; HTML strip; entity decode.
- Parameterized DB; bind all vars; escape raw; quote safe; input log.

VS Code: Schema previews JSON Store; inline hints; VSCode schema; validate on type. Specific: Test inputs. General: Review validate code; validate library.

---

### 11. Error Handling

RULE: Robust Error Management

Adopt approach for security, maintainability, UX. Specific: Cover 100% branches. General: Error taxonomy doc.

- No Internal Exposure: No traces/debug to users. Log internal; use WP_Error; suppress in prod; user vs dev mode; mask details.
- Structured Logging: JSON with timestamp/code/level/metadata; WP cron alert; rotate size; searchable; aggregate tool.
- User Messages: Descriptive, actions, plain language; admin notices; dismissible; link support; localize msg.
- Fallbacks: Cache/alts/queue; transients on fail; offline mode; grace period; retry mech.
- Auto Reporting: Test fails to GitHub Issue summary/steps/logs/tags; screenshots for E2E; attach video; auto label; triage bot.

VS Code: Debugger breakpoints. Output channels; log filter; export logs; search log. Specific: Log review weekly. General: Error playbook; post-mortem template.

---

## Performance and Optimization

### 12. Resource Management

RULE: Monitor Resources

- Release handles/connections; close cursors; GC hints; unset vars; profile alloc.
- Caching/pooling; object cache; Memcached if avail; Redis option; invalidate cache.
- Monitor memory/CPU/network; query times; flame graph; profile tool; alert high.

VS Code: Process Explorer. tasks.json profiling; run on load; threshold alert; chart view. Specific: Profile monthly. General: Perf budget; optimize guide.

---

### 13. Fair Use/Rate Limiting

RULE: Respect Services

- Config limits; WP cron track; IP hash; user based; token bucket.
- Retries backoff; max retries 3; jitter delay; circuit break; status track.
- Quotas policies; doc usage; user settings; admin adjust; report exceed.

VS Code: REST Client sims; mock responses; throttle test; delay sim. Specific: Test limits. General: Policy update; quota dashboard.

---

## Version Control and Collaboration

### 14. Commit Standards

RULE: Conventional Commits

Format: type(scope): desc
Types: feat/fix/docs/style/refactor/test/chore; scope wp-admin/db/react; body details; footer issues; breaking! if.

VS Code: GitLens templates/validation; hook enforce; amend if invalid; co-author. Specific: Review msgs. General: Convention train; commit lint.

---

### 15. Errors as Issues

RULE: Track Errors/Bugs in Issues

Log all in GitHub for visibility. Test fails first. Specific: Issue per error. General: Weekly triage; close stale.

Guidelines:
- Title: Summary e.g. "AJAX 500 empty payload"; include WP version; PHP ver; React ver; env tag.
- Description: Context/steps/expected/actual/screenshots/logs; mySQL dump if relevant; env details; repro repo; minimal code.
- Labels: bug/security/perf/docs; wp-compat; area-php/react; severity; module tag.
- Priority: critical/high/medium/low; auto based on impact.
- Assignees: Dev/team; based on area; auto assign; notify; rotate.
- Linkage: Commits/PRs/discussions; WP Trac; related issues; duplicate check; blocks.
- Lifecycle: Create/triage/assign/resolve/close; weekly review; close criteria; verified label; reopen policy.

ENFORCE:
- Document all; no private reports; reference in close; template for issues; required fields; bot validate; auto close duplicates.

VS Code: GitHub ext create/manage; inline link; bulk edit; search issues. Specific: Template load. General: Issue bot; label policy.

---

### 16. Branch Management

RULE: Structured Branching

- main: Prod ready; tag releases; sign tags; protect push; require status.
- develop: Integration; merge weekly; hotfix to develop too; nightly build; test branch.
- feature/*: New; from develop; ticket in name; WIP prefix; delete after merge.
- hotfix/*: Urgent; to main; backport develop; test urgent; short life.
- release/*: Prep; final tests; cherry-pick fixes; RC tag; freeze code.

VS Code: Source Control switch. Git Graph visualize; color branches; merge conflict tool; branch create cmd. Specific: Delete merged. General: Branch policy doc; prune old.

---

### 17. Pre-commit

RULE: Automated Checks

- Linting PHPCS/ESLint; custom sniffs; fix auto; format code; style guide.
- Type-check; PHPStan level 5; TS strict; no errors; warn only.
- Tests pass; coverage min; unit only pre; quick smoke; integration light.
- Security scans; composer audit; npm audit; secrets scan; vuln fix.

VS Code: Husky/lint-staged tasks. Ctrl+Shift+P Run Task; status bar indicator; pass/fail icon; quick fix. Specific: Run silent. General: Update hooks; check log.

---

### 18. Commit/Merge to main

RULE: Commit/Merge with Notes

Follow tasklists/mergeGitHub.md. Specific: Notes required. General: Merge train; post-merge hook.

Guidelines:
- Commit: Stage Source Control. Conventional msgs detail/rationale/files/testing, no emojis; prefix slug; sign commits; verify sign; amend if lint fail.
- Merge: PR feature to main. Notes scope/details/coverage/impacts. Reviews 1+; assign reviewer; auto review request; thread comments; conflict resolve.
- Format: Summary/Changes/Testing/Rationale/Next; risks section; related PRs; dep on PR; user story.
- ENFORCE: No direct push. Approvals. Actions checks. Reference checklists; rebase option; squash default; merge msg; timeout PR.

VS Code: GitLens history/blame. GitHub Repos PR/merge; inline review; resolve thread; draft PR. Specific: Auto draft. General: Review guidelines; merge bot.

---

## Development Workflow

### 19. Feature Process

WORKFLOW: Git Flow for WP. Specific: 1 week max feature. General: Retrospective end; sprint plan.

1. Branch from develop: feature/<id>-desc. Pull latest; update submodules; stash if needed; local branch; init git flow.
2. Implement/tests: TDD, 98% coverage. Mock WP; commit often; branch tests; lint per commit; daily build.
3. Docs: Inline/README/API. Generate; check links; update pot; translate sample; doc test.
4. Checks: Lint/test/build WP-CLI; coverage report; WP version test; theme compat; security scan.
5. PR: Checklist/tests/docs/screenshots. Link issues; draft if WIP; labels auto; size estimate; auto lint.
6. Review: 1+ approval. Address feedback; ping if stalled; re-review changes; unit test add; conflict free.
7. Merge develop: Squash/conflicts. CI integration; notify channel; update changelog; tag dev; back merge.
8. Staging: CI/CD deploy. Verify flows; multisite test; load test; user accept; smoke prod.
9. Main/prod: Approval. Tag/notify; WP.org submit; release notes; announce; monitor first day.

Guidelines:
- Roles: Dev implement/review standards/QA WP; rotate reviewers; mentor new; pair program; QA gate.
- Tools: GitHub Projects/Slack; ticket templates; bot reminders; integration bot; time track.
- Escalation: Issues/standups; daily sync; blockers board; escalate matrix; root cause.
- Metrics: <3 days cycle; track velocity; burn down; lead time; defect rate.

VS Code: Terminal Ctrl+` WP-CLI. keybindings Git; task for full flow; PR template; workflow ext. Specific: Auto task run. General: Workflow doc; sprint board.

---

### 20. Code Review

RULE: Mandatory for Merges

Checkpoints for standards/knowledge/issues. <24h, constructive. Specific: 2 reviewers if complex. General: Review training; metrics track.

Check:
- Correctness: Logic/edges WP; run manual test; console check; input vary; repro steps.
- Security: OWASP/nonces; scan static; secrets scan; role test; auth flow.
- Coverage: 98%/quality; review mocks; edge coverage; mutation test; integration link.
- Docs: Complete; examples work; translatable; link valid; user guide.
- Consistency: Layers/naming/WP; standards check; perf note; bundle size; i18n ready.

Guidelines:
- Template: PR sections; required fields; checkbox; auto fill; video demo.
- Practices: Impact/alts/approve confident; positive notes; suggestion code; diff view; block if critical.
- Escalation: >48h lead; escalate log; timeout auto; notify lead; mediation process.
- Learning: Retros; monthly; feedback form; anon survey; review tips doc.

VS Code: Live Share annotate. GitHub comments/approvals; thread feedback; emoji reactions; suggest changes. Specific: Review checklist. General: Review metrics; peer swap.

---

## Environment and Dependencies

### 21. Environment Config

RULE: Isolated Environments

- Dev/test/prod WP configs; Docker for local; compose file; volume mount; network isolate.
- Local overrides no commit; .env per env; gitignore env; template copy; var validate.
- Validate schemas; JSON lint; config test; diff tool; backup config.

VS Code: Multi-root folders. .vscode/settings overrides; env switch task; reload window; workspace file. Specific: Env vars secure. General: Env doc; rotate creds.

---

### 22. Dependency Management

RULE: Secure Practices

- Audit composer/npm; weekly; alert vulns; patch apply; report summary.
- Pin versions locks; no ^/~; exact semver; range test; dep tree.
- Trusted sources; WP.org first; verify checksum; signature check; source audit.
- Auto updates; PR for changes; test post-update; rollback plan; freeze critical.

VS Code: npm Intellisense complete/warn; audit on install; dep graph; tree view. Specific: Lock commit. General: Dep policy; quarterly review.

---

## Monitoring and Maintenance

### 23. Health Monitoring

RULE: Automated Monitoring

- Health endpoints REST; /wp-json/myplugin/health; metrics JSON; status codes; uptime ping.
- Monitor DB/API; ping cron; uptime check; response time; error count.
- Metrics track; query times; render time; error rate; user sessions.
- Alert real-time; email on fail; Slack hook; threshold set; escalation chain.

VS Code: Tasks endpoint test; mock server; response view; curl task. Specific: Daily ping. General: Monitor dashboard; alert tune.

---

### 24. Logging

RULE: Structured Logging

- Levels error/warn/info/debug WP; custom levels; filter by user; context add; level rotate.
- Correlation IDs requests; for AJAX; trace ID; propagate; span log.
- Security explicit; nonce fails; login attempts; IP log; audit trail.
- JSON prod; rotate size; backup logs; search index; export format.

VS Code: Log Viewer parse/filter; search keywords; export CSV; tail file. Specific: Log review weekly. General: Log retention; compliance audit.

---

## Deployment and Production

### 25. Readiness

RULE: Pass Checklist

- BVT post-build; smoke run; version check; dep check; build verify.
- Tests pass; E2E too; parallel run; matrix WP; coverage full.
- Scans complete; vuln zero; static analysis; code smell; dep vuln.
- Benchmarks valid; load test; threshold pass; perf budget; scale sim.
- Docs updated; WP.org ready; zip build; changelog include; license check.
- Monitoring operational; alert test; prod config; env vars; rollback test.

VS Code: tasks.json Run Build; checklist task; output report; pass gate. Specific: Checklist sign. General: Deploy doc; readiness gate.

---

### 26. Rollback

RULE: Safe Deploy/Rollback

- Rollback mechanisms; deactivate CLI; DB revert; file backup; snapshot.
- Staged/blue-green; versioned deploys; canary users; rollout percent; monitor rollout.
- Monitor metrics post; baseline compare; anomaly detect; rollback trigger; post-roll log.
- Runbooks; scenarios list; contact list; test runbook; drill quarterly.

VS Code: MD Outline reference; search runbook; print PDF; link external. Specific: Rollback drill. General: Incident plan; post-incident review.

---

## VS Code Guidelines

RULE: Optimize Environment

- Extensions: ESLint/Prettier/GitLens/Thunder/Jest/Error Lens/Version Lens/GitHub PRs/Markdown/PHP Intelephense/WP-CLI; WP Snippets; Docker; Remote SSH; Bracket Pair.
- Settings: .vscode/json formatOnSave true, quote single, autoSave delay, codeActions fixAll eslint/phpcs; WP debug true; indent 2; tab size 2; bracket color.
- Tasks: tasks.json test/npm, wp-lint/shell; WP activate task; build zip; deploy task; clean cache.
- Launch: launch.json Node React, Xdebug PHP; mySQL breakpoint; WP debug port; React hot reload; multi config.
- Keybindings: json Ctrl+Shift+T test, Ctrl+G Git, Ctrl+Alt+W WP-CLI; save all; format doc; comment toggle; find symbol.
- Workspace: extensions.json recommend. Remote-Containers WP; Docker compose; port forward; volume map; devcontainer.
- ENFORCE: Shared .vscode no secrets. Configure on open; sync settings; backup config; migrate guide; validate setup.

Specific: Update exts quarterly. General: Team config share; VS Code policy.

---

## AI Task Completion

RULE: Reinforcement for Tasklist

Adhere/complete tasklist for deliverables. Use checklist. Specific: Check per response. General: Weekly self-audit; share with user.

Principles:
- Holistic: Verify tasks pre-final. MD notes; mark done; share checklist; version checklist; archive complete.
- No Skip: Mandatory. Issue if delay; priority high; due date; escalate; notify user.
- Iterative: Audit post-step. Review query; log audit; metric track; improve; adjust plan.
- Prompts: "Review: Architecture complete, Testing 98%, etc." Commit notes; timestamp; sign off; peer review; user confirm.

Checklist:
- [ ] Architecture: Layers/hooks separated; no direct queries; SOLID check; namespace all; layer map.
- [ ] Naming: Patterns WP/PHP/React; prefix slug; consistent case; no abbr; lint names.
- [ ] Language: Strict/types/styles; nonces in async; escapes all; capability check; validate lib.
- [ ] Testing: 98%/18 organized; cache clear; flaky fixed; device test; trend up.
- [ ] Docs: VERSION/CHANGELOG/README/inline; examples run; links valid; translate ready; coverage 90.
- [ ] Versioning: Semantic WP; dep notices; lock commit; audit clean; policy doc.
- [ ] Security: Secrets/validate/errors; OWASP check; scan pass; role test; rotation plan.
- [ ] Performance: Resources/limits; transients use; index queries; bundle min; budget set.
- [ ] VC: Commits/issues/branches; templates; signed commits; protect main; prune old.
- [ ] Pre-commit: Linting/types/tests/scans; coverage gate; audit zero; secrets no; update hooks.
- [ ] Workflow: Branch/PR/review/merge; staging verify; prod prep; release tag; retrospective.
- [ ] Review: Checks performed; feedback addressed; approved 1+; changes test; metrics track.
- [ ] Env: Isolated validated; Docker local; config diff; backup env; rotate creds.
- [ ] Deps: Audited/pinned; weekly audit; update PR; compat test; source audit.
- [ ] Monitoring: Endpoints/logging; alert test; log sample; metric dash; tune alerts.
- [ ] Deploy: Checklist/rollback; blue-green; canary; post deploy test; incident plan.
- [ ] VS Code: Exts/settings/tasks; keybinds; workspace sync; theme set; policy share.
- [ ] Overall: Enhancements covered, errors fallbacks; WP multisite; user notice; perf baseline; user test.

ENFORCE: Integrate response. Guide users. Loop 100%; flag incompletes; progress bar; auto complete; share audit.

---

## Necessity of Tasks

RULE: Exhaustive Completion

Completion is requirement for quality/reliability/professionalism. Partial undermines consistency/maintainability/security, leads to failures/debt/trust loss. Specific: 100% per PR. General: Audit annual; train new.

Why Essential:
- Risk: Skips expose vulns/regressions/bottlenecks, breaches/downtime/losses. Completion identifies/resolves pre-prod; mySQL injections; nonce bypass; XSS risk; audit trail.
- Scalability: Fragments code, complicates enhancements. Full modular/self-doc, reduces onboarding; WP updates easy; theme compat; multisite scale; extend API.
- Compliance: Violates gates/reviews/audits. Full accountability/trace/versioning, prod-ready; WP.org approve; license check; GPL comply; legal review.
- User: Expects seamless/secure/perf. Neglect frustrates/gaps. Full vets 18 categories, robust software; admin ease; mobile support; i18n ready; feedback loop.
- Efficiency: Upfront min rework/debug/incidents. Tools automate, habit; CI time save; bug rate down; velocity up; ROI calc.

ENFORCE: Contract. Deviate Issue/plan. Quarterly metrics success. 100% or mediocrity; track adherence; reward full; penalty partial; celebrate milestones.

---

## Usage Guidelines

### Changes:
- Checks lint/tests/type; WP-CLI run; coverage check; scan secrets; build verify.
- Docs immediate; inline first; update pot; commit doc; examples add.
- Naming/structure; prefix check; case verify; length limit; folder org.
- Tests coverage; mock WP; run local; edge cases; mutate test.
- Commits conventional; scope WP; body lines; footer notes; sign verify.

### Submitting:
- Tests pass; coverage 98; no flaky; all categories; parallel env.
- No sensitive; scan commit; git secret; diff check; history clean.
- Versions semantic; header update; lock bump; changelogs; compat note.
- Docs complete; links work; build docs; FAQ add; video guide.
- Staging validate; multisite; theme test; user sim; load sim.

### Projects:
- Adapt domain; WP specific; post type; block support; shortcode opt.
- Security/testing strict; nonces always; sanitize all; rate limit; audit log.
- Docs consistent; PHPDoc all; JSDoc props; ADR update; glossary.
- Errors/logging structured; WP_Error use; JSON log; alert chain; retention policy.

### Times:
- Follow rules; check section; daily read; quiz team; update rules.
- Plan actions; list steps; timeline; risks id; milestone mark.
- Logical process; sequence layers; dep order; parallel if; review plan.
- Cover instances; multisite too; edge users; role vary; scale sim.
- Errors/fallbacks; notices user; retry button; log user action; trace full.
- Reference reinforcement conclude; checklist mark; sign off; archive complete; share learn.

### Debugging/Script

RULE: Scripts for Debug/Resolve

Generate scripts debug WP. Analyze bugs/common, code-complete solve. No script, manual. Add: Use WP-CLI in scripts; DB query script; hook list; log analyzer.

Force Scripts Prevent ?. Assignment

TASK: Fix scripts ?. assignments React.

REQUIREMENTS:
1. Search JS/TS transformers /assets/js/; grep pattern; file list; line highlight.
2. Patterns ?. access; regex match; line num; context show.
3. Non-distinguish read/write; AST parse; node inspect; type infer.
4. Exclude assignments; node type check; comment code; branch cover.
5. Validate no ?. LHS =/+=; AST check; unit test fix; lint rule; build test.

PATTERNS:
- obj.prop = value to obj?.prop = value (INCORRECT)
- obj.prop to obj?.prop read (CORRECT)

DELIVERABLES:
1. Fixes prevent; ESLint rule; doc update; test case; migrate old.

Focus: Prevent future; CI lint; PR check; scan tool.

---

# Method for Augment AI VS Code WP

Rules for VS Code WP repo interact. Thoughtful solve, clarify assumptions, collaborate user, best practices. Adhere, quality/understanding > speed.

## Principles
- Clarity: Understand context pre-changes. State/verify assumptions; list numbered; WP specific; cite file; update on new info.
- Question: Ask targeted uncertainties, no guesses; 1-3 max; follow up; clarify term; prioritize Qs.
- Incremental: Small/testable, code move/refactor; one file first; commit per; revert easy; test each.
- Collaboration: Partner, pause decisions, no brute; confirm plan; alt propose; user lead; co-decide.
- Reflection: Identify blocker, propose Qs/alts; list options; prioritize; time box; doc lesson.

## Rules
1. Analysis:
   - Scan structure deps/modules patterns pre-suggest; note WP dirs; dep tree; size est; compat check.
   - Unclear? Ask: "Files React/PHP WP; clarify framework?" List key files; purpose query; goal align; constraint list.

2. Movement/Refactor:
   - Outline impact: "Move hook affects 3 files; verify?" List files; risk list; cost benefit; test plan.
   - No large no approval. Plan ask: "Align goals?" Steps numbered; time est; fallback; milestone.

3. Assumption:
   - List start response: "Assumptions: (1) /v1/users; (2) errorHandler." Bold key; source cite; confidence level; impact if wrong.
   - Q each: " (1) accurate, check config?" Suggest verify; default if no; update list; confirm all.

4. Questioning:
   - Ambiguity: Pause 1-3 Qs; specific WP; closed/open mix; why/how; user impact.
   - Examples: "Goal perf/read?" "5 files; compat?" "Resolve type; details hook?" Edge mySQL; user role; scale impact; alt cost.
   - Suggest post-answer; no code til clear; confirm answer; summarize; next Q if; chain Qs.

5. Workflow:
   - Assess: Describe confirm: "Asking [task]; correct?" WP restate; goal align; scope bound; success measure.
   - Plan: High-level risks: "Risk deps; mitigate?" Bullet risks; tools list; milestones; resource need.
   - Execute: One change, test terminal, feedback: "Diff; npm test results?" Run command; expected out; diff show; verify user.
   - Iterate: Explain fail pivot: "Failed [ ]; explore [ ]?" Why brief; lesson note; retry count; adjust plan.

6. Pitfalls:
   - Concise thorough; under 200 words; bullet if list; bold key; user focus.
   - Ask > assume; 1+ Q unless clear; unless trivial; log Qs; Q count; follow-through.
   - Override speed: Quality > Velocity; log override; balance time; user time; estimate effort.

## Enforcement/Log
- Log Qs/assumps "Augment AI Log" channel; timestamp, export option(s), and filter log(s); search by date/user.
- Violate flag: "Proceeded no full—review"; suggest fix, self correct, learn from, and report incident.

Follow rules, human-like WP dev VS Code; iterate method; feedback loop; continuous improve.
