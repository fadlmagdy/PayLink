# PayLink Requirements

## 1. Problem Summary

PayLink is a bill-payment and digital wallet backend system for a company in Egypt, similar to Fawry or Vodafone Cash. It allows customers to pay utility bills, including electricity, water, mobile, and internet bills, using their digital wallets.

The system aims to make bill payments faster and more convenient for customers while ensuring accurate financial transactions, secure wallet management, and the ability to support multiple billers.

## 2. Stakeholders

### 2.1 Customers
Customers use PayLink to pay utility bills through the mobile app or with the help of an agent. They can manage their wallets and view payment history.

### 2.2 Agents
Agents are small shops with PayLink terminals. They help customers make bill payments and earn commissions for completed payments.

### 2.3 Billers
Billers are companies that issue bills and receive payments, such as electricity, water, mobile operators, and internet providers.

### 2.4 Admins
Admins manage billers and services, update fees and limits without redeploying the system, and view daily payment statistics.

### 2.5 Support Employees
Support employees search for payments using their references, view payment history, and handle account-related support actions and refund approvals.

## 3. High-Level Payment Flow

### 3.1 Login and limits: 
The system identifies the customer and checks verification level and daily limits.

### 3.2  Bill inquiry:
The customer enters a reference such as a meter number, and PayLink asks the biller for the bill and displays the due amount.

### 3.3 Fee and total:
PayLink adds its service fee and shows the total.

### 3.4 Fraud checks:
The system checks daily limits, payment velocity (number of payments in the last minute), and whether the account is blocked.

### 3.5 Hold the money:
The total amount is moved from the available balance to a held balance (not spent yet).   

### 3.6 Call the biller: 
PayLink sends the payment to the biller with a unique reference.   

### 3.7 Confirmation:
Once the biller confirms payment, PayLink turns the hold into a final charge, credits fee income, and records agent commission if an agent was involved.   

### 3.8 Receipt:
The customer receives notifications (such as SMS and push notification).   

### 3.9 Night reconciliation:
The biller sends a daily settlement file of received payments, which PayLink compares with its own records and flags any differences.



## 4. Functional Requirements and User Stories

### US-01: Customer Registration

**Priority:** Must

As a customer,  
I want to register an account,  
So that I can use PayLink services.

**Acceptance Criteria:**

* **Given** that a customer does not have an account  
  **When** the customer successfully registers  
  **Then** the system creates a customer account  
  **And** assigns the Basic verification level

---

### US-02: Customer Upgrade

**Priority:** Should

As a customer,  
I want to upgrade my account to Verified,  
So that I can have higher payment limits.

**Acceptance Criteria:**

* **Given** that the customer has a Basic verification level  
  **When** the customer is successfully upgraded to Verified  
  **Then** the system changes the customer's verification level to Verified  
  **And** increases the customer's daily payment limit from 5,000 EGP to 30,000 EGP

---

### US-03: Customer Views Wallet

**Priority:** Must

As a customer,  
I want to view my wallet balance (available and held) and transaction history,  
So that I can track my money and previous transactions.

**Acceptance Criteria:**

* **Given** that the customer is logged in  
  **When** the customer opens the wallet  
  **Then** the system displays the available balance  
  **And** displays the held balance  
  **And** displays the transaction history

---

### US-04: Customer Tops Up Wallet

**Priority:** Must

As a customer,  
I want to add money to my wallet using a simulated card or agent cash,  
So that I can pay my bills.

**Acceptance Criteria:**

* **Given** that the customer has a valid wallet  
  **When** the customer successfully tops up using a simulated card  
  **Then** the system increases the wallet balance by the top-up amount

* **Given** that the customer receives cash through an agent  
  **When** the agent successfully completes the top-up  
  **Then** the system increases the customer's wallet balance by the top-up amount

---

### US-05: Record Wallet Balance Changes

**Priority:** Must

As a system,  
I want to record every wallet balance change in the ledger,  
So that all money movements are tracked accurately.

**Acceptance Criteria:**

* **Given** that a money movement occurs in a wallet  
  **When** the transaction is completed  
  **Then** the system creates a ledger entry for that movement  
  **And** calculates the wallet balance from the ledger entries

---

### US-06: Admin Manages Billers and Services

**Priority:** Must

As an admin,  
I want to add, edit, and disable billers and their services,  
So that I can manage the available payment services.

**Acceptance Criteria:**

* **Given** that the admin is authorized  
  **When** the admin adds a new biller  
  **Then** the system saves the biller

* **Given** that a biller already exists  
  **When** the admin edits its information  
  **Then** the system updates the biller's information

* **Given** that a biller is active  
  **When** the admin disables it  
  **Then** the system marks the biller as disabled

---

### US-07: Customer Views Available Billers and Required Fields

**Priority:** Must

As a customer,  
I want to view available billers and their required input fields,  
So that I can provide the correct information for my bill.

**Acceptance Criteria:**

* **Given** that the customer opens the bill payment section  
  **When** the system loads the available billers  
  **Then** the system displays the list of available billers

* **Given** that the customer selects a biller  
  **When** the biller is selected  
  **Then** the system displays the input fields required by that biller

---

### US-08: Customer Inquires About a Bill

**Priority:** Must

As a customer,  
I want to inquire about my bill,  
So that I can know the amount and fees before paying.

**Acceptance Criteria:**

* **Given** that the customer selects a biller and enters the required information  
  **When** the customer requests a bill inquiry  
  **Then** the system retrieves the bill amount from the biller  
  **And** calculates the service fee  
  **And** displays the bill amount, service fee, and total amount before payment

---

### US-09: Customer Pays a Bill Using Wallet

**Priority:** Must

As a customer,  
I want to pay a bill using my wallet,  
So that I can complete my bill payment through PayLink.

**Acceptance Criteria:**

* **Given** that the customer has completed a bill inquiry  
  **And** has sufficient available wallet balance  
  **When** the customer confirms the payment  
  **Then** the system processes the payment

* **Given** that the customer has insufficient available wallet balance  
  **When** the customer attempts to pay the bill  
  **Then** the system rejects the payment  
  **And** informs the customer that the balance is insufficient

---

### US-10: Handle Repeated Payment Requests

**Priority:** Must

As a system,  
I want to handle repeated payment requests using an idempotency key,  
So that the customer is not charged more than once for the same request.

**Acceptance Criteria:**

* **Given** that a payment request has already been processed with a specific idempotency key  
  **When** the system receives another request with the same key  
  **Then** the system returns the original payment result  
  **And** does not create another payment  
  **And** does not charge the customer again

---

### US-11: Manage Payment Life Cycle

**Priority:** Must

As a system,  
I want to manage payment status transitions,  
So that every payment follows the defined life cycle.

**Acceptance Criteria:**

* **Given** that a payment has been initiated  
  **When** the payment passes the required checks and funds are held  
  **Then** the payment status changes to FundsHeld

* **Given** that the payment is ready to be sent to the biller  
  **When** the system sends the payment request  
  **Then** the payment status changes to SentToBiller

* **Given** that the biller confirms the payment  
  **When** the system receives a successful response  
  **Then** the payment status changes to Completed

* **Given** that the payment is rejected because of insufficient funds or failed fraud checks  
  **When** the system processes the rejection  
  **Then** the payment status changes to Rejected

* **Given** that the biller refuses the payment  
  **When** the system receives a failure response  
  **Then** the payment status changes to Failed

* **Given** that the biller does not respond within the timeout period  
  **When** the system processes the timeout  
  **Then** the payment status changes to Unknown

* **Given** that a payment has reached a final state  
  **When** another status transition is requested  
  **Then** the system rejects the invalid transition

---

### US-12: Hold Funds Before Calling the Biller

**Priority:** Must

As a system,  
I want to hold the required funds before calling the biller,  
So that the customer cannot spend the same money during payment processing.

**Acceptance Criteria:**

* **Given** that the customer has sufficient available balance  
  **When** the payment process starts  
  **Then** the system moves the required amount from the available balance to the held balance  
  **And** holds the funds before calling the biller

* **Given** that the biller refuses the payment  
  **When** the payment status becomes Failed  
  **Then** the system releases the held funds  
  **And** returns the amount to the customer's available balance

---

### US-13: Handle Payment Timeouts

**Priority:** Must

As a customer,  
I want timed-out payments to be marked as Unknown and re-queried according to the retry rules,  
So that I can receive the final payment result.

**Acceptance Criteria:**

* **Given** that a biller does not respond within the specific timeout,  
  **When** the timeout period expires,  
  **Then** the system changes the payment status to unknown and keeps the funds held.

* **Given** that the payment status is unknown,  
  **When** the retry schedule is triggered,  
  **Then** the system re-queries the biller according to the configured retry rules.

---

### US-14: Handle Asynchronous Callbacks

**Priority:** Must

As a system,  
I want to receive asynchronous callbacks from TopUpNet and ignore duplicate callbacks,  
So that payments are completed only once.

**Acceptance Criteria:**

* **Given** that a payment has been sent to TopUpNet,  
  **When** TopUpNet sends a callback with a success or failure result,  
  **Then** the system updates the payment status according to the callback result.

* **Given** that the system callback has already been processed,  
  **When** the system receives it again,  
  **Then** the system ignores the duplicate callback and doesn’t process the payment again.

---

### US-15: Support Multiple Billers

**Priority:** Must

As a customer,  
I want PayLink to support multiple billers with different communication formats,  
So that I can pay different types of bills through one system.

**Acceptance Criteria:**

* **Given** that PayLink supports ElectroCo, AquaCo and TopUpNet,  
  **When** a customer makes a payment through any of these billers,  
  **Then** the system communicates with the selected biller using its required protocol and data format.

---

### US-16: Calculate Service Fees

**Priority:** Must

As a customer,  
I want the system to calculate service fees according to the configured fee rules and promotions,  
So that I know the correct total amount before paying.

**Acceptance Criteria:**

* **Given** that a customer requests a bill inquiry,  
  **When** the system calculates the service fee,  
  **Then** it applies the correct fixed, percentage, or tiered fee according to the selected service.

* **Given** that an active promotion applies to the selected biller,  
  **When** the service fee is calculated,  
  **Then** the system applies a zero service fee during the promotion period.

---

### US-17: Agents Payments and Commission

**Priority:** Should

As an agent,  
I want to pay bills on behalf of customers,  
So that I can provide payment services and earn a commission.

**Acceptance Criteria:**

* **Given** that an agent is authorized to perform payments,  
  **When** the agent successfully completes a payment on behalf of a customer,  
  **Then** the system calculates the agent’s commission and credits it to the agent’s wallet when the payment completes.

---

### US-18: Enforce Payment Limits

**Priority:** Must

As a system,  
I want to reject payments that exceed the single-payment or daily payment limits,  
So that payment operations follow the defined business rules.

**Acceptance Criteria:**

* **Given** that a payment amount exceeds the allowed single-payment limit or the customer’s remaining daily limit,  
  **When** the customer attempts to make the payment,  
  **Then** the system rejects the payment and provides a clear rejection reason.

---

### US-19: Detect Excessive Payment Attempts

**Priority:** Should

As a system administrator,  
I want the system to detect more than five payments within 60 seconds from the same account,  
So that suspicious payment activity can be prevented.

**Acceptance Criteria:**

* **Given** that an account performs more than five payments within 60 seconds,  
  **When** the system detects the excessive payment activity,  
  **Then** the system blocks the account for 10 minutes.

---

### US-20: Search Payment History

**Priority:** Must

As a support employee,  
I want to search for a payment using its reference number,  
So that I can review its complete history and assist customers.

**Acceptance Criteria:**

* **Given** that a support employee has access to payment records,  
  **When** the employee searches using a payment reference number,  
  **Then** the system displays the payment’s full history.

---

### US-21: Block and Unblock Accounts

**Priority:** Should

As a support employee,  
I want to block and unblock customer accounts,  
So that I can manage accounts when necessary.

**Acceptance Criteria:**

* **Given** that a support employee has access to account management,  
  **When** the employee chooses to block or unblock a customer account,  
  **Then** the system updates the account status accordingly.

---

### US-22: Send Payment Notifications

**Priority:** Must

As a customer,  
I want to receive an SMS and a push notification when my payment is completed or fails,  
So that I can stay informed about my payment status.

**Acceptance Criteria:**

* **Given** that a payment is completed or fails,  
  **When** the system updates the payment status,  
  **Then** the system sends an SMS and a push notification to the customer with the payment result.

---

### US-23: Retry Failed Notifications

**Priority:** Should

As a system administrator,  
I want the system to retry failed notifications three times and then mark them as undelivered,  
So that customers have another chance to receive important messages.

**Acceptance Criteria:**

* **Given** that an SMS or email notification fails to be delivered,  
  **When** the notification retry process is triggered,  
  **Then** the system retries sending the notification up to three times.

* **Given** that all three retry attempts fail,  
  **When** the final attempt is unsuccessful,  
  **Then** the system marks the notification as undelivered.

---

### US-24: Update Fees and Limits

**Priority:** Should

As an administrator,  
I want to edit service fee and payment limit values without redeploying the application,  
So that I can update business rules easily.

**Acceptance Criteria:**

* **Given** that an administrator has permission to manage system configuration,  
  **When** the administrator updates a service fee or payment limit,  
  **Then** the system saves the new value and applies it without requiring an application redeployment.

---

### US-25: Maintain Audit Logs

**Priority:** Must

As a system administrator,  
I want every money movement and administrative action to be recorded in an audit log,  
So that system activities can be tracked and reviewed.

**Acceptance Criteria:**

* **Given** that a money movement or administrative action occurs,  
  **When** the system processes the action,  
  **Then** the system records the action in the audit log.

---

### US-26: Generate Reconciliation Reports

**Priority:** Should

As a support employee,  
I want the system to generate a daily reconciliation report using settlement files,  
So that I can identify differences between PayLink records and biller records.

**Acceptance Criteria:**

* **Given** that a biller provides a settlement file,  
  **When** the system processes the settlement file,  
  **Then** the system compares its payment records with the settlement file and generates a daily report showing any differences.

---

### US-27: Request a Refund

**Priority:** Could

As a customer,  
I want to request a refund for a completed payment,  
So that I can recover my money when a refund is needed.

**Acceptance Criteria:**

* **Given** that a customer has a completed payment,  
  **When** the customer submits a refund request,  
  **Then** the system records the refund request and sends it to the support team for approval.

* **Given** that the support team approves the refund request,  
  **When** the system processes the approved refund,  
  **Then** the system returns the payment amount to the customer's wallet.

---

### US-28: View Daily Payment Statistics

**Priority:** Could

As an administrator,  
I want to view daily payment statistics,  
So that I can monitor the financial and operational performance of PayLink.

**Acceptance Criteria:**

* **Given** that payment records are available,  
  **When** the administrator requests the daily statistics,  
  **Then** the system displays the total number of payments, fees earned, commissions paid, and failures per biller.

**Priority:** Could

As an administrator,
I want to view daily payment statistics,
So that I can monitor the financial and operational performance of PayLink.

**Acceptance Criteria:**

Given: that payment records are available,
When: the administrator requests the daily statistics,
Then: the system displays the total number of payments, fees earned, commissions paid, and failures per biller.


## 5. Non-Functional Requirements

### NFR-01: Correctness

**Priority:** Must

The system must prevent double charges and negative wallet balances.

* **Measurement:** Zero double charges and zero negative balances in the concurrency and duplicate payment tests.

---

### NFR-02: Performance

**Priority:** Must

The system must handle a high volume of payment requests efficiently.

* **Measurement:** 100 payments per second sustained for 1 minute against the mock billers.

---

### NFR-03: Resilience

**Priority:** Must

The system must continue processing payments to other billers if one biller becomes unavailable.

* **Measurement:** A biller outage does not stop payments to other billers.

---

### NFR-04: Modifiability

**Priority:** Must

The system must allow adding a new biller without modifying existing modules.

* **Measurement:** Adding a biller touches only the Biller Integration module and configuration.

---

### NFR-05: Auditability

**Priority:** Must

The system must record every money movement with details about who performed it, when it happened, and why it happened.

* **Measurement:** 100% of money movements have an audit record with who, when, and why.

---

### NFR-06: Testability

**Priority:** Must

The system must provide sufficient test coverage for its domain logic.

* **Measurement:** At least 70% line coverage on domain logic, measured in CI.


## 6. Ambiguities, Questions, and Decisions

### AM-01: Refunding a Payment in Unknown State
* **Question / Ambiguity:** Can a payment in the `Unknown` state be refunded automatically if a timeout happens or a callback is delayed[cite: 4, 5]?
* **Decision:** No. A payment in `Unknown` is never refunded automatically; money is only returned once the system is sure the biller did not receive it (for example after retries or reconciliation confirms failure or absence)[cite: 4, 5].

### AM-02: Retry Schedule for Unknown Payments
* **Question / Ambiguity:** After a live biller times out (10 seconds) and moves the payment to `Unknown`, how many times and at what intervals should the system re-query before waiting for nightly reconciliation[cite: 4, 5]?
* **Decision:** Re-query up to 3 times after 1 minute, 5 minutes, and 15 minutes before waiting for the daily reconciliation file[cite: 5].

### AM-03: Handling Duplicate Payment Requests and Duplicate Callbacks
* **Question / Ambiguity:** What should happen if the mobile app sends the same payment request twice due to network slowness, or if TopUpNet sends the `"success"` callback twice[cite: 6]?
* **Decision:** Enforce idempotency keys on payment requests so a duplicate request returns the original result without creating another payment or charging again[cite: 3, 6, 8]. For duplicate asynchronous callbacks, process once and ignore/log duplicate callbacks[cite: 6, 8].

### AM-04: Wallet Balance Calculation Source of Truth
* **Question / Ambiguity:** Should wallet balances be updated directly in place, or derived from transaction records[cite: 3]?
* **Decision:** Every balance change creates a ledger entry, and balances are computed from ledger entries rather than overwriting a balance directly[cite: 3, 8].

### AM-05: Biller Scope and NetCo Batch Handling
* **Question / Ambiguity:** Which billers are mandatory in the core flow, and is NetCo's daily batch CSV flow required in the initial scope[cite: 4, 8]?
* **Decision:** Support ElectroCo, AquaCo, and TopUpNet as the core billers with their respective protocols and behaviors, while treating NetCo daily batch CSV processing as optional[cite: 4, 8].

### AM-06: Failed Notifications vs. Payment Completion
* **Question / Ambiguity:** If the SMS or notification provider fails when a payment completes, should the payment itself fail or roll back[cite: 6]?
* **Decision:** No. The payment still completes, and the failed notification is retried up to 3 times and then marked as `undelivered` if all retries fail[cite: 5, 6, 9].