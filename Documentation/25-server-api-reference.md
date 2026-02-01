# Server API Reference

**Last Modified:** January 31, 2026

---

## Overview

This document provides a comprehensive reference for the Hytale Server API, covering core systems that plugin developers interact with most frequently.

> **Note:** This is based on community research and may change as Hytale evolves.

---

## Table of Contents

1. [Universe & World Management](#universe--world-management)
2. [Entity System (ECS)](#entity-system-ecs)
3. [Time System](#time-system)
4. [Task Scheduling](#task-scheduling)
5. [Messages API](#messages-api)
6. [Event System](#event-system)
7. [Sleep System](#sleep-system)
8. [Built-in Commands](#built-in-commands)
9. [Built-in Modules](#built-in-modules)

---

## Universe & World Management

### Universe Singleton

**Package:** `com.hypixel.hytale.server.core.universe`

The Universe is the top-level container for all worlds and players.

```java
// Access the universe singleton
Universe universe = Universe.get();

// Get all online players
List<PlayerRef> players = universe.getPlayers();

// Get all loaded worlds
Map<String, World> worlds = universe.getWorlds();

// Broadcast message to all players
universe.sendMessage(Message.raw("Server announcement!"));
```

### World Management

```java
// Create a new world
CompletableFuture<World> future = universe.addWorld("my_world");

// Load an existing world
CompletableFuture<World> future = universe.loadWorld("my_world");

// Remove a world
boolean removed = universe.removeWorld("my_world");
```

### World Class

**Package:** `com.hypixel.hytale.server.core.universe.world`

```java
World world = // ... get world reference

// Get current tick
long tick = world.getTick();

// Pause/unpause the world
world.setPaused(true);
boolean paused = world.isPaused();

// Get players in this world
List<Player> players = world.getPlayers();

// Broadcast to world
world.sendMessage(Message.raw("World announcement!"));

// Spawn an entity
Entity entity = world.spawnEntity(
    entityType,
    new Vector3d(x, y, z),    // position
    new Vector3f(0, 0, 0)      // rotation (pitch, yaw, roll)
);

// Get chunk asynchronously
CompletableFuture<WorldChunk> chunk = world.getChunkAsync(chunkKey);
```

### Thread Safety

**CRITICAL:** Component access must happen on the world thread:

```java
world.execute(() -> {
    // Safe to access components here
    Store<EntityStore> store = world.getEntityStore().getStore();
    var resource = store.getResource(resourceType);
});
```

---

## Entity System (ECS)

Hytale uses an Entity-Component-System architecture.

### Core Concepts

| Concept | Description |
|---------|-------------|
| `Entity` | Base object with UUID and network ID |
| `Component` | Data attached to entities |
| `ComponentType` | Type identifier for components |
| `Store` | Container for components |
| `Ref` | Reference to an entity |

### Accessing Components

```java
// Get the component type
ComponentType<EntityStore, Player> playerType = Player.getComponentType();

// Get component from entity
Player playerComponent = entity.getComponent(playerType);

// Get component from store
Store<EntityStore> store = world.getEntityStore().getStore();
Player playerComponent = store.getComponent(entityRef, playerType);
```

### Entity Base Class

**Package:** `com.hypixel.hytale.server.core.entity`

```java
Entity entity = // ...

// Identity
UUID uuid = entity.getUuid();
int networkId = entity.getNetworkId();

// Transform
TransformComponent transform = entity.getTransformComponent();
World world = entity.getWorld();

// Reference
Ref<EntityStore> ref = entity.getReference();

// Remove entity
boolean removed = entity.remove();
```

### Player Entity

**Package:** `com.hypixel.hytale.server.core.entity.entities`

Extends `LivingEntity`, implements `CommandSender`, `PermissionHolder`.

```java
Player player = // ...

// Inventory
Inventory inventory = player.getInventory();

// UI Managers
PageManager pages = player.getPageManager();
WindowManager windows = player.getWindowManager();
HudManager hud = player.getHudManager();

// Movement
player.moveTo(playerRef, x, y, z, componentAccessor);

// Permissions
boolean hasAdmin = player.hasPermission("admin.kick");
```

### PlayerRef

Persistent reference that survives world transfers.

```java
PlayerRef playerRef = // ...

UUID uuid = playerRef.getUuid();
String username = playerRef.getUsername();
Transform transform = playerRef.getTransform();

// Send message
playerRef.sendMessage(Message.raw("Hello!"));

// Transfer to another server
playerRef.referToServer("play.example.com", 25565);
```

---

## Time System

### WorldTimeResource

**Package:** `com.hypixel.hytale.server.core.modules.time`

```java
// Access time resource
world.execute(() -> {
    Store<EntityStore> store = world.getEntityStore().getStore();
    WorldTimeResource time = store.getResource(WorldTimeResource.getResourceType());

    // Read time properties
    int hour = time.getCurrentHour();           // 0-23
    float dayProgress = time.getDayProgress();  // 0.0-1.0
    double sunlight = time.getSunlightFactor(); // ambient light level
    int moonPhase = time.getMoonPhase();        // lunar cycle state
    Instant gameTime = time.getGameTime();      // full timestamp

    // Check time ranges
    boolean isAfternoon = time.isDayTimeWithinRange(0.5, 0.75);

    // Modify time
    time.setGameTime(newInstant, world, store);
    time.setDayTime(0.5, world, store);  // Set to noon (0.5 = midday)
});
```

### Time Constants

```java
// Available constants in WorldTimeResource
long NANOS_PER_DAY;
int SECONDS_PER_DAY;
int HOURS_PER_DAY;      // 24
int DAYS_PER_YEAR;
int DAYTIME_SECONDS;
int NIGHTTIME_SECONDS;
```

### Day/Night Configuration

```java
// Per-world settings
int dayDuration = world.getDaytimeDurationSeconds();
int nightDuration = world.getNighttimeDurationSeconds();

// Time dilation (slow-motion/speed-up)
world.setTimeDilation(0.5f, componentAccessor);  // Half speed
world.setTimeDilation(2.0f, componentAccessor);  // Double speed
```

---

## Task Scheduling

### Global Scheduler

**Package:** `com.hypixel.hytale.server.core`

```java
import java.util.concurrent.TimeUnit;

// One-time delayed task
HytaleServer.SCHEDULED_EXECUTOR.schedule(() -> {
    // Runs after 5 seconds
}, 5, TimeUnit.SECONDS);

// Repeating task
HytaleServer.SCHEDULED_EXECUTOR.scheduleAtFixedRate(() -> {
    // Runs every second
}, 0, 1, TimeUnit.SECONDS);
```

### TaskRegistry

Register tasks for automatic cleanup on plugin shutdown:

```java
// In your plugin
TaskRegistry taskRegistry = getTaskRegistry();

// Register a CompletableFuture
CompletableFuture<String> future = loadDataAsync();
taskRegistry.registerTask(future);

// Register a ScheduledFuture
ScheduledFuture<?> scheduled = HytaleServer.SCHEDULED_EXECUTOR
    .scheduleAtFixedRate(this::tick, 0, 1, TimeUnit.SECONDS);
taskRegistry.registerTask(scheduled);
```

### World Thread Execution

Always access components on the world thread:

```java
world.execute(() -> {
    // This runs on the world thread
    Store<EntityStore> store = world.getEntityStore().getStore();
    var resource = store.getResource(resourceType);
    // Safe to read/write components here
});
```

---

## Messages API

### Creating Messages

**Package:** `com.hypixel.hytale.server.core`

```java
// Simple message
Message msg = Message.raw("Hello, world!");

// Formatted message
Message formatted = Message.raw("Important!")
    .color("red")
    .bold(true)
    .italic(false);

// With parameters
Message parameterized = Message.raw("Welcome, {name}!")
    .param("name", "Steve");

// Styled options
Message styled = Message.raw("Click here")
    .monospace(true)
    .link("https://example.com");

// Combining messages
Message combined = Message.raw("Prefix: ")
    .insert(anotherMessage);
```

### Sending Messages

```java
// To a player
player.sendMessage(Message.raw("Hello!"));

// To all players in a world
world.sendMessage(Message.raw("World announcement!"));

// To all players on the server
Universe.get().sendMessage(Message.raw("Server announcement!"));
```

### Available Formatting

| Method | Description |
|--------|-------------|
| `.color("red")` | Set text color |
| `.bold(true)` | Bold text |
| `.italic(true)` | Italic text |
| `.monospace(true)` | Monospace font |
| `.link("url")` | Clickable link |
| `.param("key", "value")` | Replace {key} placeholder |
| `.insert(message)` | Append another message |

---

## Event System

### Registration Methods

**Package:** `com.hypixel.hytale.event`

```java
EventRegistry events = getEventRegistry();

// Basic registration
events.register(PlayerConnectEvent.class, event -> {
    Player player = event.getPlayer();
    // Handle event
});

// With priority
events.register(EventPriority.HIGH, PlayerConnectEvent.class, event -> {
    // Runs before lower priority handlers
});

// Async registration
events.registerAsync(PlayerChatEvent.class, future -> {
    return future.thenApply(event -> {
        // Async processing
        return event;
    });
});

// Keyed events (scoped to a specific key)
events.register(SomeKeyedEvent.class, "my_key", event -> {
    // Only handles events with this key
});

// Global keyed events
events.registerGlobal(PlayerChatEvent.class, event -> {
    // Handles all instances
});
```

### Common Events Reference

| Event | Type | Key Properties |
|-------|------|----------------|
| `PlayerConnectEvent` | Sync | `playerRef`, `player`, `world` |
| `PlayerDisconnectEvent` | Sync | `playerRef`, `disconnectReason` |
| `PlayerChatEvent` | Async, Keyed | `sender`, `targets`, `content` |
| `PlayerInteractEvent` | Sync, Cancellable | `player`, `actionType`, `itemInHand` |
| `BreakBlockEvent` | Sync, Cancellable | `itemInHand`, `targetBlock` |
| `PlaceBlockEvent` | Sync, Cancellable | `itemInHand`, `targetBlock` |
| `UseBlockEvent` | Sync, Cancellable | `player`, `blockPos` |

### Cancelling Events

```java
events.register(BreakBlockEvent.class, event -> {
    if (shouldPrevent(event)) {
        event.setCancelled(true);
    }
});
```

---

## Sleep System

### PlayerSomnolence Component

**Package:** `com.hypixel.hytale.builtin.beds.sleep`

Tracks player sleep state.

```java
// Get sleep component
ComponentType<EntityStore, PlayerSomnolence> somnolenceType =
    PlayerSomnolence.getComponentType();
PlayerSomnolence somnolence = player.getComponent(somnolenceType);

// Check sleep state
SleepState state = somnolence.getSleepState();
```

### Sleep States

| State | Description |
|-------|-------------|
| `FullyAwake` | Player is not in bed |
| `NoddingOff` | Player is getting into bed |
| `Slumber` | Player is fully asleep |
| `MorningWakeUp` | Player is waking up |

---

## Built-in Commands

### Time Commands

```
/time day                    Set time to day
/time night                  Set time to night
/time noon                   Set time to noon
/time midnight               Set time to midnight
/time sunrise                Set time to sunrise
/time sunset                 Set time to sunset
/time set <hour>             Set specific hour (0-23)
/time query                  Show current time
```

### Player Commands

```
/gamemode <mode>             Set gamemode (survival/creative/spectator/adventure)
/gm <s|c|sp|a>               Gamemode shorthand
/tp <player> <target>        Teleport player to target
/tp <player> <x> <y> <z>     Teleport to coordinates
/tphere <player>             Teleport player to you
/kick <player> [reason]      Kick player
/ban <player> [reason]       Ban player
/unban <player>              Unban player
```

### Operator Commands

```
/op add <player>             Make player an operator
/op remove <player>          Remove operator status
/op self                     Make yourself an operator
```

### Permission Commands

```
/perm user <player> add <permission>    Add permission to player
/perm user <player> remove <permission> Remove permission
/perm group <group> add <permission>    Add permission to group
/perm test <permission>                 Test if you have permission
```

### World Commands

```
/world list                  List all worlds
/world tp <world>            Teleport to world
/world create <name>         Create new world
/world remove <name>         Remove world
```

### Item Commands

```
/give <player> <item> [qty]  Give items to player
/clear <player> [item]       Clear inventory
/spawnitem <item> [x y z]    Spawn item in world
```

### Entity Commands

```
/summon <entity> [x y z]     Spawn entity
/kill <selector>             Kill entities (@e, @a, @p, or specific)
```

### Server Commands

```
/stop                        Stop the server
/save-all                    Save all data
/reload                      Reload configuration
```

### Weather Commands

```
/weather clear               Clear weather
/weather rain                Set rain
/weather storm               Set storm
```

---

## Built-in Modules

Hytale includes these built-in modules that plugins can interact with:

| Module | Package | Description |
|--------|---------|-------------|
| Combat | `builtin.combat` | Combat mechanics, damage, weapons |
| Crafting | `builtin.crafting` | Recipe system, crafting tables |
| NPCs | `builtin.npcs` | NPC spawning, dialogue, AI |
| Portals | `builtin.portals` | Portal teleportation mechanics |
| Mounts | `builtin.mounts` | Mount riding system |
| Fluid | `builtin.fluid` | Water and lava mechanics |
| Block Physics | `builtin.blockphysics` | Falling blocks, physics |
| World Gen | `builtin.worldgen` | Procedural world generation |
| Instances | `builtin.instances` | Instanced dungeons |
| Adventure | `builtin.adventure` | Quests, farming, objectives |
| Parkour | `builtin.parkour` | Parkour mechanics |
| Mantling | `builtin.mantling` | Climbing and ledge grabbing |
| Teleport | `builtin.teleport` | Teleportation utilities |
| Beds/Sleep | `builtin.beds` | Sleep system, bed mechanics |
| Access Control | `builtin.accesscontrol` | Bans, whitelist |

---

## Math Utilities

### Vectors

**Package:** `com.hypixel.hytale.math.vector`

```java
// Position (double precision)
Vector3d position = new Vector3d(100.5, 64.0, -200.5);

// Rotation (float precision)
Vector3f rotation = new Vector3f(0, 90, 0);  // pitch, yaw, roll

// Block position (integer)
Vector3i blockPos = new Vector3i(100, 64, -200);

// Operations
double length = position.length();
Vector3d normalized = position.normalize();
Vector3d added = position.add(new Vector3d(1, 0, 0));
```

### Transform

```java
Transform transform = new Transform(
    position,   // Vector3d
    rotation,   // Vector3f
    scale       // Vector3f
);
```

### Additional Math Classes

- `Mat4f` - 4x4 transformation matrix
- `Quatf` - Quaternion for rotations
- `Vec2f`, `Vec4f` - 2D and 4D vectors
- `Range` - Numeric ranges

---

## Codecs & Serialization

### BuilderCodec

**Package:** `com.hypixel.hytale.codec`

```java
public class MyConfig {
    private final String name;
    private final int count;
    private final boolean enabled;

    public static final Codec<MyConfig> CODEC = BuilderCodec.builder(MyConfig::new)
        .field("name", MyConfig::getName, Codec.STRING)
        .field("count", MyConfig::getCount, Codec.INT)
        .field("enabled", MyConfig::isEnabled, Codec.BOOLEAN)
        .build();

    // Constructor and getters...
}
```

### Common Codecs

| Codec | Type |
|-------|------|
| `Codec.STRING` | String |
| `Codec.INT` | Integer |
| `Codec.LONG` | Long |
| `Codec.FLOAT` | Float |
| `Codec.DOUBLE` | Double |
| `Codec.BOOLEAN` | Boolean |
| `Codec.UUID` | UUID |

---

## Best Practices Summary

1. **Thread Safety:** Always use `world.execute()` for component access
2. **Resource Access:** Get resources via `store.getResource(ResourceType)`
3. **Event Registration:** Register events in plugin's `start()` method
4. **Task Cleanup:** Use `TaskRegistry` for automatic cleanup on shutdown
5. **Component Pattern:** Get type first, then access via `getComponent(type)`

---

## Getting Help

**Official Channels:**
- **Discord:** [Official Hytale Discord](https://discord.gg/hytale)
- **Blog:** [Hytale News](https://hytale.com/news)

**Community Resources:**
- **Britakee Discord:** [discord.gg/gCRv62araB](https://discord.gg/gCRv62araB)
- **API Reference:** [rentry.co/gykiza2m](https://rentry.co/gykiza2m)
