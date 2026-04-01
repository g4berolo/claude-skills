---
name: buddy-reset
description: "Reset Claude Code buddy/companion by changing the season seed (fk_) in cli.js and removing the companion field from ~/.claude.json, allowing re-hatching a new buddy. Triggers: buddy reset, reroll buddy, new buddy, change buddy, reset companion, reroll companion, buddy reroll"
allowed-tools: Bash(*)
---

# Buddy Reset

Reset your Claude Code coding buddy so you can hatch a new one.

## What This Skill Does

1. Reads the current season seed (`fk_`) from `cli.js`
2. Generates a random new seed automatically
3. Replaces the seed in `cli.js`
4. Removes the `companion` field from `~/.claude.json`
5. Reminds you to restart and run `/buddy`

## Instructions

When this skill is invoked, follow these steps exactly:

### Step 1: Locate cli.js and read current seed

Find the Claude Code `cli.js` file. It is typically at:
```
~/AppData/Roaming/npm/node_modules/@anthropic-ai/claude-code/cli.js
```

Use Bash to extract the current `fk_` value:
```bash
grep -o 'fk_="[^"]*"' "$HOME/AppData/Roaming/npm/node_modules/@anthropic-ai/claude-code/cli.js"
```

Show the user the current seed value.

### Step 2: Generate a random seed and replace in cli.js

Generate a random seed string using the format `buddy-YYYYMMDD-XXXXXX` where `YYYYMMDD` is the current date and `XXXXXX` is a random 6-character alphanumeric string. Use Bash:
```bash
NEW_SEED="buddy-$(date +%Y%m%d)-$(cat /dev/urandom | tr -dc 'a-z0-9' | head -c 6)"
echo "$NEW_SEED"
```

Then use `sed` to replace the old seed with the new one in `cli.js`:
```bash
sed -i "s/fk_=\"OLD_VALUE\"/fk_=\"NEW_VALUE\"/" "$HOME/AppData/Roaming/npm/node_modules/@anthropic-ai/claude-code/cli.js"
```

Verify the replacement succeeded by grepping again.

### Step 3: Remove companion from ~/.claude.json

Use python to remove the `companion` field:
```bash
python -c "
import json
path = '$HOME/.claude.json'.replace('$HOME', __import__('os').path.expanduser('~'))
with open(path, 'r', encoding='utf-8') as f:
    data = json.load(f)
removed = data.pop('companion', None)
if removed:
    print('Removed companion:', json.dumps(removed, ensure_ascii=False)[:200])
else:
    print('No companion field found - already clean')
with open(path, 'w', encoding='utf-8') as f:
    json.dump(data, f, indent=2, ensure_ascii=False)
    f.write('\n')
"
```

Also remove `companionMuted` if present:
```bash
python -c "
import json, os
path = os.path.expanduser('~/.claude.json')
with open(path, 'r', encoding='utf-8') as f:
    data = json.load(f)
data.pop('companionMuted', None)
with open(path, 'w', encoding='utf-8') as f:
    json.dump(data, f, indent=2, ensure_ascii=False)
    f.write('\n')
"
```

### Step 4: Notify the user

Tell the user:
- The season seed has been changed from `OLD` to `NEW`
- The companion data has been removed
- They need to **restart Claude Code** (exit and reopen)
- Then run `/buddy` to hatch their new companion
- Remind them that `npm update` will reset the seed back to the original value
