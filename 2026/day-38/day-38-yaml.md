## Challenge Tasks

### Task 1: Key-Value Pairs
Create `person.yaml` that describes yourself with:
- `name`
- `role`
- `experience_years`
- `learning` (a boolean)

O/P:
    name: Himmat
    role: Software Developer
    experience_years: 3 years
    learning: true

**Verify:** Run `cat person.yaml` — does it look clean? No tabs?

---

### Task 2: Lists
Add to `person.yaml`:
- `tools` — a list of 5 DevOps tools you know or are learning
- `hobbies` — a list using the inline format `[item1, item2]`

O/P:
tools:
  - Docker
  - GitHub
  - GitHub Actions
  - Terraform
  - Kubernetes
  - Ansible
Hobbies: [ Fitness Training, Sports, Photography]

Write in your notes: What are the two ways to write a list in YAML?

---

### Task 3: Nested Objects
Create `server.yaml` that describes a server:
- `server` with nested keys: `name`, `ip`, `port`
- `database` with nested keys: `host`, `name`, `credentials` (nested further: `user`, `password`)

O/P:
server:
  name: Github_actions_server
  ip: 135.226.154.156
  port: 8080

database:
  host: MYSQL_root
  name: mydb
  credentials:
    user: root
    password: root

**Verify:** Try adding a tab instead of spaces — what happens when you validate it?

---

### Task 4: Multi-line Strings
In `server.yaml`, add a `startup_script` field using:
1. The `|` block style (preserves newlines)
2. The `>` fold style (folds into one line)

Write in your notes: When would you use `|` vs `>`?

| - Preserves line breaks exactly as written. Used when we need exact formatting.
> - Converts line breaks into spaces (except blank lines), used when thr wrapped text that becomes on paragraph. 

---

### Task 5: Validate Your YAML
1. Install `yamllint` or use an online validator
2. Validate both your YAML files
3. Intentionally break the indentation — what error do you get?
4. Fix it and validate again

- Done.

---

### Task 6: Spot the Difference
Read both blocks and write what's wrong with the second one:

```yaml
# Block 1 - correct
name: devops
tools:
  - docker
  - kubernetes
```

```yaml
# Block 2 - broken
name: devops
tools:
- docker
  - kubernetes
```

Ans:
    on second block inside tools docker and kubernetes are not alligned properly, docker starts at columne 0 and kubernets starts at 3, YAML sees them at different levels which cause parsing errors.

---

## Hints
- YAML uses **spaces only** — never tabs
- Indentation is everything — 2 spaces is standard
- Strings don't need quotes unless they contain special characters (`:`, `#`, etc.)
- `true`/`false` are booleans, `"true"` is a string
- Validate online: yamllint.com
