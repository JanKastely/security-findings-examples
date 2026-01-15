# Access Control Logic Flaw via State Manipulation

---

## Summary

The application inconsistently enforces workflow-based access control between the user interface and backend services.

An authenticated user can manipulate requests based on order and state of objects, which could allow execution of actions that are not performed until later in the workflow.

This is a logic-based access control issue that compromises process integrity.

---

## Context

The application offers authenticated users managing orders through a multi-step lifecycle:

- created
- confirmed
- processed
- completed

There are certain actions that can only function on an order that exists in a certain state.

---

## Finding Description

Backend authorization checks are valid based on request context, not enforced state validation.

The UI stops the user from doing things out of order; however, from the backend it doesn’t always check whether an action is per the current status of the order.

Therefore, restricted actions can be performed by repeating or changing the requests in backend requests.

---

## Impact

Now, the attacker with a standard authenticated account can:

- skip approval or confirmation steps
- prematurely fire off backend operations
- manipulate logic for the ordering system as to processing orders
- create conflicting or invalid system states
Depending on various business circumstances, this can result in loss of revenue, unauthorized process processing or loss of faith in the system’s workflow.

---

## Risk Assessment

| Metric        | Value   |
|--------------|---------|
| Impact       | High    |
| Likelihood   | Medium  |
| Confidence   | High    |
| Overall Risk | High    |

---

## Severity

**High**

The vulnerability attacks logic of the core business itself and allows for bypass of the desired authorization controls.
Exploitation need not be of elevated privileges or more complex conditions.

---

## Reproduction Steps (Simplified)

1. Authenticate as a standard user.
2. Create a new order.
3. Intercept a request intended for a later workflow stage.
4. Replay the request while the order remains in an earlier state.
5. Observe that the backend accepts the action without validating the order state.

---

## Root Cause

- The client-driven approach and dependency on workflow progression
- absence of server-side enforcement of state transitions
- assumptions that users go through all the expected UI paths

Authorization decisions are not always constrained by object state.

---

## Recommendation

To remediate this issue:

- enforce validated server-side state transitions
- ensure all state-changing functions verify the current object state
- centralize access control and workflow validation logic
- consider workflow state to be an additional security boundary
Backend authorization does not depend on UI.

---

## Notes

This finding is a logic & access control glitch, not a typical IDOR.

It is subtle, hard to catch automatically scanning and extremely dependent on comprehension of application workflows.
