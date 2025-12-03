## Task 1

>Compare package type distribution between `Syft` and `Trivy`

Syft identified 1139 packages, 1128 are `npm`, 10 are `deb`, and 1 is binary. In contrast, Trivy identified 1135 packages, 1125 of which are node.js packages, and 10 are of `debian` type. It is generally reported that Syft identifies more packages and supports a wide range of language and OS ecosystems, while Trivy is more focused on OS package types.

> Which tool found more/better dependency data?

In this run neither tool clearly wins, since the dependency landscapes are roughly identical. Syft provides a slightly more detailed taxonomy, while Trivy separates the packages by type.

> Which tool found more/better license data?

Syft identified slightly more licenses in a more detailed manner. Trivy found most of these licenses and presented them in a bit more consumer friendly manner.

___
## Task 2

> Compare `Grype` and `Trivy` vulnerability detection capabilities

Trivy has identified 2 more `high` severity and 3 more `low/negligible` vulnerabilities, achieving slightly better coverage. Both tools identified a lot of highly critical vulnerabilities, which suggests considerable detection capabilities.

```
=== Vulnerability Analysis ===

Grype Vulnerabilities by Severity:
      8 Critical
     23 High
      1 Low
     25 Medium
     12 Negligible

Trivy Vulnerabilities by Severity:
      8 CRITICAL
     25 HIGH
     16 LOW
     25 MEDIUM

=== License Analysis Summary ===
Tool Comparison:
- Syft found 32 unique license types
- Trivy found 28 unique license types
```

> Analyze top 5 most critical findings with remediation

| Identifier            | Consequence                          | Mechanism                    | Remediation                                         |
| --------------------- | ------------------------------------ | ---------------------------- | --------------------------------------------------- |
| `CVE‑2023‑32314`      | RCE                                  | Sandbox escape               | Upgrade `vm2` to `3.9.18`, then replace it entirely |
| `CVE‑2023‑37903`      | RCE                                  | Sandbox escape               | Replace `vm2` with an alternative                   |
| `CVE‑2023‑37466`      | RCE                                  | Sandbox escape               | Replace `vm2` with an alternative                   |
| `GHSA-c7hr-j4mj-j2w6` | Non-legitimate authentication        | Authentication token forgery | Upgrade `jsonwebtoken` to at least `4.2.2`          |
| `CVE‑2019‑10744`      | DoS, data tampering, RCE (sometimes) | Prototype pollution          | Upgrade `lodash` to `4.17.12` or newer              |
​
These 5 vulnerabilities are critical because they enable RCE or at least non-legitimate authentication, which lead to full compromise of confidentiality, integrity, and availability.

> Assess risky licenses and infer compliance recommendations

| Risk Level | Licenses                                              | Requirements                                                   | Reccomendation                                                                       |
| ---------- | ----------------------------------------------------- | -------------------------------------------------------------- | ------------------------------------------------------------------------------------ |
| High       | GPL‑1/2/3, GPL‑1+/2.0, generic "GPL"                  | Source code disclosure                                         | Avoid using in proprietary components, or use in separate non-critical microservices |
| High       | GFDL‑1.2                                              | Keeping certain sections of license text                       | Avoid using in proprietary components                                                |
| Medium     | LGPL‑2.1/3.0                                          | Disclosure in case of component modification or static linking | Use only in dynamic linking mode in proprietary components                           |
| Low        | MIT, BSD‑2/3‑Clause, ISC, Apache‑2.0, 0BSD, Unlicense | Notice of third-party components                               | Maintain a notice in all components                                                  |

> Analyze secret scanning results

Secret scanning located 4 secret instances: an RSA key used twice and a hard-coded JWT token used twice. A constant private key exposure enables anyone with access to repository to bypass authenticity mechanisms. The hard-cded JWT token seems to be used for testing, thus the severity is much lower, yet this is still undesirable.
