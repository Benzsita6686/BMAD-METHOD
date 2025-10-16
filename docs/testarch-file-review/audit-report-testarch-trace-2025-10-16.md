# Workflow Audit Report

**Workflow:** testarch-trace
**Audit Date:** 2025-10-16
**Auditor:** Audit Workflow (BMAD v6)
**Workflow Type:** Document workflow (template-based)
**Workflow Path:** `/Users/brianmadison/dev/BMAD-METHOD/src/modules/bmm/workflows/testarch/trace`

---

## Executive Summary

**Overall Status:** ⚠️ CONCERNS - Significant bloat detected, missing web_bundle configuration

- Critical Issues: 1
- Important Issues: 2
- Cleanup Recommendations: 8

**Key Findings:**

1. ✅ Standard config block is correctly configured
2. ⚠️ Massive bloat detected - 52 variables defined in workflow.yaml with extensive unused fields
3. ❌ No web_bundle configuration (critical for web deployment)
4. ⚠️ Config variable usage needs improvement in instructions

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

**Total YAML fields analyzed:** 52 variables defined in workflow.yaml (excluding standard config block and metadata)

**Categorization:**

#### INSTRUCTION_USED (Variables referenced in instructions.md):

The following variables appear to be contextually referenced in the instructions but NOT with explicit {variable_name} syntax:

- story_file (mentioned conceptually in "Read story file")
- test_dir (mentioned as test directory)
- source_dir (mentioned as source directory)
- coverage_levels (conceptually referenced in coverage analysis)
- output_file (used in deliverables)
- gate_type (used in Phase 2)
- decision_mode (used in decision rules)
- test_results (required for Phase 2)
- nfr_file (optional NFR loading)
- Various threshold values (min_p0_coverage, etc.) used in decision rules

#### TEMPLATE_USED (Variables referenced in trace-template.md):

Template uses these placeholder patterns:

- {STORY_ID}, {STORY_TITLE}, {DATE}
- {P0_TOTAL}, {P0_FULL}, {P0_PCT}, {P0_STATUS}
- {P1_TOTAL}, {P1_FULL}, {P1_PCT}, {P1_STATUS}
- {P2_TOTAL}, {P2_FULL}, {P2_PCT}, {P2_STATUS}
- {P3_TOTAL}, {P3_FULL}, {P3_PCT}, {P3_STATUS}
- {TOTAL}, {FULL}, {PCT}, {STATUS}
- {CRITERION_ID}, {CRITERION_DESCRIPTION}, {PRIORITY}
- {COVERAGE_STATUS}, {STATUS_ICON}
- {TEST_ID}, {TEST_FILE}, {LINE}
- {GIVEN}, {WHEN}, {THEN}
- Many more...

**CRITICAL ISSUE:** Template uses curly braces `{VARIABLE}` instead of double curly braces `{{variable}}`. This is inconsistent with BMAD v6 template variable standards which use `{{variable_name}}` notation.

#### UNUSED_BLOAT (Variables defined but NOT explicitly used in instructions or template):

This is where MASSIVE bloat exists. The workflow.yaml contains 52 variables, most of which are:

1. Boolean configuration flags (auto_discover_tests, map_by_test_id, require_explicit_mapping, etc.)
2. Threshold numbers (min_p0_coverage, min_p1_pass_rate, etc.)
3. Path specifications that should be derived, not pre-configured

**Bloat Items:**

- acceptance_criteria (empty string, should be elicited)
- auto_discover_tests, map_by_test_id, map_by_describe, map_by_filename (behavior flags that should be workflow defaults, not variables)
- require_explicit_mapping, flag_unit_only, flag_integration_only, flag_partial_coverage (all behavior configuration)
- prioritize_by_risk, suggest_missing_tests, check_duplicate_coverage (more behavior flags)
- use_test_design, use_tech_spec, use_prd (file loading flags - should be automatic)
- generate_gate_yaml, generate_coverage_badge, update_story_file (output control flags)
- min_p0_coverage, min_p1_coverage, min_overall_coverage (threshold config)
- auto_load_knowledge, include_code_coverage, check_assertions (more behavior flags)
- enable_gate_decision (Phase 2 control flag)
- allow_waivers, require_evidence, check_all_workflows_complete, validate_evidence_freshness, require_sign_off (gate configuration)
- min_p0_pass_rate, min_p1_pass_rate, min_overall_pass_rate (more thresholds)
- max_critical_nfrs_fail, max_security_issues (threshold config)
- allow_p2_failures, allow_p3_failures, escalate_p1_failures (risk tolerance flags)
- gate_output_file, append_to_history, notify_stakeholders (output configuration)

**Total Variables Analyzed:** 52 variables
**Used in Instructions (explicitly):** ~10-15 conceptually (but not with {variable_name} syntax)
**Used in Template:** 0 (template uses {UPPERCASE} pattern, not {{yaml_variables}})
**Unused (Bloat):** ~35-40 variables (75%+ of defined variables!)

**Status:** ❌ **FAIL** - Severe bloat detected (75%+ unused variables)

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

- ⚠️ **PARTIAL** - Output folder is referenced in workflow.yaml (`output_file: "{output_folder}/..."`), but instructions don't explicitly use {output_folder} variable pattern
- File writes mention saving to output_folder conceptually
- Severity: MINOR

### Date Usage Check:

- ✅ **USED** - Date is available in workflow.yaml and used in template as {DATE}
- Severity: N/A

**Status:** ⚠️ **IMPORTANT** - Config variables not properly utilized in instructions

---

## 4. Web Bundle Validation

### Web Bundle Present: ❌ **NO**

**Status:** ❌ **CRITICAL** - No web_bundle configuration found

The workflow.yaml contains:

```yaml
web_bundle: false
```

This means the workflow is **NOT** configured for web deployment. For a production workflow, this is a critical omission.

**Missing web_bundle requirements:**

- No web_bundle_files list
- No existing_workflows mapping (critical since instructions reference knowledge fragments and other workflows)
- No web deployment path configuration

**Workflow Dependencies Detected in Instructions:**
The instructions reference loading knowledge fragments:

- `test-priorities-matrix.md`
- `risk-governance.md`
- `probability-impact.md`
- `test-quality.md`
- `selective-testing.md`

These fragments should be declared in a web_bundle configuration.

**Impact:** This workflow cannot be bundled for web deployment without web_bundle configuration.

**Severity:** CRITICAL (if web deployment is intended)

---

## 5. Bloat Detection

### Unused YAML Fields Analysis

**Total YAML fields:** 52 variables (excluding standard config and metadata)
**Used fields:** ~15 (estimated, mostly conceptual usage, not explicit {variable} references)
**Unused fields:** ~37
**Bloat percentage:** **71%**

### Bloat Categories:

#### Category 1: Boolean Behavior Flags (Should be workflow defaults, not variables)

These should be removed from variables and implemented as hardcoded workflow behavior:

1. `auto_discover_tests: true` - This should be the default workflow behavior
2. `map_by_test_id: true` - Mapping strategy should be built into the workflow
3. `map_by_describe: true` - Same as above
4. `map_by_filename: true` - Same as above
5. `require_explicit_mapping: true` - Quality standard, not a variable
6. `flag_unit_only: true` - Analysis behavior, not configurable
7. `flag_integration_only: true` - Same
8. `flag_partial_coverage: true` - Same
9. `prioritize_by_risk: true` - Core workflow principle
10. `suggest_missing_tests: true` - Core workflow output
11. `check_duplicate_coverage: true` - Quality check, not configurable
12. `use_test_design: true` - Should auto-detect file existence
13. `use_tech_spec: true` - Same
14. `use_prd: true` - Same
15. `generate_gate_yaml: true` - Core workflow output
16. `generate_coverage_badge: true` - Optional output, could be removed
17. `update_story_file: true` - Optional behavior, could be removed
18. `auto_load_knowledge: true` - Should be default behavior
19. `include_code_coverage: false` - Optional feature flag, acceptable to keep
20. `check_assertions: true` - Quality check, not configurable
21. `enable_gate_decision: true` - Workflow mode, could be simplified
22. `allow_waivers: true` - Gate policy, could be hardcoded or project-level config
23. `require_evidence: true` - Gate policy, should be default
24. `check_all_workflows_complete: true` - Validation behavior
25. `validate_evidence_freshness: true` - Quality check
26. `require_sign_off: false` - Optional gate requirement
27. `allow_p2_failures: true` - Risk policy
28. `allow_p3_failures: true` - Risk policy
29. `escalate_p1_failures: true` - Risk policy
30. `append_to_history: true` - Output behavior
31. `notify_stakeholders: true` - Output behavior

**Recommendation:** Remove 25+ boolean flags. These should be workflow defaults or auto-detected behaviors, not user-configurable variables.

#### Category 2: Threshold Configuration (Should be project-level config, not workflow variables)

These belong in module config.yaml or project-specific configuration, not workflow.yaml:

1. `min_p0_coverage: 100`
2. `min_p1_coverage: 90`
3. `min_overall_coverage: 80`
4. `min_p0_pass_rate: 100`
5. `min_p1_pass_rate: 95`
6. `min_overall_pass_rate: 90`
7. `max_critical_nfrs_fail: 0`
8. `max_security_issues: 0`

**Recommendation:** Move thresholds to bmm/config.yaml as project-wide quality standards. Reference via {config_source}:threshold_name pattern.

#### Category 3: Empty/Placeholder Variables (Should be elicited, not pre-declared)

1. `story_file: ""` - Should be elicited with <ask> tag
2. `acceptance_criteria: ""` - Same
3. `nfr_file: ""` - Optional, should be elicited if needed
4. `test_results: ""` - Should be elicited for Phase 2

**Recommendation:** Remove empty variables. Use <ask> tags in instructions to elicit required inputs.

#### Category 4: Derived/Output Paths (Should be calculated in workflow, not pre-defined)

1. `test_dir: "{project-root}/tests"` - Should be auto-detected or elicited
2. `source_dir: "{project-root}/src"` - Same
3. `output_file: "{output_folder}/traceability-matrix.md"` - Duplicates default_output_file
4. `gate_output_file: "{output_folder}/gate-decision-{gate_type}-{story_id}{epic_num}{release_version}.md"` - Complex derivation

**Recommendation:** Remove redundant path variables. Use default_output_file or calculate paths in instructions.

#### Category 5: Acceptable Variables (Keep these)

These variables have legitimate workflow-specific purpose:

1. `coverage_levels: "e2e,api,component,unit"` - Customizable test level scope
2. `gate_type: "story"` - Determines gate scope (story/epic/release/hotfix)
3. `decision_mode: "deterministic"` - Affects decision process
4. `installed_path`, `instructions`, `validation`, `template` - Standard workflow paths (KEEP)

**Total Bloat Items:** 35+ variables that should be removed or relocated

**Bloat Percentage:** 71%
**Cleanup Potential:** HIGH - Removing bloat would reduce workflow.yaml from 146 lines to ~30-40 lines

---

## 6. Template Variable Mapping

### Template Variables Analysis

**Template Variable Pattern:** Template uses `{UPPERCASE_VARIABLE}` format (e.g., {STORY_ID}, {P0_TOTAL})

**Critical Issue:** ❌ BMAD v6 standard uses `{{lowercase_variable}}` format for template variables.

The trace-template.md uses 60+ template placeholders with `{UPPERCASE}` pattern instead of `{{variable_name}}` pattern.

### Cross-Reference with Instructions

**Instructions <template-output> tags:** None found in instructions.md

**Critical Problem:** The instructions.md file does NOT contain `<template-output>` tags to map workflow outputs to template variables.

**Workflow Type:** Document workflow (has template: "{installed_path}/trace-template.md")

**Expected Behavior:** Document workflows should have <template-output> tags in instructions to populate template sections.

**Actual Behavior:** Instructions are written as pure procedural markdown without template-output integration.

**Impact:** This workflow appears to be a HYBRID workflow (action + document) but is configured as a document workflow. The template exists but isn't integrated with the workflow execution.

**Recommendation:**

1. Either remove the template and set `template: false` (make it an action workflow)
2. Or refactor instructions to use <template-output> tags and update template to use {{variable}} pattern

**Status:** ❌ **IMPORTANT** - Template/instruction integration broken

---

## Recommendations

### Critical (Fix Immediately)

1. **Add web_bundle configuration**
   - Add web_bundle block with all required files (instructions, template, checklist)
   - Map knowledge fragments (test-priorities-matrix.md, risk-governance.md, etc.)
   - Enable web deployment capability
   - Severity: CRITICAL
   - Impact: Workflow cannot be deployed to web without this

### Important (Address Soon)

2. **Fix template integration**
   - Either: Remove template, set `template: false`, make this an action workflow
   - Or: Add <template-output> tags to instructions and convert template to {{variable}} format
   - Current hybrid state is broken
   - Severity: IMPORTANT
   - Impact: Template is not being populated by workflow execution

3. **Add config variable usage in instructions**
   - Add communication_language support for multilingual workflows
   - Consider adding user_name for personalization (optional)
   - Ensure output_folder is explicitly used in all file write operations
   - Severity: IMPORTANT
   - Impact: Workflows not following BMAD v6 config standards

### Cleanup (Nice to Have)

4. **Remove boolean behavior flags (25+ variables)**
   - Delete: auto_discover_tests, map_by_test_id, require_explicit_mapping, flag_unit_only, prioritize_by_risk, suggest_missing_tests, check_duplicate_coverage, use_test_design, generate_gate_yaml, auto_load_knowledge, check_assertions, enable_gate_decision, require_evidence, check_all_workflows_complete, allow_p2_failures, escalate_p1_failures, append_to_history, notify_stakeholders, etc.
   - These should be workflow defaults, not user-configurable variables
   - Severity: CLEANUP
   - Impact: Reduces bloat from 71% to ~15%, improves maintainability

5. **Move threshold configuration to module config**
   - Move to bmm/config.yaml: min_p0_coverage, min_p1_coverage, min_overall_coverage, min_p0_pass_rate, min_p1_pass_rate, min_overall_pass_rate, max_critical_nfrs_fail, max_security_issues
   - Reference via {config_source}:threshold_name
   - Severity: CLEANUP
   - Impact: Centralizes quality standards, enables project-wide consistency

6. **Remove empty placeholder variables**
   - Delete: story_file: "", acceptance_criteria: "", nfr_file: "", test_results: ""
   - Use <ask> tags in instructions to elicit these inputs
   - Severity: CLEANUP
   - Impact: Cleaner workflow.yaml, more dynamic input gathering

7. **Remove redundant path variables**
   - Delete: output_file (duplicates default_output_file)
   - Auto-detect or elicit: test_dir, source_dir
   - Simplify: gate_output_file (calculate in instructions)
   - Severity: CLEANUP
   - Impact: Reduces path configuration redundancy

8. **Simplify variables block**
   - Keep only: coverage_levels, gate_type, decision_mode, include_code_coverage (optional feature flag)
   - Remove everything else per recommendations 4-7
   - Severity: CLEANUP
   - Impact: Workflow.yaml drops from 146 lines to ~35 lines (76% reduction)

9. **Fix template variable naming convention**
   - Convert {UPPERCASE} to {{lowercase_variable}} throughout trace-template.md
   - Align with BMAD v6 standards
   - Severity: CLEANUP
   - Impact: Template consistency with framework standards

10. **Add <ask> tags for required inputs**
    - Add <ask> for story_file path at start of workflow
    - Add <ask> for test_results path before Phase 2
    - Add conditional <ask> for nfr_file if release-level gate
    - Severity: CLEANUP
    - Impact: Makes workflow properly interactive

11. **Document workflow mode decision**
    - Decide: Is this an action workflow or document workflow?
    - If action: Remove template, set template: false
    - If document: Add <template-output> tags and integrate template properly
    - Severity: CLEANUP
    - Impact: Clarifies workflow purpose and execution model

---

## Validation Checklist

Use this checklist to verify fixes:

- [ ] All standard config variables present and correct ✅ (Already passing)
- [ ] No unused yaml fields (bloat removed to <15%)
- [ ] Config variables used appropriately in instructions
- [ ] Web bundle includes all dependencies
- [ ] Template variables properly mapped (or template removed)
- [ ] File structure follows v6 conventions
- [ ] Variables block reduced from 52 to ~4-5 essential variables
- [ ] Threshold config moved to bmm/config.yaml
- [ ] Boolean flags removed (workflow defaults)
- [ ] Empty placeholders removed (<ask> tags added)

---

## Next Steps

1. **Review critical issues** and fix web_bundle configuration immediately
2. **Address important issues** in next iteration (template integration, config usage)
3. **Consider cleanup recommendations** for optimization (bloat removal)
4. **Re-run audit** after fixes to verify improvements

**Estimated Cleanup Impact:**

- Workflow.yaml: 146 lines → ~35 lines (76% reduction)
- Bloat: 71% → <15%
- Maintainability: Significantly improved
- Web deployment: Enabled (after web_bundle added)

---

**Audit Complete** - Generated by audit-workflow v1.0
