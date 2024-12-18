# **Introduction to Move Programming Language**
The **Move programming language** is a smart contract platform designed for secure and programmable blockchain development. It focuses on resource-oriented programming, ensuring ownership, safety, and integrity of on-chain data.

---

## **1. Basics of Move Language**

### **Move Key Features**
1. **Resource-Oriented Programming**:
   - Resources are data types that cannot be duplicated or deleted unintentionally.
   - This ensures the safety of assets (e.g., tokens, NFTs) on the blockchain.

2. **Modules**:
   - A module in Move acts as a container for functions, structs, and constants.

3. **Objects**:
   - Objects hold state on-chain and can have access control rules.

4. **Ownership and Safety**:
   - Resources are tracked with a type system to prevent duplication or accidental loss.

---

### **Move Data Types**
Move provides various types for programming:
- **Primitives**:
  - `bool`: `true` or `false`
  - `u8, u64, u128`: Unsigned integers.
  - `address`: Blockchain address.
  - `vector<T>`: A collection of values of type `T`.

- **Custom Types**:
  - **Structs**: Used for grouping related fields together.
  - Example:
    ```move
    public struct Game has key {
        id: UID,
        board: vector<u8>,
        turn: u8,
        x: address,
        o: address,
    }
    ```

---

### **Move Functions**
- **Public and Private Functions**:
  - `public fun`: Accessible outside the module.
  - `fun`: Private, accessible only within the module.
  
- **Syntax**:
  ```move
  public fun function_name(param: type): return_type {
      // logic
  }
  ```

- **Assertions**:
  - Assertions are used for error handling.
  ```move
  assert!(condition, error_message);
  ```

- **Resource Transfers**:
  - Transfer resources between users or functions using `transfer::share_object` or `transfer::transfer`.

---

## **2. Understanding the Tic-Tac-Toe Contracts**

The implementation is split into two modules:
1. **Shared Module**: A version where the game state is shared between players.
2. **Owned Module**: A version where a centralized admin controls the game state.

---

### **Shared Module**

The **shared module** allows both players to mutate the game state directly. Key components are:

#### **Structs**
- `Game`:
  - Represents the current game state.
  ```move
  public struct Game has key {
      id: UID,
      board: vector<u8>,
      turn: u8,
      x: address,
      o: address,
  }
  ```
- `Trophy`:
  - An NFT sent to players at the game's end.
  ```move
  public struct Trophy has key {
      id: UID,
      status: u8,
      board: vector<u8>,
      turn: u8,
      other: address,
  }
  ```

#### **Key Functions**
1. **`new`**:
   - Creates a new game.
   - Initializes a `Game` object with an empty board and player addresses.
   ```move
   public fun new(x: address, o: address, ctx: &mut TxContext)
   ```

2. **`place_mark`**:
   - Players add a mark (`X` or `O`) on their turn.
   - Checks for win/draw conditions.
   ```move
   public fun place_mark(game: &mut Game, row: u8, col: u8, ctx: &mut TxContext)
   ```

3. **`ended`**:
   - Checks whether the game has ended in a win, draw, or is still ongoing.
   ```move
   public fun ended(game: &Game): u8
   ```

4. **`next_player`**:
   - Determines the next player to make a move.

---

### **Owned Module**

The **owned module** relies on an admin to validate moves. This introduces a **TurnCap** for controlling turns.

#### **Structs**
- `Game`: Same as shared.
- `TurnCap`:
  - Grants the ability to make a move.
  ```move
  public struct TurnCap has key {
      id: UID,
      game: ID,
  }
  ```
- `Mark`:
  - Represents a player’s intention to place a mark.

#### **Key Functions**
1. **`new`**:
   - Creates a new game with an admin address.
   ```move
   public fun new(x: address, o: address, admin: vector<u8>, ctx: &mut TxContext)
   ```

2. **`send_mark`**:
   - Allows a player to send a `Mark` object.
   ```move
   public fun send_mark(cap: TurnCap, row: u8, col: u8, ctx: &mut TxContext)
   ```

3. **`place_mark`**:
   - Admin finalizes the mark and updates the game state.
   ```move
   public fun place_mark(game: &mut Game, mark: Receiving<Mark>, ctx: &mut TxContext)
   ```

---

## **3. Constants and Errors**
### **Constants**
- **Marks**:
  - `MARK__`: Empty slot.
  - `MARK_X`: Represents `X`.
  - `MARK_O`: Represents `O`.
- **Trophy Status**:
  - `TROPHY_NONE`: Game ongoing.
  - `TROPHY_DRAW`: Game drawn.
  - `TROPHY_WIN`: Game won.

### **Errors**
- `EInvalidLocation`: Move is out of bounds.
- `EWrongPlayer`: Move made by the wrong player.
- `EAlreadyFinished`: Game has already ended.

---

## **4. Workflow Summary**

### **Shared Module** Workflow
1. Create a game using `new`.
2. Players place marks using `place_mark`.
3. The game checks for end conditions (`win`/`draw`).
4. A `Trophy` is minted for the winning player or both players in case of a draw.

### **Owned Module** Workflow
1. Create a game with an admin using `new`.
2. Players send a `Mark` object using `send_mark`.
3. The admin finalizes moves using `place_mark`.
4. The game checks end conditions and issues a `Trophy`.

---

## **5. Move Best Practices**
1. **Avoid Redundancy**:
   - Use helper functions like `test_triple` to validate winning conditions.
2. **Resource Safety**:
   - Always transfer ownership of objects securely.
3. **Error Handling**:
   - Use `assert!` and custom error messages for checks.
4. **Efficiency**:
   - Use `shared` modules for direct player updates.
   - Use `owned` modules for admin validation.

---

## **6. Conclusion**
This documentation outlines the **Move basics**, key constructs, and explains how the Tic-Tac-Toe contract is implemented. Shared and Owned implementations showcase two models of state management:
- **Shared**: More straightforward but expensive.
- **Owned**: Centralized admin validation for cost efficiency.

