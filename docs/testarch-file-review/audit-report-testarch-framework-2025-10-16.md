# Workflow Audit Report

**Workflow:** testarch-framework
**Audit Date:** 2025-10-16
**Auditor:** Audit Workflow (BMAD v6)
**Workflow Type:** Action workflow (no template)
**Workflow Path:** `/Users/brianmadison/dev/BMAD-METHOD/src/modules/bmm/workflows/testarch/framework`

---

## Executive Summary

**Overall Status:** ⚠️ CONCERNS - Some bloat detected, missing web_bundle configuration

- Critical Issues: 1
- Important Issues: 1
- Cleanup Recommendations: 4

**Key Findings:**

1. ✅ Standard config block is correctly configured
2. ✅ Action workflow correctly configured (template: false implied, no template file)
3. ⚠️ Moderate bloat detected - 14 variables defined with ~50% bloat
4. ❌ No web_bundle configuration (critical for web deployment)
5. ✅ Instructions well-written with good knowledge base integration

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

**Total YAML fields analyzed:** 14 variables defined in workflow.yaml (excluding standard config block and metadata)

**Files Present:**

- ✅ workflow.yaml
- ✅ instructions.md
- ✅ checklist.md
- ❌ No template file (correct for action workflow)

**Workflow Type:** Action workflow (no template: field, defaults to false)

### Categorization:

#### INSTRUCTION_USED (Variables referenced in instructions.md):

**Explicitly used with {variable} syntax:**

- {project-root} - Used throughout instructions for paths
- None of the workflow variables are explicitly referenced with {variable} syntax in instructions

**Contextually referenced:**

- test_framework - Mentioned conceptually ("Playwright or Cypress")
- project_type - Mentioned in "Extract project type (React, Vue, Angular...)"
- bundler - Mentioned in "Identify bundler (Vite, Webpack...)"
- test_dir - Used conceptually as "Root test directory"
- config_file - Framework config file paths mentioned
- use_typescript - TypeScript preference mentioned
- framework_preference - Framework selection logic
- project_size - Framework selection criteria

**Output path used:**

- default_output_file: "{test_dir}/README.md" - Used for main deliverable

#### Variables with NO clear usage:

**Bloat Variables:**

1. `standalone_mode: true` - Not referenced in instructions (appears to be a flag for workflow behavior, not used)
2. `generate_env_example: true` - Behavior flag, but .env.example is generated unconditionally in Step 2.4
3. `generate_nvmrc: true` - Behavior flag, but .nvmrc is generated unconditionally in Step 2.5
4. `generate_readme: true` - Behavior flag, but README is generated unconditionally in Step 2.10
5. `generate_sample_tests: true` - Behavior flag, but sample tests are generated unconditionally in Step 2.8

**Empty Placeholders (should be elicited):** 6. `test_framework: ""` - Should be elicited with <ask> tag 7. `project_type: ""` - Auto-detected, empty placeholder unnecessary 8. `bundler: ""` - Auto-detected, empty placeholder unnecessary 9. `config_file: ""` - Derived path, should be calculated not declared

**Total Variables Analyzed:** 14 variables
**Used in Instructions (contextually):** ~8 (test_framework, project_type, bundler, test_dir, use_typescript, framework_preference, project_size, default_output_file)
**Unused (Bloat):** ~6 variables (43% bloat)

**Bloat Breakdown:**

- Boolean behavior flags: 5 (generate_env_example, generate_nvmrc, generate_readme, generate_sample_tests, standalone_mode)
- Empty placeholders: 3 (test_framework, project_type, bundler) - should use <ask> instead
- Derived paths: 1 (config_file) - should be calculated

**Status:** ⚠️ **CONCERNS** - Moderate bloat (43% of variables unused or redundant)

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

- ⚠️ **INDIRECT** - Output folder is used via default_output_file: "{test_dir}/README.md"
- However, test_dir defaults to "{project-root}/tests", not {output_folder}
- This workflow creates artifacts in the test directory, not the output folder
- This is ACCEPTABLE for a setup workflow (artifacts belong in test infrastructure)
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

- `fixture-architecture.md` - Pure function → fixture → mergeTests composition (406 lines, 5 examples)
- `data-factories.md` - Faker-based factories with overrides (498 lines, 5 examples)
- `network-first.md` - Network-first testing safeguards (489 lines, 5 examples)
- `playwright-config.md` - Playwright configuration standards (722 lines, 5 examples)
- `test-quality.md` - Test design principles (658 lines, 5 examples)

These fragments should be declared in a web_bundle configuration for web deployment.

**Expected web_bundle structure:**

```yaml
web_bundle:
  workflow_path: 'bmad/bmm/workflows/testarch/framework/workflow.yaml'
  web_bundle_files:
    - 'bmad/bmm/workflows/testarch/framework/instructions.md'
    - 'bmad/bmm/workflows/testarch/framework/checklist.md'
    - 'bmad/bmm/testarch/knowledge/fixture-architecture.md'
    - 'bmad/bmm/testarch/knowledge/data-factories.md'
    - 'bmad/bmm/testarch/knowledge/network-first.md'
    - 'bmad/bmm/testarch/knowledge/playwright-config.md'
    - 'bmad/bmm/testarch/knowledge/test-quality.md'
    - 'bmad/bmm/testarch/tea-index.csv'
```

**Impact:** This workflow cannot be bundled for web deployment without web_bundle configuration.

**Severity:** CRITICAL (if web deployment is intended)

---

## 5. Bloat Detection

### Unused YAML Fields Analysis

**Total YAML fields:** 14 variables (excluding standard config and metadata)
**Used fields:** ~8 (57%)
**Unused fields:** ~6 (43%)
**Bloat percentage:** **43%**

### Bloat Categories:

#### Category 1: Boolean Behavior Flags (Should be removed - outputs generated unconditionally)

These variables suggest conditional generation, but the instructions generate these artifacts unconditionally:

1. **`generate_env_example: true`**
   - Instructions always generate `.env.example` in Step 2.4
   - Flag has no effect on workflow behavior
   - **Recommendation:** Remove variable, always generate .env.example

2. **`generate_nvmrc: true`**
   - Instructions always generate `.nvmrc` in Step 2.5
   - Flag has no effect on workflow behavior
   - **Recommendation:** Remove variable, always generate .nvmrc

3. **`generate_readme: true`**
   - Instructions always generate `tests/README.md` in Step 2.10
   - Flag has no effect on workflow behavior
   - **Recommendation:** Remove variable, always generate README

4. **`generate_sample_tests: true`**
   - Instructions always generate sample tests in Step 2.8
   - Flag has no effect on workflow behavior
   - **Recommendation:** Remove variable, always generate samples

5. **`standalone_mode: true`**
   - Not referenced anywhere in instructions
   - Purpose unclear (possibly for workflow orchestration?)
   - **Recommendation:** Remove if unused, or document purpose

**Impact:** These 5 variables create false impression of configurability when behavior is hardcoded.

#### Category 2: Empty Placeholders (Should use <ask> tags instead)

These variables are empty strings waiting to be populated, but instructions should elicit them dynamically:

6. **`test_framework: ""`**
   - Instructions describe auto-detection logic in Step 2.1
   - Should use <ask> tag if auto-detection fails
   - **Recommendation:** Remove variable, use <ask> in instructions

7. **`project_type: ""`**
   - Auto-detected from package.json in Step 1.1
   - Empty placeholder unnecessary
   - **Recommendation:** Remove variable, detect in instructions

8. **`bundler: ""`**
   - Auto-detected from package.json in Step 1.1
   - Empty placeholder unnecessary
   - **Recommendation:** Remove variable, detect in instructions

9. **`config_file: ""`**
   - Derived path: `{project-root}/{framework}.config.{ts|js}`
   - Should be calculated in instructions based on detected framework
   - **Recommendation:** Remove variable, calculate in instructions

**Impact:** These 4 variables add no value and clutter the configuration.

#### Category 3: Acceptable Variables (Keep these)

These variables have legitimate workflow-specific purpose:

1. **`test_dir: "{project-root}/tests"`** - KEEP
   - Defines root test directory location
   - Used throughout instructions for directory structure
   - User might want to customize (e.g., "e2e-tests")

2. **`use_typescript: true`** - KEEP
   - Determines whether to generate .ts or .js files
   - Legitimate configuration option

3. **`framework_preference: "auto"`** - KEEP
   - Allows user to override auto-detection (playwright, cypress, auto)
   - Legitimate configuration option

4. **`project_size: "auto"`** - KEEP
   - Influences framework recommendation (small, large, auto)
   - Legitimate configuration option

5. **`default_output_file: "{test_dir}/README.md"`** - KEEP
   - Standard workflow output path
   - Required field

**Total Bloat Items:** 9 variables should be removed or relocated

**Bloat Percentage:** 64% (9 of 14 variables)
**Cleanup Potential:** MEDIUM - Removing bloat would reduce variables from 14 to 5

---

## 6. Template Variable Mapping

**Workflow Type:** Action workflow (no template)

**Template File:** None (correct)

**Template Variable Mapping:** N/A - This is an action workflow that generates files directly (config files, fixtures, sample tests, README).

**<template-output> Tags:** None (correct for action workflow)

**Status:** ✅ **N/A** - Action workflow correctly configured without template

---

## Recommendations

### Critical (Fix Immediately)

1. **Add web_bundle configuration**
   - Add web_bundle block with all required files (instructions, checklist)
   - Map knowledge fragments (fixture-architecture.md, data-factories.md, network-first.md, playwright-config.md, test-quality.md)
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

3. **Remove boolean behavior flags (5 variables)**
   - Delete: `generate_env_example`, `generate_nvmrc`, `generate_readme`, `generate_sample_tests`, `standalone_mode`
   - These artifacts are always generated (flags have no effect)
   - Simplifies configuration and removes false impression of conditionality
   - Severity: CLEANUP
   - Impact: Reduces bloat from 64% to 29%, improves clarity

4. **Remove empty placeholder variables (4 variables)**
   - Delete: `test_framework: ""`, `project_type: ""`, `bundler: ""`, `config_file: ""`
   - Use <ask> tags in instructions to elicit test_framework if auto-detection fails
   - Auto-detect project_type and bundler in instructions (no variable needed)
   - Calculate config_file path in instructions based on detected framework
   - Severity: CLEANUP
   - Impact: Reduces variable count from 14 to 5 (64% reduction)

5. **Add <ask> tag for framework selection**
   - In Step 2.1, add: `<ask>Which test framework would you like to use? [playwright/cypress/auto]</ask>`
   - Use response to set framework choice
   - Remove `test_framework: ""` placeholder variable
   - Severity: CLEANUP
   - Impact: Makes workflow properly interactive

6. **Document standalone_mode purpose or remove**
   - Variable is not referenced in instructions
   - If intended for workflow orchestration, document usage
   - If unused, remove
   - Severity: CLEANUP
   - Impact: Clarifies configuration intent

---

## Validation Checklist

Use this checklist to verify fixes:

- [x] All standard config variables present and correct ✅ (Already passing)
- [ ] No unused yaml fields (bloat removed to <20%)
- [ ] Config variables used appropriately in instructions
- [ ] Web bundle includes all dependencies
- [ ] Template variables properly mapped (N/A - action workflow) ✅
- [x] File structure follows v6 conventions ✅
- [ ] Variables block reduced from 14 to 5 essential variables
- [ ] Boolean flags removed (outputs always generated)
- [ ] Empty placeholders removed (<ask> tags added)
- [x] Action workflow correctly configured (no template) ✅

---

## Next Steps

1. **Review critical issues** and fix web_bundle configuration immediately
2. **Address important issues** in next iteration (config usage)
3. **Consider cleanup recommendations** for optimization (bloat removal)
4. **Re-run audit** after fixes to verify improvements

**Estimated Cleanup Impact:**

- Variables: 14 → 5 (64% reduction)
- Bloat: 64% → 0%
- Maintainability: Improved
- Web deployment: Enabled (after web_bundle added)

---

## Positive Observations

**What This Workflow Does Well:**

1. ✅ **Excellent Instructions Quality**
   - Clear step-by-step scaffolding process
   - Comprehensive code examples (Playwright config, Cypress config, fixtures, factories)
   - Good knowledge base integration (5 fragments referenced)
   - Practical preflight checks

2. ✅ **Proper Action Workflow Structure**
   - No template file (correct for action workflow)
   - Generates artifacts directly (config files, fixtures, tests)
   - Clear deliverables listed in Step 3

3. ✅ **Good Workflow Design**
   - Auto-detection logic (package.json, framework)
   - Intelligent defaults (Playwright for large projects, Cypress for small teams)
   - Comprehensive test infrastructure (fixtures, factories, helpers)
   - Best practices built in (data-testid selectors, auto-cleanup, failure-only artifacts)

4. ✅ **Knowledge Base Integration**
   - References tea-index.csv
   - Loads 5 relevant knowledge fragments
   - Applies patterns from knowledge base (fixture architecture, data factories)

**Overall:** This is a well-designed workflow with moderate bloat. Cleanup would make it excellent.

---

**Audit Complete** - Generated by audit-workflow v1.0
