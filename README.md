# Roblox State-Authoritative Inventory & Shop Backend

A secure and modular backend system for Roblox that handles **inventory, currency, shop purchases, equipping items, and consumables**.

The system is designed to be **server-authoritative**, meaning the client never controls important game data. All validation happens on the server to prevent exploits and maintain consistent state across players.

---

## Overview

This framework separates the game into clear layers so each part of the system has a single responsibility.

* **Backend Logic** – Handles validation, transactions, and state management (ServerScriptService)
* **Configuration** – Stores item data such as price, stack size, and rarity (ReplicatedStorage/Shared)
* **Remotes** – Communication bridge between client and server (ReplicatedStorage/Remotes)
* **UI** – Inventory, shop, and currency displays (StarterGui)

The client can **request actions**, but it never decides outcomes.
The server validates everything before changing player data.

---

## Security Design

This system was built with exploit resistance in mind.

Key protections include:

* Server-side validation for every buy or sell request
* Quantity checks to ensure only valid integers are accepted
* Stack limits enforced on the server
* Per-player transaction locks to prevent race conditions
* Atomic transactions with rollback safety
* Protection against remote spam
* Snapshot-based syncing to keep UI consistent
* Centralized item configuration to prevent mismatches

These safeguards help prevent common exploits such as:

* Item duplication
* Stack overflow abuse
* Negative currency manipulation
* Client-side price tampering

---

## Architecture

```
ServerScriptService
├─ Handlers
│   └─ ShopRemoteHandler
├─ Services
│   ├─ ConsumableService
│   ├─ CurrencyService
│   ├─ DataService
│   ├─ DataStoreService
│   ├─ EquipService
│   ├─ InventoryService
│   ├─ Logger
│   ├─ ShopService
│   └─ StateService
└─ Main.server.lua

ReplicatedStorage
├─ Remotes
│   ├─ EquippedUpdate
│   ├─ GameAction
│   └─ StateSync
├─ Shared
│   └─ ItemConfig
└─ Tools
    ├─ potion
    └─ sword

StarterGui
├─ CoinGui
├─ InventoryGui
└─ ShopGui
```

Each service is responsible for one part of the system.
This makes the backend easier to maintain and expand as the game grows.

---

## Buy Flow

When a player purchases an item, the server follows a strict validation process:

1. Confirm the item exists in the configuration
2. Validate the quantity requested
3. Check that stack limits are not exceeded
4. Deduct the correct amount of currency
5. Add the item to the player's inventory
6. Roll back the transaction if any step fails
7. Send an updated state snapshot to the client

Every step runs on the **server only**.

---

## Sell Flow

Selling items follows a similar server-side transaction process:

1. Confirm the player owns the item
2. Remove the item from inventory
3. Add the appropriate currency value
4. Roll back the transaction if something fails
5. Sync the updated state to the client

This keeps inventory and currency changes consistent even during heavy server activity.

---

## Item Configuration

Items are defined in a shared configuration file.

Location:

```
ReplicatedStorage/Shared/ItemConfig.lua
```

Example:

```lua
local ItemConfig = {

	potion = {
		BuyPrice = 100,
		SellPrice = 50,
		Stackable = true,
		Max = 99,
		Power = 10,
		Rarity = "Rare",
		Image = "rbxassetid://93407771464016"
	},

	sword = {
		BuyPrice = 500,
		SellPrice = 250,
		Stackable = false,
		Max = 1,
		Power = 100,
		Rarity = "Legendary",
		Image = "rbxassetid://82345109307919"
	}

}

return ItemConfig
```

### Required Fields

* **BuyPrice** – Currency required to purchase the item
* **SellPrice** – Currency returned when selling (optional)
* **Max** – Maximum stack size allowed

Keeping item data centralized makes balancing and expansion much easier.

---

## Installation

1. Place all backend modules inside **ServerScriptService**
2. Ensure **ReplicatedStorage** contains:

   * a `Remotes` folder
   * `Shared/ItemConfig`
3. Connect UI buttons to the **GameAction** RemoteEvent
4. Add items to `ItemConfig`
5. Start the server

Once the server starts, the system will handle validation and state syncing automatically.

---

## Remote Events

**GameAction**

Used by the client to request actions such as:

* buying items
* selling items
* equipping gear
* using consumables

The server verifies every request before applying changes.

---

**StateSync**

Sends a snapshot of the player’s current state back to the client so the UI stays updated.

---

**EquippedUpdate**

Notifies the client when equipment changes.

---

## Use Cases

This backend works well for games that need a reliable economy system, such as:

* RPGs
* Simulator games
* Monetized experiences
* Persistent multiplayer worlds

---

## Why This Architecture Works

Many Roblox shop systems make a critical mistake:
they trust the client.

This framework avoids that by enforcing:

* strict server authority
* atomic transactions
* separation between UI and backend logic
* modular service design

The result is a system that is **harder to exploit and easier to maintain**.

---

## Final Notes

The backend focuses on **security, structure, and reliability**.

UI systems are intentionally kept separate, which allows developers to redesign interfaces without touching the backend logic.
