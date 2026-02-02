# Parallel Agents YAML Configuration Format

**Last Updated:** February 2026  
**CAI Version:** 0.6+

---

## Overview

CAI supports running multiple agents in parallel through YAML configuration files. This enables distributed reconnaissance, multi-perspective analysis, and coordinated security workflows.

---

## ✅ Correct YAML Format

### Basic Structure

```yaml
parallel_agents:
  - name: <agent_type>         # Required: Agent to instantiate
    model: <model_name>         # Optional: Model override
    prompt: <initial_prompt>    # Optional: Initial prompt
    auto_run: true|false        # Optional: Auto-execute (default: true)
```

### Complete Example

```yaml
# reconnaissance_team.yaml
parallel_agents:
  - name: redteam_agent
    model: alias1
    prompt: "Enumerate subdomains for example.com using passive techniques"
    description: "Subdomain enumeration"
    auto_run: true
  
  - name: network_security_analyzer_agent
    model: alias1
    prompt: "Scan example.com for open ports 80, 443, 8080, 8443"
    description: "Port scanning"
    auto_run: true
  
  - name: bug_bounter_agent
    model: alias1
    prompt: "Check for bug bounty-eligible vulnerabilities"
    description: "Bug bounty analysis"
    auto_run: true
```

**Execute:**
```bash
cai --yaml reconnaissance_team.yaml
```

---

## 📋 Field Reference

### Required Fields

| Field | Description | Example |
|-------|-------------|---------|
| `name` | Agent type to instantiate | `redteam_agent` |

### Optional Fields

| Field | Type | Description | Default |
|-------|------|-------------|---------|
| `model` | string | Model override | Global model |
| `prompt` | string | Initial prompt to execute | None |
| `team` | string | Team/group label (TUI) | None |
| `auto_run` | boolean | Auto-execute on startup | `true` |
| `unified_context` | boolean | Share history (CLI) | `false` |
| `env` | object | Environment variables | `{}` |
| `description` | string | Human-readable description | None |

---

## 🎯 Common Patterns

### Pattern 1: Distributed Reconnaissance

Split reconnaissance across multiple agents:

```yaml
# recon_distributed.yaml
parallel_agents:
  - name: redteam_agent
    model: alias1
    prompt: "Enumerate subdomains for A-M range of target.com"
  
  - name: redteam_agent
    model: alias1
    prompt: "Enumerate subdomains for N-Z range of target.com"
  
  - name: network_security_analyzer_agent
    model: alias1
    prompt: "Scan all discovered hosts for services"
```

**Benefits:**
- Faster coverage through parallel execution
- Each agent focuses on specific scope
- Results aggregate automatically

---

### Pattern 2: Red vs Blue Analysis

Compare offensive and defensive perspectives:

```yaml
# redblue_analysis.yaml
parallel_agents:
  - name: redteam_agent
    model: alias1
    prompt: "Identify attack vectors for webapp.example.com"
  
  - name: blueteam_agent
    model: alias1
    prompt: "Analyze defensive posture of webapp.example.com"
```

**Benefits:**
- Simultaneous offensive and defensive analysis
- Compare findings from different perspectives
- Identify gaps in security posture

---

### Pattern 3: Multi-Model Comparison

Test the same prompt across different models:

```yaml
# model_comparison.yaml
parallel_agents:
  - name: redteam_agent
    model: alias1
    prompt: "Find SQL injection vulnerabilities in login.php"
  
  - name: bug_bounter_agent
    model: alias1
    prompt: "Test for authentication bypass in login.php"
  
  - name: network_security_analyzer_agent
    model: alias1
    prompt: "Analyze security headers in login.php"
```

**Benefits:**
- Compare model capabilities
- Cross-validate findings
- Identify model-specific strengths

---

## 🔧 Advanced Features

### Shared Configuration

Define common settings for all agents:

```yaml
# Shared defaults
shared:
  model: alias1
  auto_run: true
  prompt: "Analyze security of example.com"

parallel_agents:
  - name: redteam_agent
    # Inherits shared settings
  
  - name: blueteam_agent
    prompt: "Analyze defenses of example.com"  # Overrides shared
  
  - name: bug_bounter_agent
    model: alias1  # Overrides shared model
```

---

### Environment Variables Per Agent

Inject environment variables for each agent:

```yaml
parallel_agents:
  - name: blueteam_agent
    prompt: "Defend the container from inside"
    env:
      CTF_INSIDE: true
      DEFENSIVE_MODE: enabled
  
  - name: redteam_agent
    prompt: "Attack the container from outside"
    env:
      CTF_INSIDE: false
      AGGRESSIVE_SCAN: true
```

---

### Unified Context Mode

Share message history across agents (CLI only):

```yaml
parallel_agents:
  - name: redteam_agent
    prompt: "Enumerate vulnerabilities in target.com"
    unified_context: true  # Shares history
  
  - name: bug_bounter_agent
    prompt: "Validate findings from previous scan"
    unified_context: true  # Sees redteam's findings
```

**Note:** `unified_context: true` makes agents share their conversation history in CLI mode.

---

## 🚨 Common Mistakes

### ❌ Wrong Root Key

```yaml
agents:           # ❌ WRONG - Should be 'parallel_agents:'
  - name: redteam_agent
```

**Fix:**
```yaml
parallel_agents:  # ✅ CORRECT
  - name: redteam_agent
```

---

### ❌ Missing Agent Name

```yaml
parallel_agents:
  - model: alias1          # ❌ WRONG - Missing 'name' field
    prompt: "Scan target"
```

**Fix:**
```yaml
parallel_agents:
  - name: redteam_agent    # ✅ CORRECT
    model: alias1
    prompt: "Scan target"
```

---

### ❌ Using Legacy Field Names

```yaml
parallel_agents:
  - agent_type: redteam_agent      # ❌ Legacy field
    initial_prompt: "Scan target"  # ❌ Legacy field
```

**Fix:**
```yaml
parallel_agents:
  - name: redteam_agent            # ✅ Current field
    prompt: "Scan target"          # ✅ Current field
```

> **Note:** CAI v0.6+ includes backward compatibility for legacy formats, but using the correct format is recommended.

---

## 🔍 Troubleshooting

### No Agents Loaded

**Symptoms:**
```
No parallel agent definitions found
```

**Causes:**
- Wrong root key (use `parallel_agents:` not `agents:`)
- Missing `name` field in agent entries
- Invalid YAML syntax

**Solution:**
```bash
# Validate YAML syntax
python3 -c "import yaml; yaml.safe_load(open('your_file.yaml'))"

# Check for required fields
grep -E "^parallel_agents:|  - name:" your_file.yaml
```

---

### Agents Not Auto-Running

**Symptoms:** Agents load but don't execute their prompts

**Cause:** `auto_run: false` is set

**Solution:**
```yaml
parallel_agents:
  - name: redteam_agent
    prompt: "Scan target"
    auto_run: true         # ✅ Explicitly enable
```

---

## 💡 Best Practices

### 1. Use Descriptive Descriptions

```yaml
parallel_agents:
  - name: redteam_agent
    description: "Subdomain enumeration"   # ✅ Good
    prompt: "Enumerate subdomains"
```

### 2. Limit Parallel Agents

```yaml
# ✅ Good: 2-4 agents for most tasks
parallel_agents:
  - name: redteam_agent
  - name: bug_bounter_agent
  - name: network_security_analyzer_agent
```

**Why:** More agents = higher cost and complexity

### 3. Use Shared Configuration

```yaml
# ✅ Good: DRY principle
shared:
  model: alias1
  auto_run: true

parallel_agents:
  - name: redteam_agent
  - name: blueteam_agent
```

### 4. Set Cost Limits

```bash
# Always set a price limit when using parallel agents
CAI_PRICE_LIMIT=5.0 cai --yaml recon_team.yaml
```

---

## 📚 Related Documentation

- [Environment Variables](../../environment_variables.md) - CAI_PARALLEL settings
- [Commands Reference](commands.md) - `/parallel` command
- [Teams & Parallel Execution (TUI)](../../tui/teams_and_parallel_execution.md) - TUI parallel features

---

## ⚠️ Backward Compatibility Note

CAI v0.6+ automatically normalizes legacy formats for backward compatibility:

| Legacy Format | Current Format | Status |
|---------------|----------------|--------|
| `agents:` | `parallel_agents:` | ✅ Auto-converted |
| `agent_type:` | `name:` | ✅ Auto-converted |
| `initial_prompt:` | `prompt:` | ✅ Auto-converted |

**Recommendation:** Use the current format for new configurations.

---

**For questions or issues:**
- Documentation: https://aliasrobotics.github.io/cai/
- Repository: https://gitlab.com/aliasrobotics/alias_research/cai

