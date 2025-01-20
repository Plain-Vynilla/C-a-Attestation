# C-a-Attestation
This repository contains a high-level architecture and specifications to implement a flexible remote attestation orchestration that can be integrated in a data space negotiation protocol like IDSA.

### The problem we solve.

We want to solve the problem of running an application at a third party service provider, while preserving security and confidentiality requirements intact. In a nutshell, the system should answer questions like:
- Will my application be protected from reverse engineering if it's running on an untrusted environment?
- Will my model parameters and data remain secret, when running on an untrusted environment?
- Will the application/model leak confidential information, when running on an untrusted environment?
- Is the application/model free from hidden, malicious, code or backdoors?
- Can I trust that the application/model delivers the results I expect?
- Can I deploy my application/model on a third party environment and be sure that a certain risk is mitigated or resolved?
- ... 

All these concerns, and many more, can be answered by running a secure-built application on a confidential computing environment that implements security mechanisms across selected parts of its stack, i.e., from hardware to kernel, I/O, OS, to runtime and application.
The security mechanisms must be validated by a trusted entity, commonly by verifying that their state responds to expected values. If the trusted entity is outside the platform where the application/model is built and executed, we speak of **Remote Attestation**.

And because the different stack layers of a running environment require very different Remote Attestation mechanisms, we combine the relevant ones for each confidentiality concern, this is what we call **Composable Attestation**.

