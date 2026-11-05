
# SaaS Subscription Billing - Class Diagram

## Purpose
This diagram represents the main classes required to develop
the SaaS Subscription Billing Engine.

```mermaid
classDiagram

    class Customer {
        +String customer_id
        +String name
        +String billing_country
        +String billing_state
        +String gstin
        +String payment_method
        +bool trial_eligible
        +create_subscription()
    }

    class Plan {
        +String plan_id
        +String plan_name
        +Decimal monthly_price
        +Decimal annual_price
        +int included_seats
        +String feature_tier
    }

    class Subscription {
        +String subscription_id
        +String status
        +Date start_date
        +Date period_start
        +Date period_end
        +int seat_count
        +change_plan()
        +change_seats()
        +pause()
        +resume()
        +cancel()
    }

    class Coupon {
        +String coupon_id
        +String coupon_type
        +Decimal discount_value
        +int first_n_months
        +validate()
        +apply_discount()
    }

    class Credit {
        +String credit_id
        +Decimal amount
        +Date created_date
        +Date expiry_date
        +apply_credit()
    }

    class Invoice {
        +String invoice_id
        +String invoice_number
        +Date invoice_date
        +Decimal subtotal
        +Decimal tax_amount
        +Decimal total
        +generate_invoice()
        +print_invoice()
    }

    class InvoiceLine {
        +String description
        +int quantity
        +Decimal unit_price
        +Decimal line_total
    }

    class PaymentAttempt {
        +String payment_id
        +Decimal amount
        +String payment_status
        +Date attempt_date
        +process_payment()
    }

    class ProrationCalculator {
        +calculate_proration()
        +calculate_upgrade_difference()
    }

    class TaxCalculator {
        +calculate_gst()
        +determine_tax_type()
    }

    class DunningManager {
        +int failure_count
        +retry_payment()
        +update_subscription_status()
    }

    class BillingEngine {
        +create_subscription()
        +process_command()
        +generate_invoice()
        +process_billing_cycle()
    }

    Customer "1" --> "0..*" Subscription : owns
    Plan "1" --> "0..*" Subscription : selected plan
    Subscription "1" --> "0..*" Invoice : generates
    Invoice "1" *-- "1..*" InvoiceLine : contains
    Invoice "1" --> "0..*" PaymentAttempt : has
    Subscription "1" --> "0..1" Coupon : uses
    Customer "1" --> "0..*" Credit : owns

    BillingEngine ..> ProrationCalculator : uses
    BillingEngine ..> TaxCalculator : uses
    BillingEngine ..> DunningManager : uses
    DunningManager ..> PaymentAttempt : manages
```

## Main Class Responsibilities

1. Customer: Stores customer and billing information.
2. Plan: Stores monthly and annual plan prices and seat details.
3. Subscription: Manages subscription status, seats, plan changes, pause and cancellation.
4. Coupon: Applies discounts to subscription invoices.
5. Credit: Stores customer account credits and expiry details.
6. Invoice: Generates and stores billing invoices.
7. InvoiceLine: Stores individual charges in an invoice.
8. PaymentAttempt: Records payment attempts and their results.
9. ProrationCalculator: Calculates charges for mid-cycle changes.
10. TaxCalculator: Calculates the applicable GST.
11. DunningManager: Handles failed payments and retry schedules.
12. BillingEngine: Coordinates the overall billing process.
