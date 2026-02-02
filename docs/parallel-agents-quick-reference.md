# Parallel Agents Quick Reference

**CAI Version:** 0.6+

---

## YAML Format

### Minimal Configuration

```yaml
parallel_agents:
  - name: redteam_agent
    prompt: "Your task here"
```

### Complete Configuration

```yaml
parallel_agents:
  - name: redteam_agent              # Required
    model: alias1                     # Optional
    prompt: "Your task"               # Optional
    description: "Agent description"  # Optional
    auto_run: true                    # Optional (default: true)
    unified_context: false            # Optional (default: false)
    team: "Team 1"                    # Optional (TUI only)
    env:                              # Optional
      KEY: value
```

---

## Quick Commands

```bash
# Load agents
cai --yaml agents.yaml

# Inside CAI
/parallel list              # Show loaded agents
/parallel clear             # Clear configuration
/parallel merge             # Merge agent histories
/env | grep PARALLEL        # View parallel settings
/cost                       # View costs per agent
```

---

## Common Patterns

### Pattern 1: Split Work

```yaml
parallel_agents:
  - name: redteam_agent
    prompt: "Scan A-M subdomains"
  - name: redteam_agent
    prompt: "Scan N-Z subdomains"
```

### Pattern 2: Different Perspectives

```yaml
parallel_agents:
  - name: redteam_agent
    prompt: "Find attack vectors"
  - name: blueteam_agent
    prompt: "Analyze defenses"
```

### Pattern 3: Model Comparison

```yaml
parallel_agents:
  - name: redteam_agent
    model: alias1
    prompt: "Find XSS"
  - name: bug_bounter_agent
    model: alias1
    prompt: "Validate XSS"
```

---

## Field Reference

| Field | Required | Type | Default |
|-------|----------|------|---------|
| `name` | ✅ Yes | string | - |
| `model` | ❌ No | string | Global |
| `prompt` | ❌ No | string | None |
| `auto_run` | ❌ No | boolean | `true` |
| `unified_context` | ❌ No | boolean | `false` |
| `description` | ❌ No | string | None |
| `team` | ❌ No | string | None |
| `env` | ❌ No | object | `{}` |

---

## Common Mistakes

| ❌ Wrong | ✅ Correct |
|---------|-----------|
| `agents:` | `parallel_agents:` |
| `agent_type:` | `name:` |
| `initial_prompt:` | `prompt:` |
| Missing `name` field | Always include `name` |

---

## Troubleshooting

| Problem | Solution |
|---------|----------|
| No agents loaded | Check root key is `parallel_agents:` |
| Agents not running | Set `auto_run: true` |
| High costs | Limit agents, set `CAI_PRICE_LIMIT` |
| Context not shared | Set `unified_context: true` |

---

## Best Practices

✅ **DO:**
- Use 2-4 agents for most tasks
- Set cost limits: `CAI_PRICE_LIMIT=5.0`
- Use descriptive `description` fields
- Monitor costs with `/cost`

❌ **DON'T:**
- Use too many agents (expensive)
- Forget to set `auto_run: true`
- Skip validation of YAML syntax

---

## Environment Variables

```bash
CAI_PARALLEL=3                # Number of parallel agents
CAI_PARALLEL_AGENTS="a,b,c"   # Agent names (auto-set)
CAI_PRICE_LIMIT=5.0           # Cost limit
```

---

## Examples

### Reconnaissance

```yaml
parallel_agents:
  - name: redteam_agent
    prompt: "Enumerate subdomains"
  - name: network_security_analyzer_agent
    prompt: "Scan for open ports"
```

### Bug Bounty

```yaml
parallel_agents:
  - name: redteam_agent
    prompt: "Find OWASP Top 10"
  - name: bug_bounter_agent
    prompt: "Check bug bounty scope"
```

### CTF

```yaml
parallel_agents:
  - name: redteam_agent
    env:
      CTF_NAME: "challenge1"
    prompt: "Solve the challenge"
```

---

## Validation

```bash
# Check YAML syntax
python3 -c "import yaml; yaml.safe_load(open('agents.yaml'))"

# Verify structure
grep -E "^parallel_agents:|  - name:" agents.yaml
```

---

## Links

- [Full YAML Format Guide](parallel-agents-yaml-format.md)
- [Detailed Usage Guide](parallel-agents-cli-usage.md)
- Documentation: https://aliasrobotics.github.io/cai/

