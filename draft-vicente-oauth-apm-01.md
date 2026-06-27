---
title: "Authorization Posture Mechanism (APM) for OAuth 2.0"
abbrev: "OAuth APM"
docname: draft-vicente-oauth-apm-01
category: info
submissiontype: IETF

ipr: trust200902
area: Security
workgroup: Web Authorization Protocol
keyword:
  - oauth
  - authorization
  - posture
  - zero trust
  - device posture
  - graduated outcome
  - least privilege

stand_alone: yes
pi:
  rfcedstyle: yes
  toc: yes
  tocindent: yes
  sortrefs: yes
  symrefs: yes
  strict: yes
  comments: yes
  inline: yes
  text-list-symbols: o*+-
  docmapping: yes

author:
  -
    ins: B. Vicente
    name: Brian Vicente
    organization: Sanctum SecOps LLC
    email: brian@sanctumsecops.io
    city: Pine City
    region: NY
    country: United States of America

normative:
  RFC2119:
  RFC8174:
  RFC6749:
  RFC8705:
  RFC9449:
  RFC6750:
  RFC7519:
  RFC7518:
  RFC7662:
  RFC8446:

informative:
  RFC9470:
  RFC9396:
  RFC7009:
  I-D.ietf-oauth-attestation-based-client-auth:
    title: "OAuth 2.0 Attestation-Based Client Authentication"
    author:
      - ins: T. Looker
      - ins: P. Bastian
      - ins: C. Bormann
    date: 2026-03
    seriesinfo:
      "Internet-Draft": "draft-ietf-oauth-attestation-based-client-auth-08"
  I-D.ietf-oauth-transaction-tokens:
    title: "Transaction Tokens"
    author:
      - ins: A. Tulshibagwale
      - ins: G. Fletcher
      - ins: P. Kasselman
    date: 2026-01
    seriesinfo:
      "Internet-Draft": "draft-ietf-oauth-transaction-tokens-07"
  CAEP:
    title: "OpenID Continuous Access Evaluation Profile 1.0"
    author:
      - org: OpenID Foundation
    date: 2025-09
    target: https://openid.net/specs/openid-caep-specification-1_0.html
  NIST.SP.800-207:
    title: "Zero Trust Architecture"
    author:
      - org: National Institute of Standards and Technology
    date: 2020-08
    seriesinfo:
      "NIST Special Publication": "800-207"
    target: https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-207.pdf
  SHOR1994:
    title: "Algorithms for Quantum Computation: Discrete Logarithms and Factoring"
    author:
      - ins: P.W. Shor
        name: Peter W. Shor
    date: 1994
    seriesinfo:
      "Proceedings of the 35th Annual Symposium on Foundations of Computer Science": "pp. 124-134"
    target: https://doi.org/10.1109/SFCS.1994.365700
  GROVER1996:
    title: "A Fast Quantum Mechanical Algorithm for Database Search"
    author:
      - ins: L.K. Grover
        name: Lov K. Grover
    date: 1996
    seriesinfo:
      "Proceedings of the 28th Annual ACM Symposium on Theory of Computing": "pp. 212-219"
    target: https://doi.org/10.1145/237814.237866
  FIPS204:
    title: "Module-Lattice-Based Digital Signature Standard"
    author:
      org: "NIST"
    date: 2024-08
    target: https://doi.org/10.6028/NIST.FIPS.204

--- abstract

OAuth 2.0 access tokens encode an authorization decision made at issuance time.
Sender-constraining mechanisms such as Mutual-TLS certificate binding
({{RFC8705}}) and Demonstrating Proof of Possession ({{RFC9449}}) extend this
model by proving key possession at request time, but neither mechanism
re-evaluates the full security context — certificate validity, token claims, and
device-level security posture — against the conditions present at token issuance,
nor provides a normative pathway for graduated least-privilege outcomes when that
context has degraded.

This document describes the Authorization Posture Mechanism (APM) problem domain:
the gap between what per-request sender-constraining mechanisms prove
cryptographically and what Zero Trust Architecture requires for per-request
authorization decisions. It motivates and specifies requirements for a mechanism
that (a) assembles a per-request Consistency View comprising the client
certificate, the bound access token, and an integrity-protected device-posture
signal; (b) evaluates the Consistency View against the Issuance Posture recorded
at token issuance; and (c) produces graduated, least-privilege outcomes — scope
reduction, method restriction, or full denial — proportional to the degree of
posture degradation detected.


# Source and Archival {-}

Source for this draft is maintained at <https://github.com/Sanc-Admin/RFC-Final-Draft>.
A citable archival version of this document is available at Zenodo:
<https://doi.org/10.5281/zenodo.20584241>.
Author ORCID iD: <https://orcid.org/0009-0006-6395-5308>.

# IPR Considerations {-}

The author and Sanctum SecOps LLC hold or have applied for United States
patent applications covering subject matter related to this document.
Specifically, the subject matter herein overlaps in part with the disclosure
of U.S. Provisional Patent Application No. 64/080,137 ("Multi-Tenant Public
Key Infrastructure with Drift-Gated Issuance, Per-Transaction Semantic
Consistency Verification, and Topology-Aware Post-Quantum Certificate
Rotation", filed 2026-06-01) and U.S. Patent Application No. 19/698,870
("System and Method for Post-Quantum Cryptographic Readiness Observability
and Feedback in Networked Environments", attorney docket SANC-2026-002,
filed 2026-06-05).

By posting this Internet-Draft, the author submits to the IETF Trust the
rights described in Section 5 of BCP 78 and BCP 79. Patent licensing terms
are not yet known. Implementers and reviewers should consult the IETF
Datatracker IPR disclosure page for this document for current disclosure
status. No portion of this document is offered as a trade secret.

# Patent-Pending Claim Scope (Informational) {-}

The Authorization Posture Mechanism (APM) requirements described in this
document interoperate with, and are informed by, the following pending claim
families identified in the IPR Considerations note above. This summary is
provided to inform implementers of the protected scope; it is not an enabling
disclosure and does not waive any patent right. The specific evaluation
algorithms, weighting coefficients, adequacy thresholds, posture-binding
predicates, graduated-outcome decision rules, and re-evaluation triggers
that practice these claims are not disclosed in this Internet-Draft.

Per-Transaction Posture Consistency Verification: apparatus, method, and
non-transitory computer-readable medium claims (related to U.S. Provisional
Patent Application No. 64/080,137) directed to assembling a per-request
Consistency View comprising a client certificate, a bound access token, and
an integrity-protected device-posture signal.

Graduated Least-Privilege Outcome Derivation: apparatus, method, and
computer-readable medium claims (related to U.S. Provisional Patent
Application No. 64/080,137) directed to producing graduated, least-privilege
authorization outcomes proportional to a measured degree of posture
degradation.

Posture-Bound Audit Evidence: apparatus, method, and computer-readable
medium claims (related to U.S. Provisional Patent Application No.
64/080,137) directed to producing per-request audit records that
cryptographically bind authorization decisions to the posture inputs and to
the policy version in force at the time of evaluation.

Posture-Linked Readiness Assessment: apparatus, method, and
computer-readable medium claims (related to U.S. Patent Application No.
19/698,870) directed to consuming authorization-posture observations as
inputs to a cryptographic-readiness scoring and adequacy classification
function.

Nothing in this Internet-Draft is to be construed as disclosing the manner
in which any of the above claims is reduced to practice.

# Author Credentials {-}

The author holds the following industry certifications, independently
verifiable through Pearson Credly at
<https://www.credly.com/users/brian-vicente>:

* CompTIA Security+ ce (2026-03-29 / valid through 2029-03-29)
* CompTIA Secure Infrastructure Specialist - CSIS Stackable (2026-03-29 / valid through 2029-03-29)
* CompTIA Secure Cloud Professional - CSCP Stackable (2026-03-29 / valid through 2027-06-29)
* CompTIA Cloud Admin Professional - CCAP Stackable (2025-10-10 / valid through 2027-06-29)
* CompTIA IT Operations Specialist - CIOS Stackable (2025-10-10 / valid through 2029-03-29)
* CompTIA Network+ ce (2025-10-10 / valid through 2029-03-29)
* CompTIA Cloud+ ce (2024-06-29 / valid through 2027-06-29)
* Linux Professional Institute Linux Essentials (2024-01-16)
* CompTIA A+ ce (2023-10-16 / valid through 2029-10-16)


--- middle

# Introduction {#introduction}

## Background and Motivation {#motivation}

Zero Trust Architecture, as defined in NIST SP 800-207 {{NIST.SP.800-207}},
requires that access to resources be granted on a per-request basis using dynamic
policy that includes the observable state of the client identity, the requesting
asset, and behavioral or environmental attributes. NIST SP 800-207 §3.2 states
explicitly:

> "No resource is inherently trusted. Every asset must have its security posture
> evaluated via a PEP before a request is granted to an enterprise-owned resource.
> This evaluation should be continual for as long as the session lasts."

The OAuth 2.0 framework {{RFC6749}} issues access tokens that represent an
authorization decision frozen at the moment of issuance. The resource server
validates token expiry and scope coverage; it does not re-evaluate whether the
security conditions that justified issuance still hold. This design was deliberate
and appropriate for the HTTP API delegation use case OAuth was designed to serve,
but it creates a structural gap with respect to ZTA's per-request evaluation
requirement.

Two families of sender-constraining mechanisms have been standardized to address
portions of this gap:

1. **Certificate-bound access tokens** ({{RFC8705}}): The authorization server
   records a SHA-256 thumbprint of the client's mutual-TLS certificate in the
   `cnf/x5t#S256` claim. The resource server checks that the certificate
   presented in the current TLS handshake matches the thumbprint. This
   proves that the presenter of the token also possesses the private key of the
   enrolled certificate. It does not prove that the certificate is currently
   valid, that the device on which the key resides is in a compliant security
   state, or that any conditions beyond key possession hold at request time.
   Section 6.5 of {{RFC8705}} explicitly places TLS termination and revocation
   re-evaluation outside the scope of the specification.

2. **Demonstrating Proof of Possession** ({{RFC9449}}): Each HTTP request carries
   a freshly generated DPoP proof JWT binding the request to a specific HTTP
   method (`htm`) and URI (`htu`). This provides per-request proof of key
   possession and prevents token-theft replay for a different endpoint. Section
   11.7 of {{RFC9449}} states definitively that DPoP does not ensure the
   integrity of the request payload or headers beyond method and URI. Section
   11.11 states that cryptographic binding between DPoP and client authentication
   mechanisms beyond the scope of that specification is out of scope.

Neither mechanism addresses the third dimension: device-level security posture.
Neither mechanism evaluates, per-request, whether the triple formed by (client
certificate, bound access token, device posture) remains consistent with the
security context recorded at token issuance.

When inconsistency is detected — for example, when a device that was MDM-enrolled
and fully patched at token issuance is later found to be missing a critical
security update — the current OAuth ecosystem offers only binary outcomes: either
the request succeeds (if the token is still structurally valid) or fails (if the
resource server applies an out-of-band revocation).

## Gaps in Adjacent Mechanisms {#adjacent-gaps}

### Step-Up Authentication (RFC 9470) {#gap-step-up}

RFC 9470 {{RFC9470}} defines the `insufficient_user_authentication` error code
and a `WWW-Authenticate` challenge mechanism by which a resource server can
signal that the authentication context associated with a token does not meet
requirements. The client must then re-authenticate at the required
`acr_values` or within the required `max_age`.

This mechanism is reactive (it fires on a specific request failure, not
proactively), addresses authentication strength only (not device posture), and
its outcome is structurally binary: the authentication requirement is either
satisfied upon re-authentication, or the request is denied. There is no
mechanism for the resource server to respond with "proceed with a reduced set
of permitted operations" rather than "re-authenticate or be denied."

### Rich Authorization Requests (RFC 9396) {#gap-rar}

RFC 9396 {{RFC9396}} defines the `authorization_details` parameter, enabling
fine-grained authorization grants specifying `actions`, `locations`,
`datatypes`, and `privileges` at issuance time. This is a significant improvement
over the coarse `scope` model for expressing initial grants.

However, the `authorization_details` claim in an issued token is static for
the token's lifetime. Section 5.3 of {{RFC9396}} does not define any mechanism
for the resource server to narrow the `actions` or `privileges` fields
per-request in response to a runtime posture signal. If the device posture
degrades after token issuance, there is no standardized pathway to downgrade
from `"actions": ["read", "write", "delete"]` to `"actions": ["read"]` for
the duration of the degraded state.

### Continuous Access Evaluation (CAEP) {#gap-caep}

The OpenID Continuous Access Evaluation Profile {{CAEP}} defines an
event-driven, asynchronous push mechanism by which a Transmitter (such as an
MDM system or identity provider) can signal state changes to a Receiver (such
as a resource server or authorization server). The Device Compliance Change
event type carries only a binary `previous_status` / `current_status` of
`compliant` or `not-compliant`.

CAEP operates asynchronously. Between the occurrence of a compliance change
event and the Receiver's processing of the pushed Security Event Token, there
is a temporal gap during which a client with a valid access token may interact
with a resource server against a degraded security posture that has not yet
been signaled. CAEP does not provide a per-request, in-band posture assertion
alongside individual HTTP requests.

### Attestation-Based Client Authentication {#gap-attestation}

The draft {{I-D.ietf-oauth-attestation-based-client-auth}} defines a two-token
structure for attesting client instance properties: a Client Attestation JWT
(issued by the client's backend) and a per-request Client Attestation PoP JWT.
The Client Attestation JWT may be reused across requests until its `exp` claim.

Section 7 of that draft notes that if device posture degrades within the
validity window of a Client Attestation JWT, there is no normative mechanism to
force fresh attestation. Furthermore, the draft explicitly does not define a
schema for device posture claims within the Client Attestation JWT; what
hardware and software properties are attested is left to deployment-specific
conventions. The mechanism operates at the client authentication layer, not the
per-request resource-access layer.

## Relationship to This Document {#relationship}

This document defines the APM problem domain and specifies functional
requirements for a mechanism addressing the three identified gaps:

1. **Gap A** {#gap-a}: No per-request triple consistency verification — no
   normative mechanism evaluates the certificate, bound token, and device-posture
   signal together per-request.

2. **Gap B** {#gap-b}: No graduated least-privilege outcomes — no normative
   mechanism responds to posture degradation with anything other than a binary
   allow/deny decision.

3. **Gap C** {#gap-c}: No method-level downgrade on posture degradation — no
   normative mechanism restricts permitted HTTP methods or operation types for
   an existing valid token in response to a runtime posture change.

This document is informational. It describes the problem domain and specifies
requirements; it does not mandate a specific protocol design. Implementors,
working group participants, and standards authors are encouraged to use the
requirements in Section 5 as evaluation criteria for proposed solutions.

## Post-Quantum Relevance {#pq-relevance}

The APM mechanism is motivated not only by current Zero Trust Architecture
requirements but also by the post-quantum cryptographic transition
underway across the Internet.

Shor's algorithm {{SHOR1994}} solves the Integer Factorization Problem
(IFP) and the Elliptic Curve Discrete Logarithm Problem (ECDLP) in
polynomial quantum time, meaning that a sufficiently capable
Cryptographically Relevant Quantum Computer (CRQC) can break RSA and
ECDSA/ECDH keys.  The TLS client certificates that form the certificate
component of the APM Consistency View are today predominantly RSA or
ECDSA certificates.  If a CRQC breaks the private key corresponding to
such a certificate, the `cnf/x5t#S256` binding asserted by {{RFC8705}}
is defeated: an attacker can present any token bound to the compromised
certificate thumbprint and forge the TLS handshake.

Grover's algorithm {{GROVER1996}} provides a quadratic speedup for
unstructured search, effectively halving the bit-security of hash-based
constructs.  SHA-256 hashes used in `cnf/x5t#S256` ({{RFC8705}}) and
`cnf/jkt` ({{RFC9449}}) provide approximately 128-bit quantum preimage
resistance — sufficient for current threat models, but motivating the
migration to PQ-signed certificates as the certificate component of the
Consistency View.

The Harvest-Now-Decrypt-Later (HNDL) threat further amplifies the
urgency: an adversary who records TLS sessions today can retroactively
recover the session keys once a CRQC is available, recovering the access
tokens exchanged within those sessions.  Per-request triple consistency
evaluation (REQ-1 through REQ-3) and the short posture-assertion freshness
window (Section 6.2) limit the window of exposure of any individual
access token and its associated Consistency View to the current request
rather than an entire session lifetime.

The APM mechanism is designed to remain applicable as the OAuth ecosystem
transitions to PQ-signed certificates: the certificate component of the
Consistency View is algorithm-agnostic; {{RFC8705}} does not restrict the
certificate's key type.  A Consistency View assembled from an ML-DSA
certificate (as defined in {{FIPS204}}) functions identically to one
assembled from an ECDSA certificate.

# Conventions and Definitions {#conventions}

{::boilerplate bcp14-tagged}

The following terms are used throughout this document.

**Access Token:**
A credential issued by an authorization server to a client, as defined in
{{RFC6749}} Section 1.4. For the purposes of this document, all access tokens
are assumed to be sender-constrained using either {{RFC8705}} (mTLS
certificate binding) or {{RFC9449}} (DPoP key binding), or both.

**Authorization Server (AS):**
The server issuing access tokens to the client after successfully
authenticating the resource owner and obtaining authorization, as defined in
{{RFC6749}} Section 1.1.

**Client:**
An application making protected resource requests, as defined in {{RFC6749}}
Section 1.1.

**Consistency View:**
The triple of (a) the TLS client certificate or DPoP public key presented with
a request, (b) the bound access token's claims, and (c) the device-posture
signal supplied with the request. A Consistency View is assembled per-request
at the resource server or a co-located policy enforcement point. The Consistency
View is evaluated against the Issuance Posture to determine whether posture
degradation has occurred.

**Device-Posture Signal:**
An integrity-protected assertion about the security state of the device from
which the request originates. The specific encoding and transport of the
device-posture signal is deployment-specific but MUST be integrity-protected
(e.g., cryptographically signed) to prevent forgery. The device-posture
signal is treated as an opaque input to the Consistency View evaluation; this
document does not define a normative schema for posture attributes.

**Graduated Outcome:**
A result of Consistency View evaluation that is neither unconditional access
nor unconditional denial. Graduated outcomes reduce the effective permission
set in proportion to the degree of posture degradation detected. Graduated
outcomes include but are not limited to Scope Reduction and Method Restriction.

**Issuance Posture:**
A representation of the security posture of the client and its device at the
time the access token was issued by the authorization server. Issuance Posture
is recorded either as a claim in the access token or as an entry in the AS's
token metadata store associated with the token identifier. The Issuance Posture
serves as the baseline against which per-request posture signals are compared.

**Method Restriction:**
A Graduated Outcome that limits the set of HTTP methods (or equivalently, the
set of resource operation types expressed as `actions` in the sense of
{{RFC9396}}) that the resource server will process for a given request.
For example: an access token that nominally authorizes `GET`, `POST`, `PUT`,
and `DELETE` may be restricted to `GET` only when posture degradation is
detected.

**Policy Enforcement Point (PEP):**
A component that evaluates the Consistency View and applies the resulting
graduated outcome, consistent with the ZTA reference architecture in
{{NIST.SP.800-207}} Section 3.

**Posture Degradation:**
The condition in which one or more attributes of the current device-posture
signal are less secure than the corresponding attributes of the Issuance
Posture. Posture degradation is a matter of degree; the evaluation function
maps a degradation vector to an outcome class.

**Privileged Request:**
An HTTP request carrying an access token where the requested resource or
operation has been designated by policy as requiring per-request triple
consistency evaluation. Not all requests need undergo Consistency View
evaluation; policy may designate specific resource URIs, HTTP methods, or
token scopes as requiring APM processing.

**Resource Server (RS):**
The server hosting protected resources, accepting access tokens, as defined
in {{RFC6749}} Section 1.1.

**Scope Reduction:**
A Graduated Outcome that substitutes a narrower scope string or a narrower
`authorization_details` object for the one granted at issuance. Scope
reduction takes effect for the current request and possibly subsequent requests
until posture is restored. Scope reduction MUST NOT be implemented by
modifying the token itself; it MUST be implemented by the resource server
applying the reduced scope as an additional constraint during request
processing.

# Problem Statement {#problem-statement}

## The Static Authorization Problem {#prob-static}

RFC 6749 {{RFC6749}} describes a model in which the access token represents an
authorization decision made at the time of issuance. Section 7 of {{RFC6749}}
specifies that the resource server validates the token's expiry and scope, but
"the methods used by the resource server to validate the access token, as well
as any error responses, are beyond the scope of this specification." No
mechanism in {{RFC6749}} addresses re-evaluation of the conditions that
justified issuance. The token is, in effect, a session surrogate: once issued,
it carries the authorization context of the issuance event until expiry or
explicit revocation.

This model was appropriate for the original delegation use case. It is
insufficient for environments governed by Zero Trust Architecture principles,
which {{NIST.SP.800-207}} §2.1 describes as requiring:

> "...least privilege per-request access decisions in information systems and
> services in the face of a network viewed as compromised."

NIST SP 800-207 Tenet 6 states: "All resource authentication and authorization
are dynamic and strictly enforced before access is allowed." This requirement
cannot be satisfied by a token whose authorization context was frozen at
issuance.

## The Single-Dimension Binding Problem {#prob-single-dim}

Both {{RFC8705}} and {{RFC9449}} extend the static token model by adding a
sender-constraint dimension. Certificate-bound tokens ({{RFC8705}}) prove that
the presenter holds the private key corresponding to the certificate thumbprint
embedded in `cnf/x5t#S256`. DPoP ({{RFC9449}}) proves that the presenter holds
the key corresponding to the `cnf/jkt` thumbprint and that the proof covers
the specific HTTP method and URI of the current request.

These mechanisms close a critical replay-token vulnerability but remain
single-dimension: they prove cryptographic possession of a key. They do not
evaluate:

- Whether the certificate has been revoked since issuance ({{RFC8705}} §6.5
  explicitly excludes revocation re-evaluation from scope)
- Whether the device possessing the key is in a compliant security state at
  the time of the request
- Whether the triple of (certificate, token, device posture) is mutually
  consistent with what was presented and recorded at token issuance

A threat actor who compromises a device to the point of exfiltrating its
private key material can generate valid DPoP proofs and present valid
certificate-bound tokens indefinitely, because neither mechanism evaluates
device security state per-request.

## The Binary Outcome Problem {#prob-binary}

When a resource server determines that the authentication or authorization
context of a request is insufficient, the RFC 9470 step-up mechanism
{{RFC9470}} provides a single escalation path: the client must re-authenticate
at the required `acr_values`. The outcome is binary: upon successful
re-authentication, full access resumes; without it, the request is denied.

This binary model imposes disproportionate operational consequences for minor
posture degradations. Consider a device that was fully patched at token
issuance but is now one day behind on a non-critical software update. Under
the binary model, the choices are:

1. Accept the risk and serve the request as if posture were unchanged.
2. Deny the request entirely, disrupting the user's workflow.

Neither choice aligns with least-privilege principles. A graduated model would
permit a reduced set of operations — for example, read-only operations — while
requiring posture remediation or step-up authentication before write or
delete operations are permitted. {{RFC9396}} enables fine-grained initial
grants but provides no mechanism for post-issuance narrowing of
`authorization_details` in response to a runtime posture signal ({{RFC9396}}
§5.3).

## The Asynchronous Signaling Problem {#prob-async}

OpenID CAEP {{CAEP}} provides the closest existing mechanism for continuous
access evaluation in the OAuth ecosystem. CAEP's Device Compliance Change
event signals a transition between `compliant` and `not-compliant` states.
However:

1. CAEP operates asynchronously: the event is pushed from a Transmitter to a
   Receiver via the SSE framework. There is a temporal gap between the occurrence
   of a compliance change and the Receiver's action on that event.

2. CAEP's device compliance vocabulary is binary. No event in the CAEP
   specification carries fine-grained posture attributes that could be used to
   differentiate, for example, "missing non-critical update" from "MDM enrollment
   revoked."

3. CAEP does not provide a per-request, synchronous posture assertion. A client
   with a valid access token may successfully complete multiple requests in the
   window between a compliance change occurrence and the Receiver's propagation
   of that change into an authorization enforcement decision.

4. CAEP does not define graduated outcomes. What a Receiver should do upon
   receiving a Device Compliance Change event is out of scope; no normative
   mapping from posture transition to permission change is defined.

## Summary: Why All Four Together Still Leave a Gap {#prob-summary}

The combination of RFC 8705, RFC 9449, RFC 9470, RFC 9396, and CAEP does not
close the APM gap because:

- **Per-request sender-constraining** ({{RFC8705}}, {{RFC9449}}): Proves key
  possession but does not evaluate device posture.

- **Fine-grained initial grants** ({{RFC9396}}): Precise at issuance but
  static thereafter.

- **Reactive step-up** ({{RFC9470}}): Binary, fires on authentication-level
  failures only, does not address device posture.

- **Asynchronous push events** ({{CAEP}}): Not synchronous with individual
  requests, binary compliance vocabulary, no normative graduated outcomes.

None of these mechanisms defines: (a) the per-request triple of certificate,
token, and device-posture signal as a unit of evaluation; (b) a normative
comparison of that triple against the conditions recorded at issuance; or (c) a
normative mapping from the result of that comparison to a graduated,
least-privilege outcome. This gap is the subject of the APM requirements in
Section 5.

# Requirements {#requirements}

This section specifies functional requirements for a solution to the APM problem
domain. Requirements are expressed in abstract, non-implementation-specific
language. These requirements describe what a solution SHOULD satisfy; they do
not constitute a protocol specification. The key words are as defined in
Section 2.

A compliant APM solution SHOULD satisfy the following requirements.

**REQ-1: Per-Request Triple Assembly.**
For each Privileged Request, the enforcing entity (resource server or PEP)
SHOULD assemble a Consistency View comprising (a) the client certificate
thumbprint or DPoP public key thumbprint presented with the request, (b) the
claims of the bound access token, including the `cnf` claim, and (c) an
integrity-protected device-posture signal supplied with or verifiably associated
with the request.

**REQ-2: Issuance Posture Recording.**
At token issuance, the authorization server SHOULD record the security posture
of the requesting client as the Issuance Posture. The Issuance Posture SHOULD
be associated with the token either as a signed claim in the token itself or as
metadata in a token-associated record accessible via token introspection
({{RFC7662}}). The Issuance Posture MUST NOT contain posture claims of higher
sensitivity than the access token itself warrants.

**REQ-3: Consistency Evaluation.**
The enforcing entity SHOULD evaluate the Consistency View against the Issuance
Posture to determine whether posture degradation has occurred. This evaluation
SHOULD be synchronous with the processing of the Privileged Request. The
evaluation function SHOULD be deterministic: the same Consistency View and the
same Issuance Posture SHOULD produce the same outcome classification.

**REQ-4: Graduated Outcome Production.**
The enforcing entity SHOULD produce graduated outcomes ordered by the least-
privilege principle. Outcome classes SHOULD include at minimum:

- **Permit**: The Consistency View is consistent with the Issuance Posture;
  the request proceeds under the full authorization of the access token.
- **Scope Reduction**: One or more posture attributes are degraded but the
  degradation does not warrant full denial; the request proceeds under a
  narrower effective scope.
- **Method Restriction**: Posture degradation warrants restricting permitted
  HTTP methods or operation types; the request proceeds only if the requested
  method is within the restricted set.
- **Full Denial**: Posture degradation is sufficiently severe that no
  operation is permitted; the request is denied with an error response that
  includes actionable guidance for the client.

**REQ-5: Method-Level Restriction.**
The graduated outcome framework SHOULD include Method Restriction as a distinct
outcome class. Method Restriction SHOULD be expressible in terms of both HTTP
methods and, where applicable, operation types expressed as `actions` in the
sense of {{RFC9396}} §2.2. A Method Restriction outcome MUST NOT prevent the
client from discovering which operations remain available; the response MUST
convey the permitted method set or operation set.

**REQ-6: Scope Reduction.**
The graduated outcome framework SHOULD include Scope Reduction as a distinct
outcome class. Scope Reduction MUST NOT be implemented by modifying the access
token. Scope Reduction MUST be implemented at the resource server or PEP layer
as an additional constraint applied during request processing. When a Scope
Reduction outcome is produced, the response SHOULD convey the reduced effective
scope to the client in a manner compatible with the client's ability to
re-request operations within the reduced scope.

**REQ-7: Deterministic Outcome Mapping.**
A deployment implementing APM SHOULD define a policy that maps posture
degradation dimensions to outcome classes in a deterministic, auditable manner.
The policy SHOULD be expressed such that the mapping from degradation to outcome
is reproducible and does not depend on transient state beyond the Consistency
View and Issuance Posture.

**REQ-8: Integrity-Protected Posture Signal.**
The device-posture signal component of the Consistency View MUST be integrity-
protected to prevent a compromised or malicious client from supplying a falsely
elevated posture signal. Integrity protection SHOULD be cryptographic (e.g.,
a signed attestation). The signing authority for the posture signal SHOULD be
distinct from the client itself; where only client-self-signed posture signals
are available, the enforcing entity SHOULD apply additional skepticism to the
resulting Consistency View evaluation.

**REQ-9: Backward Compatibility with RFC 8705 and RFC 9449.**
An APM mechanism SHOULD operate as an additive layer over existing sender-
constraining mechanisms. The APM mechanism MUST NOT weaken the `cnf/x5t#S256`
binding assurance provided by {{RFC8705}} or the `cnf/jkt` binding assurance
provided by {{RFC9449}}. Implementations that do not support APM MUST continue
to function correctly when encountering APM-related claims or response
parameters they do not recognize; unknown parameters MUST be ignored.

**REQ-10: Non-Interference with Token Revocation.**
An APM mechanism that produces a Scope Reduction or Method Restriction outcome
for a given request MUST NOT be interpreted as a token revocation event.
Access token revocation is governed by {{RFC7009}}. A graduated APM outcome
is request-scoped; the token itself remains valid for subsequent requests,
which may yield different outcome classifications if posture is restored.

# Mechanism {#mechanism}

## Overview {#mech-overview}

The APM mechanism operates at three points in the OAuth request lifecycle:

1. **Token Issuance** (Authorization Server): The AS records the Issuance
   Posture alongside the issued token.

2. **Request Processing** (Resource Server / PEP): For each Privileged
   Request, the RS or PEP assembles the Consistency View, evaluates it against
   the Issuance Posture, and determines the outcome class.

3. **Outcome Conveyance** (Resource Server / PEP to Client): The RS or PEP
   returns either a normal response (for Permit outcomes) or a structured error
   response (for Scope Reduction, Method Restriction, or Full Denial outcomes)
   that enables the client to understand its effective permission set and, if
   applicable, to take corrective action.

## Consistency View Assembly {#mech-assembly}

The Consistency View is assembled per-request at the enforcement boundary. Its
three components are obtained as follows:

**Certificate Component:**
If the access token is certificate-bound per {{RFC8705}}, the certificate
component is the SHA-256 thumbprint of the client certificate presented in
the current mutual-TLS handshake. The RS computes this thumbprint from the
certificate delivered by the TLS layer and compares it to the `cnf/x5t#S256`
claim in the token. This comparison is the existing {{RFC8705}} enforcement
step; APM does not replace or modify it.

If the access token is DPoP-bound per {{RFC9449}}, the key component is the
SHA-256 thumbprint of the public key in the DPoP proof's `jwk` header
parameter, verified against `cnf/jkt`. APM does not replace or modify the
DPoP verification step.

**Token Claims Component:**
The token claims component comprises the access token's `sub`, `scope` (or
`authorization_details`), `cnf`, `iss`, `aud`, `iat`, and `exp` claims, plus
any posture-related claims defined by the Issuance Posture recording mechanism
(see {{mech-issuance-posture}}).

**Device-Posture Signal Component:**
The device-posture signal is an integrity-protected assertion supplied by or
verifiably associated with the client for the current request. How the signal
is conveyed to the enforcement boundary is deployment-specific. Candidate
conveyance mechanisms include but are not limited to:

- A signed JWT carried in a dedicated HTTP request header.
- A signed posture claim within a DPoP proof extension field.
- A reference (handle) to a posture record previously registered with the
  AS or a posture service, verified by the RS via an out-of-band query.

Regardless of conveyance mechanism, the posture signal MUST satisfy REQ-8
(integrity protection). The RS or PEP MUST verify the integrity protection
before including the signal in the Consistency View. A posture signal that
fails integrity verification MUST be treated as absent; the enforcing entity
MUST NOT use an unverified posture signal as a basis for elevation above the
Issuance Posture.

## Issuance Posture Recording {#mech-issuance-posture}

At token issuance, the AS records the security posture of the requesting client
as the Issuance Posture. Two recording strategies are defined:

**Strategy A — Token Claim:**
The AS includes an `apm_posture` claim in the access token (JWT) or in the
token introspection response. The claim contains a compact, integrity-protected
representation of the posture attributes that were verified at issuance. The
exact structure of `apm_posture` is determined by the posture schema agreed
upon between the AS, the client, and the RS for the deployment.

Because the `apm_posture` claim is included in a token that may be presented
to third parties, implementations MUST consider the sensitivity of the posture
attributes included. Attributes that would enable re-identification or that
expose sensitive device configuration details MUST NOT be included in the token
claim form; they SHOULD be included only in the introspection-response form or
stored server-side.

**Strategy B — Server-Side Metadata:**
The AS stores the Issuance Posture server-side, associated with the token
identifier (`jti`). The RS retrieves the Issuance Posture by querying the token
introspection endpoint ({{RFC7662}}) or a dedicated APM metadata endpoint. This
strategy avoids embedding posture attributes in bearer tokens but introduces
a per-request network round-trip for posture retrieval.

## Consistency Evaluation {#mech-evaluation}

The enforcing entity evaluates the Consistency View against the Issuance Posture
using an evaluation function defined by deployment policy. The evaluation
function MUST be deterministic (REQ-3) and MUST produce one of the outcome
classes defined in REQ-4.

The evaluation proceeds in the following order:

1. **Sender constraint verification**: Verify that the certificate or key
   thumbprint in the Consistency View matches the `cnf` claim in the token.
   If this verification fails, the request MUST be denied with HTTP 401 and
   `invalid_token` error, as per {{RFC8705}} or {{RFC9449}}. APM processing
   does not proceed for requests that fail sender constraint verification.

2. **Posture signal verification**: Verify the integrity protection of the
   device-posture signal. If the signal is absent or integrity verification
   fails, the enforcing entity SHOULD treat the current posture as unknown.
   An unknown posture SHOULD be mapped to a degraded outcome class by policy.

3. **Issuance Posture retrieval**: Retrieve the Issuance Posture from the token
   claim or server-side metadata as appropriate for the deployment's recording
   strategy.

4. **Degradation vector computation**: For each posture attribute dimension
   covered by the Issuance Posture, determine whether the current value is
   equal to, better than, or worse than the issuance-time value. The set of
   dimensions that have degraded constitutes the degradation vector.

5. **Outcome class determination**: Map the degradation vector to an outcome
   class using the deployment's policy. The policy MUST be designed such that:

   - A zero-degradation vector maps to the Permit outcome.
   - An increase in the severity of the degradation vector monotonically
     increases the restrictiveness of the outcome class.
   - The outcome class for a given degradation vector is the same regardless
     of the identity of the requesting client or the specific resource being
     accessed, given the same policy.

## Graduated Outcome Application {#mech-outcomes}

The four outcome classes are ordered by increasing restrictiveness:
Permit < Scope Reduction < Method Restriction < Full Denial.
A deployment policy MUST implement this ordering monotonically: if a
degradation vector maps to Method Restriction, all more-severe degradation
vectors MUST map to Method Restriction or Full Denial.  No degradation
vector may map to a less-restrictive outcome than a strictly less-severe
degradation vector, given the same policy.

### Permit {#outcome-permit}

When the Consistency View is consistent with the Issuance Posture, the
enforcing entity permits the request to proceed under the full authorization
of the access token. No additional response headers or error codes are
generated by APM processing.

### Scope Reduction {#outcome-scope-reduction}

When the evaluation produces a Scope Reduction outcome, the enforcing entity:

1. Determines the reduced effective scope according to the deployment's
   scope-reduction policy.
2. Processes the request only for operations within the reduced effective
   scope.
3. Responds with the appropriate HTTP status code for the request (200 or
   similar for operations within scope; 403 for operations outside the reduced
   scope), and includes a `WWW-Authenticate` header on 403 responses that
   communicates both the reason and the currently effective scope.

The `WWW-Authenticate` response header for a Scope Reduction outcome SHOULD
take the following form:

~~~
WWW-Authenticate: Bearer error="apm_scope_reduced",
  error_description="Authorization posture degraded; effective scope narrowed",
  scope="<reduced-scope-string>"
~~~

Where `<reduced-scope-string>` is the space-delimited scope string representing
the effective scope under the Scope Reduction outcome. Clients receiving an
`apm_scope_reduced` error SHOULD NOT retry the denied operation without first
restoring posture. Clients MAY retry operations that fall within the reduced
scope.

### Method Restriction {#outcome-method-restriction}

When the evaluation produces a Method Restriction outcome, the enforcing entity:

1. Determines the set of permitted HTTP methods (or operation types, if the
   deployment maps `authorization_details` `actions` to HTTP methods) according
   to the deployment's method-restriction policy.
2. Processes the request only if the HTTP method of the current request is
   within the permitted set.
3. Responds with HTTP 403 and a `WWW-Authenticate` header that communicates
   the permitted method set for requests that use a non-permitted method.

The `WWW-Authenticate` response header for a Method Restriction outcome SHOULD
take the following form:

~~~
WWW-Authenticate: Bearer error="apm_method_restricted",
  error_description="Authorization posture degraded; method not permitted",
  allowed_methods="GET HEAD"
~~~

Where `allowed_methods` is a space-delimited list of currently permitted HTTP
methods. Clients receiving an `apm_method_restricted` error SHOULD inspect the
`allowed_methods` value and, if the required operation is unavailable, SHOULD
initiate posture remediation or re-authorization rather than retrying the
denied method.

### Full Denial {#outcome-full-denial}

When the evaluation produces a Full Denial outcome, the enforcing entity
responds with HTTP 401 and a `WWW-Authenticate` challenge. Full Denial
challenges SHOULD use the `insufficient_authorization_posture` error code
(see {{iana-error-codes}}) rather than the generic `invalid_token`, to enable
the client to distinguish a posture-triggered denial from a token validity
failure. Clients receiving a Full Denial outcome SHOULD initiate posture
remediation and re-authorization.

The `WWW-Authenticate` response header for a Full Denial outcome SHOULD take
the following form:

~~~
WWW-Authenticate: Bearer error="insufficient_authorization_posture",
  error_description="Authorization posture degraded below minimum threshold"
~~~

## Sequence Diagram {#mech-sequence}

The following diagram illustrates the APM flow for a Privileged Request that
results in a Method Restriction outcome. Steps 1-4 are the existing OAuth
sender-constraint flow (unchanged); steps 5-9 are the additional APM steps.

~~~
 Client          Resource Server (RS / PEP)    Authorization Server (AS)
   |                        |                            |
   |  (1) Token Request                                  |
   |  (with posture signal)                              |
   |---------------------------------------------------->|
   |                                                     |
   |                        |  (2) Issuance Posture      |
   |                        |  recorded server-side      |
   |                        |       [AS internal]        |
   |                                                     |
   |  (3) Access Token      |                            |
   |<----------------------------------------------------|
   |                        |                            |
   |  (4) Privileged        |                            |
   |  Request               |                            |
   |  Authorization:        |                            |
   |  DPoP <token>          |                            |
   |  DPoP: <proof>         |                            |
   |  APM-Posture: <signal> |                            |
   |----------------------->|                            |
   |                        |                            |
   |                        | (5) Verify sender          |
   |                        | constraint (RFC 8705       |
   |                        | / RFC 9449)                |
   |                        |---+                        |
   |                        |   | verify cnf/jkt vs      |
   |                        |   | DPoP jwk thumbprint    |
   |                        |<--+                        |
   |                        |                            |
   |                        | (6) Retrieve Issuance      |
   |                        | Posture (token claim or    |
   |                        | introspection query)       |
   |                        |---+  [or via introspect]  |
   |                        |   |<--------------------> |
   |                        |<--+                        |
   |                        |                            |
   |                        | (7) Assemble Consistency   |
   |                        | View: verify posture       |
   |                        | signal integrity;          |
   |                        | compute degradation vector |
   |                        |---+                        |
   |                        |<--+                        |
   |                        |                            |
   |                        | (8) Apply policy:          |
   |                        | degradation vector ->      |
   |                        | Method Restriction outcome |
   |                        |---+                        |
   |                        |<--+                        |
   |                        |                            |
   | (9) HTTP 403           |                            |
   | WWW-Authenticate:      |                            |
   |  error="apm_method_   |                            |
   |  restricted"           |                            |
   |  allowed_methods="GET" |                            |
   |<-----------------------|                            |
   |                        |                            |
   | (10) Client inspects   |                            |
   | WWW-Authenticate;      |                            |
   | retries with GET or    |                            |
   | initiates posture      |                            |
   | remediation            |                            |
   |---+                    |                            |
   |<--+                    |                            |
~~~
{: #fig-sequence title="APM Flow for Method Restriction Outcome"}

# Message Formats {#message-formats}

## Issuance Posture Claim {#fmt-issuance-posture}

When recording Issuance Posture as a token claim (Strategy A from
{{mech-issuance-posture}}), the claim name is `apm_posture`. The claim value is
a JSON object. The schema of the `apm_posture` object is deployment-defined;
this document specifies the following registered sub-fields:

`posture_at`:
: (REQUIRED) A JSON numeric value representing the time at which posture was
  recorded, as a NumericDate per {{RFC7519}} §2.

`posture_hash`:
: (OPTIONAL) A base64url-encoded, integrity-protected digest of the posture
  attributes evaluated at issuance. The digest algorithm and the input format
  are identified by the `posture_alg` sub-field.

`posture_alg`:
: (OPTIONAL, REQUIRED if `posture_hash` is present) A string identifying the
  algorithm used to compute `posture_hash`. Values follow the JOSE JWA registry
  ({{RFC7518}}). Implementations SHOULD use `S256` (SHA-256).

`posture_ref`:
: (OPTIONAL) A URI pointing to a server-side posture record that can be
  retrieved for detailed comparison. Use of `posture_ref` requires that the RS
  or PEP have the ability to dereference the URI.

Example `apm_posture` claim embedded in a JWT access token:

~~~json
{
  "iss": "https://as.example.com",
  "sub": "user@example.com",
  "aud": "https://rs.example.com",
  "exp": 1800000000,
  "iat": 1799996400,
  "jti": "aB3xQ9pR",
  "cnf": {
    "jkt": "NzbLsXh8uDCcd-6MNwXF4W_7noWXFZAfHkxZsRGC9Xs"
  },
  "scope": "records:read records:write",
  "apm_posture": {
    "posture_at": 1799996400,
    "posture_hash": "Y2Fj...base64url-truncated",
    "posture_alg": "S256",
    "posture_ref": "https://as.example.com/posture/aB3xQ9pR"
  }
}
~~~
{: #fig-token-posture-claim title="JWT Access Token with apm_posture Claim"}

## Consistency View Request Headers {#fmt-cv-headers}

When conveying the device-posture signal as an HTTP request header, the header
name is `APM-Posture`. The value is a compact-serialized signed JWT (a JWS in
compact serialization per {{RFC7519}}) whose payload contains the posture
attributes as of the time of the request.

The APM-Posture JWT MUST include the following claims:

`iat`:
: (REQUIRED) Issuance time of the posture assertion. Resource servers and PEPs
  MUST reject posture assertions whose `iat` is more than a deployment-defined
  freshness window in the past. A freshness window of 60 seconds is RECOMMENDED
  for most deployments; lower values SHOULD be used for high-sensitivity
  resources.

`jti`:
: (REQUIRED) A per-request unique identifier for the posture assertion. Resource
  servers and PEPs SHOULD maintain a short-term record of seen `jti` values to
  detect replayed posture assertions.

`sub`:
: (REQUIRED) The subject for which posture is asserted. MUST match the `sub`
  claim of the bound access token.

`ath`:
: (REQUIRED) A base64url-encoded SHA-256 hash of the access token to which this
  posture assertion is bound, following the same convention as the DPoP `ath`
  claim in {{RFC9449}} §4.2. This binds the posture assertion to the specific
  token presented in the same request.

Additional posture-specific claims are deployment-defined. The signing key for
the APM-Posture JWT SHOULD be a key controlled by a posture attestation service
that is distinct from the client itself, to satisfy REQ-8.

Example APM-Posture JWT payload:

~~~json
{
  "iat": 1799996400,
  "jti": "posture-X7pQ2w",
  "sub": "user@example.com",
  "ath": "fUHyO2r8tx45IsZggRC...base64url-truncated"
}
~~~
{: #fig-posture-jwt title="APM-Posture JWT Payload (Deployment-Specific Fields Omitted)"}

## Graduated Outcome Error Responses {#fmt-error-responses}

### Scope Reduction Response {#fmt-scope-reduction-resp}

~~~
HTTP/1.1 403 Forbidden
WWW-Authenticate: Bearer realm="example",
  error="apm_scope_reduced",
  error_description="Authorization posture degraded; effective scope reduced",
  scope="records:read"
~~~
{: #fig-scope-reduction title="Scope Reduction 403 Response"}

The `scope` parameter in the `WWW-Authenticate` header reflects the reduced
effective scope. The client MAY retry the request using only the operations
within the indicated scope. The client SHOULD NOT retry the denied operation
until posture is remediated and a new access token is issued under the full
scope.

### Method Restriction Response {#fmt-method-restriction-resp}

~~~
HTTP/1.1 403 Forbidden
WWW-Authenticate: Bearer realm="example",
  error="apm_method_restricted",
  error_description="Authorization posture degraded; method not permitted",
  allowed_methods="GET HEAD OPTIONS"
~~~
{: #fig-method-restriction title="Method Restriction 403 Response"}

The `allowed_methods` parameter lists the HTTP methods the RS will process for
this client under the current degraded posture. Methods not in `allowed_methods`
will be rejected with the same `apm_method_restricted` error.

### Full Denial Response {#fmt-full-denial-resp}

~~~
HTTP/1.1 401 Unauthorized
WWW-Authenticate: Bearer realm="example",
  error="insufficient_authorization_posture",
  error_description="Authorization posture degraded below minimum threshold"
~~~
{: #fig-full-denial title="Full Denial 401 Response"}

Clients receiving this response MUST initiate posture remediation before
retrying. Clients SHOULD surface a user-facing notification indicating that
access has been suspended due to a security posture issue.

### Interaction with RFC 9470 Step-Up {#fmt-step-up-interaction}

An APM Full Denial response and an RFC 9470 step-up challenge address different
conditions and MUST NOT be conflated:

- An RFC 9470 `insufficient_user_authentication` challenge means the
  authentication event associated with the token does not meet the RS's
  requirements. The remedy is re-authentication.

- An APM `insufficient_authorization_posture` challenge means the device
  security posture associated with the token does not meet the RS's requirements.
  The remedy is posture remediation followed by re-authorization.

A resource server MAY issue both challenges simultaneously. Per RFC 9110
§11.6.1, a single response MAY include multiple `WWW-Authenticate` header
fields, each carrying one challenge, when both conditions are present:

~~~
HTTP/1.1 401 Unauthorized
WWW-Authenticate: Bearer realm="example",
  error="insufficient_user_authentication",
  acr_values="urn:mace:incommon:iap:silver",
  max_age=300
WWW-Authenticate: Bearer realm="example",
  error="insufficient_authorization_posture",
  error_description="Device posture below minimum threshold"
~~~
{: #fig-combined-challenge title="Combined Step-Up and APM Challenge (Two WWW-Authenticate Header Fields)"}

Clients MUST resolve both challenges before retrying a request that produced
both errors. The order in which challenges are resolved is client-defined, but
both MUST be resolved before a new request is made to the protected resource.

# Security Considerations {#security}

## Confused-Deputy Hazard from Partial Operation Under Reduced Scope {#sec-confused-deputy}

When a Scope Reduction or Method Restriction outcome is produced, the client
continues to hold a valid access token that nominally authorizes a broader set
of operations. If the client uses this token to perform operations within the
reduced effective scope at a resource server that does not implement APM, those
operations will succeed against the nominal token scope, without the posture
evaluation that an APM-aware resource server would apply.

Implementations MUST NOT assume that APM enforcement is applied uniformly across
all resource servers that accept the same token. Token issuance should scope
tokens to the specific resource server audience (`aud`) that implements APM
enforcement for the protected resources in question. Token issuance for
multiple-audience tokens in APM deployments SHOULD be approached with caution.

Clients that receive a Scope Reduction or Method Restriction error from an APM-
aware resource server SHOULD NOT use the same access token to perform the
restricted operations against a different, potentially non-APM-aware resource
server that shares the same audience.

## Replay of Stale Posture Signal {#sec-posture-replay}

The device-posture signal is integrity-protected but may be replayed by an
attacker who captures a previously valid signal. Replaying a posture signal
generated at a time when the device was in a higher-compliance state allows the
attacker to present an artificially elevated Consistency View.

Mitigations:

1. The APM-Posture JWT MUST include a per-request `jti` claim. Resource servers
   and PEPs MUST maintain a short-term cache of seen `jti` values and MUST
   reject posture assertions with `jti` values seen within the freshness window.

2. The APM-Posture JWT MUST include an `iat` claim. Resource servers and PEPs
   MUST reject posture assertions whose `iat` is more than the freshness window
   in the past.

3. The APM-Posture JWT MUST include an `ath` claim binding the posture assertion
   to the specific access token of the current request. A posture assertion
   generated for one token MUST NOT be accepted when a different token is
   presented.

4. The freshness window SHOULD be set to a value compatible with the sensitivity
   of the protected resources. A freshness window of 60 seconds is sufficient for
   many deployments; high-sensitivity resources MAY require shorter windows.

## Downgrade-Forcing Attack {#sec-downgrade}

An attacker with the ability to manipulate the device-posture signal in transit
— or to prevent the device-posture signal from reaching the resource server —
may attempt to force a lower-posture Consistency View than the actual device
state. The intent may be to cause a Method Restriction or Full Denial outcome
that disrupts service (a denial-of-service variant), or to cause the client to
reveal information about which resources require what posture levels (a
reconnaissance variant).

Mitigations:

1. The device-posture signal MUST be integrity-protected. If the signal is
   absent, the enforcing entity SHOULD treat current posture as unknown and
   apply a conservative outcome class. Implementations that treat an absent
   posture signal as equivalent to a passing posture SHOULD NOT be deployed in
   contexts requiring ZTA-level per-request evaluation.

2. Transport of the APM-Posture JWT MUST be protected by TLS ({{RFC8446}} or
   later). Implementations MUST NOT transmit posture signals over unprotected
   transports.

3. The signing key for the APM-Posture JWT SHOULD be managed by a posture
   attestation service separate from the client, so that a compromised client
   cannot forge an elevated posture signal.

## Side-Channel from Per-Request Posture Evaluation {#sec-side-channel}

Per-request posture evaluation introduces observable timing and response
differences between requests that pass and requests that fail. An attacker who
can make multiple requests and observe the response codes and timing may be able
to infer information about the target system's posture policy — for example,
which degradation dimensions trigger which outcome classes.

Implementations SHOULD take care that:

1. Error responses do not include more detail than necessary to enable client
   recovery. The `error_description` field SHOULD be informative but SHOULD NOT
   enumerate the specific posture attributes that caused the outcome.

2. The timing of posture evaluation SHOULD NOT be correlated with the specific
   posture attributes being compared in a way that enables attribute inference.

3. Deployments SHOULD log APM evaluation events for audit purposes but SHOULD
   NOT expose these logs via unauthenticated channels.

## Interaction with Token Introspection {#sec-introspection}

When Strategy B (server-side Issuance Posture metadata) is used, the resource
server queries the authorization server's token introspection endpoint
({{RFC7662}}) to retrieve the Issuance Posture per-request. This introduces a
network dependency that is not present in the token-claim strategy.

Implementations using the introspection strategy MUST:

1. Authenticate to the token introspection endpoint per {{RFC7662}} §2.1.
2. Protect the introspection query with TLS.
3. Consider the latency and availability implications of a per-request
   introspection call.
4. Cache introspection responses only for durations compatible with the
   required posture freshness; posture-related introspection responses SHOULD
   NOT be cached beyond the APM freshness window.

## Non-Weakening of RFC 8705 Binding {#sec-no-weaken}

APM processing MUST NOT weaken the sender-constraint binding provided by
{{RFC8705}} or {{RFC9449}}. Specifically:

1. APM evaluation begins only after sender-constraint verification succeeds.
   A Scope Reduction or Method Restriction outcome from APM does not exempt the
   request from the requirement to pass {{RFC8705}} or {{RFC9449}} verification.

2. APM outcome state (e.g., "this token is currently under a Method Restriction
   outcome") MUST NOT be used as a basis for relaxing the sender-constraint
   verification requirement.

3. The `cnf` claim in the access token is not modified by APM processing; it
   retains its original value for all subsequent verification steps.

## Posture Signal Integrity and Trust Anchors {#sec-trust-anchor}

The security of the APM mechanism depends on the trustworthiness of the posture
signal. Deployments MUST establish a trust anchor for posture signal signing
keys that is independent of the client. Possible trust anchors include:

- A hardware-rooted attestation mechanism (e.g., TPM, Secure Enclave,
  Android Hardware Attestation) where the signing key is bound to the device
  hardware and cannot be exported.
- A Mobile Device Management platform that signs posture assertions on behalf
  of enrolled devices.
- A remote attestation verifier (in the sense of the IETF RATS architecture,
  {{RFC9334}}) that vouches for the client's posture independently.

Where no independent trust anchor is available and the client must self-sign
posture assertions, the enforcing entity SHOULD apply a lower level of trust
to the resulting Consistency View and SHOULD map unknown or client-self-signed
posture signals to a more restrictive outcome class.

# IANA Considerations {#iana}

## OAuth Parameters Registry {#iana-oauth-params}

This document requests registration of the following entries in the OAuth
Parameters Registry established by {{RFC6749}} and maintained by IANA.

### apm_posture {#iana-apm-posture-param}

Parameter name:
: `apm_posture`

Parameter usage location:
: access token (JWT claim); token introspection response

Change controller:
: IETF

Specification document:
: This document, Section 6.1

Description:
: A JSON object recording the Issuance Posture at the time the access token
  was issued. Used as the baseline for per-request Consistency View evaluation.

## OAuth Extensions Error Registry {#iana-error-codes}

This document requests registration of the following error codes in the OAuth
Extensions Error Registry per {{RFC6749}} Appendix A.7:

### apm_scope_reduced {#iana-error-scope-reduced}

Error name:
: `apm_scope_reduced`

Error usage location:
: Resource access (bearer token error, {{RFC6750}} §3.1)

Change controller:
: IETF

Specification document:
: This document, Section 6.3.1

Description:
: The request was processed under a reduced effective scope because the
  per-request Consistency View indicated posture degradation below the
  scope-reduction threshold. The `scope` parameter in the `WWW-Authenticate`
  header conveys the reduced effective scope.

### apm_method_restricted {#iana-error-method-restricted}

Error name:
: `apm_method_restricted`

Error usage location:
: Resource access (bearer token error, {{RFC6750}} §3.1)

Change controller:
: IETF

Specification document:
: This document, Section 6.3.2

Description:
: The requested HTTP method is not permitted under the Method Restriction
  outcome produced by per-request Consistency View evaluation. The
  `allowed_methods` parameter in the `WWW-Authenticate` header conveys the set
  of currently permitted methods.

### insufficient_authorization_posture {#iana-error-full-denial}

Error name:
: `insufficient_authorization_posture`

Error usage location:
: Resource access (bearer token error, {{RFC6750}} §3.1)

Change controller:
: IETF

Specification document:
: This document, Section 6.3.3

Description:
: The per-request Consistency View indicated posture degradation severe enough
  to warrant full denial of the request. The token remains valid; the client
  SHOULD initiate posture remediation and re-authorization before retrying.

## JWT Claims Registry {#iana-jwt-claims}

This document requests registration of the following entries in the JSON Web
Token Claims Registry established by {{RFC7519}} §10.1:

### apm_posture Claim {#iana-jwt-posture-claim}

Claim name:
: `apm_posture`

Claim description:
: Issuance Posture recorded at token issuance time for use in APM Consistency
  View evaluation.

Change controller:
: IETF

Specification document:
: This document, Section 6.1

## Experimental OID Arc {#iana-pen}

The IANA Private Enterprise Number (PEN) arc `1.3.6.1.4.1.65953` is allocated
to Sanctum SecOps LLC. Extensions, OIDs, or experimental identifiers under this
arc are for experimental and private use only. Any identifiers under this arc
MUST NOT be used in IETF-standardized protocols in place of IANA-registered
identifiers. Permanent IANA assignments for APM-related identifiers are
requested via the procedures described in this section; TBD placeholders will be
resolved during IETF processing.

# References {#references}

## Normative References

The following documents are referenced normatively.

RFC 2119, RFC 8174, RFC 6749, RFC 6750, RFC 7519, RFC 8705, RFC 9449.

## Informative References

The following documents are referenced for context and comparison.

RFC 7009, RFC 7662, RFC 8414, RFC 9068, RFC 9334, RFC 9396, RFC 9470, RFC 9794.

--- back

# Acknowledgements {#acknowledgements}
{:unnumbered}

The author thanks the members of the IETF OAuth Working Group for their work on
the specifications analyzed in this document. The analysis of gap areas builds
on the normative text of RFC 8705, RFC 9449, RFC 9470, RFC 9396, and the OpenID
CAEP specification, and the author acknowledges the authors of those documents
for their foundational contributions.

# Appendix A: Gap Summary Table {#appendix-gaps}
{:unnumbered}

The following table summarizes the coverage of APM requirements by existing
specifications. The symbol "Y" indicates the requirement is addressed
normatively; "P" indicates partial coverage with identified gaps; "N" indicates
the requirement is not addressed.

~~~
Requirement         | RFC   | RFC   | RFC   | RFC   | RFC   | CAEP  | Attest
                    | 6749  | 8705  | 9449  | 9470  | 9396  |       | Draft
--------------------+-------+-------+-------+-------+-------+-------+-------
REQ-1 Triple        |       |       |       |       |       |       |
Assembly            |  N    |  P    |  P    |  N    |  N    |  N    |  P
--------------------+-------+-------+-------+-------+-------+-------+-------
REQ-2 Issuance      |       |       |       |       |       |       |
Posture Recording   |  N    |  N    |  N    |  N    |  N    |  N    |  P
--------------------+-------+-------+-------+-------+-------+-------+-------
REQ-3 Consistency   |       |       |       |       |       |       |
Evaluation          |  N    |  N    |  N    |  N    |  N    |  N    |  N
--------------------+-------+-------+-------+-------+-------+-------+-------
REQ-4 Graduated     |       |       |       |       |       |       |
Outcomes            |  N    |  N    |  N    |  N    |  N    |  N    |  N
--------------------+-------+-------+-------+-------+-------+-------+-------
REQ-5 Method        |       |       |       |       |       |       |
Restriction         |  N    |  N    |  N    |  N    |  P    |  N    |  N
--------------------+-------+-------+-------+-------+-------+-------+-------
REQ-6 Scope         |       |       |       |       |       |       |
Reduction           |  N    |  N    |  N    |  N    |  P    |  N    |  N
--------------------+-------+-------+-------+-------+-------+-------+-------
REQ-7 Deterministic |       |       |       |       |       |       |
Outcome Mapping     |  N    |  N    |  N    |  N    |  N    |  N    |  N
--------------------+-------+-------+-------+-------+-------+-------+-------
REQ-8 Integrity-    |       |       |       |       |       |       |
Protected Signal    |  N    |  N    |  N    |  N    |  N    |  P    |  Y
--------------------+-------+-------+-------+-------+-------+-------+-------
REQ-9 Backward      |       |       |       |       |       |       |
Compatible          |  -    |  -    |  -    |  -    |  -    |  -    |  -
--------------------+-------+-------+-------+-------+-------+-------+-------
REQ-10 Non-         |       |       |       |       |       |       |
Revocation          |  -    |  -    |  -    |  -    |  -    |  -    |  -

Y = addressed normatively
P = partially addressed with identified gaps
N = not addressed
- = not applicable (requirement specifies APM behavior, not existing spec scope)
~~~
{: #fig-gap-table title="APM Requirement Coverage by Existing Specifications"}

# Appendix B: Relationship to NIST ZTA Model {#appendix-zta}
{:unnumbered}

NIST SP 800-207 {{NIST.SP.800-207}} defines a three-component decision plane
consisting of the Policy Engine (PE), Policy Administrator (PA), and Policy
Enforcement Point (PEP). APM maps to this architecture as follows:

The **Policy Enforcement Point** is the resource server or a co-located
enforcement component. The PEP assembles the Consistency View (REQ-1), applies
sender-constraint verification, and enforces the graduated outcome produced by
policy evaluation.

The **Policy Engine** consumes the Consistency View and the Issuance Posture and
produces an outcome class. The PE function may be co-located with the PEP or
may be a distinct service queried per-request. The PE is responsible for the
deterministic outcome mapping required by REQ-7.

The **Policy Administrator** communicates policy configuration from the
authorization server to the PEP — for example, conveying the scope-reduction
policy and the method-restriction policy that the PEP applies when evaluating
Consistency Views. The PA may also be responsible for recording the Issuance
Posture at token issuance time (REQ-2).

This mapping illustrates that APM does not introduce new architectural roles
beyond those already defined in {{NIST.SP.800-207}}; rather, it specifies the
information flows — specifically, the Consistency View and Issuance Posture —
that are needed to enable the PE to make the per-request graduated-outcome
decisions that ZTA requires.

# Appendix C: Comparison with Transaction Tokens {#appendix-txn-tokens}
{:unnumbered}

Transaction Tokens {{I-D.ietf-oauth-transaction-tokens}} address a related but
distinct problem: propagating user identity and authorization context across
workloads within a trusted domain during processing of a single external request.
Transaction Tokens are scoped to intra-domain service-mesh use cases and
explicitly MUST NOT include the access token they were derived from.

APM addresses the boundary between the external client and the resource server —
the initial protected-resource access, not the subsequent intra-domain hops.
The two mechanisms are complementary: an RS that receives an APM-evaluated
request may subsequently issue Transaction Tokens for intra-domain workload
propagation. The graduated outcome produced at the APM boundary informs the
scope of the Transaction Token(s) issued downstream.

Specifically, if the APM evaluation produces a Scope Reduction outcome (e.g.,
reducing effective scope from `records:read records:write` to `records:read`),
a Transaction Token Service that derives a Txn-Token from the external access
token MUST honor the APM reduced scope and MUST NOT issue a Txn-Token whose
`scope` claim exceeds the APM-reduced effective scope.
