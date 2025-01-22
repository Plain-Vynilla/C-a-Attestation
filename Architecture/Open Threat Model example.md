The following is a Threat Model encoded in OTM 0.1 standard.
The Threat Model describes an application running within a WASM runtime where any kind of I/O is forbidden, except exposing an HTTPS frontend for an UI.
While the use case is rather unprobable, it is simple enough to show the general structure of the Threat Model and how stratified Threats can be, requiring multiple mitigations.

The general structure of the file is:

```
Project
  |
  |- Components
  |- Data flows
  |- Trust Zones
  |- Threats
  |- Mitigations
  |- Exceptions (i.e., the UI channel)
```

The sample code:

```yaml
otmVersion: 0.1.0
project:
    name: WASM-Based Secure Application
    id: wasm-secure-app

components:

 # Core Application Components
  - id: wasm_module
    name: WebAssembly Module
    type: process
    tags: [core, computation]
    description: Core WASM module handling sensitive data processing

  - id: web_ui
    name: Web UI Interface
    type: web-application
    tags: [interface]
    description: User interface served over HTTPS (port 443)

  - id: https_interface
    name: HTTPS Interface
    type: interface
    tags: [network]
    description: Only allowed external communication channel (TLS 1.3+ on port 443)

dataflows:

  - name: User Input
    source: web_ui
    destination: wasm_module
    protocol: HTTPS
    tags: [user-data]

  - name: Processed Output
    source: wasm_module
    destination: web_ui
    protocol: HTTPS
    tags: [sanitized-data]

trustZones:

  - id: user_zone
    name: User Browser Environment
    risk: medium

  - id: hosted_zone
    name: Secure Hosting Environment
    risk: low

threats:

  - Data Exfiltration Threats
    id: THREAT-01
    name: Unauthorized Data Export via Web UI
    description: Malicious actor attempts to exfiltrate data through the Web UI
    category: data-exfiltration
    risk:
    likelihood: medium
    impact: high
    components: [web_ui, wasm_module]
    mitigations: [MIT-01, MIT-02]

  - id: THREAT-02
    name: WASM Module Memory Extraction
    description: Exploit WASM memory management to extract sensitive data
    category: memory-safety
    risk:
    likelihood: low
    impact: critical
    components: [wasm_module]
    mitigations: [MIT-03]

  - id: THREAT-03
    name: HTTPS Interface Abuse
    description: Misuse of legitimate HTTPS channel for data exfiltration
    category: protocol-abuse
    risk:
    likelihood: medium
    impact: high
    components: [https_interface]
    mitigations: [MIT-04, MIT-05]

  - id: THREAT-04
    name: UI Redressing (Clickjacking)
    description: Malicious UI overlays to trick users into actions
    category: ui-manipulation
    risk:
    likelihood: medium
    impact: medium
    components: [web_ui]
    mitigations: [MIT-06]

mitigations:

  - id: MIT-01
    name: Strict Output Sanitization
    description: All WASM output undergoes strict content validation/sanitization
    type: technical
    implementation:
    WASM memory isolation
    Output Content Security Policy (CSP)
    Data type/structure validation

  - id: MIT-02
    name: Input Validation
    description: All user inputs validated against strict regex patterns
    type: technical
    implementation:
    Allow-list input validation
    Maximum size limits
    No raw string concatenation

  - id: MIT-03
    name: WASM Memory Hardening
    description: Prevent direct memory access
    type: technical
    implementation:
    WASM sandboxing with no filesystem/network access
    Memory encryption during idle periods
    Zeroize-on-release for sensitive data

  - id: MIT-04
    name: HTTPS Strict Transport Security
    description: Enforce secure communication
    type: technical
    implementation:
    TLS 1.3+ only
    Certificate pinning
    HSTS headers with max-age=31536000

  - id: MIT-05
    name: Rate Limiting & Pattern Detection
    description: Monitor HTTPS traffic for exfiltration patterns
    type: monitoring
    implementation:
    Request size limits
    Unusual payload pattern detection
    Behavioral analysis of outbound traffic

  - id: MIT-06
    name: UI Security Headers
    description: Prevent UI-based attacks
    type: technical
    implementation:
    Content-Security-Policy: default-src 'none'; script-src 'self'
    X-Frame-Options: DENY
    Cross-Origin-Opener-Policy: same-origin

exceptions:

  - id: EX-01
    description: No outbound connections except HTTPS on 443
    components: [wasm_module, web_ui]
    justification: "Principle of least privilege - only allow essential communication"
```