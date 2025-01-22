# C-a-Attestation

The Confidential Computing panorama is fragmented across platforms, vendors, and technologies. While modern CPUs can assure that a workload is executed in total isolation from the running environment through TEEs, these cover only part of the Threat Models. For instance, a workload in a TEE could still exfiltrate information, inadvertently or on purpose.
We need a way to implement Confidential Computing at more than one layer of the IT stack and, most of all, we need to be able to Attest that the composition of these Confidential Computing layers responds to precise security requirements.

This repository contains a high-level architecture and component specifications to implement a remote attestation orchestration that adapts to the confidentiality requirements associated with the workload (application/AI model) to protect.
We make use of an Encoder LLM to query a knowledge base on specific confidential computing and attestation toolchains; the results are ranked and refined and further mapped to the availability of such toolchains. A Decoder LLM generates the Attestation Evidence and its Expected Values, and it feeds an Attestation Orchestrator that deploy the different tech stacks and the required policies.

## The problem(s) we solve.

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

## What makes this architecture different from a generic LLM-based system?

It is true that the LLM is the layer that provide adaptiveness, however, LLM are known to provide unprecise results. To obviate this issue, the LLM is used to isolate requirements from **precise knowledge** so that we reach the precision of a rule-based system. Further rankoing and filtering eliminate spurious and duplicate Evidence. The latter is needed to avoid compose attestations that overlap in terms of security.

Precise knowledge is built by tokenizing generic knowledge about Confidential Computing and adding a number of "conditioning" rules that "align" the prompting with the result. The use of precise inputs, like schema-based Threat Models, makes the mapping even more precise.

## Integration with Data Spaces and contract-based exchanges

Both the Threat Model and Attestation Evidence are represented with schema-based, semantic specifications that can be parsed and analysed by common policy operators (i.e., Equality, Similarity, Negation, etc.)
In a most common use case, an Application Provider receives a request from a data space participant to deploy and execute the Application (workload). The two parties agree on a Threat Model that preserves both sides' security requirements, like: no access to source code or data, and no exfiltration of results belonging to the requesting party.
A data space connector can act as the governance gateway of such deployment, because it can provide one or more Attestation Services with the Reference Values to be appraised. 

## Organization of the repository

The **Architecture** folder contains an overview of the proposed design and a document for each asset of the architecture: services, schema, protocol, interactions.

The **Diagrams** folder contains all graphic material used in every document of this repository.

The **Code** folder contains demos and code examples.