## Trust Boundaries
An application and its stack are layered in Trust Boundaries (see [[Definitions#Trust boundary]]). Within a Trust Boundary, one or more **Attestation Services** assess the state of its components (**Evidence** [[Definitions#Evidence]], evaluating threats and remediation, and compute a **Trust Rating**  [[Definitions#Trust Rating]].

The trust boundaries are:
- CPU, RAM and firmware boundary
- Kernel boundary
- Hypervisor boundary
- Runtime boundary (including alternate micro-kernels)
- Microcode boundary (e.g., WASI)
- Source code boundary (including the Secure Software Development lifecycle)

**N.B. :** A Trust Boundary is not Trust. We might have a system that implements trust in some of the Boundaries here defined, but not all. Depending on the confidentiality requirements of the application, that might be enough.

## Composability
The notion of Composability takes two forms in RATS:
- **Layered:** it defines a component (called "Root of trust") that endorses the upper layers (Trust Boundaries) in a system that provides secure Runtime. See example n.1
- **Composite device:** A group of systems rely on a master component (Called "Lead Attester") that collect Evidence from each single system. Each system is therefore a separate Attester, generating Evidence about its respective (sub-)modules. 

#### Example n.1
In a common example, a TEE-enabled CPU measures the integrity of the bootloader. The successfully measured bootloader becomes (or contains) an Attesting Environment for the next layer: the kenel to be booted.
The final Evidence thus contains two sets of Claims: one set about the bootloader as measured and signed by the BIOS and another set of Claims about the kernel as measured and signed by the bootloader.
The example can be extended further by making the kernel become another Attesting Environment for an application as another Target Environment. In this case we add the application's Claims to the Evidence.
