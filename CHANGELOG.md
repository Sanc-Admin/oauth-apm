# Changelog — draft-vicente-oauth-apm

## -03 (2026-08-12)

### Added
- **Gap D**: No normative threat-response pathway for active cryptographic threat
  events (HNDL interception signals, algorithm-agility triggers, cryptographic
  oracle events). Documented in §1.4 and §3.5.
- **REQ-11**: Threat-Event Integration — normative requirement for a Threat-Event
  Signal channel enabling external network/cryptographic monitors to drive
  per-request APM graduated outcomes.
- **REQ-12**: Algorithm-Agility Trigger — normative requirement for escalating
  outcome class when the algorithm family used in `cnf/x5t#S256` or `cnf/jkt`
  binding is deprecated or under active attack.
- **`alg_upgrade_required`** error code — new IANA OAuth Extensions Error
  registration for Method Restriction outcomes triggered by algorithm deprecation.
- **APM Threat Event Type Registry** — new IANA registry request for threat event
  type vocabulary (`hndl_interception`, `algorithm_deprecation`,
  `cryptographic_oracle`).
- **Threat-Event Signal JWT format** — §6.2 defines signed JWT structure for
  machine-readable threat event delivery to RS/PEP.
- **`threat_type`, `threat_scope`, `min_outcome`** JWT claims — new IANA JWT
  Claims Registry registration requests.
- **`evidence/apm-gap-evidence.json`** — machine-readable gap-to-requirement
  evidence mapping with RFC/NIST/FIPS citations and IP notice.
- **Security considerations** for Gap D mechanisms: Threat-Event Signal integrity,
  HNDL detection limitations, Algorithm-Agility Trigger as DoS vector.
- **IPR notice** updated to reference Gap D / REQ-11 / REQ-12 subject matter.

### Modified
- Abstract updated to describe -03 additions.
- Conventions section: Added definitions for Threat Event, Threat-Event Signal,
  HNDL, Algorithm-Agility Trigger.
- Consistency View definition extended to include (d) Threat-Event Signal Component.
- Consistency evaluation sequence (§5.4) extended with steps 5-6 for
  Threat-Event Signal lookup and combined outcome determination.
- Date updated to 2026-08-12.

---

## -02 (2026-06-06)

### Modified
- Minor editorial corrections from -01 review.
- Date updated.

---

## -01 (initial)

### Added
- Initial problem statement: Gaps A, B, C.
- Requirements REQ-1 through REQ-10.
- APM mechanism: Consistency View assembly, Issuance Posture recording,
  graduated outcome framework (Permit, Scope Reduction, Method Restriction,
  Full Denial).
- IANA registrations: `apm_scope_reduced`, `apm_method_restricted`,
  `insufficient_authorization_posture`, `apm_posture` JWT claim.
- Security considerations: confused-deputy hazard, posture replay,
  downgrade-forcing, side-channel, introspection interaction, non-weakening
  of RFC 8705 binding, trust anchor requirements.
