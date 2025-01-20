## The role of the Threat Model in composable attestation
A **Threat Model Description**, possibly written in **OTM**, formalises what we want to protect, from which threat, and how we want to protect it.
The Threat Model Description is used as:
- The source to identify the combination of the system's trusted components (and their **Remote Attestation Services**) we need.
- The desired **Reference Values**, to which we must compare the state of the system.
- The **level of Trustworthiness** we can achieve.

For instance, a system deploys a web service whose identified threats are:
- Denial of service.
- Information disclosure (spanning from code infiltration to data exfiltration)
- Spoofing (either application spoofing or DNS spoofing). 

If we use the **Open Threat Model** ([[#Open Threat Model specification]]), *these threats are modelled and their remediations are detailed in the model itself*:
- One remediation encapsulates the web service in an attested, Secure Runtime. That would solve unwanted data exfiltration, (but it would give no guarantee that malicious code in the web server can exfiltrate information via a legit channel.)
- The other remediation is to execute the runtime within an attested TEE to solve infiltration Information disclosure and application Spoofing threats.

Once these remediations are attested, the system will achieve the desired level of trust (**Trust Rating** [[Definitions#Trust Rating]])

In a nutshell: the Threat Model allows to define what is the **expected state** of an application from the standpoint of trust. Attestation services **provide proof that the expected state is achieved**; in a composition of attestations such proof might reinforce one the other.

## Open Threat Model specification
OTM (See Github repository [here](https://github.com/iriusrisk/OpenThreatModel)) is an agnostic format to describe threats of any system's component and their remediations.
The model is both human- and machine-readable and has reached enough maturity to be usable. OWASP's [Threat Dragon](https://owasp.org/www-project-threat-dragon/) and [Pytm](https://owasp.org/www-project-pytm/) are compatible with OTM, and more applications will adopt the standard.

The model is well endowed to represent layered components and their interactions with each other. By nature, it is recursive, allowing composition of threats descriptions and their mitigations.

OTM provides a structural model of a threat, it doesn't go as deep as normalizing its description or categorisation. For this purpose, a framework like Microsoft's [STRIDE](https://en.wikipedia.org/wiki/STRIDE_model) formalizes the types of threats (**S**poofing, **T**ampering, **R**epudiation, **I**nformation disclosure, **D**enial of service, **E**levation of rights.)

OTM provides standardised JSON and YAML representations, making it ideal to be parsed by a rule engine or a policy controller.

## Examples of OTM entities:
**Assets** are entities that have no active role but are subject to confidentiality measures. Data is an asset, an image or a binary are assets.
`"assets": [`
    `{`
      `"name": "Epitaxial Scrubbers Data",`
      `"id": "EPI-SC-data",`
      `"description": "Epitaxial scrubbers are used in the semiconductor manufacturing industry to remove contaminants from gases produced during the epitaxial deposition process",`
      `"risk": {`
        `"confidentiality": 70,`
        `"integrity": 100,`
        `"availability": 100,`
        `"comment": "Data must be always available in secure form  - averagely sensitive information"`
      `},`
      `"attributes": null`
    `},`
    `{`
      `"name": "Public Info",`
      `"id": "public-info",`
      `"description": "Public information meant to be seen by any interested customer",`
      `"risk": {`
        `"confidentiality": 0,`
        `"integrity": 100,`
        `"availability": 50,`
        `"comment": "Public information has no confidentiality at all but it is quite important for it to be available and to not be changed by attackers"`
      `},`
      `"attributes": null`
    `}`
  `],`

**Dataflows** are representation of of movements of assets between Components or trustZones.
`"dataflows": [`
    `{`
      `"name": "Dataflow between webservice and mongo.",`
      `"id": "cc-store-in-db",`
      `"bidirectional": true,`
      `"source": "web-service",`
      `"destination": "customer-database",`
      `"tags": [`
        `"tag1-df",`
        `"tag2-df"`
      `],`
      `"assets": [`
        `"cc-data"`
      `],`
      `"representations": null,`
      `"threats": [`
        `{`
          `"threat": "22724267-be7e-44c0-8b1f-d7d33e9a34ec",`
          `"state": "exposed",`
          `"mitigations": [`
            `{`
              `"mitigation": "fd6136f4-e2ff-11eb-ba80-0242ac130004",`
              `"state": "required"`
            `}`
          `]`
        `}`
      `],`
      `"attributes": null`
    `}`
  `],`

