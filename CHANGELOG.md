# Changelog

All notable changes to **ryke** are documented here. The format is based on
[Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and the project adheres
to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [0.2.0] - 2026-07-11

Server-side termination of native OS VPN clients (iOS / Android): the responder
can now authenticate, address, and NAT-traverse a stock device with no custom
client software.

### Added
- **EAP-MSCHAPv2 authentication** (RFC 3748 / RFC 2759), both sides. `EapResponder`
  terminates native clients with a server certificate plus username/password;
  `EapInitiator` drives the exchange for testing. Server certificate auth uses
  RFC 7427 Digital Signature (method 14) and falls back to classic ECDSA
  (method 9) for peers that advertise no `SIGNATURE_HASH_ALGORITHMS` (e.g. iOS).
- **Configuration Payload** (`CFG_REPLY`): the responder assigns the client its
  inner IP (and DNS) and narrows the traffic selector to that host `/32`.
- **NAT traversal (NAT-T)**: `NAT_DETECTION_SOURCE/DESTINATION_IP` notifies, float
  to UDP 4500 behind the non-ESP marker, UDP-encapsulated ESP, and `INVALID_KE`
  Diffie–Hellman group renegotiation.
- **IKE_SA_INIT cookies** (RFC 7296 §2.6): stateless return-routability cookie
  (`ike_cookie`, `CookiePolicy`, `SaInitResult::CookieRequired`) to shed spoofed
  SA_INIT floods before performing Diffie–Hellman.
- Per-user identity extraction (`peer_id_from_auth`) for per-identity PSK lookup.
- Examples: `ike_client` (PSK + Configuration Payload), `ike_client_eap`
  (EAP-MSCHAPv2 with CA verification), `ike_client_natt` (NAT-T float).

### Changed
- PSK `AUTH` verification now uses a constant-time MAC comparison.

## [0.1.0]

- Initial release: IKEv2 / IKEv1 message framing, crypto core (X25519, HMAC-SHA256
  PRF, `prf+`), the `IKE_SA_INIT` key schedule, PSK `IKE_AUTH`, userspace ESP
  (AES-GCM), and the `Tunnel` data-plane helper.
