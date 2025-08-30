# Changelog

All notable changes to $UNITA (UNITACoin) will be documented in this file.  
The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/) and follows [Semantic Versioning](https://semver.org/).

----------

## [1.1.0] - 2025-08-30

### Added

- Updated `UNITACoin.sol` to enterprise-grade, Certik-style audited version:
    - Max supply increased from 10 billion to 100 billion tokens.
    - Implemented all high- and medium-risk fixes identified in the Certik-style audit.
    - Optimized contract for gas efficiency and BSCScan verification compatibility.
    - Preserved all original functionality including AccessControl roles, Pausable, ReentrancyGuard, staking and LP mechanisms.
    - Added full documentation for constructor, functions, and events inline in the smart contract for maintainability and audit purposes.

### Updated

- Updated README.md to reference the new enterprise-grade, audited `UNITACoin.sol`.
- Updated SECURITY.md to reflect the latest Certik-style audit results and applied fixes.
- Updated AUDIT.me to include final deep Certik-style audit confirming all previous issues are resolved.
- Updated ROADMAP.md to note the redeployment and verification of the 100B max supply smart contract.

----------

## [1.0.3] - 2025-08-26

### Added

- Added `POLITICAL.md`:
    - Documents the historical and political context of the UNITA name in Angola.
    - Explicitly clarifies that $UNITA is not endorsed, sanctioned, affiliated, or in partnership with the UNITA political party.
    - Includes analysis of public perception, political sensitivities, and community trust considerations.

### Updated

- Updated `README.md` to reference `POLITICAL.md`:
    - Added POLITICAL.md to Quick Links section.
    - Added short note under Angola Context mentioning political neutrality.
    - Expanded Disclaimer section to emphasize separation from the UNITA political party.

----------

## [1.0.2] - 2025-08-26

### Updated

- Updated README.md to reference MARKET_ANALYSIS.md:
    - Added a short note highlighting that detailed insights on $UNITA's adoption potential, youth demographics, competitor landscape, regulatory context, technology access, behavioral patterns, and projected economic benefits in Angola are available in MARKET_ANALYSIS.md.
    - Ensured no existing content in README.md was removed or altered.

----------

## [1.0.1] - 2025-08-25

### Updated

- Polished README.md for improved public release:
    - Added version, license, and build badges.
    - Added Quick Links to WHITEPAPER, ROADMAP, BRANDING, ANGOLA.md, and CHANGELOG.
    - Retained all previous content including Features, Tokenomics, Branding, Roadmap, Angola context, Disclaimer, Repository Structure, Contributing, and Contact.

----------

## [1.0.0] - 2025-08-25

### Added

- Developed and deployed the $UNITA BEP-20 utility token smart contract (`UNITACoin.sol`).
- Added all core repository documentation:
    - WHITEPAPER.md – Technical specifications and tokenomics.
    - ANGOLA.md – Economic context and token use case.
    - ROADMAP.md – Project roadmap.
    - BRANDING.md – Official token logo, colors, typography, and usage guidelines.
    - README.md – Project overview, features, tokenomics, roadmap, branding, Angola context, and links to WHITEPAPER.md and BRANDING.md.
    - CONTRIBUTING.md – Guidelines for contributions.
    - SECURITY.md – Security policies and reporting.
    - CODE_OF_CONDUCT.md – Community behavior standards.
    - AUDIT.me – Certik-style audit analysis.
    - LICENSE.md – Repository license.
    - CHANGELOG.md – Documenting version 1.0.0 release.
