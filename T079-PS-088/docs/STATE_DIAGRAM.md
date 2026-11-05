
# Subscription State Diagram

## Purpose
This diagram represents the lifecycle of a SaaS subscription, including
trial, activation, pause, payment failure, suspension and cancellation.

```mermaid
stateDiagram-v2
    [*] --> TRIAL: Trial subscription created
    [*] --> ACTIVE: Paid subscription created

    TRIAL --> ACTIVE: Trial ends / customer converts
    TRIAL --> CANCELLED: Trial cancelled or expires without conversion

    ACTIVE --> PAUSED: Customer pauses subscription
    PAUSED --> ACTIVE: Customer resumes subscription

    ACTIVE --> PAST_DUE: Payment fails on due date
    PAST_DUE --> ACTIVE: Payment succeeds
    PAST_DUE --> SUSPENDED: 3 payment failures / day 21
    SUSPENDED --> ACTIVE: Payment succeeds
    SUSPENDED --> UNPAID: Remains unpaid

    ACTIVE --> CANCELLED: Immediate cancellation
    ACTIVE --> CANCELLED: Period-end cancellation takes effect
    PAUSED --> CANCELLED: Cancellation takes effect
    PAST_DUE --> CANCELLED: Day 45 write-off and cancellation
    UNPAID --> CANCELLED: Day 45 write-off and cancellation

    CANCELLED --> [*]
```

## Important Notes
- TRIAL represents the 14-day free trial period.
- ACTIVE means the subscription is currently working.
- PAUSED means billing and subscription access are paused according to the pause rules.
- PAST_DUE means a payment has failed and retries are in progress.
- SUSPENDED means the customer has entered restricted, read-only access.
- UNPAID means the subscription remains unpaid after the specified dunning period.
- CANCELLED means the subscription has ended.
- A successful payment during the retry or suspension period restores ACTIVE status.
- Period-end cancellation takes effect only when the current billing period ends.
