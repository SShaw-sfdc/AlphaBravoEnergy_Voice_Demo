# Alpha Bravo Energy — WISMO Flow Action Spec

## Purpose
This Autolaunched Flow is called by the Agentforce Voice agent as an action to look up order status.

## Flow Details

| Field | Value |
|---|---|
| Flow Type | Autolaunched Flow (No Trigger) |
| Label | `Get Order Status` |
| API Name | `Get_Order_Status` |
| Description | Returns order status and line items for a given order number. Used by the Alpha Bravo Energy Agentforce Voice agent. |

---

## Input Variables

| API Name | Data Type | Description |
|---|---|---|
| `orderNumber` | Text | The order number provided by the customer (e.g. 00000110) |

## Output Variables

| API Name | Data Type | Description |
|---|---|---|
| `orderStatus` | Text | The Status field from the Order record |
| `orderItems` | Text | Comma-separated list of product names on the order |
| `accountName` | Text | The Account name the order belongs to |
| `errorMessage` | Text | Populated if the order is not found |

---

## Flow Logic (step by step)

### Element 1 — Get Order Record
- **Type:** Get Records
- **Label:** `Get Order by Number`
- **Object:** Order
- **Filter:** `OrderNumber = {!orderNumber}`
- **Fields to store:** Id, OrderNumber, Status, AccountId, Account.Name
- **Store as:** Single record → variable `orderRecord`
- **If no records found:** Let flow continue (handle with decision below)

### Element 2 — Decision: Order Found?
- **Label:** `Was Order Found?`
- **Outcome 1 — Yes:** `orderRecord` is not null
- **Outcome 2 — No:** default

### Element 3a (Yes path) — Get Order Items
- **Type:** Get Records
- **Label:** `Get Order Items`
- **Object:** OrderItem
- **Filter:** `OrderId = {!orderRecord.Id}`
- **Fields:** Product2.Name
- **Store as:** All records → collection `orderItemCollection`

### Element 4a — Build Item List (Loop)
- **Type:** Loop
- **Label:** `Loop Order Items`
- **Collection:** `orderItemCollection`
- Append `{!currentItem.Product2.Name}` to a text variable `itemListText` with comma separator

### Element 5a — Assign Outputs (Yes path)
- `orderStatus` = `{!orderRecord.Status}`
- `orderItems` = `{!itemListText}`
- `accountName` = `{!orderRecord.Account.Name}`
- `errorMessage` = (empty)

### Element 3b (No path) — Assign Error
- `errorMessage` = `"I wasn't able to find an order with that number. Could you double-check the order number, or I can transfer you to a representative?"`
- `orderStatus` = (empty)
- `orderItems` = (empty)

---

## Agentforce Action Registration

After activating the Flow, register it as an Agent Action:

1. **Setup > Agent Actions > New**
2. **Action Type:** Flow
3. **Flow:** `Get Order Status`
4. **Action Label:** `Get Order Status`
5. **Description for AI:**
   ```
   Looks up an Alpha Bravo Energy customer order by order number. 
   Returns the order status and a list of products on the order.
   Use this when a customer provides an order number and asks about their order status, 
   shipping status, or what is included in their order.
   ```
6. **Input parameter — orderNumber:**
   - Collect from User: Yes
   - Description: `The customer's order number, typically 8 digits (e.g. 00000110)`
   - Required: Yes

7. Add this action to the **Order Status** topic on the agent.
