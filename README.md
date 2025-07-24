
# STX-NFTQuest- Smart Contract

## Overview

**Quest** is a blockchain-based game contract implemented in Clarity for the Stacks blockchain. It supports player registration, team formation, item management via NFTs, a marketplace for trading game items, and a leveling system based on experience points.

---

## Features

* **Player Management**: Register players with initial stats and inventory.
* **Team System**: Create teams with leaders and members.
* **NFT Game Items**: Mint, own, and trade game items as NFTs.
* **Marketplace**: List game items for sale and buy listed items.
* **Player Progression**: Gain experience and level up.
* **Admin Controls**: Set and change the admin.

---

## Constants & Errors

| Constant           | Description                         |
| ------------------ | ----------------------------------- |
| ERR-NOT-ADMIN      | Caller is not the admin             |
| ERR-NOT-FOUND      | Entity (player/item/team) not found |
| ERR-INVALID-PARAMS | Invalid parameters provided         |
| ERR-UNAUTHORIZED   | Unauthorized action                 |
| ERR-INVALID-PRICE  | Price is out of allowed range       |
| ERR-INVALID-LEVEL  | Level exceeds maximum allowed       |
| ERR-INVALID-TEAM   | Team ID already exists              |

---

## Data Variables

| Variable  | Type      | Initial Value | Description                            |
| --------- | --------- | ------------- | -------------------------------------- |
| admin     | principal | `tx-sender`   | Contract admin (owner)                 |
| min-price | uint      | 1             | Minimum price for marketplace listings |
| max-price | uint      | 1,000,000,000 | Maximum price for marketplace listings |
| max-level | uint      | 100           | Maximum player level                   |

---

## Tokens

* **game-item**: Non-Fungible Token (NFT) representing in-game items.

---

## Maps

### Players

Maps player principal addresses to player data:

* `level`: Player's current level (uint)
* `experience`: Player's accumulated experience (uint)
* `inventory`: List of 10 item IDs (uint)
* `achievements`: List of 5 achievement IDs (uint)

### Teams

Maps team ID (uint) to team data:

* `leader`: Team leader (principal)
* `members`: List of 4 team members (principal)

### Market Listings

Maps item ID (uint) to marketplace listing:

* `price`: Sale price (uint)
* `seller`: Seller's principal
* `active`: Boolean flag indicating if listing is active

---

## Public Functions

### Player Management

* **register-player()**
  Registers a new player with initial level 1, 0 experience, empty inventory, and achievements. Fails if player already registered.

### Team System

* **create-team(team-id uint)**
  Creates a new team with the caller as leader and all members initialized to the leader. Fails if the team ID exists or caller is not a registered player.

### Marketplace

* **list-item-for-sale(item-id uint, price uint)**
  Lists an owned game item for sale at a valid price. Fails if the price is invalid, item is not owned, or item does not exist.

* **buy-item(item-id uint)**
  Buys an active listed item, transferring STX from buyer to seller and the NFT from seller to buyer. Deactivates the listing after purchase.

### Game Item Management

* **mint-item(item-id uint, recipient principal)**
  Admin-only function to mint a new game-item NFT to a registered player. Fails if the item ID already exists.

### Player Progress

* **gain-experience(amount uint)**
  Adds experience points to the caller's player data.

* **level-up()**
  Levels up the player if they have enough experience (at least `current-level * 100`). Resets experience to 0 after leveling. Fails if max-level exceeded or insufficient experience.

### Admin Functions

* **set-admin(new-admin principal)**
  Allows current admin to transfer admin rights to a new principal. Fails if called by non-admin or if the new admin is the same as the current one.

---

## Read-Only Functions

* **get-player-data(player principal)**
  Returns player data (level, experience, inventory, achievements).

* **get-team-data(team-id uint)**
  Returns team data (leader, members).

* **get-market-listing(item-id uint)**
  Returns marketplace listing details for an item.

---

## Private Helper Functions

* **is-valid-item(item-id uint)**: Checks if a game-item NFT exists.
* **validate-price(price uint)**: Checks if price is within allowed min and max.
* **validate-level(level uint)**: Checks if level does not exceed max level.
* **own-item(item-id uint)**: Checks if the caller owns the specified game item.

---
