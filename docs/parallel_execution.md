# Parallel Execution and Multi-Agent Coordination in CAI

This comprehensive guide covers all aspects of parallel execution, multi-agent coordination, and concurrent workflows in the Cybersecurity AI (CAI) framework. Whether you're using the CLI or the TUI, this document provides detailed information on leveraging CAI's parallel capabilities for efficient security assessments.

---

## Table of Contents

1. [Overview](#overview)
2. [Parallelization Approaches](#parallelization-approaches)
3. [CLI Parallel Execution (CAI_PARALLEL)](#cli-parallel-execution-cai_parallel)
4. [CLI Parallel Agents (YAML Configuration)](#cli-parallel-agents-yaml-configuration)
5. [TUI Multi-Terminal Workflows](#tui-multi-terminal-workflows)
6. [Preconfigured Teams](#preconfigured-teams)
7. [Parallel Execution Patterns](#parallel-execution-patterns)
8. [Context Management Strategies](#context-management-strategies)
9. [Agentic Patterns for Coordination](#agentic-patterns-for-coordination)
10. [Queue Management](#queue-management)
11. [Terminal-Specific Commands](#terminal-specific-commands)
12. [Layout and UI Considerations](#layout-and-ui-considerations)
13. [Best Practices and Optimization](#best-practices-and-optimization)
14. [Cost Management in Parallel Workflows](#cost-management-in-parallel-workflows)
15. [Advanced Coordination Patterns](#advanced-coordination-patterns)
16. [Troubleshooting](#troubleshooting)
17. [Real-World Use Cases](#real-world-use-cases)

---

## Overview

CAI provides robust support for parallel execution and multi-agent coordination, enabling security professionals to:

- **Accelerate assessments** by running multiple agents simultaneously
- **Compare perspectives** through red team vs. blue team analysis
- **Distribute workloads** across specialized agents
- **Coordinate complex workflows** with team-based configurations
- **Optimize resources** through intelligent parallelization

### Key Concepts

**Parallel Execution**: Running multiple agent instances or terminals concurrently to process tasks simultaneously.

**Multi-Agent Coordination**: Orchestrating different specialized agents to work together on complex security workflows.

**Context Isolation**: Each parallel agent maintains its own conversation history and state, preventing context contamination.

**Team Configurations**: Predefined groups of agents optimized for specific security workflows.

---

## Parallelization Approaches

CAI offers two distinct approaches to parallelization, each suited for different use cases:

### 1. CLI-Based Parallelization (`CAI_PARALLEL`)

**Best for**:
- Automated workflows and scripting
- Batch processing of similar tasks
- CI/CD integration
- Swarm-style execution of identical agents

**Characteristics**:
- Single agent type, multiple instances
- Shared prompt, independent execution
- Results aggregated automatically
- Command-line driven

### 2. TUI-Based Multi-Terminal Parallelization

> **⚡ CAI-Pro Exclusive Feature**  
> The Terminal User Interface (TUI) is available exclusively in **CAI-Pro**. To access this feature and unlock advanced multi-agent workflows, visit [Alias Robotics](https://aliasrobotics.com/cybersecurityai.php) for more information.

**Best for**:
- Interactive security assessments
- Multi-perspective analysis
- Complex team-based workflows
- Real-time monitoring and coordination

**Characteristics**:
- Multiple agent types simultaneously
- Independent or shared context
- Visual monitoring and control
- Preconfigured team templates

---

## CLI Parallel Execution (CAI_PARALLEL)

### Environment Variable Configuration

The `CAI_PARALLEL` environment variable controls the number of parallel agent instances in CLI mode.

**Syntax**:
```bash
CAI_PARALLEL="<number>" cai
```

**Default Value**: `1` (no parallelization)

**Range**: `1` to `N` (limited by system resources and API rate limits)

### Basic Usage

#### Example 1: Parallel Vulnerability Scanning

```bash
# Run 3 instances of redteam_agent in parallel
CAI_PARALLEL="3" CAI_MODEL="alias1" cai --prompt "Scan target.com for OWASP Top 10"
```

**What happens**:
- 3 independent redteam_agent instances launch
- Each receives the same prompt
- All execute concurrently
- Results are displayed as they complete

#### Example 2: High-Throughput Bug Hunting

```bash
# Run 5 agents for rapid bug discovery
CAI_PARALLEL="5" CAI_MODEL="alias1" CAI_BRIEF="true" cai
```

**Optimization tips**:
- Use `alias1` model for parallelization
- Enable `CAI_BRIEF="true"` for concise outputs
- Set `CAI_STREAM="false"` to reduce overhead

#### Example 3: Controlled Parallel Execution

```bash
# Parallel execution with cost and turn limits
CAI_PARALLEL="4" \
CAI_MODEL="alias1" \
CAI_PRICE_LIMIT="10" \
CAI_MAX_TURNS="20" \
cai --prompt "Enumerate attack vectors on API endpoints"
```

### Parallelization Behavior

**Instance Independence**:
- Each agent instance maintains its own conversation history
- No shared context between instances
- Tool executions are independent
- Cost tracking is aggregated

**Result Aggregation**:
- All outputs are collected and displayed
- Results can be compared side-by-side
- Failed instances don't block others
- Final summary includes all perspectives

**Resource Considerations**:
- **API Rate Limits**: Each instance makes independent API calls
- **Memory Usage**: N instances = N × base memory
- **CPU Load**: Tool executions run in parallel
- **Cost Multiplier**: N instances = N × cost per prompt

### When to Use CAI_PARALLEL

✅ **Good Use Cases**:
- Testing the same target with multiple attempts
- Comparing model outputs (different temperatures/models)
- Batch processing of similar tasks
- Automated security scanning pipelines
- High-throughput vulnerability discovery

❌ **Not Recommended For**:
- Tasks requiring agent coordination
- Multi-perspective analysis (use TUI instead)
- Interactive workflows
- Complex team-based assessments

### Limitations

1. **Single Agent Type**: All instances run the same agent
2. **No Inter-Agent Communication**: Instances cannot share findings
3. **No TUI Support**: `CAI_PARALLEL` is CLI-only (TUI has built-in Teams)
4. **Shared Model**: All instances use the same model configuration

> **Note**: For running different agents simultaneously, use YAML parallel agents configuration (see next section).

---

## CLI Parallel Agents (YAML Configuration)

### Overview

In addition to `CAI_PARALLEL` for running multiple instances of the **same agent**, CAI supports running **different agents** simultaneously via YAML configuration files.

**Key Differences:**

| Feature | CAI_PARALLEL | YAML Parallel Agents |
|---------|--------------|----------------------|
| **Agent Types** | Same agent × N | Different agents |
| **Configuration** | Environment variable | YAML file |
| **Prompts** | Same for all | Different per agent |
| **Models** | Same for all | Configurable per agent |
| **Use Case** | Swarm execution, batch | Multi-perspective workflows |

### Quick Start

#### 1. Create Configuration File

Create a YAML file defining your parallel agents:

```yaml
# my_agents.yaml
parallel_agents:
  - name: redteam_agent
    model: alias1
    prompt: "Find vulnerabilities in target.com"
    description: "Offensive security testing"
    auto_run: true
  
  - name: blueteam_agent
    model: alias1
    prompt: "Analyze defensive posture of target.com"
    description: "Defensive analysis"
    auto_run: true
```

#### 2. Execute

```bash
cai --yaml my_agents.yaml
```

**What happens:**
- Both agents load automatically
- Each executes its configured prompt in parallel
- Results stream to the terminal
- Each agent maintains independent context

#### 3. Manage in Session

```bash
# Check loaded agents
/parallel list

# View environment variables
/env | grep PARALLEL

# Merge agent histories
/parallel merge

# Check costs
/cost
```

### Common Patterns

#### Pattern 1: Multi-Perspective Analysis

```yaml
# multi_perspective.yaml
parallel_agents:
  - name: redteam_agent
    prompt: "Identify attack vectors for webapp.com"
  
  - name: blueteam_agent
    prompt: "Analyze defenses for webapp.com"
  
  - name: bug_bounter_agent
    prompt: "Find bug bounty targets in webapp.com"
```

#### Pattern 2: Distributed Workload

```yaml
# distributed_scan.yaml
parallel_agents:
  - name: redteam_agent
    prompt: "Scan subdomains A-M of target.com"
  
  - name: redteam_agent
    prompt: "Scan subdomains N-Z of target.com"
  
  - name: network_security_analyzer_agent
    prompt: "Analyze discovered hosts for services"
```

#### Pattern 3: Model Comparison

```yaml
# model_comparison.yaml
parallel_agents:
  - name: redteam_agent
    model: alias1
    prompt: "Find SQL injection vulnerabilities"
  
  - name: bug_bounter_agent
    model: alias1
    prompt: "Validate SQL injection findings"
```

### Advanced Features

#### Shared Configuration

Define common settings across all agents:

```yaml
shared:
  model: alias1
  auto_run: true

parallel_agents:
  - name: redteam_agent
    prompt: "Test security"
  
  - name: blueteam_agent
    prompt: "Review defenses"
    model: alias1  # Override shared model
```

#### Environment Variables Per Agent

```yaml
parallel_agents:
  - name: blueteam_agent
    prompt: "Defend container"
    env:
      CTF_INSIDE: "true"
      DEFENSIVE_MODE: enabled
  
  - name: redteam_agent
    prompt: "Attack container"
    env:
      CTF_INSIDE: "false"
      AGGRESSIVE_SCAN: "true"
```

#### Unified Context Mode

Share conversation history between agents (CLI only):

```yaml
parallel_agents:
  - name: redteam_agent
    prompt: "Find vulnerabilities"
    unified_context: true
  
  - name: bug_bounter_agent
    prompt: "Validate findings from previous analysis"
    unified_context: true  # Sees redteam's work
```

### Available Fields

| Field | Required | Type | Default | Description |
|-------|----------|------|---------|-------------|
| `name` | ✅ Yes | string | - | Agent type to instantiate |
| `model` | ❌ No | string | Global | Model override |
| `prompt` | ❌ No | string | None | Initial prompt to execute |
| `description` | ❌ No | string | None | Human-readable description |
| `auto_run` | ❌ No | boolean | `true` | Auto-execute on startup |
| `unified_context` | ❌ No | boolean | `false` | Share history (CLI only) |
| `team` | ❌ No | string | None | Team label (TUI only) |
| `env` | ❌ No | object | `{}` | Environment variables |

### Cost Management

```bash
# Set cost limit
CAI_PRICE_LIMIT=5.0 cai --yaml agents.yaml

# Monitor costs
/cost  # Check total cost
```

### Detailed Documentation

For complete YAML syntax, field reference, troubleshooting, and advanced examples:

- **[YAML Format Reference](parallel-agents-yaml-format.md)** - Complete specification
- **[CLI Usage Guide](parallel-agents-cli-usage.md)** - Detailed workflows
- **[Quick Reference](parallel-agents-quick-reference.md)** - Syntax cheat sheet

---

## TUI Multi-Terminal Workflows

> **⚡ CAI-Pro Exclusive Feature**

The TUI provides a sophisticated multi-terminal interface for coordinating different agents simultaneously.

### Terminal Architecture

**4 Independent Terminals**:
- Each terminal runs its own agent
- Independent model selection per terminal
- Isolated conversation histories
- Real-time output streaming

**Terminal Identification**:
- **T1**: Terminal 1 (always present, cannot be closed)
- **T2**: Terminal 2
- **T3**: Terminal 3
- **T4**: Terminal 4

### Terminal Management

#### Creating Terminals

**Method 1: Manual Addition**

1. Click `[Add+]` button in the top bar
2. New terminal appears (T2, T3, or T4)
3. Select agent from dropdown
4. Select model from dropdown
5. Terminal is ready for input

**Method 2: Team Selection**

1. Open sidebar with `Ctrl+S`
2. Navigate to **Teams** tab
3. Click any team button (e.g., "#1: 2 red + 2 bug")
4. All 4 terminals configure automatically
5. Agents are assigned based on team composition

#### Removing Terminals

**Keyboard Shortcut**: `Ctrl+E`

**Behavior**:
- Closes the currently focused terminal
- Terminal 1 (T1) cannot be closed
- Terminal numbers don't shift (T1, T2, T3, T4 remain fixed)
- Conversation history is lost unless session is saved

**Save Before Closing**:
```bash
/save my_assessment.json  # Save before closing
Ctrl+E                    # Then close terminal
```

#### Focusing Terminals

**Navigation Shortcuts**:
- `Ctrl+N` - Focus next terminal (T1 → T2 → T3 → T4 → T1)
- `Ctrl+B` - Focus previous terminal (T1 → T4 → T3 → T2 → T1)
- **Mouse Click** - Click anywhere in terminal output area

**Visual Indicators**:
- **Focused Terminal**: Highlighted header, bright colors, active cursor
- **Inactive Terminals**: Dimmed header, normal colors, background execution continues

### Terminal States

Each terminal can be in one of four states:

#### 1. Active State
- **Visual**: Normal border, standard colors
- **Behavior**: Ready to receive input
- **Actions**: Can send prompts, change agent/model, run commands

#### 2. Focused State
- **Visual**: Highlighted border (accent color), brightest display
- **Behavior**: Receives all keyboard input
- **Actions**: All actions available, input directed to this terminal

#### 3. Busy State
- **Visual**: Spinner or progress indicator in header
- **Behavior**: Agent is executing, streaming output
- **Actions**: Can cancel with `Ctrl+C`, cannot send new prompts

#### 4. Error State
- **Visual**: Red border or error indicator
- **Behavior**: Execution failed or encountered error
- **Actions**: Can retry, clear error, or continue with new prompt

**State Transition Diagram**:
```
Active ──[Send Prompt]──> Busy ──[Complete]──> Active
   │                        │
   │                        └──[Error]──> Error State
   │                                           │
   │                                           └──[Clear]──> Active
   │
   └──[Focus Terminal]──> Focused ──[Focus Other]──> Active
```

---

## Preconfigured Teams

CAI TUI includes **11 preconfigured teams** optimized for common security workflows. These teams provide instant multi-agent configurations with a single click.

### Team Catalog

| Team # | Composition | Terminals | Best For | Use Case Examples |
|--------|-------------|-----------|----------|-------------------|
| **#1** | 2 redteam + 2 bug_bounter | T1,T2: redteam<br>T3,T4: bug_bounter | Comprehensive vulnerability discovery | Penetration testing, bug bounty hunting |
| **#2** | 1 redteam + 3 bug_bounter | T1: redteam<br>T2,T3,T4: bug_bounter | Bug bounty with red team leadership | Bug bounty programs, coordinated research |
| **#3** | 2 redteam + 2 blueteam | T1,T2: redteam<br>T3,T4: blueteam | Adversarial offense + defense testing | Purple team exercises, security validation |
| **#4** | 2 blueteam + 2 bug_bounter | T1,T2: blueteam<br>T3,T4: bug_bounter | Defense-focused with validation | Security posture assessment, defensive audits |
| **#5** | red + blue + retester + bug | T1: redteam<br>T2: blueteam<br>T3: retester<br>T4: bug_bounter | Full security lifecycle coverage | Complete security assessments, holistic reviews |
| **#6** | 2 redteam + 2 retester | T1,T2: redteam<br>T3,T4: retester | Aggressive testing with validation | Exploit development, validation workflows |
| **#7** | 2 blueteam + 2 retester | T1,T2: blueteam<br>T3,T4: retester | Defensive validation and retesting | Control validation, defense verification |
| **#8** | 4 redteam | T1,T2,T3,T4: redteam | Maximum offensive power | Intensive penetration testing, red team ops |
| **#9** | 4 blueteam | T1,T2,T3,T4: blueteam | Comprehensive defensive analysis | Defense in depth reviews, security hardening |
| **#10** | 4 bug_bounter | T1,T2,T3,T4: bug_bounter | Intense bug bounty hunting | Bug bounty competitions, vulnerability research |
| **#11** | 4 retester | T1,T2,T3,T4: retester | Large-scale retesting campaigns | Regression testing, validation sweeps |

### Team Selection Workflow

#### Step-by-Step Guide

1. **Open Sidebar**: Press `Ctrl+S`
2. **Navigate to Teams Tab**: Click "Teams" in sidebar
3. **Review Team Options**: Browse the 11 available teams
4. **Select Team**: Click desired team button
5. **Confirmation**: All terminals update instantly with new agent assignments
6. **Verify Configuration**: Check each terminal header shows correct agent
7. **Start Working**: Send prompts to individual terminals or broadcast to all

#### What Happens When You Select a Team

```
Before Team Selection:
┌─────────┬─────────┐
│ T1: X   │ T2: Y   │  (arbitrary agents)
├─────────┼─────────┤
│ T3: Z   │ T4: W   │
└─────────┴─────────┘

Click "Team #1: 2 red + 2 bug"

After Team Selection:
┌─────────┬─────────┐
│ T1: red │ T2: red │  (team configuration)
├─────────┼─────────┤
│ T3: bug │ T4: bug │
└─────────┴─────────┘
```

**Automatic Changes**:
1. All terminal agent dropdowns update to reflect new assignments
2. Previous conversation output remains visible (not cleared)
3. Each terminal is immediately ready to receive prompts
4. No cost impact (configuration is free)
5. You can switch teams anytime without losing work

### Custom Team Creation

While the 11 preconfigured teams cover most scenarios, you can create custom configurations:

**Process**:
1. Manually configure each terminal with desired agents
2. Manually select models for each terminal
3. Save the session: `/save my_custom_team.json`
4. Load it later: `/load my_custom_team.json`

**Custom Configuration Example**:
```bash
# Configure custom team manually
T1: /agent dfir_agent
T2: /agent reverse_engineering_agent
T3: /agent network_security_analyzer_agent
T4: /agent reporting_agent

# Save configuration
/save forensics_team_config.json

# Use later
/load forensics_team_config.json
```

---

## Parallel Execution Patterns

These patterns represent proven strategies for coordinating multiple agents effectively.

### Pattern 1: Broadcast to All Terminals

**Description**: Send the same command to all terminals simultaneously to get multiple perspectives on a single task.

**Syntax**: Append `all` to the end of your prompt

**Use Cases**:
- Compare different agent approaches
- Get diverse perspectives on the same problem
- Validate findings across multiple agents
- Stress-test targets with multiple concurrent probes

**Example Workflow**:

```bash
# Step 1: Select Team #3 (2 Red + 2 Blue)
<Click Team #3 in sidebar>

# Step 2: Broadcast prompt
Assess the security of https://target.com/api all

# Step 3: Observe results
T1 (redteam):    "Identified 3 SQL injection points..."
T2 (redteam):    "Found authentication bypass via JWT..."
T3 (blueteam):   "Defensive controls missing for..."
T4 (blueteam):   "Rate limiting not implemented..."
```

**Benefits**:
- ✅ Multiple perspectives in parallel
- ✅ Comprehensive coverage
- ✅ Identifies blind spots

**Considerations**:
- ⚠️ **Cost**: 4× the cost of a single prompt
- ⚠️ **Redundancy**: Some findings may overlap
- ⚠️ **API Load**: 4 simultaneous API calls

**Best Used For**:
- Initial reconnaissance
- High-value targets requiring thorough analysis
- Validation of critical findings
- Comparative analysis of different agent types

### Pattern 2: Sequential Terminal Execution (Pipeline)

**Description**: Execute tasks in sequence, passing results from one terminal to the next.

**Workflow Structure**:
```
T1: Execute Task A → Output A
      ↓
T2: Use Output A → Execute Task B → Output B
      ↓
T3: Use Output A + B → Execute Task C → Output C
      ↓
T4: Use A + B + C → Generate Report
```

**Example: Comprehensive Web Assessment**

```bash
# Terminal 1: Bug Bounter - Discovery
T1> Find all input fields and forms on target.com

<Wait for T1 to complete>

# Terminal 2: Red Team - Testing
T2> Test the 5 input fields found by T1 for XSS vulnerabilities

<Wait for T2 to complete>

# Terminal 3: Retester - Validation
T3> Validate the XSS findings from T2 and confirm exploitability

<Wait for T3 to complete>

# Terminal 4: Reporting - Documentation
T4> Create a comprehensive report from T1, T2, and T3 findings
```

**Benefits**:
- ✅ Clear separation of concerns
- ✅ Quality assurance at each stage
- ✅ Traceable workflow
- ✅ Each agent specializes in its role

**Considerations**:
- ⏱️ **Sequential**: Takes longer than parallel execution
- 🔗 **Dependencies**: Later stages depend on earlier results
- 💬 **Context Sharing**: Agents must reference previous work

**Best Used For**:
- Multi-stage assessments
- Workflows with dependencies
- Quality-gated processes
- Professional engagements requiring documentation

### Pattern 3: Parallel Independent Tasks

**Description**: Execute completely different tasks simultaneously with no dependencies.

**Task Distribution**:
```
T1: Task A ──┐
T2: Task B ──┼──→ All execute simultaneously
T3: Task C ──┤
T4: Task D ──┘
```

**Example: Multi-Vector Assessment**

```bash
# All terminals execute simultaneously
T1> nmap -sV -A target.com
T2> ffuf -w wordlist.txt -u https://target.com/FUZZ
T3> Enumerate all DNS records for target.com
T4> Analyze SSL/TLS configuration of target.com
```

**Benefits**:
- ⚡ **Maximum Speed**: True parallelization
- ♻️ **Resource Efficiency**: All terminals actively working
- 🎯 **Coverage**: Multiple attack surfaces simultaneously
- ⏱️ **Time Savings**: N tasks in ~1× time vs. N× time sequential

**Considerations**:
- 🧩 **Coordination**: Manual coordination of independent results
- 📊 **Aggregation**: Results must be manually consolidated
- 💰 **Cost**: All tasks incur costs simultaneously

**Best Used For**:
- Initial reconnaissance phases
- Target profiling
- Broad vulnerability scanning
- Time-critical assessments

### Pattern 4: Focused Execution with Monitoring

**Description**: One primary agent executes while others monitor different aspects.

**Role Assignment**:
```
T1: Main Executor (Active Agent)
T2: Monitor Aspect A
T3: Monitor Aspect B  
T4: Monitor Aspect C (often documentation)
```

**Example: Monitored Penetration Test**

```bash
# Terminal 1: Primary Execution (Red Team)
T1> Perform full penetration test on target.com

# While T1 works, launch monitoring agents:

# Terminal 2: Defensive Analysis (Blue Team)
T2> Monitor defensive weaknesses as T1 progresses. Review T1's approach.

# Terminal 3: Forensics Tracking (DFIR)
T3> Track and analyze artifacts generated by T1's activities

# Terminal 4: Live Documentation (Reporting)
T4> Document findings in real-time from T1, T2, T3
```

**Benefits**:
- 🔍 **Multi-Dimensional Analysis**: Multiple perspectives on same engagement
- 📝 **Live Documentation**: Real-time report generation
- 🛡️ **Defense Awareness**: Understand defensive visibility
- 🔬 **Artifact Tracking**: DFIR perspective on actions

**Considerations**:
- 🎯 **Primary Agent**: T1 does the heavy lifting
- 👁️ **Monitoring Overhead**: Other agents add observability cost
- ⚖️ **Balance**: Ensure monitors don't overwhelm primary

**Best Used For**:
- Training and education
- Purple team exercises
- High-value engagements
- Comprehensive assessments requiring documentation

### Pattern 5: Divide and Conquer (Workload Distribution)

**Description**: Split a large task into smaller chunks distributed across terminals.

**Division Strategy**:
```
Large Task
    ├── Subtask A → T1
    ├── Subtask B → T2
    ├── Subtask C → T3
    └── Subtask D → T4
```

**Example: Large-Scale Subdomain Enumeration**

```bash
# Divide alphabetically
T1> Enumerate subdomains of example.com starting with A-F
T2> Enumerate subdomains of example.com starting with G-M
T3> Enumerate subdomains of example.com starting with N-S
T4> Enumerate subdomains of example.com starting with T-Z

# Or divide by method
T1> Use DNS brute force for subdomain discovery on example.com
T2> Use certificate transparency logs for subdomains of example.com
T3> Use search engines for subdomain discovery of example.com
T4> Use reverse DNS for subdomain discovery of example.com
```

**Benefits**:
- ⚡ **Speed**: N× faster completion
- 📊 **Scalability**: Handles large workloads
- ⚖️ **Load Balancing**: Even distribution of work

**Considerations**:
- 📋 **Planning**: Requires upfront task division
- 🔀 **Merging**: Results must be combined
- ⚖️ **Balance**: Uneven divisions slow overall completion

**Best Used For**:
- Large-scale enumeration
- Bulk vulnerability scanning
- Data processing tasks
- Comprehensive target profiling

### Pattern 6: Phased Workflow with Team Switching

**Description**: Execute sequential phases, switching teams between phases for optimal agent composition.

**Phase Structure**:
```
Phase 1: Reconnaissance (Team #8: 4 Red)
    ↓ (Switch Teams)
Phase 2: Exploitation (Team #1: 2 Red + 2 Bug)
    ↓ (Switch Teams)
Phase 3: Validation (Team #11: 4 Retester)
    ↓ (Switch Teams)
Phase 4: Reporting (Custom: 4 Reporting)
```

**Example: Complete Assessment Lifecycle**

```bash
# Phase 1: Reconnaissance
<Select Team #8: 4 redteam>
Enumerate all attack vectors on target.com all

<Wait for completion>

# Phase 2: Vulnerability Discovery
<Select Team #1: 2 red + 2 bug>
T1-T2: Exploit findings from Phase 1
T3-T4: Hunt for additional vulnerabilities

<Wait for completion>

# Phase 3: Validation
<Select Team #11: 4 retester>
Validate all findings from Phase 2 all

<Wait for completion>

# Phase 4: Documentation
<Manually configure 4 reporting agents>
Generate comprehensive assessment report all
```

**Benefits**:
- 🎯 **Optimized Agent Selection**: Right agents for each phase
- 📈 **Progressive Refinement**: Build on previous phases
- ✅ **Quality Gates**: Validation between phases

**Considerations**:
- ⏱️ **Time**: Sequential phases take longer
- 💾 **Context Preservation**: Save sessions between phases
- 🔄 **Coordination**: Manual team switching required

**Best Used For**:
- Professional security engagements
- Comprehensive assessments
- Quality-driven workflows
- Training and methodological approaches

---

## Context Management Strategies

Understanding how context is managed between parallel agents is crucial for effective coordination.

### Split Context (Independent Analysis)

**Definition**: Each terminal maintains completely isolated conversation history and context.

**Characteristics**:
- ✅ No cross-contamination between agents
- ✅ Independent reasoning and decision-making
- ✅ Diverse approaches and perspectives
- ❌ No automatic knowledge sharing
- ❌ Agents cannot build on each other's work without manual intervention

**When Context is Split**:
- By default, all TUI terminals use split context
- Each terminal's conversation history is isolated
- Agents don't see other terminals' outputs
- Manual context sharing required (e.g., "Review T1's findings...")

**Example: Parallel Hypothesis Testing**

```bash
# Each terminal tests a different hypothesis independently

T1> Hypothesis: Target uses weak session management. Test for session fixation.
T2> Hypothesis: Target has SQL injection in user search. Test all search forms.
T3> Hypothesis: Target's API lacks rate limiting. Perform rate limit testing.
T4> Hypothesis: Target has XSS in comment fields. Test all user input fields.

# Results:
# - Each agent pursues its hypothesis independently
# - No bias from other agents' findings
# - Comprehensive coverage of different attack vectors
# - Compare results to identify most viable approaches
```

**Best Split Context Use Cases**:
- **Comparative Analysis**: Compare different approaches to same problem
- **Blind Testing**: Prevent agents from biasing each other
- **Diverse Exploration**: Explore different attack vectors simultaneously
- **Independent Validation**: Multiple agents validate same findings independently
- **Hypothesis Testing**: Test multiple theories in parallel

**Advantages**:
1. **No Cognitive Bias**: Agents don't influence each other
2. **True Parallelism**: Completely independent execution
3. **Diverse Insights**: Different reasoning paths
4. **Fault Isolation**: One agent's errors don't affect others

**Disadvantages**:
1. **Redundant Work**: Agents may repeat same steps
2. **Manual Coordination**: Requires human-in-the-loop for knowledge sharing
3. **Context Overhead**: Large context in each terminal
4. **No Collaboration**: Agents work in silos

### Shared Context (Collaborative Analysis)

**Definition**: Agents have access to a unified knowledge base or explicitly share findings.

**Implementation in CAI**:
- Not automatic - requires manual context sharing
- Use `/load` to share session data
- Explicitly reference other terminals in prompts
- Use `/merge` command in CLI parallel mode

**Manual Context Sharing Methods**:

**Method 1: Explicit References**
```bash
T1> Find SQL injection vulnerabilities in target.com

<Wait for T1 completion>

T2> Review the SQL injection findings from T1 and develop exploits

<Wait for T2 completion>

T3> Analyze T1's findings and T2's exploits from a defensive perspective
```

**Method 2: Session Loading**
```bash
# Terminal 1 completes work
T1> Comprehensive reconnaissance of target.com
T1> /save t1_recon.json

# Terminal 2 loads T1's context
T2> /load t1_recon.json
T2> Based on the loaded reconnaissance, identify vulnerabilities
```

**Method 3: Merge Command (YAML Parallel Agents)**
```bash
# Create YAML configuration file (agents.yaml):
# parallel_agents:
#   - name: redteam_agent
#     model: alias1
#     prompt: "Analyze security of target.com from offensive perspective"
#   - name: blueteam_agent
#     model: alias1
#     prompt: "Analyze security of target.com from defensive perspective"

# Load and execute
cai --yaml agents.yaml

# Inside session, merge results
/merge  # Combines both agents' findings into unified view
```

**Best Shared Context Use Cases**:
- **Sequential Workflows**: Each agent builds on previous work
- **Incremental Refinement**: Progressive improvement of findings
- **Collaborative Analysis**: Agents work together on complex problems
- **Knowledge Accumulation**: Build comprehensive understanding over time

**Advantages**:
1. **Efficiency**: Avoid redundant work
2. **Progressive Refinement**: Each agent improves on previous findings
3. **Comprehensive Analysis**: Unified knowledge base
4. **Contextual Awareness**: Agents understand full picture

**Disadvantages**:
1. **Cognitive Bias**: Later agents influenced by earlier findings
2. **Error Propagation**: Mistakes can compound
3. **Context Bloat**: Shared context grows large quickly
4. **Reduced Diversity**: Less diverse perspectives

### Hybrid Approach: Selective Context Sharing

**Best Practice Strategy**: Start with split context, selectively share key findings.

**Workflow**:
```bash
# Phase 1: Independent Discovery (Split Context)
T1> Scan target.com for vulnerabilities (independent)
T2> Scan target.com for vulnerabilities (independent)
T3> Scan target.com for vulnerabilities (independent)
T4> Scan target.com for vulnerabilities (independent)

# Phase 2: Review and Consolidate
<Human reviews all findings, identifies key insights>

# Phase 3: Selective Sharing (Shared Context)
T1> Focus on SQL injection found in Phase 1
T2> Exploit the XSS vulnerability from T1's findings
T3> Validate critical findings from T1 and T2
T4> Document consolidated results from T1, T2, T3
```

**Benefits of Hybrid**:
- ✅ Initial diversity from split context
- ✅ Efficiency from selective sharing
- ✅ Human-in-the-loop quality control
- ✅ Best of both approaches

---

## Agentic Patterns for Coordination

CAI supports various architectural patterns for orchestrating multiple agents, inspired by research in multi-agent systems and cybersecurity automation.

### Pattern Taxonomy

Based on CAI's architecture documentation and the OpenAI Swarm principles, agents can be coordinated using these patterns:

| Pattern | Coordination Style | Decision Making | Knowledge Sharing | Best For |
|---------|-------------------|-----------------|-------------------|----------|
| **Swarm** | Decentralized | Dynamic/Emergent | Peer-to-peer | Complex, adaptive tasks |
| **Hierarchical** | Top-down | Centralized planner | Structured handoffs | Predictable workflows |
| **Chain-of-Thought** | Sequential | Step-by-step reasoning | Linear pipeline | Systematic analysis |
| **Parallelization** | Independent | Autonomous per agent | Manual/none | Speed-critical tasks |
| **Auction-Based** | Competitive | Best-fit selection | Bid evaluation | Resource optimization |
| **Recursive** | Self-refinement | Iterative improvement | Self-feedback | Quality-driven tasks |

### 1. Swarm Pattern (Decentralized)

**Description**: Agents self-organize and dynamically handoff tasks without central coordination.

**Implementation in CAI**:
- `bb_triage_swarm_pattern` - Bug bounty triage coordination
- `redteam_swarm_pattern` - Red team agent swarm
- Dynamic handoffs between agents
- Emergent task distribution

**Example: CTF Challenge Solving**
```bash
# Multiple agents collaborate on CTF
T1: bb_triage_swarm_pattern
T2: redteam_swarm_pattern
T3: blueteam_agent
T4: retester_agent

# Agents dynamically handoff:
# T1 identifies interesting endpoint → hands to T2
# T2 finds vulnerability → hands to T3 for defense check
# T3 suggests fix → hands to T4 for validation
```

**Characteristics**:
- Self-organization and task distribution
- Dynamic handoffs based on agent capabilities
- No central orchestrator
- Emergent coordination

**Advantages**:
- Adaptive to changing conditions
- Resilient to individual agent failures
- Scalable to many agents

**Challenges**:
- Unpredictable execution paths
- Difficult to trace decision-making
- May have coordination overhead

### 2. Hierarchical Pattern (Top-Down)

**Description**: A coordinator agent assigns tasks to specialized sub-agents.

**Implementation**:
```bash
# Terminal 1: Coordinator/Planner
T1: selection_agent  # Intelligent agent selection and routing

# Terminals 2-4: Worker Agents
T2: redteam_agent
T3: blueteam_agent
T4: reporting_agent

# Workflow:
T1> Analyze target.com and create an assessment plan
<T1 creates structured plan>

T1> T2: Execute reconnaissance phase
T1> T3: Evaluate defensive posture  
T1> T4: Begin documentation framework
```

**Characteristics**:
- Central planner or coordinator
- Structured task delegation
- Clear hierarchy and responsibilities
- Predefined handoff structure

**Advantages**:
- Predictable execution
- Clear accountability
- Easy to understand and trace

**Challenges**:
- Single point of failure (coordinator)
- Less adaptive
- Coordinator overhead

### 3. Chain-of-Thought Pattern (Sequential)

**Description**: Linear pipeline where each agent processes and hands off to the next.

**Implementation**:
```bash
# Linear workflow
T1: redteam_agent      # Step 1: Discovery
T2: bug_bounter_agent  # Step 2: Analysis
T3: retester_agent     # Step 3: Validation
T4: reporting_agent    # Step 4: Documentation

# Sequential execution:
T1> Discover vulnerabilities → Output to T2
T2> Analyze findings from T1 → Output to T3
T3> Validate vulnerabilities from T2 → Output to T4
T4> Generate report from all previous steps
```

**Characteristics**:
- Linear, sequential flow
- Each step depends on previous
- Reasoning builds progressively
- Clear input/output chain

**Advantages**:
- Easy to understand and debug
- Traceable reasoning path
- Quality gates at each step

**Challenges**:
- Slowest pattern (fully sequential)
- Bottlenecks at slow stages
- Limited parallelization

### 4. Parallelization Pattern (Independent)

**Description**: Multiple agents execute independent subtasks simultaneously.

**Implementation**: (Already covered in Pattern 3: Parallel Independent Tasks)

**Characteristics**:
- True parallel execution
- No dependencies between tasks
- Independent results
- Maximum speed

### 5. Auction-Based Pattern (Competitive)

**Description**: Agents "bid" on tasks based on their capabilities, with a decision agent selecting the best fit.

**Implementation**:
```bash
# Terminal 1: Auctioneer/Decision Agent
T1: selection_agent

# Terminals 2-4: Capability-based agents
T2: redteam_agent       # Strength: exploitation
T3: dfir_agent          # Strength: forensics
T4: network_security_analyzer_agent  # Strength: network analysis

# Workflow:
T1> Task: "Analyze suspicious network traffic from incident"

T1> Request capabilities from T2, T3, T4
# T2: Low fit (not network-focused)
# T3: High fit (forensics + network)
# T4: High fit (network specialist)

T1> Assign to T4 (best fit for network analysis)
T4> Execute network traffic analysis
T1> Follow-up with T3 for forensic correlation
```

**Characteristics**:
- Capability-based task assignment
- Competitive selection
- Optimization of agent strengths
- Dynamic allocation

**Advantages**:
- Optimal agent utilization
- Leverages agent specializations
- Flexible task distribution

**Challenges**:
- Requires capability metadata
- Selection overhead
- Complex implementation

### 6. Recursive Pattern (Self-Refinement)

**Description**: An agent iteratively refines its own output through self-evaluation.

**Implementation**:
```bash
# Single terminal with recursive prompting
T1: thought_agent  # Or any agent with reasoning capability

# Recursive workflow:
T1> Initial analysis: "Analyze target.com security"
<Review output>

T1> Critique previous analysis and identify gaps
<Review critique>

T1> Refine analysis based on critique
<Review refinement>

T1> Final validation: Does this analysis meet quality bar?
```

**Characteristics**:
- Self-evaluation and improvement
- Iterative refinement
- Internal feedback loop
- Single agent, multiple iterations

**Advantages**:
- Continuous quality improvement
- Self-correcting
- Deep analysis

**Challenges**:
- Can be slow (many iterations)
- May not converge
- Higher cost (multiple LLM calls)

### Pattern Selection Guide

**Choose Swarm when**:
- Task is complex and adaptive
- Agents have overlapping capabilities
- Dynamic coordination needed

**Choose Hierarchical when**:
- Clear task structure exists
- Central planning beneficial
- Accountability is critical

**Choose Chain-of-Thought when**:
- Sequential reasoning required
- Quality gates needed
- Clear dependencies exist

**Choose Parallelization when**:
- Speed is critical
- Tasks are independent
- No coordination needed

**Choose Auction-Based when**:
- Agents have diverse specializations
- Optimal allocation important
- Tasks vary in requirements

**Choose Recursive when**:
- Quality is paramount
- Single-agent refinement preferred
- Iterative improvement valuable

---

## Queue Management

CAI provides sophisticated queue management for batching and automating prompts.

### Queue File Feature (CLI & TUI)

**Environment Variable**: `CAI_QUEUE_FILE`

**Purpose**: Automatically load and execute a queue of prompts from a text file on startup.

**Configuration**:
```bash
# Set queue file path
export CAI_QUEUE_FILE="/path/to/prompts.txt"

# Or inline
CAI_QUEUE_FILE="~/security_workflow.txt" cai
```

**Queue File Format**:
```text
# Comments start with # and are ignored
# Blank lines are skipped

# CLI commands
/help
/agent list
/model

# Shell commands (prefix with $)
$ nmap -sV target.com
$ curl https://target.com

# Natural language prompts
Scan the network 192.168.1.0/24
Check for vulnerabilities in https://example.com
Test for SQL injection in all forms
Generate a security report

# Exit automatically (optional)
/exit
```

**Execution Behavior**:
1. CAI loads all prompts from file on startup
2. Display shows number of prompts loaded
3. Prompts execute automatically in order
4. Each prompt waits for previous to complete
5. Returns to interactive mode when queue empty

**Use Cases**:
- **Automated Workflows**: Standard security assessment scripts
- **Batch Processing**: Process multiple targets
- **Team Standardization**: Consistent methodology across team
- **CI/CD Integration**: Automated security testing pipelines

**Example: Automated Web Security Assessment**
```text
# security_assessment_workflow.txt

# Setup
/agent redteam_agent
/model alias1

# Phase 1: Reconnaissance
Enumerate all subdomains of target.com
$ nmap -sV -p- target.com
Identify web technologies used by target.com

# Phase 2: Vulnerability Discovery
Test for SQL injection in all input fields
Check for XSS vulnerabilities
Analyze authentication mechanisms for weaknesses
Test for CSRF protection

# Phase 3: Analysis
$ curl -I https://target.com
Analyze HTTP security headers
Check for sensitive information disclosure

# Phase 4: Reporting
Generate comprehensive vulnerability report
/history
/cost

# Optional: Exit when done
# /exit
```

**Running the workflow**:
```bash
CAI_QUEUE_FILE="security_assessment_workflow.txt" cai
```

### TUI Queue Tab

> **⚡ CAI-Pro Exclusive Feature**

**Location**: Sidebar → Queue Tab

**Features**:
- View all queued prompts
- Add prompts to queue manually
- Clear entire queue
- Execute queue sequentially
- Monitor execution progress
- Toggle broadcast mode

**Automatic Queue Accumulation**:
When a terminal is busy, new prompts automatically accumulate in the queue:

```bash
# Terminal 1 is busy executing
T1> [BUSY] Performing comprehensive scan... (2 min remaining)

# Send new prompts - they queue automatically
T1> Test for SQL injection   # Queued (position 1)
T1> Analyze authentication   # Queued (position 2)
T1> Generate report          # Queued (position 3)

# When T1 finishes, queue executes in order
```

**Manual Queue Management**:
```bash
# Add to queue explicitly
/queue add "Scan target.com"
/queue add "Test vulnerabilities"

# View queue
/queue list

# Execute queue
/queue execute

# Clear queue
/queue clear
```

**Broadcast Queue Mode**:
- Toggle in Queue Tab
- When enabled, queued prompts broadcast to all terminals
- When disabled, prompts go to specific terminals

---

## Terminal-Specific Commands

Send commands to specific terminals without focusing them, enabling efficient multi-terminal management.

### Syntax Overview

CAI provides two syntaxes for terminal-specific commands:

**Prefix Notation**: `T<number>:<command>`
**Flag Notation**: `<command> t<number>`

Both achieve the same result - choose based on preference or context.

### Prefix Notation (T#:)

**Format**: `T<terminal_number>:<command>`

**Examples**:
```bash
# Agent management
T2:/agent blueteam_agent           # Switch T2 to blueteam
T3:/agent bug_bounter_agent        # Switch T3 to bug bounty

# Model changes
T1:/model alias1                   # Change T1 model
T4:/model alias1                   # Change T4 model

# Session management
T2:/save t2_session.json           # Save T2's session
T3:/load previous_work.json        # Load into T3

# Utility commands
T1:/clear                          # Clear T1 output
T2:/flush                          # Flush T2 history
T3:/cost                           # Check T3 cost
T4:/history                        # View T4 history

# Natural language prompts
T1:Scan target.com for open ports
T2:Analyze defensive posture of target.com
T3:Test authentication mechanisms
T4:Generate progress report
```

**Benefits**:
- Clear and explicit
- Easy to read in scripts
- Intuitive "terminal : command" structure

### Flag Notation (t#)

**Format**: `<command> t<terminal_number>`

**Examples**:
```bash
# Agent management
/agent blueteam_agent t2           # Switch T2 to blueteam
/agent bug_bounter_agent t3        # Switch T3 to bug bounty

# Model changes
/model alias1 t1                   # Change T1 model
/model alias1 t4                   # Change T4 model

# Session management
/save t2_session.json t2           # Save T2's session
/load previous_work.json t3        # Load into T3

# Utility commands
/clear t1                          # Clear T1 output
/flush t2                          # Flush T2 history
/cost t3                           # Check T3 cost
/history t4                        # View T4 history

# Natural language prompts
Scan target.com for open ports t1
Analyze defensive posture of target.com t2
Test authentication mechanisms t3
Generate progress report t4
```

**Benefits**:
- Compact syntax
- Flag-style familiar to CLI users
- Easy to append to existing commands

### Broadcast Commands (all)

**Format**: `<command> all`

**Examples**:
```bash
# Broadcast to all terminals
Scan target.com for vulnerabilities all
/flush all                         # Flush all histories
/cost all                          # Check cost across all terminals
/clear all                         # Clear all outputs

# Use cases
Test authentication mechanisms all    # 4 different approaches
Enumerate subdomains of example.com all
Generate summary of findings all
```

**Behavior**:
- Command sent to T1, T2, T3, T4 simultaneously
- Each terminal executes independently
- Outputs stream in real-time
- Useful for comparative analysis

### Command Routing Priority

When you don't specify a terminal, commands go to the **focused terminal**:

```bash
# Without terminal specification
/agent redteam_agent               # Changes FOCUSED terminal's agent
Scan target.com                    # Sent to FOCUSED terminal
/cost                              # Shows FOCUSED terminal's cost

# With terminal specification (overrides focus)
T2:/agent blueteam_agent           # Changes T2 regardless of focus
T3:Scan target.com                 # Sent to T3 regardless of focus
/cost t4                           # Shows T4 cost regardless of focus
```

### Advanced Terminal Command Patterns

**Pattern 1: Round-Robin Distribution**
```bash
# Distribute tasks evenly
T1:Scan ports 1-10000
T2:Scan ports 10001-20000
T3:Scan ports 20001-30000
T4:Scan ports 30001-65535
```

**Pattern 2: Role-Based Commands**
```bash
# Configure terminals for specific roles
T1:/agent redteam_agent
T2:/agent blueteam_agent
T3:/agent dfir_agent
T4:/agent reporting_agent

# Send role-appropriate prompts
T1:Identify attack vectors
T2:Analyze defensive gaps
T3:Track forensic artifacts
T4:Document all findings
```

**Pattern 3: Sequential with Explicit References**
```bash
T1:Enumerate targets on network
# Wait for T1...
T2:Scan the targets identified by T1
# Wait for T2...
T3:Test vulnerabilities found by T2
# Wait for T3...
T4:Generate report from T1, T2, T3 findings
```

**Pattern 4: Parallel Setup, Sequential Execution**
```bash
# Setup all terminals
T1:/agent redteam_agent
T2:/agent redteam_agent
T3:/agent bug_bounter_agent
T4:/agent bug_bounter_agent

# Execute parallel phase
Execute reconnaissance on target.com all

# Wait for completion, then sequential
T3:Analyze red team findings for additional bugs
T4:Validate findings from T3
```

---

## Layout and UI Considerations

The TUI dynamically adjusts terminal layouts based on the number of active terminals.

### Layout Modes

#### 1. Single Terminal Layout (1 Terminal)

**Display**: Full-width terminal occupies entire workspace

**Dimensions**: 100% width, maximum vertical space

**Visual**: 
```
┌───────────────────────────────────────┐
│                                       │
│                                       │
│            Terminal 1 (T1)            │
│              Full Screen              │
│                                       │
│                                       │
└───────────────────────────────────────┘
```

**Best For**:
- Single-agent workflows
- Detailed analysis requiring maximum space
- Report generation
- Learning and experimentation
- Deep focus on one task

**Shortcuts**:
- `Ctrl+T` - Toggle fullscreen mode (if supported)

#### 2. Split Terminal Layout (2 Terminals)

**Display**: Side-by-side split

**Dimensions**: 50% width each, full height

**Visual**:
```
┌──────────────────┬──────────────────┐
│                  │                  │
│                  │                  │
│   Terminal 1     │   Terminal 2     │
│      (T1)        │      (T2)        │
│                  │                  │
│                  │                  │
└──────────────────┴──────────────────┘
```

**Best For**:
- Comparative analysis (Red vs Blue)
- Model comparison
- Before/after validation
- Master-worker patterns

#### 3. Triple Terminal Layout (3 Terminals)

**Display**: Three-column vertical split

**Dimensions**: ~33% width each, full height

**Visual**:
```
┌────────┬────────┬────────┐
│        │        │        │
│   T1   │   T2   │   T3   │
│        │        │        │
│        │        │        │
└────────┴────────┴────────┘
```

**Best For**:
- Three-phase workflows
- Balanced multi-agent analysis
- Mid-complexity assessments

#### 4. Quad Terminal Layout (4 Terminals)

**Display**: 2×2 grid

**Dimensions**: 50% width × 50% height each

**Visual**:
```
┌──────────────────┬──────────────────┐
│                  │                  │
│   Terminal 1     │   Terminal 2     │
│      (T1)        │      (T2)        │
├──────────────────┼──────────────────┤
│                  │                  │
│   Terminal 3     │   Terminal 4     │
│      (T3)        │      (T4)        │
└──────────────────┴──────────────────┘
```

**Best For**:
- Full team operations
- Maximum parallelization
- Preconfigured team execution
- Comprehensive assessments

**Default**: Activated automatically with Team selection

#### 5. Scrollable Layout (5+ Terminals)

**Display**: 2-column grid with vertical scrolling

**Dimensions**: 50% width, variable height, scrollable

**Visual**:
```
┌──────────────────┬──────────────────┐
│       T1         │       T2         │
├──────────────────┼──────────────────┤
│       T3         │       T4         │
├──────────────────┼──────────────────┤
│       T5         │       T6         │  ↓ Scroll
├──────────────────┼──────────────────┤
│       T7         │       T8         │  ↓ Down
└──────────────────┴──────────────────┘
```

**Best For**:
- Large-scale testing campaigns
- Custom advanced workflows
- Experimental configurations

**Navigation**:
- Scrollbar appears on right side
- Mouse scroll or keyboard navigation
- `Ctrl+N` / `Ctrl+B` still cycle through terminals

### Layout Optimization Tips

**Visibility**:
- More terminals = less space per terminal
- Consider horizontal monitor layouts for 4+ terminals
- Use external monitors for 5+ terminal workflows

**Terminal Focus**:
- Focused terminal is highlighted
- Keyboard shortcuts work across any layout
- Mouse click anywhere in terminal to focus

**Dynamic Resizing**:
- Resize terminal window to see layout adjust
- Toggle sidebar (`Ctrl+S`) for more workspace
- Fullscreen mode for maximum focus (if supported)

---

## Best Practices and Optimization

### Strategic Planning

**Before Starting Parallel Work**:

1. **Define Objectives**: What are you trying to achieve?
2. **Choose Pattern**: Which execution pattern fits best?
3. **Select Team**: Does a preconfigured team match your needs?
4. **Plan Context Strategy**: Split or shared context?
5. **Estimate Costs**: How much will this parallel execution cost?

### Terminal Management Best Practices

**1. Start Small, Scale Up**
- Begin with 1-2 terminals
- Add more only when parallelization provides clear benefit
- Don't default to 4 terminals for simple tasks

**2. Use Preconfigured Teams**
- Leverage built-in teams instead of manual configuration
- Teams are optimized for common workflows
- Save time on setup

**3. Name Your Sessions**
- Use descriptive filenames for saved sessions:
```bash
/save 2025-11-27_webapp_pentest_team3.json
/save target_company_recon_phase.json
/save bug_bounty_validation_session.json
```

**4. Monitor Costs Per Terminal**
```bash
/cost            # Current terminal
/cost t1         # Terminal 1 specifically
/cost t2         # Terminal 2 specifically
/cost all        # All terminals aggregate
```

**5. Clear Between Tasks**
- Reset terminals between unrelated tasks:
```bash
/flush all       # Clear all conversation histories
/clear           # Clear visual output (current terminal)
```

**6. Document Terminal Roles**
- Add role comments in first prompt:
```bash
T1> [RECON] Enumerate target.com infrastructure
T2> [EXPLOIT] Test for authentication bypass
T3> [VALIDATE] Confirm findings from T2
T4> [REPORT] Document all findings
```

**7. Use Broadcast Wisely**
- Broadcast is **expensive** (4× cost)
- Use only when you need multiple perspectives
- Not efficient for independent tasks

**8. Leverage Terminal Independence**
- Different testing phases
- Completely separate tasks
- Long-running operations in background terminals

### Performance Optimization

**Model Selection Strategy**:

| Task Type | Recommended Model | Rationale |
|-----------|------------------|-----------|
| Reconnaissance | `alias1` | Sufficient for enumeration |
| Exploitation | `alias1` | Powerful reasoning for complex exploits |
| Validation | `alias1` | Fast confirmation, cost-effective |
| Documentation | `alias1` | Quality writing, structured output |
| Analysis | `alias1` | Deep reasoning capabilities |

**Token Management**:

Monitor token usage to optimize costs:
- Use `/context` command (CAI-Pro) to see token breakdown
- Optimize prompts for brevity
- Use `/clear` to reset context when not needed
- Load only relevant ICL context (don't load unrelated sessions)

**Output Control**:
```bash
# Concise output for automated workflows
CAI_BRIEF="true"

# Disable streaming for faster processing
CAI_STREAM="false"

# Truncate large outputs
CAI_CTX_TRUNC="true"
```

### Workflow Optimization

**Sequential Optimization**:
- Minimize wait time between stages
- Prepare next prompts while agents work
- Use terminal-specific commands to queue next steps

**Parallel Optimization**:
- Ensure tasks are truly independent
- Balance workload across terminals
- Monitor for bottlenecks in Queue tab

**Hybrid Optimization**:
- Start with parallel discovery
- Switch to sequential for dependent tasks
- End with parallel validation

---

## Cost Management in Parallel Workflows

Parallel execution multiplies costs - strategic management is essential.

### Cost Tracking

**Real-Time Monitoring** (TUI):
- **Stats Tab**: View costs across all terminals
- **Status Bar**: Shows cumulative session cost
- **Per-Terminal**: Check individual terminal costs

**CLI Cost Commands**:
```bash
/cost              # Current focused terminal
/cost t1           # Terminal 1 cost
/cost t2           # Terminal 2 cost
/cost all          # Aggregate all terminals
```

### Cost Calculation

**Broadcast Cost Impact**:
```
Single prompt: $0.02
Broadcast to 4 terminals: $0.02 × 4 = $0.08
10 broadcasts: $0.80

Strategy: Use broadcast sparingly, only for critical multi-perspective needs
```

**Parallel Independent Tasks**:
```
4 terminals, different tasks
T1: $0.15
T2: $0.12
T3: $0.20
T4: $0.18
Total: $0.65

Strategy: Efficient if tasks would be done anyway
```

### Cost Optimization Strategies

**1. Set Price Limits**
```bash
# Environment variable
CAI_PRICE_LIMIT="5.0"      # Stop at $5

# Runtime configuration
/config set 18 "10.0"      # Increase limit to $10
```

**2. Use Cost-Effective Models**
```bash
# Configure all terminals with alias1
T1: /model alias1
T2: /model alias1
T3: /model alias1
T4: /model alias1
```

**3. Optimize Prompt Length**
- Be concise in prompts
- Avoid repeating context unnecessarily
- Use references instead of full text (e.g., "the findings from T1" vs. pasting full findings)

**4. Strategic Terminal Use**
```bash
# Good: Use 2 terminals for 2 distinct tasks
T1: Web application assessment
T2: Network infrastructure assessment
# Cost: 2× base cost, but necessary for coverage

# Bad: Use 4 terminals for 1 simple task
T1, T2, T3, T4: All doing basic port scanning
# Cost: 4× base cost, redundant work
```

**5. Clear Context Regularly**
```bash
# After completing a major phase
/flush all               # Clear histories
# Start next phase with clean context
```

**6. Avoid Redundant Work**
- Don't broadcast tasks that can be split
- Don't repeat identical prompts across terminals
- Share results instead of re-running analysis

### Cost Alerts and Controls

**Price Limit Behavior**:
When `CAI_PRICE_LIMIT` is reached:
- Warning displayed to user
- Further prompts blocked until limit increased
- CLI commands still available (`/cost`, `/config`, etc.)

**Increasing Limit During Session**:
```bash
# Check current limit
/config

# Increase limit (item #18 is CAI_PRICE_LIMIT)
/config set 18 "15.0"

# Verify
/cost all
```

---

## Advanced Coordination Patterns

### Pattern: Progressive Refinement

**Concept**: Each terminal progressively refines the analysis from previous terminals.

**Example: Vulnerability Deep Dive**
```bash
# Terminal 1: Broad Discovery
T1: bug_bounter_agent
T1> Scan target.com for all vulnerability types

# Terminal 2: Focus on Critical
T2: redteam_agent
T2> Review T1's findings. Focus on the 3 most critical vulnerabilities.

# Terminal 3: Exploit Development
T3: redteam_agent
T3> Based on T2's prioritization, develop proof-of-concept exploits.

# Terminal 4: Comprehensive Report
T4: reporting_agent
T4> Create detailed report from T1 (discovery), T2 (prioritization), T3 (exploitation).
```

**Benefits**:
- Each stage adds value
- Progressive focus narrows to high-value targets
- Final output is refined and actionable

### Pattern: Split Context Analysis

**Concept**: Analyze the same scenario from multiple independent perspectives to identify blind spots.

**Example: Red Team vs Blue Team**
```bash
# Setup
T1,T2: redteam_agent (split context)
T3,T4: blueteam_agent (split context)

# Broadcast same scenario
Analyze the security of this authentication system: [details] all

# Results:
T1: Offensive perspective #1 (independent reasoning)
T2: Offensive perspective #2 (different attack vectors)
T3: Defensive perspective #1 (independent controls)
T4: Defensive perspective #2 (different mitigations)

# Analysis:
# Compare T1 & T2 for offensive coverage
# Compare T3 & T4 for defensive depth
# Identify gaps where offense found issues defense missed
```

**Benefits**:
- No cognitive bias between independent agents
- Comprehensive coverage
- Identifies blind spots through diversity

### Pattern: Hypothesis Testing

**Concept**: Test multiple competing hypotheses in parallel.

**Example: Root Cause Analysis**
```bash
T1> Hypothesis: Vulnerability is due to input validation failure. Test this theory.
T2> Hypothesis: Vulnerability is due to authentication bypass. Test this theory.
T3> Hypothesis: Vulnerability is due to authorization flaw. Test this theory.
T4> Hypothesis: Vulnerability is due to configuration error. Test this theory.

# Results:
T1: No evidence of input validation issue
T2: Strong evidence of authentication bypass
T3: Some evidence of authorization issues  
T4: No configuration errors found

# Conclusion: Primary issue is authentication bypass (T2), secondary issue is authorization (T3)
```

**Benefits**:
- Parallel exploration of solution space
- Efficient hypothesis elimination
- Data-driven conclusions

### Pattern: Distributed Workload

**Concept**: Divide large workload into chunks, process in parallel, merge results.

**Example: Large-Scale Network Scan**
```bash
# Divide target space
T1> Scan 192.168.1.0/26   (IPs 1-63)
T2> Scan 192.168.1.64/26  (IPs 64-127)
T3> Scan 192.168.1.128/26 (IPs 128-191)
T4> Scan 192.168.1.192/26 (IPs 192-255)

# Wait for completion

# Merge and analyze
T1> /save t1_results.json
T2> /save t2_results.json
T3> /save t3_results.json
T4> /save t4_results.json

# Consolidation (use separate terminal or manual aggregation)
<Manually review or use external tool to merge JSON results>
```

**Benefits**:
- Linear speedup (4× faster with 4 terminals)
- Scalable to large targets
- Even load distribution

### Pattern: Pipeline with Checkpoints

**Concept**: Sequential workflow with validation checkpoints.

**Example: Secure Development Lifecycle Simulation**
```bash
# Stage 1: Development (T1)
T1: thought_agent
T1> Design a secure authentication system for a web application

# Checkpoint 1: Security Review (T2)
T2: blueteam_agent
T2> Review the authentication design from T1. Identify security gaps.

# Stage 2: Refinement (T3)
T3: cybersecurity_engineer
T3> Refine the T1 design based on T2's security feedback.

# Checkpoint 2: Penetration Test (T4)
T4: redteam_agent
T4> Attempt to break the refined authentication system from T3.

# Final Output: Secure design that passed both review and penetration testing
```

**Benefits**:
- Quality gates ensure high standards
- Multi-perspective validation
- Iterative improvement
- Professional-grade outputs

---

## Troubleshooting

### Common Issues and Solutions

#### Issue: Terminal Not Responding

**Symptoms**:
- Terminal stuck in busy state
- No output appearing
- Spinner continues indefinitely

**Solutions**:
1. **Wait**: Some operations genuinely take time (large scans, complex analysis)
2. **Check Status**: Look for activity indicators
3. **Cancel**: Press `Ctrl+C` to interrupt
4. **Check MCP**: If using MCP tools, run `/mcp status`
5. **Restart Terminal**:
```bash
/flush         # Clear history
/clear         # Clear output
# Send new prompt to test
```

#### Issue: Team Selection Not Working

**Symptoms**:
- Agent dropdowns don't update after selecting team
- Terminals still show old agents
- Team button clicked but no change

**Solutions**:
1. **Verify Sidebar**: Ensure sidebar is open (`Ctrl+S`)
2. **Click Again**: Try clicking team button a second time
3. **Manual Update**: Manually update agent dropdowns if issue persists
4. **Check Version**: Run `cai --version` and report bug if on latest

#### Issue: Broadcast Commands Not Executing on All Terminals

**Symptoms**:
- `all` flag doesn't broadcast to all terminals
- Only one or some terminals execute

**Solutions**:
1. **Verify Syntax**: Ensure `all` is at the end: `command all`
2. **Check Terminal States**: Ensure all terminals are not busy
3. **Try Individual**: Use terminal-specific commands as workaround:
```bash
T1:command
T2:command
T3:command
T4:command
```

#### Issue: Cost Tracking Incorrect

**Symptoms**:
- Cost numbers seem wrong
- Costs don't update
- Discrepancy between displayed cost and expected

**Solutions**:
1. **Check All Terminals**: Run `/cost all` for complete breakdown
2. **Verify Model Pricing**: Check `/model` for current model costs
3. **Multiple Terminals**: Confirm you're not forgetting terminals 2-4 costs
4. **Save and Review**: Save session and review cost data offline:
```bash
/save cost_review_session.json
# Review JSON file to see detailed cost breakdown
```

#### Issue: Layout Not Adjusting

**Symptoms**:
- Terminal layout doesn't change when adding/removing terminals
- Display is misaligned
- Terminals overlapping

**Solutions**:
1. **Resize Window**: Drag terminal window to trigger layout recalculation
2. **Toggle Fullscreen**: Press `Ctrl+T` and back (if supported)
3. **Restart TUI**: Close and restart CAI TUI if issue persists

#### Issue: High Cost from Parallel Execution

**Symptoms**:
- Cost increasing faster than expected
- Price limit hit quickly

**Solutions**:
1. **Review Strategy**: Are you broadcasting unnecessarily?
2. **Check All Terminals**: Run `/cost all` to see breakdown
3. **Optimize Models**:
```bash
# Ensure all terminals use alias1
T1:/model alias1
T2:/model alias1
```
4. **Reduce Parallelization**: Close unnecessary terminals with `Ctrl+E`
5. **Set Lower Limit**: Reduce `CAI_PRICE_LIMIT` for tighter control

#### Issue: Agents Not Collaborating

**Symptoms**:
- Agents ignore each other's findings
- No context sharing happening

**Expected Behavior**:
- By design, agents have **split context** by default
- Manual context sharing is required

**Solutions**:
1. **Explicit References**: Tell agents to reference other terminals:
```bash
T2> Review the findings from T1 and build upon them
```
2. **Session Sharing**: Save and load sessions:
```bash
T1> /save t1_findings.json
T2> /load t1_findings.json
```
3. **Use Merge (CLI)**:
```bash
/merge   # In CLI parallel mode
```

---

## Real-World Use Cases

### Use Case 1: Bug Bounty Program Attack

**Objective**: Maximize vulnerability discovery on a bug bounty target.

**Team Selection**: Team #10 (4 bug_bounter agents)

**Strategy**: Parallel Independent Tasks pattern

**Workflow**:
```bash
# Setup
<Select Team #10>

# Divide attack surface
T1> Focus on authentication and session management vulnerabilities
T2> Focus on input validation and injection attacks (SQL, XSS, etc.)
T3> Focus on business logic flaws and authorization issues
T4> Focus on API security and rate limiting bypasses

# Execute in parallel (all terminals work simultaneously)

# Results after 1 hour:
T1: Found session fixation vulnerability
T2: Identified XSS in comment field, SQL injection in search
T3: Discovered IDOR in user profile API
T4: Found rate limiting bypass on password reset

# Consolidation
T1> /save bug_bounty_session_findings.json

# Total: 6 distinct vulnerabilities in parallel execution time
```

**Outcome**: 4× throughput compared to single-agent approach.

### Use Case 2: Penetration Testing Engagement

**Objective**: Comprehensive penetration test with offense and defense perspectives.

**Team Selection**: Team #3 (2 redteam + 2 blueteam)

**Strategy**: Parallel execution with monitoring pattern

**Workflow**:
```bash
# Setup
<Select Team #3: 2 Red + 2 Blue>

# Phase 1: Parallel Reconnaissance
Enumerate all services, ports, and technologies on target.local all

# Wait for completion

# Phase 2: Offensive + Defensive Analysis
T1> [RED] Develop attack plan based on Phase 1 findings
T2> [RED] Execute attacks on web application
T3> [BLUE] Monitor T1 and T2, evaluate defensive visibility
T4> [BLUE] Document security controls and gaps

# Phase 3: Reporting
T4> Generate comprehensive report from all perspectives:
    - Red team attack vectors and exploits
    - Blue team visibility and control analysis
    - Recommendations for remediation
```

**Outcome**: Balanced purple team assessment with actionable recommendations.

### Use Case 3: CTF Competition

**Objective**: Solve CTF challenges quickly in a competition.

**Team Selection**: Team #1 (2 redteam + 2 bug_bounter)

**Strategy**: Swarm pattern with dynamic handoffs

**Workflow**:
```bash
# Setup
<Select Team #1>

# Parallel challenge solving
T1> [CTF Challenge 1] Solve "Web Exploit Easy"
T2> [CTF Challenge 2] Solve "Reverse Engineering Medium"
T3> [CTF Challenge 3] Solve "Cryptography Hard"
T4> [CTF Challenge 4] Solve "OSINT Challenge"

# When terminal completes, immediately assign new challenge
T1> [COMPLETED Challenge 1] → [NEW] Solve "Binary Exploitation"
T2> [STUCK] → Hand off to T4 for fresh perspective
T3> [COMPLETED Challenge 3] → [NEW] Solve "Forensics Challenge"
T4> [Helping T2] Review RE challenge from T2's work

# Dynamic coordination continues until all flags captured
```

**Outcome**: Maximum parallelization, faster flag collection, higher competition ranking.

### Use Case 4: Incident Response

**Objective**: Analyze security incident with forensics, analysis, and reporting.

**Team Selection**: Custom team (dfir + network_analyzer + thought + reporting)

**Strategy**: Sequential pipeline with checkpoints

**Workflow**:
```bash
# Setup custom team
T1: /agent dfir_agent
T2: /agent network_security_analyzer_agent
T3: /agent thought_agent
T4: /agent reporting_agent

# Phase 1: Evidence Collection (T1)
T1> Analyze the provided memory dump and disk image for IoCs

# Phase 2: Network Analysis (T2)
T2> Review the network traffic logs from T1's timeframe. Identify C2 communication.

# Phase 3: Attribution and Analysis (T3)
T3> Based on T1's forensic findings and T2's network analysis, determine:
    - Attack vector
    - Threat actor profile
    - Timeline of compromise
    - Scope of breach

# Phase 4: Executive Report (T4)
T4> Generate executive incident report summarizing:
    - T1's forensic evidence
    - T2's network indicators
    - T3's analysis and attribution
    - Recommended response actions
```

**Outcome**: Comprehensive incident response with expert analysis and executive-ready report.

### Use Case 5: Security Code Review

**Objective**: Multi-perspective code security analysis.

**Team Selection**: Team #5 (red + blue + retester + bug)

**Strategy**: Split context analysis pattern

**Workflow**:
```bash
# Setup
<Select Team #5>

# Broadcast code for independent review
Review this authentication implementation for security issues: [paste code] all

# Results from independent perspectives:
T1 (redteam):     "Found potential SQL injection in user lookup"
T2 (blueteam):    "Insufficient input validation and logging"
T3 (retester):    "Confirmed SQLi is exploitable, found timing attack vector"
T4 (bug_bounter): "Identified session fixation and weak password requirements"

# Consolidation
<Human reviewer consolidates findings>
# Total: 5 distinct issues from 4 different perspectives
```

**Outcome**: Comprehensive security review with diverse expert perspectives.

---

## Summary

CAI's parallel execution capabilities provide powerful tools for accelerating security workflows:

**Key Takeaways**:
- ✅ **Two Modes**: CLI parallelization (`CAI_PARALLEL`) for batch work, TUI multi-terminals for interactive coordination
- ✅ **11 Teams**: Preconfigured teams cover most security workflows
- ✅ **6 Patterns**: Broadcast, Sequential, Independent, Monitoring, Divide-and-Conquer, Phased
- ✅ **Context Control**: Split context for diversity, shared for collaboration
- ✅ **Cost Awareness**: Parallelization multiplies costs - use strategically
- ✅ **Terminal Commands**: Control any terminal from any location
- ✅ **Queue Management**: Automate workflows with queue files
- ✅ **Dynamic Layouts**: UI adapts from 1 to 5+ terminals

**Getting Started**:
1. Start with Team #1 (2 red + 2 bug) for general use
2. Use broadcast (`all`) sparingly
3. Monitor costs with `/cost all`
4. Save sessions for complex workflows
5. Experiment with patterns to find what works for you

**Next Steps**:
- [TUI Getting Started](tui/getting_started.md) - Basic TUI navigation
- [Teams and Parallel Execution](tui/teams_and_parallel_execution.md) - Team-specific guide
- [Commands Reference](tui/commands_reference.md) - Complete command documentation
- [Advanced Features](tui/advanced_features.md) - MCP, ICL, Guardrails

---

*Last updated: November 2025 | CAI Framework v0.6+*

**Questions or Feedback?** Visit [CAI GitHub Issues](https://github.com/aliasrobotics/cai/issues) or contact [Alias Robotics](https://aliasrobotics.com).

