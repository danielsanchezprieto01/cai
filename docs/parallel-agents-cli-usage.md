# Parallel Agents CLI Usage Guide

**Last Updated:** February 2026  
**CAI Version:** 0.6+

---

## Quick Start

### 1. Create Configuration File

```yaml
# my_agents.yaml
parallel_agents:
  - name: redteam_agent
    model: alias1
    prompt: "Scan example.com for vulnerabilities"
  
  - name: network_security_analyzer_agent
    model: alias1
    prompt: "Enumerate services on example.com"
```

### 2. Load and Execute

```bash
cai --yaml my_agents.yaml
```

**What happens:**
- Both agents start automatically
- Each executes its configured prompt in parallel
- Results display in the terminal

---

## CLI Commands

### Loading Agents

```bash
# Load from YAML file
cai --yaml path/to/agents.yaml

# With model override
cai --yaml agents.yaml --model alias1

# With cost limit
CAI_PRICE_LIMIT=5.0 cai --yaml agents.yaml
```

---

### Managing Parallel Agents

Inside a CAI session:

```bash
# List loaded parallel agents
/parallel list

# View parallel configuration
/env | grep PARALLEL

# Merge agent histories
/parallel merge

# Clear parallel configuration
/parallel clear

# Load new configuration
/parallel load new_agents.yaml
```

---

### Monitoring Execution

```bash
# View agent status
CAI> /parallel list
[P1] redteam_agent [alias1] - Enumerate subdomains...
[P2] network_security_analyzer_agent [alias1] - Scan services...
[P3] bug_bounter_agent [alias1] - Check vulnerabilities...

# Check environment variables
CAI> /env | grep PARALLEL
CAI_PARALLEL=3
CAI_PARALLEL_AGENTS=redteam_agent,network_security_analyzer_agent,bug_bounter_agent

# View costs per agent
CAI> /cost
Total session cost: $0.45
  - P1 (redteam_agent): $0.15
  - P2 (network_security_analyzer_agent): $0.20
  - P3 (bug_bounter_agent): $0.10
```

---

## Usage Examples

### Example 1: Reconnaissance Workflow

```yaml
# recon_workflow.yaml
parallel_agents:
  - name: redteam_agent
    model: alias1
    prompt: "Enumerate subdomains for target.com using passive techniques"
  
  - name: redteam_agent
    model: alias1
    prompt: "Perform port scan on target.com ports 1-1000"
  
  - name: network_security_analyzer_agent
    model: alias1
    prompt: "Analyze discovered services for known vulnerabilities"
```

```bash
cai --yaml recon_workflow.yaml
```

---

### Example 2: Multi-Perspective Analysis

```yaml
# perspective_analysis.yaml
parallel_agents:
  - name: redteam_agent
    model: alias1
    prompt: "Identify attack vectors for API at api.example.com"
  
  - name: blueteam_agent
    model: alias1
    prompt: "Analyze defensive controls for API at api.example.com"
  
  - name: bug_bounter_agent
    model: alias1
    prompt: "Find bug bounty-eligible issues in api.example.com"
```

```bash
cai --yaml perspective_analysis.yaml
```

---

### Example 3: Model Comparison

```yaml
# multi_agent_test.yaml
parallel_agents:
  - name: redteam_agent
    model: alias1
    prompt: "Find XSS vulnerabilities in login form"
  
  - name: bug_bounter_agent
    model: alias1
    prompt: "Test for SQL injection in login form"
  
  - name: network_security_analyzer_agent
    model: alias1
    prompt: "Analyze authentication mechanisms"
```

```bash
cai --yaml model_test.yaml
```

---

## Advanced Usage

### Shared Configuration

```yaml
# shared_config.yaml
shared:
  model: alias1
  auto_run: true
  prompt: "Analyze security of example.com"

parallel_agents:
  - name: redteam_agent
    # Uses shared settings
  
  - name: blueteam_agent
    prompt: "Defensive analysis of example.com"  # Override prompt
  
  - name: bug_bounter_agent
    model: alias1  # Override model
```

---

### Environment Variables

```yaml
# env_config.yaml
parallel_agents:
  - name: blueteam_agent
    prompt: "Defend container from inside"
    env:
      CTF_INSIDE: true
      DEFENSIVE_MODE: enabled
  
  - name: redteam_agent
    prompt: "Attack container from outside"
    env:
      CTF_INSIDE: false
      AGGRESSIVE_SCAN: true
```

---

### Unified Context Mode

```yaml
# unified_context.yaml
parallel_agents:
  - name: redteam_agent
    prompt: "Find vulnerabilities in target.com"
    unified_context: true  # Agent 1 runs first
  
  - name: bug_bounter_agent
    prompt: "Validate and prioritize findings"
    unified_context: true  # Agent 2 sees Agent 1's work
```

**Note:** Agents with `unified_context: true` share their conversation history.

---

## Environment Variables

### CAI_PARALLEL

Number of parallel agents to run:

```bash
# Set via environment
CAI_PARALLEL=3 cai

# Set via configuration file (auto-detected)
cai --yaml agents.yaml
```

### CAI_PARALLEL_AGENTS

Comma-separated list of agent names (auto-set when loading YAML):

```bash
CAI_PARALLEL_AGENTS=redteam_agent,blueteam_agent,bug_bounter_agent
```

---

## Performance Tips

### 1. Limit Number of Agents

```yaml
# ✅ Good: 2-4 agents
parallel_agents:
  - name: redteam_agent
  - name: bug_bounter_agent

# ❌ Avoid: Too many agents (high cost)
parallel_agents:
  - name: agent1
  - name: agent2
  # ... 10 more agents
```

### 2. Use Fast Models for Reconnaissance

```yaml
parallel_agents:
  - name: redteam_agent
    model: alias1
    prompt: "Enumerate subdomains"
```

### 3. Set Cost Limits

```bash
# Prevent runaway costs
CAI_PRICE_LIMIT=5.0 cai --yaml agents.yaml
```

### 4. Monitor Usage

```bash
# Check costs regularly
CAI> /cost

# View agent activity
CAI> /parallel list
```

---

## Troubleshooting

### Agents Not Starting

**Problem:** Agents load but don't execute

**Solution:**
```yaml
parallel_agents:
  - name: redteam_agent
    auto_run: true  # ✅ Ensure auto_run is enabled
    prompt: "Your prompt"  # ✅ Ensure prompt is set
```

---

### High Costs

**Problem:** Parallel execution is expensive

**Solutions:**
1. Reduce number of agents
2. Use faster/cheaper models
3. Set price limits

```bash
CAI_PRICE_LIMIT=2.0 cai --yaml agents.yaml
```

---

### Agents Not Sharing Context

**Problem:** Agents don't see each other's results

**Solution:**
```yaml
parallel_agents:
  - name: redteam_agent
    unified_context: true  # ✅ Enable for CLI
  
  - name: bug_bounter_agent
    unified_context: true  # ✅ Enable for all agents
```

---

## Best Practices

### ✅ DO

- Use 2-4 agents for most tasks
- Set `auto_run: true` for automation
- Use `description` field for clarity
- Set cost limits with `CAI_PRICE_LIMIT`
- Monitor costs with `/cost`

### ❌ DON'T

- Don't use too many parallel agents (high cost)
- Don't forget to set prompts if not using shared
- Don't skip `auto_run: true` for automation
- Don't ignore cost warnings

---

## Example Configurations

### Bug Bounty Workflow

```yaml
# bug_bounty.yaml
parallel_agents:
  - name: redteam_agent
    model: alias1
    prompt: "Find OWASP Top 10 vulnerabilities in target.com"
  
  - name: bug_bounter_agent
    model: alias1
    prompt: "Identify bug bounty-eligible issues in target.com"
  
  - name: network_security_analyzer_agent
    model: alias1
    prompt: "Scan for misconfigurations and exposed services"
```

### CTF Challenge

```yaml
# ctf_challenge.yaml
parallel_agents:
  - name: redteam_agent
    model: alias1
    prompt: "Analyze challenge binary for vulnerabilities"
    env:
      CTF_NAME: "hackthebox_challenge"
  
  - name: reverse_engineering_agent
    model: alias1
    prompt: "Reverse engineer the binary"
    env:
      CTF_NAME: "hackthebox_challenge"
```

### Security Assessment

```yaml
# security_assessment.yaml
shared:
  model: alias1
  prompt: "Comprehensive security assessment of corporate-webapp.com"

parallel_agents:
  - name: redteam_agent
    description: "Offensive testing"
  
  - name: blueteam_agent
    description: "Defensive analysis"
  
  - name: bug_bounter_agent
    description: "Vulnerability research"
```

---

## Quick Reference

### Load Agents
```bash
cai --yaml agents.yaml
```

### List Agents
```bash
CAI> /parallel list
```

### View Config
```bash
CAI> /env | grep PARALLEL
```

### Check Costs
```bash
CAI> /cost
```

### Merge Histories
```bash
CAI> /parallel merge
```

---

**For more information:**
- [YAML Format Reference](parallel-agents-yaml-format.md)
- [Environment Variables](../../environment_variables.md)
- [Commands Reference](commands.md)

