# Smart Canteen System Workflow

```mermaid
flowchart TD
    %% Define Styles
    classDef student fill:#e0f2fe,stroke:#0284c7,stroke-width:2px,color:#0f172a
    classDef canteen fill:#fef08a,stroke:#ca8a04,stroke-width:2px,color:#0f172a
    classDef system fill:#f3f4f6,stroke:#4b5563,stroke-width:2px,color:#0f172a
    classDef auth fill:#e8b4b8,stroke:#b56576,stroke-width:2px,color:#0f172a
    
    %% Entry Point
    Start((Start)) --> Login
    Login{Login Page}:::auth
    
    %% Authentication Routing
    Login -->|Valid Student ID| SDash[Student Dashboard]:::student
    Login -->|Admin Password| CDash[Canteen Dashboard]:::canteen
    
    %% Student Flow
    subgraph Student Workflow
        SDash --> Browse[Browse Menu Categories]:::student
        Browse --> Cart[Add Items to Cart]:::student
        Cart --> Checkout[Place Order]:::student
        Checkout --> Track[Track Estimated Wait Time]:::student
        Track --> Wait[Wait for Order Ready]:::student
        Wait -.-> Pickup((Pickup Food)):::student
    end
    
    %% System Flow (Context/Storage)
    subgraph State Management
        Checkout --> Context[OrderContext generates UUID & saves to LocalStorage]:::system
        Context --> UpdateQueue[Update Pending Orders Queue]:::system
    end
    
    %% Canteen Flow
    subgraph Canteen Workflow
        CDash --> ViewQueue[View Pending Orders]:::canteen
        UpdateQueue --> ViewQueue
        ViewQueue --> PrepareFood[Prepare Food]:::canteen
        PrepareFood --> CompleteOrder[Mark Order as Completed]:::canteen
        CompleteOrder --> History[Moved to Order History]:::canteen
    end
    
    %% Closing the loop
    CompleteOrder -->|Triggers Notification| Wait
```

### Explanation of the Flow:
1. **Authentication**: Users log in. Based on their credentials, they are routed to either the Student Dashboard or the Canteen Dashboard.
2. **Student Ordering**: Students browse the menu, add items to their cart, and place an order.
3. **State Management**: The application saves the order with a unique ID into the browser's local storage and updates the global state.
4. **Canteen Fulfillment**: The order appears in real-time on the Canteen Dashboard. Staff prepares the food and marks it as complete.
5. **Notification**: Marking the order as complete updates the system, which automatically notifies the student that their food is ready for pickup.
