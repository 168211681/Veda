RFC-0048 — Neural Marketplace

Status: Draft
Version: 1.0
Layer: Layer 18 — Ecosystem
Depends on: RFC-0038, RFC-0039, RFC-0041, RFC-0047
Related: RFC-0042, RFC-0043, RFC-0044, RFC-0045, RFC-0046

⸻

1. Abstract

RFC-0048 defines the Neural Marketplace, a discovery, distribution, evaluation, transaction, and lifecycle-management layer for the Veda ecosystem.

The Neural Marketplace allows participants to discover, evaluate, acquire, install, update, license, share, and retire Neural Packages defined by RFC-0047.

Supported package categories include:

* Models
* Knowledge
* Memory
* Skills
* Tools
* Agents
* Worlds
* Workflows
* Datasets
* Evaluations
* Policies
* Bundles

The Marketplace provides an economic and distribution layer around these artifacts.

It does not become the authority over:

* Identity
* Trust
* Authorization
* Reality
* Package correctness
* Package safety
* Veda Constitution
* Human decisions

The fundamental separation is:

Marketplace
    ↓
Discovery / Distribution / Transaction
    ↓
Neural Package
    ↓
Verification / Security / Conformance
    ↓
Installation
    ↓
Activation
    ↓
Capability / Authorization
    ↓
Execution

Acquiring a package does not grant it authority.

Downloading a package does not make it trusted.

A marketplace listing does not make a package verified.

A high rating does not prove correctness.

A package being installed does not mean it is active.

An active package does not automatically receive capabilities.

⸻

2. Motivation

As the Veda ecosystem grows, intelligence and capabilities will increasingly exist as independently distributable artifacts.

Without a marketplace/distribution architecture, users would need to manually:

1. discover packages,
2. inspect metadata,
3. verify publishers,
4. download artifacts,
5. verify integrity,
6. inspect dependencies,
7. inspect licenses,
8. evaluate security,
9. benchmark compatibility,
10. install,
11. configure,
12. update,
13. rollback,
14. revoke,
15. track provenance.

This creates fragmentation and supply-chain risk.

RFC-0048 therefore defines a standardized marketplace layer.

The marketplace should make package acquisition easier without weakening Veda’s authority boundaries.

⸻

3. Design Principles

3.1 Marketplace Is Not Authority

The Marketplace may distribute an artifact.

It may not authorize the artifact to act.

Marketplace
    ≠
Authorization

⸻

3.2 Listing Is Not Verification

A package can be listed while remaining:

UNVERIFIED

Verification must come from explicit evidence and verification procedures.

⸻

3.3 Rating Is Not Trust

User ratings are observations about user experience.

They are not cryptographic proof.

They are not security certification.

They are not authorization.

⸻

3.4 Purchase Is Not Installation

A transaction creates an entitlement.

It does not necessarily install anything.

Acquire
  ↓
Verify
  ↓
Install
  ↓
Activate

Each stage remains independently controlled.

⸻

3.5 Installation Is Not Activation

A package may be installed but disabled.

This permits:

* staged deployment,
* testing,
* rollback,
* quarantine,
* offline inspection,
* benchmark testing.

⸻

3.6 Activation Is Not Authority

Even an active package cannot exceed its granted capabilities.

Installed Package
        ↓
Active Package
        ↓
Capability Request
        ↓
Authorization
        ↓
Capability Lease
        ↓
Action

⸻

3.7 Evidence Over Marketing

Marketplace claims must be distinguishable from independently verified evidence.

A package page must separate:

Publisher Claims
User Reports
Marketplace Validation
Independent Evaluation
Cryptographic Evidence
Conformance Results
Security Findings

⸻

3.8 Reproducibility

Where practical, evaluations should identify:

* package version,
* benchmark version,
* evaluation engine,
* evaluation environment,
* hardware,
* configuration,
* date,
* evaluator,
* evidence.

Current work in the W3C ecosystem is also moving toward reproducible agent conformance and benchmark reporting, reinforcing the importance of making evaluation claims independently checkable. (W3C)

⸻

4. Terminology

4.1 Marketplace

A service that provides discovery, metadata, evaluation information, distribution, transactions, and lifecycle services for Neural Packages.

4.2 Listing

A marketplace representation of a package.

4.3 Publisher

Entity responsible for publishing a package.

4.4 Buyer

Entity acquiring package rights or access.

4.5 Evaluator

Entity performing package evaluation.

4.6 Marketplace Operator

Entity operating marketplace infrastructure.

4.7 Entitlement

Evidence that an entity has acquired a defined right to use or access a package.

4.8 Package Artifact

Actual package content defined by RFC-0047.

4.9 Package Version

Specific immutable version of a package.

4.10 Review

User-generated or evaluator-generated feedback.

4.11 Conformance Result

Evidence that a package passed a defined conformance suite.

4.12 Security Finding

A documented security observation or vulnerability assessment.

⸻

5. Marketplace Object Model

The primary relationship is:

Publisher
    ↓
Package
    ↓
Version
    ↓
Listing
    ↓
Evaluation
    ↓
Entitlement
    ↓
Delivery
    ↓
Installation
    ↓
Activation

⸻

6. Marketplace Listing

A Listing MUST contain sufficient metadata to allow meaningful evaluation before acquisition.

Minimum fields:

listing_id
listing_version
package_id
package_version
publisher_id
package_type
name
description
manifest_ref
artifact_hash
signature_ref
license
dependencies
capabilities_declared
resource_requirements
privacy_profile
security_profile
risk_profile
compatibility
conformance_results
evaluation_results
pricing
availability
status
created_at
updated_at

⸻

7. Package Identity

Every listing MUST reference a specific RFC-0047 package identity.

A marketplace MUST NOT silently replace the underlying package.

The following MUST remain distinguishable:

Package Identity
Package Version
Artifact Hash
Publisher Identity
Marketplace Listing
Marketplace Listing Version

⸻

8. Publisher Identity

Publisher identity MUST integrate with RFC-0039.

The Marketplace MUST support:

Publisher
    ↓
Cryptographic Identity
    ↓
Signed Package
    ↓
Package Hash

Publisher identity does not imply package correctness.

Publisher reputation does not replace verification.

⸻

9. Package Discovery

The Marketplace MUST support structured discovery.

Possible search dimensions:

Package

* type
* name
* version
* publisher
* dependencies
* compatibility

Capability

* declared capabilities
* required capabilities
* tool interfaces
* supported protocols

Hardware

* CPU
* GPU
* RAM
* storage
* accelerator
* architecture

Privacy

* local-only
* cloud-required
* telemetry
* network access
* data retention

Security

* security status
* known vulnerabilities
* sandbox requirement
* permission profile

Evaluation

* conformance
* benchmark
* test coverage
* reproducibility

Economic

* free
* paid
* subscription
* usage-based
* license-based

⸻

10. Search Semantics

Marketplace search MUST distinguish:

Text Match
Capability Match
Compatibility Match
Trust Evidence
Conformance
Security
Publisher Reputation
User Reviews
Price

These dimensions MUST NOT be collapsed into one opaque ranking.

A package being highly popular does not mean it is secure.

A package being cheap does not mean it is compatible.

A package having strong benchmarks does not mean it is authorized to act.

⸻

11. Marketplace Recommendation

The Marketplace MAY recommend packages.

Recommendations MUST expose their basis.

Example:

Recommended because:
Capability Match: 94%
Hardware Compatibility: Confirmed
Conformance: Passed
Security Scan: Passed
Privacy: Local-only
Price: Free
Publisher Identity: Verified

The system MUST NOT represent an internal recommendation score as objective truth.

⸻

12. Evaluation Model

Package evaluation SHOULD include:

Identity Verification
Integrity Verification
Dependency Analysis
License Validation
Security Analysis
Compatibility Testing
Functional Testing
Conformance Testing
Performance Benchmarking
Resource Benchmarking
Privacy Analysis
Reproducibility Testing

Evaluation results MUST include provenance.

⸻

13. Conformance

Conformance MUST reference an explicit specification.

Example:

Specification:
NPF 1.0
Test Suite:
NPF-CONFORMANCE-1.2
Environment:
Linux x86_64
Engine:
Veda Conformance Engine 0.8
Result:
PASS
Date:
2026-09-15

A package MUST NOT claim generic “verified” status without identifying what was verified.

⸻

14. Security Evaluation

Security evaluation MAY include:

* static analysis,
* dependency scanning,
* malware detection,
* sandbox execution,
* network behavior analysis,
* permission analysis,
* secret-access analysis,
* filesystem-access analysis,
* supply-chain verification,
* signature verification.

Security evaluation is time-bound.

A package marked secure today MUST NOT automatically remain secure forever.

⸻

15. Security Advisories

The Marketplace MUST support security advisories.

Advisory fields:

advisory_id
package_id
affected_versions
severity
description
evidence
discovery_source
published_at
fixed_versions
mitigation
status

Possible statuses:

OPEN
MITIGATED
FIXED
DISPUTED
RESOLVED
WITHDRAWN

⸻

16. Package Lifecycle

Marketplace lifecycle:

SUBMITTED
    ↓
IDENTITY_VERIFIED
    ↓
VALIDATING
    ↓
EVALUATING
    ↓
APPROVED_FOR_LISTING
    ↓
PUBLISHED
    ↓
AVAILABLE
    ↓
DEPRECATED
    ↓
RETIRED

Security-related states:

SUSPENDED
QUARANTINED
RECALLED
REVOKED

⸻

17. Submission

Publisher submission MUST include:

* package artifact,
* RFC-0047 manifest,
* package hash,
* signature,
* publisher identity,
* license metadata,
* dependencies,
* capability declarations,
* resource requirements,
* privacy profile,
* security profile,
* compatibility information.

Marketplace submission MUST create an auditable event.

⸻

18. Validation

Validation SHOULD include:

Manifest Validation
Schema Validation
Hash Validation
Signature Validation
Dependency Resolution
License Validation
Package Structure Validation
Artifact Integrity Validation

Failure MUST prevent automatic publication when policy requires fail-closed behavior.

⸻

19. Dependency Resolution

The Marketplace MUST inspect dependencies.

Dependency graph:

Package A
 ├── Package B
 │    ├── Package C
 │    └── Package D
 └── Package E

The system MUST detect:

* version conflicts,
* missing dependencies,
* circular dependencies,
* revoked dependencies,
* vulnerable dependencies,
* incompatible dependencies,
* excessive privilege requirements.

⸻

20. Dependency Trust

Trust MUST NOT automatically propagate through dependencies.

Trusted A
   ↓
Dependency B

does not imply:

Trusted B

Each dependency requires independent identity, provenance, integrity, and policy evaluation.

⸻

21. Supply Chain

The Marketplace SHOULD preserve:

Source
  ↓
Build
  ↓
Artifact
  ↓
Package
  ↓
Publisher
  ↓
Marketplace
  ↓
Installer

Where available, the Marketplace SHOULD support:

* build provenance,
* source commit references,
* dependency manifests,
* software bills of materials,
* reproducible-build evidence,
* build attestations,
* artifact hashes.

⸻

22. Installation

Installation MUST be separate from acquisition.

ENTITLED
    ↓
DOWNLOADED
    ↓
INTEGRITY VERIFIED
    ↓
SECURITY CHECKED
    ↓
DEPENDENCIES RESOLVED
    ↓
INSTALLED

Installation MUST NOT automatically grant runtime authority.

⸻

23. Activation

Activation is a separate operation:

Installed
   ↓
Policy Evaluation
   ↓
Compatibility Check
   ↓
Capability Review
   ↓
Approval if required
   ↓
Activation

High-risk packages SHOULD require explicit human approval.

⸻

24. Capability Declaration

Packages MAY declare required capabilities.

Example:

filesystem.read
network.http
process.execute
browser.control
github.write
camera.read

These declarations are requests.

They are not permissions.

⸻

25. Capability Grant

Actual capabilities are controlled by RFC-0009, RFC-0010, and RFC-0011.

Therefore:

Package Declaration
        ≠
Capability Grant

A marketplace MUST NOT silently grant capabilities.

⸻

26. Economic Model

The Marketplace MAY support:

* free packages,
* one-time purchases,
* subscriptions,
* usage-based pricing,
* licenses,
* rentals,
* enterprise contracts,
* donations,
* private distribution.

Economic terms MUST be represented independently from technical trust.

⸻

27. Entitlement

An entitlement represents the right to obtain or use a package.

Example:

entitlement_id
subject_id
package_id
version_scope
license_ref
usage_limits
expiration
region_scope
device_scope
status
issuer
signature

Entitlement does not override:

* Constitution,
* authorization,
* capability policy,
* security policy,
* human approval requirements.

⸻

28. License

License information MUST be machine-readable where possible.

The Marketplace SHOULD track:

* license type,
* permitted use,
* prohibited use,
* attribution,
* redistribution,
* modification,
* commercial use,
* geographic restrictions,
* expiration,
* dependency licenses.

License interpretation MUST remain distinct from technical authorization.

⸻

29. Reviews

Reviews MAY provide useful experience information.

Reviews MUST be classified.

Possible review types:

USER_REVIEW
EXPERT_REVIEW
SECURITY_REVIEW
FUNCTIONAL_REVIEW
CONFORMANCE_REVIEW
BENCHMARK_REPORT

A user review MUST NOT be presented as a conformance result.

⸻

30. Review Integrity

The Marketplace SHOULD detect:

* spam,
* fake reviews,
* review manipulation,
* coordinated manipulation,
* purchased reviews,
* duplicate reviews,
* publisher self-review,
* identity spoofing.

Review deletion MUST be auditable.

Disputed reviews SHOULD remain distinguishable from verified evidence.

⸻

31. Marketplace Trust

The Marketplace MAY expose trust-related information from RFC-0041.

However:

Marketplace Trust Information
        ≠
Authorization

Trust MUST remain contextual.

For example:

Publisher trusted for:
    Model distribution
Publisher not evaluated for:
    Financial software
Publisher identity:
    Verified
Package security:
    Unknown

This is preferable to one meaningless number like:

Trust = 97/100

because apparently humanity has not suffered enough from arbitrary numerical scores.

⸻

32. Package Quality

Quality SHOULD be multidimensional.

Possible dimensions:

Correctness
Reliability
Performance
Compatibility
Security
Privacy
Documentation
Maintenance
Conformance
Reproducibility
Resource Efficiency
Community Adoption

The Marketplace MUST avoid representing these dimensions as one objective “quality” value unless the aggregation methodology is explicitly defined.

⸻

33. Benchmarking

Benchmark results MUST include:

package_version
benchmark_id
benchmark_version
environment
hardware
configuration
engine_version
dataset_version
date
result
limitations

Benchmark results MUST NOT be interpreted outside their stated scope.

⸻

34. Reproducibility

A benchmark SHOULD be reproducible by an independent evaluator.

The Marketplace SHOULD support:

Reproduce
    ↓
Same Package
Same Benchmark
Same Environment
Same Configuration
    ↓
Comparable Result

⸻

35. Package Updates

Updates MUST be versioned.

A new version MUST NOT silently replace an old version.

v1.2.0
   ↓
v1.3.0

must preserve lineage.

⸻

36. Update Risk

Updates SHOULD be evaluated for:

* changed permissions,
* changed capabilities,
* changed dependencies,
* changed network behavior,
* changed privacy profile,
* changed resource requirements,
* changed license,
* changed security status,
* behavioral changes.

A major capability change SHOULD trigger re-evaluation.

⸻

37. Automatic Updates

Automatic updates MAY be allowed for low-risk packages.

For high-risk packages:

Update
   ↓
Evaluate
   ↓
Simulate
   ↓
Approve
   ↓
Deploy
   ↓
Verify

Automatic updates MUST NOT bypass safety policy.

⸻

38. Rollback

Every update SHOULD maintain rollback information.

Rollback integrates with RFC-0027.

Current Version
      ↓
Update
      ↓
Verification
      ↓
Failure
      ↓
Rollback
      ↓
Verification

If rollback is impossible, the system MUST explicitly record:

ROLLBACK_UNAVAILABLE

rather than pretending the past can be restored by wishful thinking.

⸻

39. Package Recall

A package MAY be recalled because of:

* critical vulnerability,
* malicious behavior,
* compromised publisher key,
* fraudulent metadata,
* license violation,
* corrupted artifact,
* supply-chain compromise.

Recall MUST produce an auditable event.

⸻

40. Revocation

Revocation MUST distinguish:

Publisher Revoked
Package Revoked
Version Revoked
Entitlement Revoked
Capability Revoked
Marketplace Listing Revoked

These are different operations.

⸻

41. Quarantine

A quarantined package MAY remain available for forensic analysis.

Quarantine SHOULD prevent:

* activation,
* capability acquisition,
* automatic update,
* distribution to new installations.

Existing installations SHOULD be evaluated according to security policy.

⸻

42. Private Marketplace

Veda SHOULD support private marketplaces.

Examples:

Personal Marketplace
Home Lab Marketplace
Enterprise Marketplace
Research Marketplace
Federated Marketplace
Offline Marketplace

Private marketplaces MAY use different:

* catalogs,
* policies,
* pricing,
* trust anchors,
* evaluators,
* distribution infrastructure.

⸻

43. Local Marketplace

Veda SHOULD support an offline/local marketplace.

Example:

Local Registry
    ↓
Local Package Cache
    ↓
Offline Verification
    ↓
Local Installation

This is important for:

* privacy,
* offline operation,
* air-gapped environments,
* local models,
* local knowledge packages.

⸻

44. Federation

Marketplace federation integrates with RFC-0046.

A marketplace may discover packages from another marketplace without automatically trusting them.

Marketplace A
      ↓
Federation
      ↓
Marketplace B
      ↓
Package
      ↓
Local Verification

Remote marketplace claims remain external claims.

⸻

45. Cross-World Packages

Packages may target specific worlds.

Examples:

Coding World
Research World
Home Automation World
Business World
Simulation World
Development World

World compatibility MUST be explicit.

A package compatible with one world MUST NOT automatically be considered compatible with another.

⸻

46. Privacy

Marketplace operations SHOULD minimize personal data collection.

The Marketplace SHOULD NOT require private world state merely to discover a package.

Sensitive context SHOULD be represented using references or abstract requirements where possible.

Example:

Required:
    GPU ≥ 12 GB
Not required:
    User's complete hardware telemetry history

⸻

47. Private Recommendations

If recommendation requires private user context, the recommendation engine SHOULD operate locally when practical.

Private context MUST NOT automatically be transmitted to publishers.

⸻

48. Marketplace Analytics

Analytics MUST distinguish:

Public Aggregate Data
Private User Data
Package Telemetry
Security Telemetry
Transaction Data

Telemetry collection MUST follow explicit policy.

⸻

49. Package Telemetry

Packages MAY report operational telemetry if permitted.

Telemetry MUST NOT automatically grant access to:

* private memory,
* private world state,
* secrets,
* unrelated files,
* credentials,
* other agents’ private data.

⸻

50. Marketplace Governance

The Marketplace SHOULD define governance for:

* publication,
* moderation,
* security response,
* takedown,
* appeals,
* disputes,
* publisher verification,
* evaluator independence,
* policy changes,
* emergency response.

Governance MUST remain auditable.

⸻

51. Evaluator Independence

A marketplace MAY perform its own evaluations.

However, the system SHOULD distinguish:

Marketplace Evaluation
Independent Evaluation
Publisher Evaluation
Community Evaluation
Automated Evaluation
Human Evaluation

No evaluation source should silently impersonate another.

⸻

52. Disputes

Marketplace disputes may involve:

* ownership,
* license,
* package behavior,
* benchmark validity,
* security findings,
* publisher identity,
* fraudulent reviews,
* entitlement.

Disputes MUST create records rather than silently rewriting history.

⸻

53. Appeals

Publishers SHOULD be able to appeal:

* rejection,
* suspension,
* recall,
* security classification,
* conformance result,
* listing removal.

Appeals MUST NOT erase previous decisions.

⸻

54. Emergency Response

The Marketplace SHOULD support emergency actions:

SUSPEND
QUARANTINE
REVOKE
RECALL
BLOCK_INSTALL
BLOCK_UPDATE
BLOCK_ACTIVATION

Emergency action MUST be scoped and audited.

⸻

55. Marketplace Integrity

Marketplace metadata MUST be protected against:

* tampering,
* unauthorized modification,
* package substitution,
* version substitution,
* downgrade attacks,
* signature substitution.

Artifact hash and package identity MUST be independently verifiable.

⸻

56. Downgrade Protection

A package update system SHOULD prevent unauthorized downgrade to vulnerable versions.

Explicit rollback is different from malicious downgrade.

Authorized Rollback
    ≠
Unauthorized Downgrade

⸻

57. Typosquatting

Marketplace discovery SHOULD detect suspicious package names.

Examples:

veda-coder
veda_coder
veda-coderr
veda-c0der

Name similarity MUST NOT automatically prove malicious intent, but SHOULD trigger additional inspection.

⸻

58. Publisher Squatting

Marketplace SHOULD protect package namespaces.

Potential mechanisms:

* namespace ownership,
* publisher identity binding,
* signed releases,
* historical lineage,
* dispute records.

⸻

59. Package Counterfeiting

Two packages with similar names MUST remain distinguishable through:

Package ID
Publisher ID
Artifact Hash
Signature
Version
Provenance

Names alone are insufficient identity.

⸻

60. Marketplace API

The Marketplace SHOULD expose APIs including:

search_packages()
get_listing()
get_package()
get_version()
submit_package()
validate_package()
publish_package()
evaluate_package()
get_evaluation()
get_conformance()
get_security_status()
get_advisories()
resolve_dependencies()
check_compatibility()
create_entitlement()
get_entitlement()
revoke_entitlement()
download_package()
verify_package()
install_package()
activate_package()
deactivate_package()
update_package()
rollback_package()
quarantine_package()
recall_package()
revoke_package()
create_review()
report_review()
dispute_review()
submit_appeal()
create_security_report()
get_package_lineage()
get_provenance()
get_audit_trace()

⸻

61. Marketplace Events

The Marketplace MUST emit auditable events.

Examples:

PackageSubmitted
PackageIdentityVerified
PackageValidationStarted
PackageValidationFailed
PackageValidated
PackageEvaluationStarted
PackageEvaluationCompleted
PackageConformanceTested
PackageSecurityScanned
ListingCreated
ListingPublished
ListingUpdated
ListingSuspended
ListingDeprecated
ListingRetired
PackageDownloaded
PackageIntegrityVerified
PackageIntegrityFailed
EntitlementCreated
EntitlementExpired
EntitlementRevoked
PackageInstalled
PackageActivationRequested
PackageActivated
PackageDeactivated
PackageUpdated
PackageRollbackStarted
PackageRollbackCompleted
SecurityAdvisoryCreated
PackageQuarantined
PackageRecalled
PackageRevoked
ReviewCreated
ReviewFlagged
ReviewDisputed
AppealSubmitted
AppealResolved
MarketplaceFederationStarted
MarketplaceFederationChanged

⸻

62. Transaction Trace

Every consequential marketplace transaction SHOULD be traceable:

User Intent
    ↓
Package Discovery
    ↓
Listing Inspection
    ↓
Evaluation
    ↓
Decision
    ↓
Entitlement
    ↓
Download
    ↓
Integrity Verification
    ↓
Installation
    ↓
Activation
    ↓
Capability Grant
    ↓
Execution
    ↓
Verification

The trace integrates with RFC-0031 and RFC-0032.

⸻

63. Marketplace and Chronicle

Chronicle records marketplace history.

Examples:

Who published the package?
Which version?
Which hash?
Which evaluator?
Which benchmark?
Which entitlement?
Who installed it?
When?
Which permissions changed?
Which security advisory appeared?
When was it revoked?
Why?

Marketplace state alone is insufficient historical evidence.

⸻

64. Marketplace and Evolution Ledger

RFC-0038 records evolution of packages and package-related changes.

Example:

Experience
    ↓
Learning
    ↓
Evolution Proposal
    ↓
New Skill Package
    ↓
Evaluation
    ↓
Marketplace Publication

This creates traceability from learning to distributable artifact.

⸻

65. Marketplace and Trust Engine

RFC-0041 evaluates contextual trust.

Marketplace SHOULD expose trust evidence rather than replacing the Trust Engine.

Marketplace
    ↓
Trust Evidence
    ↓
Trust Engine
    ↓
Contextual Trust Assessment

⸻

66. Marketplace and Neural Package Format

RFC-0047 defines the artifact.

RFC-0048 defines its distribution ecosystem.

RFC-0047
Package Format
     +
RFC-0048
Marketplace

The Marketplace MUST NOT redefine package semantics.

⸻

67. Marketplace and NCP

NCP may transport package-related context:

Package capabilities
Package compatibility
Package evaluation evidence
Package requirements
Package provenance

NCP does not perform transactions.

⸻

68. Marketplace and World Delta

WDP MAY represent changes to package-related world state.

Examples:

Package installed
Package activated
Package revoked
Capability changed
Dependency changed

⸻

69. Marketplace and Multi-Agent Systems

Multiple agents MAY use the same marketplace.

Each agent retains independent:

* identity,
* trust,
* authority,
* entitlements,
* private state.

One agent purchasing a package MUST NOT automatically grant another agent access.

⸻

70. Marketplace Security Threats

The Marketplace MUST consider at least:

NMP-SEC-01 Malicious Package
NMP-SEC-02 Fake Publisher
NMP-SEC-03 Publisher Key Theft
NMP-SEC-04 Package Substitution
NMP-SEC-05 Dependency Poisoning
NMP-SEC-06 Supply Chain Compromise
NMP-SEC-07 Typosquatting
NMP-SEC-08 Namespace Squatting
NMP-SEC-09 Review Manipulation
NMP-SEC-10 Benchmark Manipulation
NMP-SEC-11 Conformance Fraud
NMP-SEC-12 Metadata Tampering
NMP-SEC-13 Malicious Update
NMP-SEC-14 Downgrade Attack
NMP-SEC-15 Entitlement Theft
NMP-SEC-16 License Abuse
NMP-SEC-17 Privacy Leakage
NMP-SEC-18 Marketplace Compromise
NMP-SEC-19 Evaluator Compromise
NMP-SEC-20 Recommendation Manipulation
NMP-SEC-21 Capability Inflation
NMP-SEC-22 Telemetry Abuse
NMP-SEC-23 Package Recall Evasion
NMP-SEC-24 Revocation Bypass
NMP-SEC-25 Dependency Confusion
NMP-SEC-26 Counterfeit Package
NMP-SEC-27 Review Sybil Attack
NMP-SEC-28 Pricing Manipulation
NMP-SEC-29 Marketplace Federation Poisoning
NMP-SEC-30 Supply-Chain Identity Confusion

⸻

71. Threat: Capability Inflation

A package may declare harmless functionality while actually requiring dangerous capabilities.

Therefore the Marketplace SHOULD compare:

Declared Capability
vs
Observed Capability
vs
Requested Capability
vs
Granted Capability

Unexpected expansion MUST trigger investigation.

⸻

72. Threat: Malicious Update

A trusted package may become malicious after update.

Therefore trust MUST be version-aware.

Trusted Package v1
    ≠
Trusted Package v2

New versions require independent evaluation.

⸻

73. Threat: Marketplace Compromise

If marketplace infrastructure is compromised, clients MUST still be able to verify:

* package identity,
* artifact hash,
* signature,
* provenance,
* version.

The Marketplace MUST NOT be a single root of trust.

⸻

74. Threat: Recommendation Manipulation

Marketplace ranking can be manipulated through:

* fake downloads,
* fake reviews,
* paid promotion,
* publisher collusion,
* click manipulation,
* benchmark gaming.

Ranking signals MUST therefore remain separate from verification evidence.

⸻

75. Threat: Dependency Confusion

The Marketplace MUST prefer explicitly bound dependencies.

Package resolution SHOULD validate:

package_id
publisher_id
version
hash
source
signature

rather than resolving solely by package name.

⸻

76. Threat: Entitlement Theft

Entitlements MUST be bound to the appropriate subject and scope.

Where required:

Identity
Device
World
Package
Version
Time
Usage

must be explicitly represented.

⸻

77. Threat: Privacy Leakage

Search queries themselves may expose sensitive intent.

Therefore private marketplace search SHOULD support:

* local indexing,
* private search,
* query minimization,
* anonymized aggregate analytics,
* no unnecessary publisher disclosure.

⸻

78. Marketplace State Machine

DISCOVERED
    ↓
INSPECTED
    ↓
EVALUATED
    ↓
ACQUIRED
    ↓
VERIFIED
    ↓
INSTALLED
    ↓
ACTIVATED
    ↓
MONITORED
    ↓
UPDATED
    ↓
DEPRECATED
    ↓
RETIRED

Security branches:

SUSPENDED
QUARANTINED
RECALLED
REVOKED

⸻

79. Reference Architecture

                    ┌──────────────────────┐
                    │      Human/User      │
                    └──────────┬───────────┘
                               │
                               ▼
                    ┌──────────────────────┐
                    │ Marketplace Client   │
                    └──────────┬───────────┘
                               │
             ┌─────────────────┼──────────────────┐
             ▼                 ▼                  ▼
       Discovery          Evaluation          Transaction
             │                 │                  │
             └─────────────────┼──────────────────┘
                               ▼
                    ┌──────────────────────┐
                    │ Marketplace Registry │
                    └──────────┬───────────┘
                               │
             ┌─────────────────┼──────────────────┐
             ▼                 ▼                  ▼
        Package Store      Trust Evidence     Security
             │                 │                  │
             └─────────────────┼──────────────────┘
                               ▼
                    ┌──────────────────────┐
                    │ RFC-0047 Package     │
                    └──────────┬───────────┘
                               │
                               ▼
                    ┌──────────────────────┐
                    │ Verify / Install     │
                    └──────────┬───────────┘
                               │
                               ▼
                    ┌──────────────────────┐
                    │ Authorization Layer  │
                    └──────────┬───────────┘
                               │
                               ▼
                    ┌──────────────────────┐
                    │ Active Veda System   │
                    └──────────────────────┘

⸻

80. Core Invariants

The following invariants MUST hold.

NMP-1

Marketplace listing MUST NOT imply authorization.

NMP-2

Package acquisition MUST NOT imply installation.

NMP-3

Installation MUST NOT imply activation.

NMP-4

Activation MUST NOT imply unrestricted capability.

NMP-5

Publisher identity MUST remain distinct from package trust.

NMP-6

Package trust MUST remain contextual.

NMP-7

Ratings MUST NOT be treated as verification.

NMP-8

Reviews MUST remain distinguishable from evidence.

NMP-9

Benchmark results MUST identify their evaluation context.

NMP-10

Conformance results MUST identify the specification and test suite.

NMP-11

Package versions MUST remain immutable once published.

NMP-12

Artifact hashes MUST identify exact artifacts.

NMP-13

Updates MUST preserve package lineage.

NMP-14

Dependencies MUST be independently identifiable.

NMP-15

Dependency trust MUST NOT automatically propagate.

NMP-16

Revocation MUST be auditable.

NMP-17

Recall MUST NOT silently erase historical records.

NMP-18

Security status MUST be version-aware.

NMP-19

Marketplace compromise MUST NOT invalidate independent package verification.

NMP-20

Marketplace recommendations MUST expose their basis where practical.

NMP-21

Private user context MUST NOT be disclosed unnecessarily.

NMP-22

Entitlements MUST be scoped.

NMP-23

Entitlements MUST NOT override authorization.

NMP-24

Economic transactions MUST remain separate from technical authority.

NMP-25

Package declarations MUST NOT be treated as granted capabilities.

NMP-26

Emergency actions MUST be auditable.

NMP-27

Quarantined packages MUST NOT bypass security controls.

NMP-28

Historical package state MUST remain reconstructable.

NMP-29

Marketplace metadata MUST preserve provenance.

NMP-30

No marketplace component may become the ultimate authority over Veda.

⸻

81. Fundamental Separation

The Neural Marketplace maintains the following separation:

Marketplace
    ↓
What exists and can be acquired
RFC-0047
    ↓
What the artifact is
RFC-0039
    ↓
Who published it
RFC-0041
    ↓
How much reliance is justified
RFC-0010
    ↓
What Veda permits
RFC-0011
    ↓
For how long and within what scope
RFC-0029
    ↓
How effects reach the external world
RFC-0026
    ↓
What actually happened
RFC-0032
    ↓
What happened historically

This separation is mandatory.

⸻

82. Complete Acquisition Flow

A complete package acquisition SHOULD follow:

User Intent
    ↓
Discovery
    ↓
Listing Inspection
    ↓
Publisher Identity Verification
    ↓
Package Integrity Verification
    ↓
Dependency Analysis
    ↓
Security Evaluation
    ↓
Conformance Evaluation
    ↓
Compatibility Evaluation
    ↓
Cost / License Evaluation
    ↓
Decision
    ↓
Entitlement
    ↓
Download
    ↓
Artifact Verification
    ↓
Sandbox Installation
    ↓
Benchmark
    ↓
Activation Approval
    ↓
Capability Evaluation
    ↓
Capability Lease
    ↓
Activation
    ↓
Monitoring
    ↓
Outcome Verification
    ↓
Chronicle

⸻

83. Design Boundary

The Neural Marketplace answers:

“What packages exist, where did they come from, what evidence exists about them, how can they be acquired, and what is their lifecycle?”

It does not answer:

“What is Veda allowed to do?”

That belongs to Authorization.

It does not answer:

“Did the package actually accomplish its objective?”

That belongs to Verification.

It does not answer:

“Is the package universally trustworthy?”

Trust remains contextual.

It does not answer:

“What is reality?”

The World Model and External World Interface remain responsible for representing and observing reality.

⸻

84. Final Principle

The Neural Marketplace is the economic and distribution layer of the Veda ecosystem.

It makes intelligence, knowledge, memory, skills, tools, agents, worlds, and other artifacts discoverable and exchangeable.

But:

Discoverable ≠ Trusted
Trusted ≠ Authorized
Authorized ≠ Successful
Successful ≠ Verified
Verified ≠ Permanent
Purchased ≠ Installed
Installed ≠ Active
Active ≠ All-Powerful

The marketplace distributes capability.

It does not own authority.

A marketplace can sell the key. It must never decide which doors Veda is allowed to open.