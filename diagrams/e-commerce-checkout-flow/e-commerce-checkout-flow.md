# Online checkout flow

A checkout is the most-abandoned journey in most products, and the usual diagram of it is a wall of
identical boxes. This one keeps every node and edge of its source and changes only how it reads.

![Online checkout flow](e-commerce-checkout-flow.svg)

## The source

Committed here so the redraw above can be checked against it by anyone, without the conversation
that produced it.

```mermaid
graph TD
    %% Define styles for different types of nodes
    classDef process fill:#f9f,stroke:#333,stroke-width:2px;
    classDef decision fill:#ff9,stroke:#333,stroke-width:2px,stroke-dasharray: 5 5;
    classDef endnode fill:#bef,stroke:#333,stroke-width:2px;
    classDef startnode fill:#9f9,stroke:#333,stroke-width:2px;

    %% Entry Point
    Start("Shopping Cart<br/>items chosen, nothing charged") --> |Proceed to Checkout| Auth{"Authentication<br/>decides how details arrive"};
    class Start startnode;
    class Auth decision;

    %% Authentication Path
    Auth -->|Guest Checkout| Shipping["Enter Shipping Information<br/>where the order goes"];
    Auth -->|Login / Create Account| UserInfo["Pre-fill User Info<br/>details from the account"];
    UserInfo --> Shipping;
    class Shipping,UserInfo process;

    %% Shipping & Delivery
    Shipping --> ShipMethod["Select Shipping Method<br/>speed against cost"];
    ShipMethod --> |Calculate Cost| Billing{"Billing Address<br/>where the card is registered"};
    class ShipMethod process;
    class Billing decision;

    %% Billing Address Decision
    Billing -->|Same as Shipping| Payment["Select Payment Method<br/>the instrument to charge"];
    Billing -->|Different Address| BillInfo["Enter Billing Information<br/>when it differs from shipping"];
    BillInfo --> Payment;
    class Payment,BillInfo process;

    %% Payment & Review
    Payment --> OrderReview["Review Order Summary<br/>last look before commitment"];
    OrderReview --> |Place Order| PayAuth{"Payment Authorization<br/>funds held, not yet captured"};
    class OrderReview process;
    class PayAuth decision;

    %% Payment Authorization Result
    PayAuth -->|Authorized| OrderConfirm["Order Confirmation Page<br/>the order now exists"];
    PayAuth -->|Denied/Error| PayError["Display Payment Error<br/>cart and details preserved"];
    PayError --> Payment;
    class OrderConfirm,PayError process;

    %% Final Output
    OrderConfirm --> Email["Send Confirmation Email<br/>a receipt outside the session"];
    Email --> End("End<br/>nothing further is required");
    class Email process;
    class End endnode;
```

## Why the captions live in the source

The first draft of this diagram was drawn from a source with node names and nothing else. The house
style asks every node to carry a one-line caption, because a bare title in a box reads flat. Those
two facts collide: writing a caption during the redraw means the picture asserts something the
source never said.

So the captions went into the source instead. **"funds held, not yet captured" is a claim about how
card payments work**, and a claim belongs where it can be reviewed, not where it can only be
admired. The redraw then does what it always does — it changes nothing.

This is the general rule. When a diagram needs structure or detail its source does not express,
update the source and redraw. Never infer it while drawing.

## What the redraw changed

**14 nodes in, 14 nodes out. 16 edges in, 16 edges out. Every title, caption, and edge label
verbatim.**

| Changed                                                           | Kept                                                 |
| ----------------------------------------------------------------- | ---------------------------------------------------- |
| Layout — one spine with side spurs, instead of an auto-laid tree  | Every node and edge                                  |
| Colour — the source's `classDef` fills replaced by house tokens   | The class _assignments_, carried by shape and border |
| Routing — rounded elbows, the retry path pulled clear to the left | Edge direction, throughout                           |
| Emphasis — one focal accent                                       | Every label and caption, word for word               |

The distinction in row two is the one that matters. `fill:#f9f` is presentation, so it goes. But
`class PayAuth decision` is a claim about what kind of thing that node is, so it survives — as a
diamond with a blue border rather than as a yellow fill.

## Reading it

**Shapes carry the node's kind**, exactly as the source assigns it:

- **Stadiums** — where the journey starts and ends.
- **Rectangles** — a step someone or something performs.
- **Diamonds** — a branch. Three of them: how you identify yourself, whose address gets billed, and
  whether the payment clears.

**Three spurs leave the spine and rejoin it.** Logging in pre-fills your details and puts you back
on the shipping step. A different billing address is an extra form before payment. Both are detours,
not alternative journeys, and the layout says so by returning them to the same column.

**One branch does not rejoin — it reverses.** A denied payment sends you back up to _Select Payment
Method_, and that back-edge is the only place in the whole flow where the reader travels upward. It
gets the accent for that reason: it is the single point where a checkout stops being a queue and
starts being a loop.

## Why it does not move

Diagram Therapy animates flow, never structure. A checkout is a journey a person takes once, not a
system with throughput, so packets travelling down these connectors would imply a liveness that does
not exist — the exact failure the house rules exist to prevent.

The retry loop is the one edge with a defensible case for motion, because repetition is genuinely
what it depicts. That is a judgment call, not a default, and it is left off here.
