# 📋 Guardian Pro - File Update Workflow

## Overview
This document provides a clear, step-by-step workflow for updating project documentation files during development sessions.

---

## 📁 File Responsibilities

### **SESSION_LOG.md** 📚
- **Purpose**: Complete project history
- **Updates**: Once per session (at end)
- **Content**: Session summaries, overall progress
- **Owner**: Session closer

### **CURRENT_CONTEXT.md** ⚡
- **Purpose**: Real-time session tracking  
- **Updates**: Session start + every 15 minutes + session end
- **Content**: Current work, decisions, progress
- **Owner**: Active developer

### **SESSION_X_HANDOFF.md** 🔄
- **Purpose**: Session-to-session continuity
- **Updates**: Once per session (create new at end)
- **Content**: Next session prep, context, blockers
- **Owner**: Session closer

### **COMPONENT_REGISTRY.md** 📊
- **Purpose**: Component status tracking
- **Updates**: When component status changes
- **Content**: Progress percentages, completion status
- **Owner**: Component implementer

---

## 🚀 Session Start Workflow (5 minutes)

### **Step 1: Load Previous Context**
```bash
# Read the handoff from previous session
cat docs/SESSION_[PREVIOUS_NUMBER]_HANDOFF.md

# Quick check project status
cat docs/COMPONENT_REGISTRY.md | grep "Session [CURRENT_NUMBER]"
```

### **Step 2: Initialize Current Session**
```bash
# Update current context file
nano docs/CURRENT_CONTEXT.md
```

**Template for CURRENT_CONTEXT.md start:**
```markdown
# Session [NUMBER] - Current Development Context

## Session Info
- **Session**: [NUMBER]
- **Date**: [TODAY'S_DATE]
- **Primary Focus**: [FROM_HANDOFF]
- **Token Usage**: 0% (starting)
- **Status**: IN_PROGRESS

## Session Objectives (from handoff)
- [ ] [Objective 1 from handoff]
- [ ] [Objective 2 from handoff]
- [ ] [Objective 3 from handoff]

## Dependencies Verified
- [x] [Dependency 1] available
- [ ] [Dependency 2] needs check

## Starting Point
- Files to modify: [list from handoff]
- Architecture context: [key points from handoff]

## Progress Log
- [TIME]: Session started, objectives set
```

### **✅ Session Start Checklist:**
- [ ] Read previous handoff document
- [ ] Update CURRENT_CONTEXT.md with today's objectives  
- [ ] Verify dependencies from handoff
- [ ] Confirm session focus area

---

## 🔧 During Session Workflow (every 15 minutes)

### **Update Only: CURRENT_CONTEXT.md**

**Every 15-minute update:**
```bash
nano docs/CURRENT_CONTEXT.md
```

**Add to Progress Log section:**
```markdown
## Progress Log
- [TIME]: Session started, objectives set
- [TIME]: [What was completed in last 15 min]
- [TIME]: [Current task, % complete]
- [TIME]: [Decision made: description]
- [TIME]: [Blocker encountered: description]
```

**Update Session Info:**
```markdown
## Session Info
- **Token Usage**: ~25% / ~50% / ~75% (estimate)
- **Status**: IN_PROGRESS
```

**Update Objectives:**
```markdown
## Session Objectives
- [x] [Completed objective]
- [ ] [Current objective - 60% complete]  
- [ ] [Pending objective]
```

### **⚡ During Session Rules:**
- **Only update CURRENT_CONTEXT.md**
- **Don't touch other files** during active development
- **Update every 15 minutes** or at major milestones
- **Note all decisions and blockers immediately**

---

## 📝 Session End Workflow (10 minutes)

### **Step 1: Final CURRENT_CONTEXT.md Update** (2 minutes)
```bash
nano docs/CURRENT_CONTEXT.md
```

**Mark session complete:**
```markdown
## Session Info
- **Token Usage**: ~75% (session complete)
- **Status**: ✅ COMPLETED

## Final Results
- [x] [All completed objectives]
- [ ] [Deferred objectives with reasons]

## Session Summary
- **Completed**: [Major achievements]
- **Decisions**: [Key decisions made]
- **Blockers**: [Any issues for next session]
- **Files Modified**: [List of changed files]
```

### **Step 2: Update SESSION_LOG.md** (3 minutes)
```bash
nano docs/SESSION_LOG.md
```

**Add new session entry:**
```markdown
## Session [NUMBER]: [FOCUS_AREA]
**Date**: [DATE]
**Duration**: [ACTUAL_TIME]
**Token Usage**: ~[PERCENTAGE]%
**Status**: ✅ COMPLETED

### 🎯 Objectives
- [x] [Completed objective 1]
- [x] [Completed objective 2] 
- [ ] [Deferred objective - reason]

### ✅ Completed
- [x] **[Component A]**: [Description and status]
- [x] **[Component B]**: [Description and status]

### 📦 Artifacts Created
1. `[artifact_name]` - [Description]
2. `[file_path]` - [Description]

### 🔄 In Progress  
- [Component C]: [Current status, next steps]

### 📝 Key Decisions
1. **[Decision 1]**: [Rationale and impact]
2. **[Decision 2]**: [Rationale and impact]

### 🔗 Integration Points Established
- [Interface A] ↔ [Component B]: [Status]
- [Interface C] ↔ [Component D]: [Status]

### 📊 Session Metrics
- **Components Completed**: [Number]
- **Tests Written**: [Number]
- **Integration Points**: [Number]
- **Documentation Updated**: [Files]
```

### **Step 3: Update COMPONENT_REGISTRY.md** (2 minutes)
```bash
nano docs/COMPONENT_REGISTRY.md
```

**Update component statuses:**
```markdown
| Component | Status | Session | Progress | Dependencies | Next Steps |
|-----------|--------|---------|----------|--------------|------------|
| **Event Bus** | 🟢 COMPLETE | 2 | 100% | None | Integration testing |
| **Config System** | 🟡 IN_PROGRESS | 2 | 70% | Event Bus | Validation logic |
```

**Update overall statistics:**
```markdown
### By Status
- 🟢 **COMPLETE**: [NUMBER] components ([PERCENTAGE]%)
- 🟡 **IN_PROGRESS**: [NUMBER] components ([PERCENTAGE]%)
- 🔴 **NOT_STARTED**: [NUMBER] components ([PERCENTAGE]%)
```

### **Step 4: Create Next Session Handoff** (3 minutes)
```bash
nano docs/SESSION_[NEXT_NUMBER]_HANDOFF.md
```

**Use this template:**
```markdown
# Session [CURRENT] → Session [NEXT] Handoff

## Completion Summary
### ✅ Completed This Session
- [x] [Component A]: [Status and location]
- [x] [Task B]: [Results and notes]

### 🟡 Partially Completed
- [ ] [Component C]: [XX% complete, next steps]

### ⏳ Deferred to Next Session
- [ ] [Task D]: [Reason for deferral]

## Session [NEXT] Objectives
### Primary Focus: [NEXT_COMPONENT/AREA]
1. **HIGH**: [Specific task with clear success criteria]
2. **MEDIUM**: [Secondary task]
3. **LOW**: [Nice-to-have task]

### Success Criteria for Session [NEXT]
- [ ] [Measurable outcome 1]
- [ ] [Measurable outcome 2]
- [ ] [Integration point working]

## Context for Next Session
### Files Ready for Development
- `path/to/file1.py`: [Current state, what needs to be done]
- `path/to/file2.py`: [Dependencies, next steps]

### Architecture Context
- **Key Decision**: [Decision and impact on next session]
- **Integration Points**: [What's ready, what needs work]
- **Constraints**: [Any limitations for next session]

### Environment Setup Required
- [ ] [Dependency 1]: [Installation/setup instructions]
- [ ] [Dependency 2]: [Configuration needed]

## Dependencies & Blockers
### Prerequisites for Session [NEXT]
- [x] [Completed prerequisite]
- [ ] [Outstanding prerequisite]: [How to resolve]

### Known Blockers
- **Blocker 1**: [Description, impact, resolution plan]
- **Research Needed**: [Specific questions to investigate]

## Implementation Plan (45 minutes)
### Recommended Approach
1. **Setup & Review** (5 min): [Specific setup tasks]
2. **Core Implementation** (30 min): [Main development work]
3. **Integration & Testing** (10 min): [Connection and validation]

### Files to Create/Modify
- `new_file.py`: [Purpose and main functions]
- `existing_file.py`: [Modifications needed]
- `test_file.py`: [Testing requirements]

### Testing Strategy
- **Unit Tests**: [What to test]
- **Integration Tests**: [Integration points to validate]
- **Manual Testing**: [User scenarios to verify]

## Quality Checklist for Session [NEXT]
- [ ] All code has type hints and docstrings
- [ ] Unit tests written and passing
- [ ] Integration points documented and working
- [ ] COMPONENT_REGISTRY.md updated
- [ ] SESSION_[NEXT+1]_HANDOFF.md created

---
**Handoff Created**: [TIMESTAMP]
**Next Session Ready**: ✅ YES
**Estimated Duration**: [TIME_ESTIMATE]
```

### **✅ Session End Checklist:**
- [ ] CURRENT_CONTEXT.md marked as COMPLETED
- [ ] SESSION_LOG.md updated with session summary
- [ ] COMPONENT_REGISTRY.md updated with new statuses
- [ ] SESSION_[NEXT]_HANDOFF.md created with clear objectives
- [ ] All modified files committed/saved
- [ ] No unresolved architectural decisions
- [ ] Next session has actionable objectives

---

## 🚨 Emergency Session End (if time/tokens run out)

### **Quick Emergency Update** (2 minutes)
```bash
# Minimal handoff if session must end abruptly
echo "🚨 EMERGENCY SESSION END

Session [NUMBER] Status:
- Completed: [What was finished]
- In Progress: [Current state of work]
- Files Modified: [List files and current state]
- Next Immediate: [Critical next step]
- Full handoff needed: Create proper handoff next session" > docs/EMERGENCY_SESSION_[NUMBER]_STATUS.md
```

### **Session Recovery** (next session start)
1. Read emergency status file
2. Check file modification timestamps
3. Validate current component states
4. Create proper handoff document
5. Continue with normal workflow

---

## 📊 File Update Matrix

| **File** | **Session Start** | **Every 15 Min** | **Session End** | **Owner** |
|----------|-------------------|-------------------|-----------------|-----------|
| **SESSION_LOG.md** | ❌ Read only | ❌ Read only | ✅ Add session summary | Session closer |
| **CURRENT_CONTEXT.md** | ✅ Set objectives | ✅ Update progress | ✅ Mark complete | Active developer |
| **SESSION_X_HANDOFF.md** | ✅ Read previous | ❌ No updates | ✅ Create new | Session closer |
| **COMPONENT_REGISTRY.md** | ❌ Reference only | ✅ If status changes | ✅ Final status update | Component implementer |
| **INTEGRATION_MAP.md** | ❌ Reference only | ✅ If interfaces change | ✅ Update if modified | Architecture owner |

---

## 💡 Best Practices

### **Do's ✅**
- **Update CURRENT_CONTEXT.md frequently** - it's your session workspace
- **Be specific in handoffs** - assume different person will continue
- **Document decisions immediately** - don't wait until session end
- **Test integration points before marking complete**
- **Create measurable success criteria** for next session

### **Don'ts ❌**
- **Don't update multiple files simultaneously** during development
- **Don't leave broken code** without clear notes in handoff
- **Don't skip the 15-minute context updates**
- **Don't create vague handoff objectives** like "continue working on X"
- **Don't end session without updating component registry**

### **Emergency Protocols**
- **80% token usage = start session closure**
- **Unexpected interruption = create emergency status file**
- **Broken integration = document in handoff with resolution plan**
- **Unclear next steps = extend current session or create research task**

---

## 🔄 Common Scenarios

### **Scenario 1: Component Completed Mid-Session**
```bash
# Immediately update
nano docs/COMPONENT_REGISTRY.md
# Change: Component X: NOT_STARTED → COMPLETE

# Note in current context
nano docs/CURRENT_CONTEXT.md
# Add: "Component X completed at [TIME]"
```

### **Scenario 2: Major Architecture Decision**
```bash
# Document immediately in current context
nano docs/CURRENT_CONTEXT.md
# Add detailed decision rationale and impact

# If affects integration, update map
nano docs/INTEGRATION_MAP.md
# Add/modify interface specifications
```

### **Scenario 3: Unexpected Blocker**
```bash
# Note in current context immediately
nano docs/CURRENT_CONTEXT.md
# Add: "Blocker: [description] - Resolution plan: [plan]"

# Include in session handoff
# Ensure next session has clear resolution steps
```

---

**File Location**: `~/guardian-pro/docs/FILE_UPDATE_WORKFLOW.md`
**Usage**: Reference before every session start and end
**Maintenance**: Update based on experience and process improvements

---

*This workflow ensures consistent, professional documentation updates across all development sessions with no information loss.*
