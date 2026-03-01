# 🧱 Roblox State-Authoritative Inventory & Shop Backend

A secure, modular, server-authoritative backend framework for building inventory, currency, shop, equip, and consumable systems in Roblox.

Designed for exploit resistance, transactional safety, and scalable architecture.

---

## 🎯 Overview

This system separates:

- **Backend logic** (ServerScriptService)
- **Configuration** (ReplicatedStorage/Shared)
- **Remotes** (ReplicatedStorage/Remotes)
- **UI** (StarterGui / Client)

All purchases, sells, equips, and currency mutations are validated server-side.

Clients never control price, quantity, or state.

---

## 🔐 Security Features

- Server-authoritative buy/sell validation  
- Integer-only quantity enforcement  
- Max stack enforcement  
- Per-player transaction locking (race-condition protection)  
- Atomic operations with rollback safety  
- Remote spam resistance  
- Snapshot-based UI state syncing  
- Centralized item configuration  

Designed to prevent:

- Duplication exploits  
- Stack overflow bypass  
- Negative currency abuse  
- Client-side price manipulation  

---

## 🏗 Architecture

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

---

## 🛒 Buy Flow (Server-Side)

1. Validate item exists  
2. Validate quantity (integer & capped)  
3. Validate stack limit  
4. Remove currency  
5. Add inventory  
6. Rollback if failure  
7. Sync updated state to client  

All steps are executed server-side.

---

## 💰 Sell Flow (Server-Side)

1. Validate ownership  
2. Remove inventory  
3. Add currency  
4. Rollback on failure  
5. Sync state  

Transactions are atomic and protected against race conditions.

---

## ⚙ Item Configuration

Create a separate `ItemConfig.lua` file inside:

```
ReplicatedStorage/Shared/ItemConfig.lua
```

Example structure:

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
		Image =  "rbxassetid://82345109307919"
	}

}

return ItemConfig

```

### Required Fields

- `BuyPrice` – Purchase cost  
- `SellPrice` – Sell value (optional)  
- `Max` – Maximum stack size  

---

## 🧩 Installation

1. Place all backend modules inside `ServerScriptService`.
2. Ensure `ReplicatedStorage` contains:
   - `Remotes` folder
   - `Shared/ItemConfig`
3. Connect UI buttons to the `GameAction` RemoteEvent.
4. Configure items inside `ItemConfig`.
5. Start the server.

System will automatically validate and sync state.

---

## 📡 Remote Structure

**GameAction**
- Handles buy, sell, equip, and use requests.

**StateSync**
- Sends updated state snapshot to the client.

**EquippedUpdate**
- Notifies client of equip changes.

Clients cannot directly mutate inventory or currency.

---

## 🧠 Designed For

- Monetized games  
- RPG systems  
- Simulator economies  
- Persistent multiplayer games  
- Developers who care about exploit resistance  

---

## 🚀 Why This Is Different

Most shop systems:

- Trust client input  
- Lack transaction locking  
- Don’t protect against race conditions  
- Mix UI and backend logic  

This system enforces:

- Clear service separation  
- Transaction safety  
- Exploit-aware design  
- Scalable modular structure  

---

## 📌 Notes

This backend focuses on structure, reliability, and security.

UI customization is fully separate from backend logic and can be modified freely without affecting system integrity.
