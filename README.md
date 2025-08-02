# 🎮 Unity Network Parenting Solution

A robust solution for parenting network objects under deeply nested GameObjects in Unity Netcode for GameObjects with **Distributed Authority** topology.

https://github.com/user-attachments/assets/8fa29e3c-a4e0-425f-a970-83b45b1c3d80
<div align="center">

<img width="505" height="390" alt="image" src="https://github.com/user-attachments/assets/4308c1d0-37b7-4f74-8728-f92e97fbe765" />

<img width="566" height="269" alt="image" src="https://github.com/user-attachments/assets/576cc186-497f-426b-b894-13b0fd4714a1" />
</div>

## 🚀 Features

- ✨ **Deep Nesting Support**: Parent network objects to deeply nested child GameObjects
- 🔄 **Automatic Synchronization**: All clients receive parenting changes automatically
- 🕐 **Late Join Support**: Players joining mid-game see correct object hierarchy
- 🎯 **Simple API**: One method call to parent any network object
- 🏗️ **Distributed Authority**: Designed for distributed authority network topology

## 📋 Prerequisites

- Unity Netcode for GameObjects
- Network Manager with **Distributed Authority** topology
- Target network objects must have **Auto Object Parent Sync turned OFF**
- Network objects to be parented must have **Ownership set to None**

## 🔧 Setup

### 1️⃣ Add Network Parent Centre

Attach the `NetworkParentCentre` component to the **root GameObject** of your network object (e.g., Player):

```
Player (Network Object) ← NetworkParentCentre goes here
├── Arm (child)
│   └── Hand (child) ← NetworkParent goes here
└── Other components...
```

### 2️⃣ Configure Network Parents

Add `NetworkParent` components to any child GameObject where you want to enable parenting:

```csharp
// On the Hand GameObject
NetworkParent networkParent = handGameObject.GetComponent<NetworkParent>();
networkParent.NetworkParentId = "Object Holder Network Parent";
```

### 3️⃣ Parent Network Objects

Use the simple API to parent any spawned network object:

```csharp
// Get reference to the Network Parent Centre
NetworkParentCentre parentCentre = playerGameObject.GetComponent<NetworkParentCentre>();

// Parent the network item (e.g., weapon) to the specified parent
bool success = parentCentre.TryToParentNetworkObject(weaponNetworkObject, "Object Holder Network Parent");
```

## 💡 Example Use Case

Perfect for scenarios like:
- 🗡️ **Weapon Systems**: Parenting weapons to player hands
- 📦 **Inventory Items**: Attaching items to specific body parts
- 🎒 **Equipment**: Mounting gear to character attachment points
- 🚗 **Vehicle Interactions**: Placing objects inside vehicles

## ⚙️ Configuration Requirements

| Setting | Value | Location |
|---------|--------|----------|
| Network Manager Topology | `Distributed Authority` | Network Manager |
| Auto Object Parent Sync | `OFF` | Network Object to be parented |
| Object Ownership | `None` | Network Object to be parented |

## 📝 API Reference

### NetworkParentCentre

```csharp
public bool TryToParentNetworkObject(NetworkObjectReference networkObjectReference, string networkParentId)
```

**Parameters:**
- `networkObjectReference`: The network object to be parented
- `networkParentId`: The identifier of the target parent

**Returns:** `bool` - Success status of the parenting operation

### NetworkParent

```csharp
public string NetworkParentId; // Set this to identify the parent location
```

## 🔍 How It Works

1. **Initialization**: `NetworkParentCentre` discovers all `NetworkParent` components in children
2. **Parenting Request**: Call `TryToParentNetworkObject()` with target object and parent ID
3. **Local Parenting**: Object is immediately parented locally on the owner
4. **Network Sync**: `ClientRpc` synchronizes the parenting across all clients
5. **Late Join Support**: New clients automatically receive the correct hierarchy

## 🚨 Important Notes

- ⚠️ Only the **owner** of the NetworkParentCentre can initiate parenting operations
- 🏗️ NetworkParentCentre must be on a **root GameObject** (no parent)
- 🔒 Target network objects must have **ownership set to None**
- 📴 Ensure **Auto Object Parent Sync is disabled** on objects to be parented
