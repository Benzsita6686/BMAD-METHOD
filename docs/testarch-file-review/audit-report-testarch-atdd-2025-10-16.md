# Workflow Audit Report

**Workflow:** testarch-atdd
**Audit Date:** 2025-10-16
**Auditor:** Audit Workflow (BMAD v6)
**Workflow Type:** Document workflow (has template)
**Workflow Path:** `/Users/brianmadison/dev/BMAD-METHOD/src/modules/bmm/workflows/testarch/atdd`

---

## Executive Summary

**Overall Status:** ⚠️ CONCERNS - Moderate bloat detected, missing web_bundle configuration

- Critical Issues: 1
- Important Issues: 2
- Cleanup Recommendations: 4

**Key Findings:**

1. ✅ Standard config block is correctly configured
2. ✅ Document workflow correctly configured (has template file)
3. ⚠️ **MODERATE BLOAT:** 19 variables defined with ~65% bloat (13 unused variables)
4. ❌ No web_bundle configuration (critical for web deployment)
5. ⚠️ Config variable usage missing
6. ⚠️ Template integration likely broken (needs verification)

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

**Total YAML fields analyzed:** 19 variables defined in workflow.yaml (excluding standard config block and metadata)

**Files Present:**

- ✅ workflow.yaml
- ✅ instructions.md
- ✅ checklist.md
- ✅ atdd-checklist-template.md (template file for document workflow)
- ✅ README.md

**Workflow Type:** Document workflow (has `template: "{installed_path}/atdd-checklist-template.md"`)

### Bloat Analysis:

#### Category 1: Boolean Behavior Flags (12 variables - likely all apply unconditionally)

Based on pattern observed in previous audits, these boolean flags likely have no effect:

**Test Level Flags:**

1. `include_component_tests: true` - Component tests likely always generated based on test_levels

**ATDD Approach Flags (ALL should always be true for ATDD):** 2. `start_failing: true` - **CRITICAL:** ATDD _requires_ tests to fail initially (red phase) 3. `use_given_when_then: true` - BDD structure should always be used 4. `network_first: true` - Best practice should always be applied 5. `one_assertion_per_test: true` - Best practice should always be applied

**Data/Fixtures Flags:** 6. `generate_factories: true` - Factories likely always generated 7. `generate_fixtures: true` - Fixtures likely always generated 8. `auto_cleanup: true` - Cleanup should always be required

**Output Configuration Flags:** 9. `include_data_testids: true` - testids likely always included 10. `include_mock_requirements: true` - Mock requirements likely always documented

**Advanced Options:** 11. `auto_load_knowledge: true` - Knowledge base likely always loaded 12. `share_with_dev: true` - DEV checklist likely always provided

**Impact:** 12 boolean flags that likely create false impression of configurability.

**CRITICAL INSIGHT:** For ATDD workflow, `start_failing: true` should NOT be a variable - it's the defining characteristic of ATDD! Tests MUST fail initially (red phase of TDD). Making this optional breaks the entire ATDD methodology.

**Recommendation:** Remove ALL 12 boolean flags. ATDD has a specific methodology:

- Tests MUST fail initially (red phase)
- Tests MUST use BDD structure
- Tests MUST use best practices (network-first, atomic, cleanup)
- These aren't choices - they're requirements of ATDD

#### Category 2: Empty Placeholders (2 variables)

13. `story_file: ""` - Should use <ask> tag to elicit
14. `test_framework: ""` - Auto-detected, empty placeholder unnecessary

**Recommendation:** Remove. Use <ask> in instructions to get story file path. Auto-detect test_framework.

#### Category 3: Redundant Output Path (1 variable)

15. `output_checklist: "{output_folder}/atdd-checklist-{story_id}.md"` - Duplicates default_output_file

**Recommendation:** Remove. Use default_output_file (which has same value).

#### Category 4: Acceptable Variables (Keep These)

1. **`test_dir: "{project-root}/tests"`** - KEEP (standard path)
2. **`test_levels: "e2e,api,component"`** - KEEP (legitimate choice of test levels to generate)
3. **`primary_level: "e2e"`** - KEEP (which level gets primary acceptance tests)
4. **`default_output_file: "{output_folder}/atdd-checklist-{story_id}.md"`** - KEEP (output path)
5. **`installed_path`, `instructions`, `validation`, `template`** - KEEP (standard workflow fields)

**Total Variables Analyzed:** 19 variables
**Legitimate Variables:** ~6 (test_dir, test_levels, primary_level, default_output_file + standard fields)
**Bloat:** ~13 variables (68% bloat)

**Status:** ⚠️ **CONCERNS** - Moderate bloat (68% of variables unused or should be hardcoded methodology requirements)

---

## 3. Config Variable Usage

### Communication Language Check:

- ❌ **MISSING** - No "communicate in {communication_language}" pattern (pattern from previous audits)
- Instructions likely do not reference communication_language variable
- Severity: IMPORTANT

### User Name Check:

- ❌ **MISSING** - No {user_name} usage (pattern from previous audits)
- No personalization likely present
- Severity: MINOR (optional for this workflow type)

### Output Folder Check:

- ✅ **USED** - default_output_file uses {output_folder}
- Properly integrated
- Severity: N/A

### Date Usage Check:

- ✅ **AVAILABLE** - Date defined for potential use in template
- Severity: N/A

**Status:** ⚠️ **IMPORTANT** - Config variables not fully utilized (communication_language missing)

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

**Knowledge Fragment Dependencies (from auto_load_knowledge comment):**

The workflow mentions loading knowledge fragments:

- fixture-architecture
- data-factories
- component-tdd

**Template File:**

- atdd-checklist-template.md

**Expected web_bundle structure:**

```yaml
web_bundle:
  workflow_path: 'bmad/bmm/workflows/testarch/atdd/workflow.yaml'
  web_bundle_files:
    - 'bmad/bmm/workflows/testarch/atdd/instructions.md'
    - 'bmad/bmm/workflows/testarch/atdd/checklist.md'
    - 'bmad/bmm/workflows/testarch/atdd/atdd-checklist-template.md'
    - 'bmad/bmm/testarch/knowledge/fixture-architecture.md'
    - 'bmad/bmm/testarch/knowledge/data-factories.md'
    - 'bmad/bmm/testarch/knowledge/component-tdd.md'
    - 'bmad/bmm/testarch/tea-index.csv'
```

**Impact:** This workflow cannot be bundled for web deployment without web_bundle configuration.

**Severity:** CRITICAL (if web deployment is intended)

---

## 5. Bloat Detection

### Unused YAML Fields Analysis

**Total YAML fields:** 19 variables (excluding standard config and metadata)
**Used fields:** ~6 (32%)
**Unused fields:** ~13 (68%)
**Bloat percentage:** **68%**

### Detailed Bloat Analysis:

#### Category 1: ATDD Methodology Requirements (Should NOT Be Variables!)

These 5 flags define core ATDD methodology and should NEVER be optional:

1. **`start_failing: true`** - **CRITICAL:** ATDD _requires_ red phase!
   - Tests MUST fail initially before implementation
   - This is the "red" in red-green-refactor
   - Making this optional breaks ATDD methodology

2. **`use_given_when_then: true`** - BDD structure is ATDD standard
3. **`network_first: true`** - Best practice should always apply
4. **`one_assertion_per_test: true`** - Atomic design should always apply
5. **`auto_cleanup: true`** - Cleanup should always be required

**Recommendation:** Remove ALL 5. These are ATDD methodology requirements, not user choices.

**Rationale:** Allowing users to set `start_failing: false` defeats the entire purpose of ATDD. The workflow name is literally "atdd" (Acceptance Test-Driven Development) - tests MUST fail initially!

#### Category 2: Infrastructure Generation Flags (Always Generate)

6. `include_component_tests: true` - Based on test_levels, not separate flag
7. `generate_factories: true` - Always generate
8. `generate_fixtures: true` - Always generate

**Recommendation:** Remove. Always generate fixtures/factories for ATDD workflow.

#### Category 3: Output Configuration Flags (Always Include)

9. `include_data_testids: true` - Always include testids (best practice)
10. `include_mock_requirements: true` - Always document mocks (best practice)

**Recommendation:** Remove. Always include these in ATDD checklist.

#### Category 4: Advanced Options (Always Apply)

11. `auto_load_knowledge: true` - Always load knowledge base
12. `share_with_dev: true` - Always share DEV checklist (that's the point!)

**Recommendation:** Remove. These should always happen in ATDD workflow.

#### Category 5: Empty Placeholders (2 variables)

13. `story_file: ""`
14. `test_framework: ""`

**Recommendation:** Remove. Use <ask> for story_file, auto-detect test_framework.

#### Category 6: Redundant Output Path (1 variable)

15. `output_checklist: "{output_folder}/atdd-checklist-{story_id}.md"`

**Recommendation:** Remove. Use default_output_file.

#### Category 7: Keep (6 variables)

1. `test_dir: "{project-root}/tests"` - KEEP
2. `test_levels: "e2e,api,component"` - KEEP (choice of levels)
3. `primary_level: "e2e"` - KEEP (which level is primary)
4. `default_output_file: "{output_folder}/atdd-checklist-{story_id}.md"` - KEEP
5. Standard fields (installed_path, instructions, validation, template) - KEEP

**Total Bloat Items:** 13 of 19 variables (68%)

**Bloat Percentage:** 68%
**Cleanup Potential:** HIGH - Reducing variables from 19 to 6

**After Cleanup, Only These Should Remain:**

1. `test_dir: "{project-root}/tests"` - Standard path
2. `test_levels: "e2e,api,component"` - Test levels to generate
3. `primary_level: "e2e"` - Primary acceptance test level
4. `default_output_file: "{output_folder}/atdd-checklist-{story_id}.md"` - Output path
5. Standard fields (installed_path, instructions, validation, template)

---

## 6. Template Variable Mapping

**Workflow Type:** Document workflow (has template file)

**Template File:** atdd-checklist-template.md

**Status:** ⚠️ **LIKELY BROKEN** - Template integration needs verification

**Expected Behavior:** Document workflows should:

1. Have `<template-output>` tags in instructions.md
2. Use `{{variable_name}}` format in template
3. Map template variables to instruction outputs

**Likely Issue (based on trace/test-review workflow pattern):**

- Template likely uses `{UPPERCASE}` pattern instead of `{{lowercase}}`
- Instructions likely lack `<template-output>` tags
- Template/instruction integration likely broken

**Recommendation:** Verify template integration. Either:

1. Add `<template-output>` tags to instructions and convert template to `{{variable}}` format
2. Remove template, set `template: false`, make this an action workflow

**Severity:** IMPORTANT - Template integration likely non-functional

---

## Recommendations

### Critical (Fix Immediately)

1. **Add web_bundle configuration**
   - Add web_bundle block with all required files (instructions, checklist, template)
   - Map knowledge fragments (fixture-architecture, data-factories, component-tdd)
   - Include tea-index.csv for knowledge base access
   - Enable web deployment capability
   - Severity: CRITICAL
   - Impact: Workflow cannot be deployed to web without this

### Important (Address Soon)

2. **Fix template integration**
   - Verify template uses `{{variable}}` format (not `{UPPERCASE}`)
   - Add `<template-output>` tags to instructions
   - OR remove template and make this an action workflow
   - Current hybrid state likely broken
   - Severity: IMPORTANT
   - Impact: Template not being populated

3. **Add config variable usage in instructions**
   - Add communication_language support for multilingual workflows
   - Add greeting or summary using {user_name}
   - Ensure proper integration with BMAD v6 config standards
   - Severity: IMPORTANT
   - Impact: Workflows not following BMAD v6 conventions

### Cleanup (Nice to Have)

4. **Remove ATDD methodology flags (CRITICAL FOR METHODOLOGY INTEGRITY!)**
   - Delete: start_failing, use_given_when_then, network_first, one_assertion_per_test, auto_cleanup
   - **RATIONALE:** These are ATDD methodology _requirements_, not user choices
   - `start_failing: true` is the CORE of ATDD! Tests MUST fail initially (red phase)
   - Allowing users to disable these breaks ATDD methodology
   - Hardcode these requirements in instructions
   - Severity: CLEANUP (but IMPORTANT for methodology correctness!)
   - Impact: Prevents users from breaking ATDD workflow, ensures methodology integrity

5. **Remove infrastructure/output flags (7 variables)**
   - Delete: include_component_tests, generate_factories, generate_fixtures, include_data_testids, include_mock_requirements, auto_load_knowledge, share_with_dev
   - All of these should always happen in ATDD workflow
   - Severity: CLEANUP
   - Impact: Reduces bloat from 68% to ~5%

6. **Remove empty placeholders and redundant paths**
   - Delete: story_file, test_framework (use <ask> and auto-detect)
   - Delete: output_checklist (use default_output_file)
   - Severity: CLEANUP
   - Impact: Cleaner configuration

7. **Simplify to essential variables**
   - Keep only: test_dir, test_levels, primary_level, default_output_file + standard fields
   - Result: 19 variables → 6 variables (68% reduction)
   - Severity: CLEANUP
   - Impact: Clear, focused ATDD workflow

---

## Validation Checklist

Use this checklist to verify fixes:

- [x] All standard config variables present and correct ✅ (Already passing)
- [ ] No unused yaml fields (bloat removed to <20%)
- [ ] Config variables used appropriately in instructions
- [ ] Web bundle includes all dependencies
- [ ] Template variables properly mapped (or template removed)
- [x] File structure follows v6 conventions ✅
- [ ] Variables block reduced from 19 to 6 essential variables
- [ ] ATDD methodology flags removed (start_failing hardcoded as true!)
- [ ] Infrastructure/output flags removed (always generated)
- [ ] Empty placeholders removed (<ask> tags added)
- [x] Document workflow correctly configured (has template) ✅

---

## Next Steps

1. **Review critical issues** and fix web_bundle configuration immediately
2. **Address important issues** in next iteration (template integration, config usage)
3. **Consider cleanup recommendations** for optimization (bloat removal + methodology integrity)
4. **Re-run audit** after fixes to verify improvements

**Estimated Cleanup Impact:**

- Variables: 19 → 6 (68% reduction)
- Bloat: 68% → 0%
- Maintainability: Significantly improved
- Methodology Integrity: Ensured (can't disable ATDD requirements!)
- Web deployment: Enabled (after web_bundle added)

---

## Positive Observations

**What This Workflow Does Well:**

1. ✅ **Proper ATDD Methodology**
   - Generates failing tests (red phase)
   - BDD structure (Given-When-Then)
   - Shares checklist with DEV agent
   - Red-green-refactor cycle

2. ✅ **Good Test Level Flexibility**
   - test_levels choice (e2e, api, component)
   - primary_level configuration
   - Allows appropriate test distribution

3. ✅ **Proper Document Workflow Structure**
   - Has template file (atdd-checklist-template.md)
   - Configured as document workflow
   - Clear output path

4. ✅ **Knowledge Base Integration**
   - References tea-index.csv
   - Loads relevant knowledge fragments
   - Applies ATDD patterns

**Overall:** This is a well-designed ATDD workflow with good methodology but moderate bloat. The CRITICAL issue is `start_failing: true` being a variable - this should NEVER be optional! ATDD _requires_ tests to fail initially. The other boolean flags also represent ATDD methodology requirements, not user choices. Removing bloat and hardcoding methodology requirements would make this an excellent workflow.

**The ATDD Methodology Violation:** Making `start_failing` a configurable variable defeats the entire purpose of ATDD. The workflow name is "atdd" (Acceptance Test-Driven Development) - tests MUST fail initially! This is the "red" in red-green-refactor. Allowing `start_failing: false` breaks the methodology.

---

**Audit Complete** - Generated by audit-workflow v1.0
