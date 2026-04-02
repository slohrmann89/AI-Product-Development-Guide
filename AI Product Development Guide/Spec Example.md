# Spec Example

# UC-USER-002: Deactivate Customer

## Purpose

Enable administrators to safely deactivate customers who are no longer active while ensuring data integrity and maintaining a complete audit trail for compliance and historical tracking.

## User Story

> As an admin, I want to deactivate customers who are no longer using our service so that we maintain a clean active customer base while preserving historical data for auditing and potential reactivation.
> 

## Primary Actor

Admin User

## Preconditions

- User is authenticated as admin
- Customer exists in system
- Customer has no open orders

## Main Flow

1. Admin selects customer to deactivate
2. System validates customer has no open orders
3. System sets customer status to "inactive"
4. System logs audit trail (timestamp, user, action)
5. System confirms deactivation to admin

## Alternative Flows

**2a. Customer has open orders**

- System displays error: "Cannot deactivate customer with open orders"
- Use case ends

**1a. Admin lacks deactivation permissions**

- System displays error: "Insufficient permissions to deactivate customers"
- Use case ends

**3a. Database write fails**

- System rolls back status change
- System logs error details
- System displays error: "Failed to deactivate customer. Please try again."

## Postconditions

- Customer status is "inactive"
- Audit log entry created
- Customer cannot access system
- Customer data remains in database (soft delete)

## Acceptance Criteria

> WHEN admin deactivates a customer with no open orders THEN the system SHALL set status to "inactive", create audit log entry, and display success confirmation
> 

> WHEN admin attempts to deactivate a customer with open orders THEN the system SHALL prevent deactivation and display error message "Cannot deactivate customer with open orders"
> 

> WHEN inactive customer attempts to login THEN the system SHALL deny access and display "Account is inactive. Please contact support."
> 

> WHEN deactivation occurs THEN the system SHALL complete audit logging within 100ms to ensure compliance requirements
> 

## Context

**Technical Constraints:**

- Must use soft delete (status field) rather than hard delete
- Audit log must be append-only and immutable
- Operation must be atomic (status + audit log together)

**Performance Requirements:**

- Validation must complete within 200ms
- Total operation must complete within 500ms
- Must handle concurrent deactivation attempts safely

**Dependencies:**

- Order Management System (to check open orders)
- Authentication Service (for admin permissions)
- Audit Logging Service

## Business Rules

- BR-001: Customer email must be unique
- BR-003: All write operations must be audited with timestamp, user, and change details
- BR-007: Customers cannot be deleted, only deactivated (regulatory compliance)
- BR-012: Only users with "admin" or "customer_manager" roles can deactivate customers

## Examples

**Example 1: Successful Deactivation**

- Input: Admin selects customer ID 12345, clicks "Deactivate"
- Validation: Customer 12345 has 0 open orders
- Output: Status updated to "inactive", audit log created: `{timestamp: "2026-01-28T09:42:00Z", user: "admin@company.com", action: "deactivate_customer", customer_id: 12345}`, confirmation message displayed

**Example 2: Blocked Deactivation**

- Input: Admin selects customer ID 67890, clicks "Deactivate"
- Validation: Customer 67890 has 2 open orders (Order #4521, Order #4589)
- Output: Error message "Cannot deactivate customer with open orders (#4521, #4589). Please close or cancel these orders first."

**Example 3: Inactive Customer Login Attempt**

- Input: Deactivated customer enters credentials on login form
- Validation: Authentication succeeds but customer status is "inactive"
- Output: Login denied, message displayed "Your account is inactive. Please contact support at [support@company.com](mailto:support@company.com)"