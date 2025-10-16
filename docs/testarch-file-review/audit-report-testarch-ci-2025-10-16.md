# Workflow Audit Report

**Workflow:** testarch-ci
**Audit Date:** 2025-10-16
**Auditor:** Audit Workflow (BMAD v6)
**Workflow Type:** Action workflow (no template)
**Workflow Path:** `/Users/brianmadison/dev/BMAD-METHOD/src/modules/bmm/workflows/testarch/ci`

---

## Executive Summary

**Overall Status:** ⚠️ CONCERNS - Significant bloat detected, missing web_bundle configuration

- Critical Issues: 1
- Important Issues: 1
- Cleanup Recommendations: 5

**Key Findings:**

1. ✅ Standard config block is correctly configured
2. ✅ Action workflow correctly configured (no template)
3. ✅ Excellent instructions with good CI/CD best practices
4. ❌ **SEVERE BLOAT:** 28 variables defined with ~75% bloat (21 unused variables!)
5. ❌ No web_bundle configuration (critical for web deployment)
6. ⚠️ Config variable usage missing

---

## 1. Standard Config Block Validation

### Required Variables Check

✅ **Config Source Check:**

- [x] `config_source` is defined: `"{project-root}/bmad/bmm/config.yaml"`
- [x] Points to correct module config path (bmm)
- [x] Uses {project-root} variable

✅ **Standard Variables Check:**

- [x] `output_folder` pulls from config_source: `"{config_source}:output_folder"`
- [x] `user_name` pulls from config_source: `"{config_source}:user_name"`
- [x] `communication_language` pulls from config_source: `"{config_source}:communication_language"`
- [x] `date` is set to system-generated: `"system-generated"`

**Status:** ✅ **PASS** - All standard config variables present and correctly configured

---

## 2. YAML/Instruction/Template Alignment

### Variables Analysis

**Total YAML fields analyzed:** 28 variables defined in workflow.yaml (excluding standard config block and metadata)

**Files Present:**

- ✅ workflow.yaml
- ✅ instructions.md
- ✅ checklist.md
- ✅ github-actions-template.yaml (template artifact, not workflow template)
- ✅ gitlab-ci-template.yaml (template artifact, not workflow template)
- ❌ No workflow template file (correct for action workflow)

**Workflow Type:** Action workflow (no template field for workflow itself)

### Categorization:

#### INSTRUCTION_USED (Variables referenced in instructions.md):

**Contextually referenced:**

- ci_platform - Platform detection logic
- test_framework - Mentioned in preflight
- test_dir - Test directory location
- node_version_source - Node version from .nvmrc
- Some configuration values mentioned conceptually (sharding, burn-in, etc.)

**Output path used:**

- default_output_file: "{project-root}/.github/workflows/test.yml"

#### MASSIVE BLOAT DETECTED:

**Category 1: Boolean Behavior Flags (18 variables - all generate outputs unconditionally!)**

The workflow defines 18 boolean/configuration flags that have NO effect on workflow execution. The instructions generate ALL artifacts and features unconditionally:

1. **`parallel_jobs: 4`** - Instructions always configure 4 shards (Step 2.3)
2. **`burn_in_enabled: true`** - Instructions always add burn-in loop (Step 2.4)
3. **`burn_in_iterations: 10`** - Hardcoded as 10 in instructions
4. **`selective_testing_enabled: true`** - Always generated (Step 2.9 scripts)
5. **`artifact_retention_days: 30`** - Hardcoded in instructions
6. **`upload_artifacts_on: "failure"`** - Always "failure" in instructions
7. **`artifact_types: "traces,screenshots,videos,html-report"`** - All types always included
8. **`cache_enabled: true`** - Caching always configured (Step 2.5)
9. **`browser_cache_enabled: true`** - Browser cache always configured
10. **`timeout_minutes: 60`** - Hardcoded timeouts in instructions
11. **`test_timeout_minutes: 30`** - Hardcoded in instructions
12. **`notify_on_failure: false`** - Instructions show notification example (Step 2.8) regardless
13. **`notification_channels: ""`** - Empty, not used
14. **`generate_ci_readme: true`** - Always generated (Step 2.10)
15. **`generate_local_mirror_script: true`** - Always generated (Step 2.9)
16. **`generate_secrets_checklist: true`** - Always generated (Step 2.10)
17. **`use_matrix_strategy: true`** - Always used in templates
18. **`use_sharding: true`** - Always used in templates
19. **`retry_failed_tests: true`** - Always configured (Step 2.7)
20. **`retry_count: 2`** - Hardcoded in instructions

**Impact:** 20 variables that create false impression of configurability when ALL features are always generated.

**Category 2: Empty Placeholders (2 variables - should be elicited)**

21. **`test_framework: ""`** - Auto-detected in Step 1.2, empty placeholder unnecessary
22. **`config_file: ""`** - Derived from framework detection, should be calculated

**Category 3: Derived Paths (1 variable)**

23. **`node_version_source: "{project-root}/.nvmrc"`** - Always reads .nvmrc, variable adds no value

**Total Variables Analyzed:** 28 variables
**Used in Instructions:** ~7 (ci_platform, test_dir, default_output_file, and contextual mentions)
**Unused (Bloat):** ~21 variables (75% bloat!)

**Bloat Breakdown:**

- Boolean behavior flags: 20 (ALL features generated unconditionally)
- Empty placeholders: 2 (test_framework, config_file)
- Unnecessary derived paths: 1 (node_version_source)

**Status:** ❌ **FAIL** - SEVERE bloat (75% of variables unused, highest bloat of all audited workflows)

---

## 3. Config Variable Usage

### Communication Language Check:

- ❌ **MISSING** - No "communicate in {communication_language}" pattern found in instructions
- Instructions do not reference communication_language variable
- Severity: IMPORTANT

### User Name Check:

- ❌ **MISSING** - No {user_name} usage found in instructions
- No personalization or greeting patterns detected
- Severity: MINOR (optional for this workflow type)

### Output Folder Check:

- ⚠️ **INDIRECT** - Output folder not used directly
- This workflow creates CI pipeline files (`.github/workflows/test.yml`), not docs
- Scripts and docs go to `scripts/` and `docs/` directories
- This is ACCEPTABLE for a CI setup workflow (artifacts belong in project structure)
- Severity: N/A (appropriate for this workflow)

### Date Usage Check:

- ✅ **AVAILABLE** - Date is defined for potential use in documentation
- Not explicitly used but available if needed
- Severity: N/A

**Status:** ⚠️ **IMPORTANT** - Config variables not properly utilized (communication_language missing)

---

## 4. Web Bundle Validation

### Web Bundle Present: ❌ **NO**

**Status:** ❌ **CRITICAL** - No web_bundle configuration found

The workflow.yaml contains:

```yaml
web_bundle: false
```

This means the workflow is **NOT** configured for web deployment.

**Missing web_bundle requirements:**

- No web_bundle_files list
- No existing_workflows mapping
- No web deployment path configuration

**Knowledge Fragment Dependencies Detected in Instructions:**

The instructions reference loading knowledge fragments from `tea-index.csv`:

- `ci-burn-in.md` - Burn-in loop patterns (678 lines, 4 examples)
- `selective-testing.md` - Changed test detection strategies (727 lines, 4 examples)
- `visual-debugging.md` - Artifact collection best practices (522 lines, 5 examples)
- `test-quality.md` - CI-specific test quality criteria (658 lines, 5 examples)
- `playwright-config.md` - CI-optimized configuration (722 lines, 5 examples)

**Template Files Present:**

- `github-actions-template.yaml`
- `gitlab-ci-template.yaml`

These should be declared in web_bundle for web deployment.

**Expected web_bundle structure:**

```yaml
web_bundle:
  workflow_path: 'bmad/bmm/workflows/testarch/ci/workflow.yaml'
  web_bundle_files:
    - 'bmad/bmm/workflows/testarch/ci/instructions.md'
    - 'bmad/bmm/workflows/testarch/ci/checklist.md'
    - 'bmad/bmm/workflows/testarch/ci/github-actions-template.yaml'
    - 'bmad/bmm/workflows/testarch/ci/gitlab-ci-template.yaml'
    - 'bmad/bmm/testarch/knowledge/ci-burn-in.md'
    - 'bmad/bmm/testarch/knowledge/selective-testing.md'
    - 'bmad/bmm/testarch/knowledge/visual-debugging.md'
    - 'bmad/bmm/testarch/knowledge/test-quality.md'
    - 'bmad/bmm/testarch/knowledge/playwright-config.md'
    - 'bmad/bmm/testarch/tea-index.csv'
```

**Impact:** This workflow cannot be bundled for web deployment without web_bundle configuration.

**Severity:** CRITICAL (if web deployment is intended)

---

## 5. Bloat Detection

### Unused YAML Fields Analysis

**Total YAML fields:** 28 variables (excluding standard config and metadata)
**Used fields:** ~7 (25%)
**Unused fields:** ~21 (75%)
**Bloat percentage:** **75%**

### Detailed Bloat Analysis:

#### Category 1: Configuration Values That Are Always Hardcoded (20 variables)

These variables suggest customization but the instructions **ALWAYS** use specific hardcoded values:

1. **`parallel_jobs: 4`** → Instructions hardcode "strategy: matrix: shard: [1, 2, 3, 4]"
2. **`burn_in_enabled: true`** → Burn-in loop always added in Step 2.4
3. **`burn_in_iterations: 10`** → Instructions hardcode "for i in {1..10}"
4. **`selective_testing_enabled: true`** → test-changed.sh always generated
5. **`artifact_retention_days: 30`** → Instructions hardcode "retention-days: 30"
6. **`upload_artifacts_on: "failure"`** → Instructions always use "if: failure()"
7. **`artifact_types: "traces,screenshots,videos,html-report"`** → All types always collected
8. **`cache_enabled: true`** → Caching always configured
9. **`browser_cache_enabled: true`** → Browser cache always configured
10. **`timeout_minutes: 60`** → Timeouts hardcoded in instructions
11. **`test_timeout_minutes: 30`** → Hardcoded
12. **`notify_on_failure: false`** → Notification example shown regardless
13. **`notification_channels: ""`** → Empty, not used
14. **`generate_ci_readme: true`** → docs/ci.md always generated
15. **`generate_local_mirror_script: true`** → ci-local.sh always generated
16. **`generate_secrets_checklist: true`** → ci-secrets-checklist.md always generated
17. **`use_matrix_strategy: true`** → Matrix always used in templates
18. **`use_sharding: true`** → Sharding always configured
19. **`retry_failed_tests: true`** → Retry logic always added
20. **`retry_count: 2`** → Hardcoded as "max_attempts: 3"

**Recommendation:** Remove ALL 20 configuration variables. The workflow should have a single, opinionated CI setup with hardcoded best practices. Users can customize the generated files if needed.

**Rationale:** These variables create maintenance burden and confusion. The instructions ignore them anyway, always generating the full-featured pipeline.

#### Category 2: Empty Placeholders (2 variables)

21. **`test_framework: ""`** - Auto-detected from config files in Step 1.2
22. **`config_file: ""`** - Derived from test_framework detection

**Recommendation:** Remove these. Use <ask> tag if auto-detection fails.

#### Category 3: Unnecessary Derived Paths (1 variable)

23. **`node_version_source: "{project-root}/.nvmrc"`** - Instructions always read .nvmrc directly

**Recommendation:** Remove. Hardcode .nvmrc path in instructions (standard location).

#### Category 4: Auto-Detection Variable (1 variable) - KEEP

24. **`ci_platform: "auto"`** - Legitimate choice (auto, github-actions, gitlab-ci, circle-ci, jenkins)

**Recommendation:** KEEP - This is the ONLY legitimate configuration variable.

#### Category 5: Standard Paths (2 variables) - KEEP

25. **`test_dir: "{project-root}/tests"`** - Standard path, could be customized
26. **`default_output_file: "{project-root}/.github/workflows/test.yml"`** - Required workflow output

**Recommendation:** KEEP - Standard workflow paths.

**Total Bloat Items:** 23 of 28 variables (82%!)

**Bloat Percentage:** 82%
**Cleanup Potential:** EXTREME - Removing bloat would reduce variables from 28 to 5

**After Cleanup, Only These Should Remain:**

1. `ci_platform: "auto"` - User choice for platform
2. `test_dir: "{project-root}/tests"` - Standard path
3. `default_output_file: "{project-root}/.github/workflows/test.yml"` - Output path
4. `installed_path`, `instructions`, `validation` - Standard workflow fields

---

## 6. Template Variable Mapping

**Workflow Type:** Action workflow (no template for workflow itself)

**Template Files:** github-actions-template.yaml, gitlab-ci-template.yaml (these are CI pipeline templates, not workflow templates)

**Template Variable Mapping:** N/A - This is an action workflow that generates CI configuration files directly.

**<template-output> Tags:** None (correct for action workflow)

**Status:** ✅ **N/A** - Action workflow correctly configured without template

---

## Recommendations

### Critical (Fix Immediately)

1. **Add web_bundle configuration**
   - Add web_bundle block with all required files (instructions, checklist, templates)
   - Map knowledge fragments (ci-burn-in.md, selective-testing.md, visual-debugging.md, test-quality.md, playwright-config.md)
   - Include github-actions-template.yaml and gitlab-ci-template.yaml
   - Include tea-index.csv for knowledge base access
   - Enable web deployment capability
   - Severity: CRITICAL
   - Impact: Workflow cannot be deployed to web without this

### Important (Address Soon)

2. **Add config variable usage in instructions**
   - Add communication_language support for multilingual workflows
   - Add greeting or summary using {user_name}
   - Ensure proper integration with BMAD v6 config standards
   - Severity: IMPORTANT
   - Impact: Workflows not following BMAD v6 conventions

### Cleanup (Nice to Have)

3. **Remove ALL configuration bloat (23 variables!)**
   - Delete: parallel_jobs, burn_in_enabled, burn_in_iterations, selective_testing_enabled, artifact_retention_days, upload_artifacts_on, artifact_types, cache_enabled, browser_cache_enabled, timeout_minutes, test_timeout_minutes, notify_on_failure, notification_channels, generate_ci_readme, generate_local_mirror_script, generate_secrets_checklist, use_matrix_strategy, use_sharding, retry_failed_tests, retry_count, test_framework, config_file, node_version_source
   - **Rationale:** Instructions ignore these variables and generate a full-featured, opinionated CI pipeline
   - Hardcode best practices directly in instructions (4 shards, 10 burn-in iterations, 30-day retention, failure-only artifacts, etc.)
   - Users can customize generated files if they need different values
   - Severity: CLEANUP
   - Impact: Reduces bloat from 82% to 0%, eliminates false configurability

4. **Simplify to minimal variables**
   - Keep only: ci_platform (user choice), test_dir (standard path), default_output_file (output path)
   - Remove everything else per recommendation 3
   - Result: 28 variables → 5 variables (82% reduction!)
   - Severity: CLEANUP
   - Impact: Dramatically improves maintainability and clarity

5. **Document opinionated defaults**
   - In instructions, add comment block explaining hardcoded values:
     - "This workflow generates an opinionated CI pipeline with production-tested defaults"
     - "4 parallel shards (balance speed vs resource usage)"
     - "10 burn-in iterations (catches ~99% of flaky tests)"
     - "30-day artifact retention (debugging window vs storage cost)"
     - "Users can customize generated files after workflow completion"
   - Severity: CLEANUP
   - Impact: Sets correct expectations about configurability

6. **Add <ask> for platform if auto-detection fails**
   - In Step 1.4, add: `<ask>Unable to auto-detect CI platform. Which platform would you like to use? [github-actions/gitlab-ci/circle-ci/jenkins]</ask>`
   - Use response to select template
   - Severity: CLEANUP
   - Impact: Makes workflow properly interactive

---

## Validation Checklist

Use this checklist to verify fixes:

- [x] All standard config variables present and correct ✅ (Already passing)
- [ ] No unused yaml fields (bloat removed to <20%)
- [ ] Config variables used appropriately in instructions
- [ ] Web bundle includes all dependencies
- [ ] Template variables properly mapped (N/A - action workflow) ✅
- [x] File structure follows v6 conventions ✅
- [ ] Variables block reduced from 28 to 5 essential variables
- [ ] Boolean flags removed (opinionated pipeline with hardcoded best practices)
- [ ] Empty placeholders removed (<ask> tags added if needed)
- [x] Action workflow correctly configured (no template) ✅

---

## Next Steps

1. **Review critical issues** and fix web_bundle configuration immediately
2. **Address important issues** in next iteration (config usage)
3. **Consider cleanup recommendations** for optimization (MASSIVE bloat removal)
4. **Re-run audit** after fixes to verify improvements

**Estimated Cleanup Impact:**

- Variables: 28 → 5 (82% reduction - HIGHEST of all workflows!)
- Bloat: 82% → 0%
- Maintainability: Dramatically improved
- Clarity: Much clearer (opinionated pipeline vs false configurability)
- Web deployment: Enabled (after web_bundle added)

---

## Positive Observations

**What This Workflow Does Well:**

1. ✅ **Excellent CI/CD Best Practices**
   - Comprehensive pipeline stages (lint, test, burn-in, report)
   - Production-proven patterns (4 shards, 10 burn-in iterations)
   - Smart artifact strategy (failure-only, 30-day retention)
   - Aggressive caching (dependencies + browser binaries)
   - Local CI mirror for debugging

2. ✅ **Strong Instructions Quality**
   - Clear step-by-step CI scaffolding
   - Platform-specific guidance (GitHub Actions, GitLab CI, Circle CI)
   - Comprehensive code examples (pipeline configs, scripts)
   - Good preflight checks (git repo, passing tests, framework setup)
   - Excellent output summary with performance targets

3. ✅ **Knowledge Base Integration**
   - References tea-index.csv
   - Loads 5 relevant knowledge fragments
   - Applies CI/CD patterns from knowledge base
   - Burn-in loop pattern from production systems

4. ✅ **Proper Action Workflow Structure**
   - No template file (correct for action workflow)
   - Generates CI configs, scripts, and docs directly
   - Clear deliverables listed

5. ✅ **Production-Ready Defaults**
   - 4 parallel shards (balanced speed)
   - 10 burn-in iterations (high confidence)
   - Failure-only artifacts (cost optimization)
   - 30-day retention (debugging window)

**Overall:** This is a well-designed workflow with EXCELLENT content but SEVERE bloat. The 23 unused variables create false impression of configurability when the workflow is (correctly) opinionated with hardcoded best practices. Removing bloat would make this an exceptional workflow.

**The Bloat Paradox:** The instructions are excellent BECAUSE they ignore the variables and use hardcoded best practices. The variables serve no purpose except confusion.

---

**Audit Complete** - Generated by audit-workflow v1.0
