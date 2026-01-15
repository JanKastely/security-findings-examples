# SSRF Leading to Internal Service Exposure

## Context

External services are accessed from the application by extracting remote resources based on input from the user.

This functionality is leveraged, for instance, to:

- fetch metadata from external URLs
- confirm third-party resources
- enrich user-generated content
The request is done server-side, and the result is partially processed by the backend.

---

## Issue Description

It allows users to dictate the destination of these server-side HTTP requests.

The user input gets some basic validation at the backend but the backend does not sufficiently restrict where the server may be allowed to send requests.

Therefore, it is possible to force the application to request internal network resources which are not supposed to be accessed externally.

The problem is a server-side request forgery (SSRF) vulnerability with internal access.

---

## Impact

This vulnerability enables an attacker to:

- engage with internal-only services
- access admin or metadata endpoints
- circumvent network-level segmentation
- develop an understanding of internal system architecture

Depending on the environment, this can result in:

- leaking of critical configuration information
- credential misuse from metadata services
- additional lateral movement inside internal networks

Even in the absence of visible response data, having access to those internal services is an important security boundary violation.

---

## Reproduction Steps (Simplified)

1. Authenticate as a standard user.
2. Locate functionality that fetches or validates a remote URL.
3. Intercept the server-side request.
4. Modify the target URL to reference an internal service.
5. Observe differences in backend behavior or responses indicating internal reachability.

No client-side access to internal systems is required.

---

## Root Cause Analysis

The reason for the vulnerability is:
- inadequate restrictions against outbound server-side requests
- reliance on input validation rather than network-level controls
- the assumption that server-side requests are inherently safe

There are no explicit or enforced definitions of allowed request destinations for the application.

---

## Recommendation

To remediate this issue:
- set strict allowlists for outbound requests
- enforce egress controls at the network level
- separate internal services from external integrations
- do not make server-side requests based solely on user input

Server-side request behavior must be handled as a significant trust boundary.

---

## Notes

This is a trust boundary failure, not a parsing or input verification problem.

The main risk comes from the ability to reach internal services, regardless of the direct response data being unavailable or filtered for this attack surface.
