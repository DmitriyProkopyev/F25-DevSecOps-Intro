## Task 1

> Explain the benefits of signing commits for security

The key benefits of commit signing for security are:
- **Protects from impersonation**. Since signing procedure relies on having the correct private key, malicious impersonation becomes less likely if commit verification has been properly set up.
- **Protects from tampering**. Due to reliance on private keys any tampering with the commit history becomes immediately visible forever, and, therefore, useless for an attacker.
- **Enables auditability and traceability**. Signed commits form a history than can be audited for compliance and that clearly traces any changes to a specific person.

> Show evidence of successful SSH key setup and configuration

![ssh-config](https://i.ibb.co/1YvRmdYB/Pasted-image-20251203012907.png)

> Why is commit signing critical in DevSecOps workflows?

DevSecOps heavily leans into continuous security. When assuring security continuously, during ideation, modelling, design, development, and maintenance, threats come not only from the external world, but also from within. Therefore, ensuring internal security and traceability is just as important as sustaining endpoint security (with a focus on outside threats). Commit signing allows DevSecOps teams to achieve exactly that. 

Additionally, development teams can be targeted by attackers during development, especially in large established companies. Commit signing prevents a subset of such attacks.

> Show screenshots or verification of the "Verified" badge on GitHub

![verified-badge](https://i.ibb.co/5xksX20M/Pasted-image-20251203012203.png)

## Task 2

In `labs/submission3.md`, document:

> Document pre-commit hook setup process and configuration

The template provided in the lab was chosen as the pre-commit hook configuration.

> Show evidence of successful secret detection blocking commits

![blocked-commit](https://i.ibb.co/TxVwzct9/Pasted-image-20251203022331.png)

> Show test results showing both blocked and successful commits

The blocked commit is shown above. After removing the secret file, the commit succeeded:

![successful-commit](https://i.ibb.co/HsdTq7P/Pasted-image-20251203025559.png)

> Analyse how automated secret scanning prevents security incidents

Accidental secret leaking through repository files is a common issue to the point where specialized secret scanners for public repositories have been developed to collect leaked credentials. Automated secret scanners can catch and prevent most of such accidental leaks by blocking commits until the secrets are hidden.
