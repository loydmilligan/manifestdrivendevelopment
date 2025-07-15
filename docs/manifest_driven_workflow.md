# Manifest-Driven Development Workflow

## Purpose & Benefits

**Manifest-driven development** is a systematic approach to software development that maintains architectural integrity through explicit state tracking and validation. It prevents the common AI development problems of scope creep, forgotten changes, and inconsistent implementations.

### Core Problems Solved
- **Baseline Drift**: AI agents generating fresh manifests instead of loading existing state
- **Validation Gaps**: No systematic comparison between planned vs. actual implementation
- **Context Bloat**: Accumulated conversation context reducing AI effectiveness
- **Architectural Inconsistency**: Changes made without considering overall system design

### Key Benefits
- **Reliable Baselines**: Always know the true current state of your codebase
- **Three-Way Validation**: Compare baseline → expected → actual for comprehensive validation
- **Context Efficiency**: Compact context while preserving essential development state
- **Architectural Governance**: Track and validate architectural evolution over time

## Core Concepts

### **Baseline Manifest** (`codebase_manifest.json`)
The single source of truth for current codebase state. Contains:
- File purposes, exports, imports, side effects
- Dependencies and architecture
- Current development status
- **Critical**: Only updated by `commit_task` command

### **Three-Way Validation**
1. **Baseline → Expected**: Is the plan logical from current state?
2. **Expected → Actual**: Was the plan implemented correctly?
3. **Baseline → Actual**: Are there unintended changes or regressions?

### **Task Structure** (`tasks/tasks.json`)
Structured task definitions with dependencies, acceptance criteria, and file specifications.

### **Context Optimization**
Systematic context compacting that preserves essential project state while eliminating conversation bloat.

## File Structure

```
project/
├── codebase_manifest.json          # Current baseline (only updated by commit_task)
├── tasks/
│   ├── tasks.json                  # Structured task definitions
│   ├── prepared/                   # Generated task preparation files
│   ├── validation/                 # Validation reports
│   ├── completed/                  # Archived completed tasks
│   └── resolutions/                # Mismatch resolution records
├── docs/
│   ├── proposed_final_manifest.json # Architectural target state
│   └── manifest_evolution.md      # Architectural decision history
└── [source code]
```

## Commands Reference

### Core Development Workflow

#### `process_task "Task-X.X"`
**Purpose**: Prepare task for implementation with full context
**Input**: Task ID from tasks.json
**Output**: `tasks/prepared/Task-X.X.json` with baseline, expected manifest, implementation notes
**Critical**: MUST load existing `codebase_manifest.json` as baseline (not generate fresh)

#### `implement_task "Task-X.X"`
**Purpose**: Implement prepared task according to plan
**Input**: Task ID (reads from `tasks/prepared/Task-X.X.json`)
**Output**: Code changes, new files, updated dependencies
**Focus**: Follow expected manifest exactly, document any deviations

#### `check_task "Task-X.X"`
**Purpose**: Validate implementation against expectations
**Input**: Task ID (reads prepared file + analyzes current codebase)
**Output**: Validation report with three-way comparison
**Critical**: Generates actual manifest from codebase, compares baseline → expected → actual

#### `commit_task "Task-X.X"`
**Purpose**: Commit successful implementation and update baseline
**Input**: Task ID (requires successful validation)
**Output**: Git commit + updates `codebase_manifest.json` with actual state
**Critical**: Only command that updates the baseline manifest

#### `resolve_mismatch "Task-X.X"`
**Purpose**: Fix discrepancies between expected and actual implementation
**Input**: Task ID (requires validation report showing major issues)
**Output**: Resolution actions + updated manifests
**Interactive**: Presents resolution options for each difference found

### Context Optimization

#### `compact_after_task "Task-X.X"`
**Purpose**: Compact context after successful task completion
**Input**: Completed task ID
**Output**: Executes `claude --compact` with comprehensive project context
**Usage**: For manual context optimization

#### `commit_task_then_compact "Task-X.X"`
**Purpose**: Commit task and automatically compact context
**Input**: Task ID (requires successful validation)
**Output**: Full commit process + automatic context compact
**Recommended**: Most efficient workflow for continuous development

### Architectural Management

#### `update_final_manifest`
**Purpose**: Update architectural target based on implementation learnings
**Trigger**: After major milestones, architectural discoveries, or drift detection
**Output**: Updates `docs/proposed_final_manifest.json` + evolution log
**Usage**: Periodic architectural governance

## Usage Patterns

### Standard Workflow
```bash
claude-code process_task "Task-1.1"
claude-code implement_task "Task-1.1"
claude-code check_task "Task-1.1"
claude-code commit_task "Task-1.1"
```

### Optimized Workflow (Recommended)
```bash
claude-code process_task "Task-1.1"
claude-code implement_task "Task-1.1"
claude-code check_task "Task-1.1"
claude-code commit_task_then_compact "Task-1.1"
# New session starts with fresh context + essential state
```

### Problem Resolution Workflow
```bash
claude-code process_task "Task-1.1"
claude-code implement_task "Task-1.1"
claude-code check_task "Task-1.1"        # Returns MAJOR_ISSUES
claude-code resolve_mismatch "Task-1.1"  # Fix issues
claude-code check_task "Task-1.1"        # Re-validate
claude-code commit_task "Task-1.1"       # Finally commit
```

## Command Input/Output Validation

All V2 commands use consistent validation format:

**📋 REQUIRED INPUTS CHECK** - Visual validation of prerequisites
**📤 REQUIRED OUTPUTS VERIFICATION** - Confirmation of successful completion

### Critical Validation Points

1. **Baseline Loading**: `process_task` MUST show "Baseline loaded: LOADED FROM FILE"
2. **Three-Way Comparison**: `check_task` MUST perform all three manifest comparisons
3. **Manifest Updates**: Only `commit_task` should modify `codebase_manifest.json`
4. **Context Compact**: Only execute if all previous steps succeeded

## Common Issues & Solutions

### "Baseline loaded: GENERATED" Error
**Problem**: AI is creating new manifest instead of loading existing
**Solution**: Ensure `codebase_manifest.json` exists; if not, create minimal: `{"files": {}, "dependencies": {}}`

### Task Parsing Failures
**Problem**: AI can't find task in task list
**Solution**: Use structured `tasks/tasks.json` format instead of markdown

### Validation Mismatches
**Problem**: Expected vs actual implementation differs significantly
**Solution**: Use `resolve_mismatch` to systematically address each difference

### Context Bloat
**Problem**: Conversation context becoming too large
**Solution**: Use `compact_after_task` or `commit_task_then_compact` regularly

## Version History

**V1**: Original sub-agent orchestration approach (deprecated)
**V2**: Human-orchestrated workflow with input/output validation (current)

## Repository Recommendations

### Separate Repository Benefits
- **Version Control**: Track command evolution and improvements
- **Reusability**: Apply to multiple projects
- **Collaboration**: Share and improve methodology
- **Documentation**: Centralized reference and examples

### Suggested Repository Structure
```
manifest-driven-development/
├── commands/
│   ├── process_task.md
│   ├── implement_task.md
│   ├── check_task.md
│   ├── commit_task.md
│   ├── resolve_mismatch.md
│   ├── compact_after_task.md
│   └── commit_task_then_compact.md
├── docs/
│   ├── README.md (this file)
│   ├── getting_started.md
│   └── examples/
├── templates/
│   ├── codebase_manifest.json
│   ├── tasks.json
│   └── project_setup.md
└── CHANGELOG.md
```

## Quick Start Checklist

1. ✅ Create `codebase_manifest.json` (use template or generate with analysis prompt)
2. ✅ Create `tasks/tasks.json` with structured task definitions
3. ✅ Set up required directory structure
4. ✅ Initialize git repository
5. ✅ Start with `process_task` for first task

## Success Metrics

- **Baseline Integrity**: Manifest only updated by commit_task
- **Validation Coverage**: All implementations validated with three-way comparison
- **Context Efficiency**: Regular context compacting maintains AI effectiveness
- **Architectural Consistency**: Evolution tracked and validated over time

---

**This methodology transforms chaotic AI development into systematic, validated, architecture-preserving workflow.**