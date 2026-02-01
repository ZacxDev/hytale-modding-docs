# Hytale Modding Documentation

> **Status**: Hytale is in Early Access (launched Dec 2025). Modding APIs are evolving rapidly.
> **Last Updated**: 2026-01-30
> **Game Version Reference**: `2026.01.28-87d03be09`

---

## Reference Sources (for updating this document)

These are the canonical sources. When modding APIs change, re-fetch from these to update this file.

| Source | URL | What It Covers |
|--------|-----|----------------|
| **Hytale Modding (community)** | https://hytalemodding.dev/en/docs | Guides, ECS, plugins, data assets, official docs mirror |
| **Britakee GitBook** | https://britakee-studios.gitbook.io/hytale-modding-documentation | Tested tutorials, plugin patterns, content packs |
| **Hytale Server API Reference** | https://rentry.co/gykiza2m | 2,959 classes across 664 packages |
| **HytaleDevLib** | https://htdevlib.netlify.app | Helper classes for plugin development |
| **Official Modding Strategy** | https://hytale.com/news/2025/11/hytale-modding-strategy-and-status | Hypixel Studios modding roadmap |
| **Official Server Manual** | https://support.hytale.com/hc/en-us/articles/45326769420827-Hytale-Server-Manual | Server setup, configuration |
| **Plugin Template (GitHub)** | https://github.com/HytaleModding/plugin-template | Official starter project |
| **Hytale Modding GitHub Org** | https://github.com/HytaleModding | Community tools, open-source resources |
| **Hytale Modding Toolkit** | https://github.com/logan-mcduffie/hytale-toolkit | Decompiled source, AI search, Maven template |

### Context7 Library IDs (for programmatic doc lookup)

```
/websites/hytalemodding_dev_en              # Community guides & docs (1125 snippets)
/websites/britakee-studios_gitbook_io_hytale-modding-documentation  # Tested tutorials (457 snippets)
/websites/rentry_co_gykiza2m                # Server API reference (343 snippets)
/websites/htdevlib_netlify_app              # DevLib helpers (440 snippets)
/logan-mcduffie/hytale-toolkit              # Toolkit + decompiled source (45103 snippets)
```

---

## Modding Architecture Overview

Hytale uses a **server-first** modding architecture. All mods run server-side; clients do not need to install mods separately. The modding system is split into three categories:

### 1. Packs (Content/Asset Packs)
- **No coding required** - JSON-driven data assets
- Created/edited via the **Hytale Asset Editor**
- Define blocks, items, NPCs, biomes, world generation, UI
- Analogous to Minecraft data packs but with significantly broader scope
- Distributed as `.zip` files dropped into `mods/` folder

### 2. Plugins (Java Plugins)
- Written in **Java** (or JVM languages like Kotlin)
- Full access to server API (`com.hypixel.hytale.server.*`)
- Packaged as `.jar` files dropped into `mods/` folder
- Extends `JavaPlugin` base class
- Build with Maven or Gradle against Hytale's Maven repository

### 3. Visual Scripting (Planned)
- Node-graph editors for complex asset types
- Will be integrated into the Hytale Asset Editor
- Aimed at non-programmers

---

## Plugin Development Quick Start

### Prerequisites
- **Java 25** (required — the Hytale server runs Java 25, plugins must target Java 25)
- **IntelliJ IDEA** (recommended IDE)
- **Maven** or **Gradle** build tool

> **Gradle KTS compatibility note**: Gradle's embedded Kotlin compiler (as of Gradle 8.14) cannot parse Java 25 version strings. Use Groovy DSL (`.gradle`) instead of Kotlin DSL (`.gradle.kts`) when building with JDK 25. This affects script compilation, not Java source compilation.

### Project Setup

```bash
# Clone official template
git clone https://github.com/HytaleModding/plugin-template.git MyFirstMod
cd MyFirstMod
```

### Maven Configuration

```xml
<repositories>
    <repository>
        <id>hytale-release</id>
        <url>https://maven.hytale.com/release</url>
    </repository>
    <repository>
        <id>hytale-pre-release</id>
        <url>https://maven.hytale.com/pre-release</url>
    </repository>
</repositories>

<dependencies>
    <dependency>
        <groupId>com.hypixel.hytale</groupId>
        <artifactId>Server</artifactId>
        <version>2026.01.28-87d03be09</version>
        <scope>provided</scope>
    </dependency>
</dependencies>
```

### Gradle Configuration

```gradle
repositories {
    mavenCentral()
    maven {
        name = "hytale-release"
        url = uri("https://maven.hytale.com/release")
    }
    maven {
        name = "hytale-pre-release"
        url = uri("https://maven.hytale.com/pre-release")
    }
}

dependencies {
    compileOnly("com.hypixel.hytale:Server:2026.01.28-87d03be09")
}
```

### Java Version Configuration

For Maven (from official plugin template):
```xml
<maven.compiler.source>25</maven.compiler.source>
<maven.compiler.target>25</maven.compiler.target>
```

For Gradle (Groovy DSL — recommended with JDK 25):
```gradle
java {
    sourceCompatibility = JavaVersion.VERSION_25
    targetCompatibility = JavaVersion.VERSION_25
}
```

### Build

```bash
./gradlew shadowJar
```

---

## Plugin Structure

### Basic Plugin (Java)

```java
package com.yourname.yourplugin;

import com.hypixel.hytale.server.core.plugin.JavaPlugin;
import com.hypixel.hytale.server.core.plugin.JavaPluginInit;
import javax.annotation.Nonnull;

public class YourPlugin extends JavaPlugin {

    public YourPlugin(@Nonnull JavaPluginInit init) {
        super(init);
        getLogger().atInfo().log("Plugin loaded!");
    }

    @Override
    protected void setup() {
        // Register commands, events, etc.
        getLogger().atInfo().log("Plugin setup!");
    }

    @Override
    protected void start() {
        getLogger().atInfo().log("Plugin started!");
    }

    @Override
    protected void shutdown() {
        getLogger().atInfo().log("Plugin shutdown!");
    }
}
```

> **Note**: `HytaleLogger` extends Google Flogger's `AbstractLogger`. Use `getLogger().atInfo().log()`,
> `getLogger().atWarning().log()`, etc. instead of `getLogger().info()`.

### Basic Plugin (Kotlin)

```kotlin
import com.hypixel.hytale.server.core.plugin.JavaPlugin
import com.hypixel.hytale.server.core.plugin.JavaPluginInit

class MyPlugin(init: JavaPluginInit) : JavaPlugin(init) {
    override fun setup() { /* registrations */ }
    override fun start() { /* enable */ }
    override fun shutdown() { /* cleanup */ }
}
```

### Plugin Manifest (`manifest.json`)

```json
{
  "Group": "com.yourname",
  "Name": "YourPlugin",
  "Version": "1.0.0",
  "Description": "Plugin description",
  "Authors": [
    { "Name": "YourName", "Email": "", "Url": "" }
  ],
  "Website": "",
  "Main": "com.yourname.yourplugin.YourPlugin",
  "Dependencies": {},
  "OptionalDependencies": {},
  "LoadBefore": {},
  "DisabledByDefault": false,
  "IncludesAssetPack": false,
  "SubPlugins": []
}
```

### Module Dependencies

If your plugin interacts with entities or blocks, declare dependencies:

```json
{
  "Dependencies": {
    "Hytale:EntityModule": "*",
    "Hytale:BlockModule": "*"
  }
}
```

### Plugin Namespacing

All plugin content (commands, items) is namespaced by the plugin's `Name` field from `manifest.json`:

| Content Type | Format | Example |
|-------------|--------|---------|
| Commands | `/pluginname:commandname` | `/flailmod:giveflail` |
| Items | `pluginname:ItemId` | `/spawnitem flailmod:Weapon_Flail_Iron` |

The namespace is derived from the manifest `Name` field (lowercase). For example, a plugin with `"Name": "FlailMod"` uses namespace `flailmod`.

---

## Plugin Lifecycle

| Phase | Java Method | Kotlin Method | Purpose |
|-------|-------------|---------------|---------|
| **Construction** | Constructor(`JavaPluginInit`) | Constructor(`JavaPluginInit`) | Initialize services, set up core state |
| **Setup** | `setup()` | `setup()` | Register commands, events, components |
| **Start** | `start()` | `start()` | Start tasks, load config |
| **Shutdown** | `shutdown()` | `shutdown()` | Save data, clean up resources, stop threads |

---

## Core Server API Packages

```
com.hypixel.hytale.server.core                                  # Core server classes (Message, etc.)
com.hypixel.hytale.server.core.plugin                           # Plugin API (JavaPlugin, JavaPluginInit, PluginBase)
com.hypixel.hytale.server.core.entity                           # Entity system
com.hypixel.hytale.server.core.command.system                   # Command system (AbstractCommand, CommandContext)
com.hypixel.hytale.server.core.command.system.arguments.system  # Argument types (OptionalArg, etc.)
com.hypixel.hytale.server.core.command.system.arguments.types   # ArgTypes (STRING, etc.)
com.hypixel.hytale.event                                        # Event system (EventRegistry, IBaseEvent, IAsyncEvent)
com.hypixel.hytale.server.core.event.events.player              # Player events (PlayerConnectEvent, PlayerChatEvent)
com.hypixel.hytale.server.core.universe                         # Universe, World, PlayerRef
com.hypixel.hytale.server.core.universe.world.storage           # EntityStore, ChunkStore
com.hypixel.hytale.component                                    # ECS (Ref, Store, Component)
com.hypixel.hytale.server.core.entity.entities.player.pages     # CustomUI Pages (CustomUIPage, InteractiveCustomUIPage, BasicCustomUIPage)
com.hypixel.hytale.server.core.entity.entities.player.hud       # CustomUI HUD (CustomUIHud)
com.hypixel.hytale.server.core.ui.builder                       # UI builders (UICommandBuilder, UIEventBuilder, EventData)
com.hypixel.hytale.protocol.packets.interface_                   # UI enums (CustomUIEventBindingType, CustomPageLifetime)
com.hypixel.hytale.codec                                        # Codec system (Codec, KeyedCodec)
com.hypixel.hytale.codec.builder                                # BuilderCodec (for event data deserialization)
com.hypixel.hytale.server.core.inventory                        # Inventory system
com.hypixel.hytale.server.core.permissions                      # Permissions
com.hypixel.hytale.server.core.task                             # Task scheduling
com.hypixel.hytale.server.core.modules                          # Server modules
```

### Available Registries (via JavaPlugin)

| Registry | Type | Purpose |
|----------|------|---------|
| `logger` | `HytaleLogger` | Plugin logging |
| `manifest` | `PluginManifest?` | Plugin metadata |
| `eventRegistry` | `EventRegistry` | Event handling |
| `commandRegistry` | `CommandRegistry` | Command registration |
| `taskRegistry` | `TaskRegistry` | Scheduled tasks |
| `dataDirectory` | `Path` | Persistent storage |
| `entityStoreRegistry` | `ComponentRegistryProxy<EntityStore>` | Entity components |
| `chunkStoreRegistry` | `ComponentRegistryProxy<ChunkStore>` | Chunk components |
| `blockStateRegistry` | `BlockStateRegistry` | Block states |
| `entityRegistry` | `EntityRegistry` | Entity types |
| `assetRegistry` | `AssetRegistry` | Game assets |
| `clientFeatureRegistry` | `ClientFeatureRegistry` | Client features |

---

## Key Modding Systems

### Commands

Commands extend `AbstractCommand` and implement an async `execute(CommandContext)` method:

```java
import com.hypixel.hytale.server.core.Message;
import com.hypixel.hytale.server.core.command.system.AbstractCommand;
import com.hypixel.hytale.server.core.command.system.CommandContext;
import com.hypixel.hytale.server.core.command.system.arguments.system.OptionalArg;
import com.hypixel.hytale.server.core.command.system.arguments.types.ArgTypes;
import java.util.concurrent.CompletableFuture;

public class MyCommand extends AbstractCommand {
    private final OptionalArg<String> nameArg;

    public MyCommand() {
        super("mycommand", "Description of my command");
        nameArg = withOptionalArg("name", "A name argument", ArgTypes.STRING);
    }

    @Override
    protected CompletableFuture<Void> execute(CommandContext ctx) {
        String name = ctx.get(nameArg);
        ctx.sendMessage(Message.raw("Hello, " + (name != null ? name : "world") + "!"));
        return CompletableFuture.completedFuture(null);
    }
}

// Register in setup(): getCommandRegistry().registerCommand(new MyCommand());
```

#### CommandContext Key Methods

| Method | Return Type | Description |
|--------|-------------|-------------|
| `sendMessage(Message)` | `void` | Send a message to the command sender |
| `get(Argument)` | `T` | Get the value of a command argument |
| `senderAsPlayerRef()` | `Ref<EntityStore>` | Get the sender's entity reference (ECS ref, **not** `PlayerRef`) |
| `isPlayer()` | `boolean` | Check if the sender is a player |

> **Important**: `CommandContext` does **not** have a `getPlayerRef()` method. Use `senderAsPlayerRef()` which returns `Ref<EntityStore>` — an ECS reference. To send messages, use `ctx.sendMessage()` directly. To obtain a unique player identifier for session tracking, use `ctx.senderAsPlayerRef().getIndex()`.

#### Command Permissions

Commands auto-generate a permission node by default (e.g. `pluginname.mycommand`). Players without that permission will see "you do not have permission." To make a command available to all players, override `canGeneratePermission()`:

```java
@Override
protected boolean canGeneratePermission() {
    return false;  // No permission required — any player can use this command
}
```

To require a specific permission, use `requirePermission("your.permission.node")` in the constructor. Permission groups can be set via `setPermissionGroups("group1", "group2")` or `setPermissionGroup(GameMode)`.

### Events

Events use `EventRegistry` with a class type and a `Consumer` lambda — no listener interfaces. The correct registration method depends on the event's key type.

#### Unkeyed Events (`IBaseEvent<Void>`)

Events like `PlayerConnectEvent` implement `IBaseEvent<Void>` and use `register()`:

```java
// Register in setup()
getEventRegistry().register(PlayerConnectEvent.class, event -> {
    PlayerRef player = event.getPlayerRef();
    player.sendMessage(Message.raw("Welcome, " + player.getUsername() + "!"));
});
```

#### Keyed Events (`IBaseEvent<K>` / `IAsyncEvent<K>`)

Events like `PlayerChatEvent` implement `IAsyncEvent<String>` (keyed by a String). Using `register()` with keyed events **will not compile** because `register(Class, Consumer)` constrains `EventType extends IBaseEvent<Void>`.

Two options for keyed events:

```java
// Option 1: registerGlobal — receives ALL events regardless of key
getEventRegistry().registerGlobal(PlayerChatEvent.class, event -> {
    String content = event.getContent();
    PlayerRef sender = event.getSender();
    // ...
});

// Option 2: register with key — receives only events matching the key
getEventRegistry().register(PlayerChatEvent.class, "someKey", event -> {
    // Only fires when the event's key matches "someKey"
});
```

> **Common mistake**: `register(PlayerChatEvent.class, handler)` will fail at compile time with `incompatible upper bounds PlayerChatEvent, IBaseEvent<Void>`. Use `registerGlobal()` instead.

#### Async Event Registration

For events implementing `IAsyncEvent<K>`, you can also use `registerAsync()` and `registerAsyncGlobal()` which accept a `Function<CompletableFuture<EventType>, CompletableFuture<EventType>>` instead of a `Consumer`:

```java
getEventRegistry().registerAsyncGlobal(PlayerChatEvent.class, future -> {
    return future.thenApply(event -> {
        // async processing
        return event;
    });
});
```

#### Full EventRegistry Method Summary

| Method | Key Type | Use Case |
|--------|----------|----------|
| `register(Class, Consumer)` | `Void` only | Unkeyed events (PlayerConnectEvent) |
| `register(Class, K, Consumer)` | Any `K` | Keyed events filtered by specific key |
| `registerGlobal(Class, Consumer)` | Any `K` | All keyed events regardless of key |
| `registerAsync(Class, Function)` | `Void` only | Async unkeyed events |
| `registerAsyncGlobal(Class, Function)` | Any `K` | Async all keyed events |
| `registerUnhandled(Class, Consumer)` | Any `K` | Events not handled by other listeners |
| `register(EventRegistration)` | Any `K` | Pre-built registration object |

All methods also have priority overloads: `(EventPriority, ...)` or `(short, ...)`.

#### Key Event Classes

| Event | Key Type | Access Player |
|-------|----------|--------------|
| `PlayerConnectEvent` | `Void` | `event.getPlayerRef()` |
| `PlayerChatEvent` | `String` | `event.getSender()` |

Package: `com.hypixel.hytale.server.core.event.events.player`
Registry: `com.hypixel.hytale.event.EventRegistry`

#### Block Events

Block events allow plugins to respond to block interactions:

| Event | Trigger | Cancellable | Key Methods |
|-------|---------|:-----------:|-------------|
| `UseBlockEvent` | Right-click on block | Yes | `getBlockPos()`, `getPlayer()` |
| `PlaceBlockEvent` | Block placed | Yes | `getBlockPos()`, `getPlayer()` |
| `BreakBlockEvent` | Block destroyed | Yes | `getBlockPos()`, `getPlayer()` |
| `DamageBlockEvent` | Block takes damage | Yes | `getBlockPos()` |

```java
@Override
protected void setup() {
    // Register for right-click on any block
    getEventRegistry().register(UseBlockEvent.class, this::onBlockUse);
}

private void onBlockUse(UseBlockEvent event) {
    if (event.isCancelled()) return;

    BlockPos pos = event.getBlockPos();
    Player player = event.getPlayer();

    // Check block type and respond
    // Open UI, trigger action, etc.
}
```

Package: `com.hypixel.hytale.server.core.event.events.block`

### Entity Component System (ECS)

Hytale uses an ECS architecture for entities. Components are added to entity holders:

```java
// Add components to entity
holder.addComponent(TransformComponent.getComponentType(),
    new TransformComponent(position, rotation));
holder.addComponent(PersistentModel.getComponentType(),
    new PersistentModel(model.toReference()));
holder.addComponent(ModelComponent.getComponentType(),
    new ModelComponent(model));
holder.addComponent(BoundingBox.getComponentType(),
    new BoundingBox(model.getBoundingBox()));
holder.addComponent(NetworkId.getComponentType(),
    new NetworkId(store.getExternalData().takeNextNetworkId()));
holder.addComponent(Interactions.getComponentType(),
    new Interactions());  // Required for interactable entities
```

### Model Assets

```java
ModelAsset modelAsset = ModelAsset.getAssetMap().getAsset("Minecart");
Model model = Model.createScaledModel(modelAsset, 1.0f);
```

### Custom UI System

Hytale's UI system is **server-authoritative** — the server defines UI layout, handles events, and pushes updates. The client renders and sends interactions back. There is inherent latency since all interactions round-trip to the server.

The system has three subsystems accessed through the Player component:

| Subsystem | Class | Purpose |
|-----------|-------|---------|
| **HUD** | `CustomUIHud` | Overlay elements visible during gameplay |
| **Pages** | `CustomUIPage` / `InteractiveCustomUIPage<T>` | Full-screen UI (blocks game interaction) |
| **Windows** | `WindowManager` | Inventory, crafting interfaces |

#### `.ui` File Format

UI layouts are defined in `.ui` files placed at `resources/Common/UI/Custom/`. The format uses a custom curly-brace markup (not HTML or XAML):

```
$Common = "Common.ui";
@MyTex = PatchStyle(TexturePath: "MyBackground.png");

Group {
  LayoutMode: Center;

  Group #MyPanel {
    Background: @MyTex;
    Anchor: (Width: 800, Height: 600);
    LayoutMode: Top;

    Label #MyLabel {
      Style: (FontSize: 32, Alignment: Center);
      Anchor: (Top: 50, Height: 40);
      Text: "Hello World";
      Padding: (Full: 10);
    }

    TextField #MyInput {
      Style: $Common.@DefaultInputFieldStyle;
      Background: $Common.@InputBoxBackground;
      Anchor: (Top: 10, Width: 400, Height: 50);
      Padding: (Full: 10);
    }
  }
}
```

**Syntax rules:**
- `$Variable = "file.ui"` — import another `.ui` file
- `@Variable = PatchStyle(TexturePath: "image.png")` — define a reusable texture
- `#ElementId` — unique identifier for Java code access
- Element types: `Group`, `Label`, `TextField`
- Properties: `Style`, `Background`, `Anchor`, `LayoutMode`, `Text`, `Padding`
- Style tuple: `(FontSize: N, Alignment: Center|Left|Right)`
- Anchor tuple: `(Top: N, Width: N, Height: N)` — positioning and sizing
- LayoutMode: `Center`, `Top`
- Texture paths are relative to the `.ui` file's directory

> **Texture requirements**: All PNG textures must be **8-bit per channel RGBA**. 16-bit/channel PNGs will fail to load and crash the client with "Failed to load CustomUI documents".

#### BasicCustomUIPage (Read-Only)

For display-only pages with no user interaction:

```java
import com.hypixel.hytale.server.core.entity.entities.player.pages.BasicCustomUIPage;
import com.hypixel.hytale.server.core.ui.builder.UICommandBuilder;
import com.hypixel.hytale.server.core.universe.PlayerRef;
import com.hypixel.hytale.protocol.packets.interface_.CustomPageLifetime;

public class InfoPage extends BasicCustomUIPage {
    public InfoPage(PlayerRef playerRef) {
        super(playerRef, CustomPageLifetime.CanDismiss);
    }

    @Override
    public void build(UICommandBuilder cmd) {
        cmd.append("MyPage.ui");
        cmd.set("#Title.Text", "Information");
    }
}
```

#### InteractiveCustomUIPage<T> (With Input)

For pages that receive user input (text fields, buttons). Requires a typed event data class with a `BuilderCodec`:

```java
import com.hypixel.hytale.codec.Codec;
import com.hypixel.hytale.codec.KeyedCodec;
import com.hypixel.hytale.codec.builder.BuilderCodec;
import com.hypixel.hytale.component.Ref;
import com.hypixel.hytale.component.Store;
import com.hypixel.hytale.server.core.Message;
import com.hypixel.hytale.server.core.entity.entities.player.pages.InteractiveCustomUIPage;
import com.hypixel.hytale.server.core.ui.builder.*;
import com.hypixel.hytale.server.core.universe.PlayerRef;
import com.hypixel.hytale.server.core.universe.world.storage.EntityStore;
import com.hypixel.hytale.protocol.packets.interface_.*;

public class MyInputPage extends InteractiveCustomUIPage<MyInputPage.EventData> {

    public MyInputPage(PlayerRef playerRef) {
        super(playerRef, CustomPageLifetime.CanDismiss, EventData.CODEC);
    }

    @Override
    public void build(Ref<EntityStore> ref, UICommandBuilder ui,
                      UIEventBuilder events, Store<EntityStore> store) {
        ui.append("MyInputPage.ui");

        // Bind a button click (static key — literal string, must start uppercase)
        events.addEventBinding(
            CustomUIEventBindingType.Activating,
            "#SaveButton",
            com.hypixel.hytale.server.core.ui.builder.EventData.of("Action", "save")
        );

        // Bind a text field change (reference key — @ prefix reads element value)
        events.addEventBinding(
            CustomUIEventBindingType.ValueChanged,
            "#NameInput",
            com.hypixel.hytale.server.core.ui.builder.EventData.of("@NameInput", "#NameInput.Value"),
            false
        );
    }

    @Override
    public void handleDataEvent(Ref<EntityStore> ref, Store<EntityStore> store,
                                EventData data) {
        if ("save".equals(data.action)) {
            // handle save button
            close();
        } else if (data.nameInput != null) {
            // handle text input change
        }
    }

    // Typed event data with BuilderCodec for deserialization
    public static class EventData {
        public static final BuilderCodec<EventData> CODEC =
            BuilderCodec.builder(EventData.class, EventData::new)
                .addField(new KeyedCodec<>("Action", Codec.STRING),
                    (d, v) -> d.action = v, d -> d.action)
                .addField(new KeyedCodec<>("NameInput", Codec.STRING),
                    (d, v) -> d.nameInput = v, d -> d.nameInput)
                .build();

        public String action;
        public String nameInput;
    }
}
```

**Event binding key conventions:**
- Static keys (e.g., `"Action"`) — literal values sent as-is, **must start with uppercase letter**
- Reference keys (e.g., `"@NameInput"`) — read UI element values at event time, prefix `@` stripped in codec

#### CustomUIHud (Gameplay Overlay)

For persistent HUD elements visible during gameplay:

```java
import com.hypixel.hytale.server.core.entity.entities.player.hud.CustomUIHud;
import com.hypixel.hytale.server.core.ui.builder.UICommandBuilder;
import com.hypixel.hytale.server.core.universe.PlayerRef;

public class MyHud extends CustomUIHud {
    public MyHud(PlayerRef playerRef) {
        super(playerRef);
    }

    @Override
    protected void build(UICommandBuilder cmd) {
        cmd.append("MyHud.ui");
    }

    public void updateStatus(String text) {
        UICommandBuilder cmd = new UICommandBuilder();
        cmd.set("#Status.TextSpans", Message.raw(text));
        update(false, cmd);  // HUD uses update(), not sendUpdate()
    }
}
```

> **Note**: `CustomUIHud` uses `update(boolean, UICommandBuilder)` for updates, while `CustomUIPage` uses `sendUpdate(UICommandBuilder)`. Don't mix them up.

#### Page Lifetime

`CustomPageLifetime` controls how the page can be closed:

| Value | Behavior |
|-------|----------|
| `CantClose` | Player cannot dismiss the page |
| `CanDismiss` | Player can press Escape to close |
| `CanDismissOrCloseThroughInteraction` | Can close via Escape or UI interaction |

#### UI Class Hierarchy

```
CustomUIPage (abstract — 4-param build)
├── BasicCustomUIPage (abstract — 1-param build)
└── InteractiveCustomUIPage<T> (abstract — typed event handling)
    └── ChoiceBasePage (dialog/choice pages)

CustomUIHud (abstract — 1-param build)
```

#### Codec System (for Event Data)

Hytale uses a custom codec system for serializing/deserializing event data:

```java
// BuilderCodec — declarative codec builder
BuilderCodec<MyData> CODEC = BuilderCodec.builder(MyData.class, MyData::new)
    .addField(new KeyedCodec<>("Key", Codec.STRING), setter, getter)
    .build();

// Available codec types: Codec.STRING, Codec.INTEGER, Codec.BOOLEAN,
// Codec.DOUBLE, Codec.FLOAT, Codec.LONG, etc.
```

Package: `com.hypixel.hytale.codec`, `com.hypixel.hytale.codec.builder`

---

## Data Assets (JSON)

### Asset Pack Directory Convention

Bundled asset packs use two root directories with distinct purposes:

| Root | Purpose | Examples |
|------|---------|----------|
| `Server/` | Data and behavior definitions | Item JSON, block JSON, language files, categories |
| `Common/` | Visual/client assets | Icons, textures, models (`.blockymodel`), UI files |

**Items go in `Server/Item/Items/`**, not `Common/Assets/Items/`. The `Common/` directory is only for visual resources like icons and models.

### Texture Format Requirements

All PNG textures in asset packs must be **8-bit per channel RGBA** (standard PNG). The game engine does not support 16-bit/channel PNGs — they will fail to load and crash the client.

| Texture Type | Location | Size Constraint |
|-------------|----------|-----------------|
| Block textures | `Common/BlockTextures/` | Multiples of 32px |
| Item icons | `Common/Icons/ItemsGenerated/` | Typically 64x64 |
| UI textures | `Common/UI/Custom/` | No size constraint |
| Model textures | `Common/Resources/` | Multiples of 32px (64px/unit for characters, 32px/unit for props) |

Convert to correct format with ImageMagick:
```bash
magick input.png -depth 8 -type TrueColorAlpha PNG32:output.png
```

Verify format with `file`:
```bash
$ file texture.png
texture.png: PNG image data, 32 x 32, 8-bit/color RGBA, non-interlaced  # correct
texture.png: PNG image data, 32 x 32, 16-bit/color RGBA, non-interlaced # WRONG — will crash
```

### Custom Item Definition

Item JSON files are placed at `Server/Item/Items/<Item_Id>.json`:

```json
{
  "TranslationProperties": {
    "Name": "My New Item",
    "Description": "My New Item Description"
  },
  "Id": "My_New_Item",
  "Icon": "Icons/ItemsGenerated/My_New_Item.png",
  "Model": "Items/My_New_Item/My_New_Item.blockymodel",
  "Texture": "Items/My_New_Item/My_New_Item_Texture.png",
  "Quality": "Common",
  "MaxStack": 1,
  "Categories": ["Items.Example"]
}
```

**Critical requirements:**
- **`Id` field is REQUIRED** — The `Id` must match the filename (without `.json`). Even though the server logs warn "Unused key(s): Id", the asset loader requires it to recognize and register the item. Items without `Id` are silently ignored.
- **Model/Texture paths must point to bundled assets** — You cannot reference vanilla assets from a plugin JAR. All referenced assets must be included in your JAR's `Common/` directory.
- **Items are namespaced** — Spawned items use the format `pluginname:ItemId` (e.g., `/spawnitem flailmod:Weapon_Flail_Iron`).

### Bundling Custom Items in Plugins (IncludesAssetPack)

Plugins can bundle data assets (items, blocks, etc.) directly inside their JAR by setting `"IncludesAssetPack": true` in `manifest.json`. The server loads assets from both `Server/` and `Common/` resource directories.

#### Directory Structure

```
your-plugin/src/main/resources/
├── manifest.json                                    # IncludesAssetPack: true
├── Server/
│   └── Item/Items/
│       └── Your_Custom_Item.json                    # Item definition (data)
└── Common/
    ├── Icons/ItemsGenerated/
    │   └── Your_Custom_Item.png                     # Item icon (64x64 PNG)
    └── Items/Your_Custom_Item/
        ├── Your_Custom_Item.blockymodel             # 3D model
        └── Your_Custom_Item_Texture.png             # Model texture
```

> **Important**: Item definitions go in `Server/Item/Items/`, **not** `Common/Assets/Items/`. The `Common/` directory is only for visual assets (icons, textures, models).

> **Critical**: The `Model` and `Texture` paths in the item JSON must reference files bundled in your JAR's `Common/` folder. You **cannot** reference vanilla game assets (e.g., `Items/Weapons/Battleaxe/Iron.blockymodel`) — they will be silently ignored and the item won't register.

#### Manifest Requirements

```json
{
  "Dependencies": {
    "Hytale:EntityModule": "*",
    "Hytale:BlockModule": "*"
  },
  "IncludesAssetPack": true
}
```

`BlockModule` is required when bundling asset packs that define items or blocks.

#### Minimal Item JSON (Inventory-Only)

An item that only appears in inventory (no world model) needs only:

```json
{
  "TranslationProperties": {
    "Name": "My Item",
    "Description": "Item description"
  },
  "Id": "My_Item",
  "Icon": "Icons/ItemsGenerated/My_Item.png",
  "Quality": "Common",
  "MaxStack": 64,
  "Categories": ["Items.Example"]
}
```

Test with `/spawnitem My_Item` from the server console (requires permissions).

### Asset Pack Manifest

```json
{
  "Group": "My Group",
  "Name": "Pack Example",
  "Version": "1.0.0",
  "Description": "An Example Asset Pack",
  "Authors": [
    { "Name": "Me", "Email": "", "Url": "" }
  ],
  "Website": "",
  "Dependencies": {},
  "OptionalDependencies": {},
  "LoadBefore": {},
  "DisabledByDefault": false,
  "IncludesAssetPack": false,
  "SubPlugins": []
}
```

---

## Custom Weapons and Animations

Weapons in Hytale combine item definitions, player animations, interactions, and optionally model animations. This system is complex and not fully documented yet.

### Weapon Item Definition

Weapons are items with additional properties. Place in `Server/Item/Items/`:

```json
{
  "TranslationProperties": {
    "Name": "Custom Sword",
    "Description": "A sword with custom animations"
  },
  "Id": "Custom_Sword",
  "Icon": "Icons/ItemsGenerated/Custom_Sword.png",
  "Model": "Resources/Custom_Sword/model.blockymodel",
  "Texture": "Resources/Custom_Sword/texture.png",
  "Quality": "Rare",
  "MaxStack": 1,
  "PlayerAnimationsId": "CustomSword",
  "Categories": ["Items.Weapons"]
}
```

Key weapon-specific properties:

| Property | Type | Purpose |
|----------|------|---------|
| `PlayerAnimationsId` | String | Links to player animation set (swing, idle, block) |
| `Model` | String | Path to `.blockymodel` file |
| `Texture` | String | Path to model texture |

### Player Animation System

The `PlayerAnimationsId` references an `ItemPlayerAnimations` asset that defines how the player character animates when holding/using the weapon.

```java
// ItemPlayerAnimations structure (from decompiled source)
public class ItemPlayerAnimations {
    protected String id;                              // Animation set ID
    protected Map<String, ItemAnimation> animations;  // Animation name → data
    protected WiggleWeights wiggleWeights;            // Physics weights
    protected CameraSettings camera;                  // Camera during animations
    protected ItemPullbackConfig pullbackConfig;      // For bows/ranged
    protected boolean useFirstPersonOverrides;        // First-person overrides
}
```

### Animation Slots

Animations are organized into slots with priority:

| Slot | Purpose | Priority |
|------|---------|----------|
| `Status` | Idle, movement states | Low |
| `Action` | Attacks, item use | High |
| `Face` | Facial expressions | Medium |

Priority hierarchy: Death/Critical → Combat → Interaction → Movement → Idle

### Playing Animations (Plugin Code)

Use `AnimationUtils` to trigger animations programmatically:

```java
import com.hypixel.hytale.server.core.entity.AnimationUtils;
import com.hypixel.hytale.protocol.AnimationSlot;

// Play a weapon-specific animation
AnimationUtils.playAnimation(
    entityRef,                    // Ref<EntityStore>
    AnimationSlot.Action,         // Animation slot
    "CustomSword",                // ItemPlayerAnimationsId
    "swing_heavy",                // Animation name
    componentAccessor             // ComponentAccessor<EntityStore>
);

// Play without item context
AnimationUtils.playAnimation(
    entityRef,
    AnimationSlot.Action,
    "attack_slash",               // Animation ID
    true,                         // sendToSelf
    componentAccessor
);
```

### Weapon Interactions

Weapons use `SimpleInstantInteraction` for attack behavior:

```java
import com.hypixel.hytale.codec.builder.BuilderCodec;
import com.hypixel.hytale.server.core.asset.type.item.config.Item;
import com.hypixel.hytale.server.core.interaction.*;

public class SwordSlashInteraction extends SimpleInstantInteraction {

    public static final BuilderCodec<SwordSlashInteraction> CODEC =
        BuilderCodec.builder(SwordSlashInteraction.class,
            SwordSlashInteraction::new,
            SimpleInstantInteraction.CODEC
        ).build();

    @Override
    protected void firstRun(InteractionType type, InteractionContext ctx,
                           CooldownHandler cooldown) {
        // Get held item
        ItemStack weapon = ctx.getHeldItem();
        if (weapon == null) {
            ctx.getState().state = InteractionState.Failed;
            return;
        }

        // Custom attack logic here
        // - Calculate damage
        // - Find targets in range
        // - Apply damage via Damage class
        // - Trigger effects
    }

    @Override
    public float getAnimationDuration(Item item) {
        return 0.5f; // Animation duration in seconds
    }
}
```

Register the interaction in your plugin:

```java
@Override
protected void setup() {
    // Register custom interaction codec
    getInteractionRegistry().register(
        "custom_sword_slash",
        SwordSlashInteraction.CODEC
    );
}
```

### Model Animations (.blockyanim)

For animating the weapon model itself (glowing, moving parts):

```json
{
  "formatVersion": 1,
  "duration": 30,
  "holdLastKeyframe": false,
  "nodeAnimations": {
    "Blade_Glow": {
      "position": [],
      "orientation": [],
      "shapeStretch": [],
      "shapeVisible": [
        {"time": 0, "delta": true, "interpolationType": "step"},
        {"time": 15, "delta": false, "interpolationType": "step"},
        {"time": 30, "delta": true, "interpolationType": "step"}
      ],
      "shapeUvOffset": []
    },
    "Handle": {
      "position": [],
      "orientation": [
        {"time": 0, "delta": {"x": 0, "y": 0, "z": 0, "w": 1}, "interpolationType": "smooth"},
        {"time": 15, "delta": {"x": 0.05, "y": 0, "z": 0, "w": 0.999}, "interpolationType": "smooth"},
        {"time": 30, "delta": {"x": 0, "y": 0, "z": 0, "w": 1}, "interpolationType": "smooth"}
      ],
      "shapeStretch": [],
      "shapeVisible": [],
      "shapeUvOffset": []
    }
  }
}
```

Animation properties:

| Property | Type | Description |
|----------|------|-------------|
| `duration` | int | Animation length in ticks (20 ticks = 1 second) |
| `holdLastKeyframe` | bool | Keep final state after animation ends |
| `position` | array | Position keyframes (x, y, z delta) |
| `orientation` | array | Rotation keyframes (quaternion x, y, z, w) |
| `shapeVisible` | array | Visibility toggle keyframes |
| `shapeUvOffset` | array | Texture UV animation keyframes |
| `interpolationType` | string | `"smooth"` or `"step"` |

### Wielding Interaction

For weapons with charged attacks or special wielding behavior:

```java
// WieldingInteraction key properties
public class WieldingInteraction {
    InteractionEffects effects;           // Visual/audio effects
    float horizontalSpeedMultiplier;      // Movement speed while wielding
    float runTime;                        // Duration in seconds
    boolean cancelOnItemChange;           // Cancel if item changes
    boolean allowIndefiniteHold;          // Can hold indefinitely
    boolean failOnDamage;                 // Fail if hit
    Map<Float, Integer> chargedNext;      // Charge thresholds → next interaction
    AngledWielding angledWielding;        // Angled hold settings
}
```

### NPC Combat Actions

For NPCs using weapons, the combat system provides attack patterns:

```json
{
  "Attack": "path/to/attack_pattern.json",
  "AttackType": "Primary",
  "ChargeFor": 0.5,
  "AttackPauseRange": [0.2, 0.5],
  "MeleeConeAngle": 90,
  "DamageFriendlies": false
}
```

Attack types: `Primary`, `Secondary`, `Ability1`, `Ability2`, `Ability3`

### Creating Weapon Models

Use **Blockbench** with the Hytale plugin:

1. Install the Hytale Blockbench plugin (character or prop variant)
2. Set texture density to **64px per unit** (required for weapons/tools/characters)
3. Define **attachment points** for hand positioning
4. Export as `.blockymodel`

Texture requirements:
- Dimensions must be multiples of 32px
- Must be 8-bit/channel RGBA PNG
- 64px/unit for weapons, 32px/unit for props

### Vanilla Weapon Asset Structure

Weapon assets are organized across multiple directories:

```
Assets.zip/
├── Server/Item/
│   ├── Items/Weapon/                    # Weapon item definitions
│   │   ├── Sword/
│   │   │   ├── Template_Weapon_Sword.json    # Base template
│   │   │   └── Weapon_Sword_Iron.json        # Specific weapon
│   │   ├── Axe/
│   │   ├── Battleaxe/
│   │   ├── Mace/
│   │   ├── Daggers/
│   │   └── Crossbow/
│   ├── RootInteractions/Weapons/        # Entry points (Primary/Secondary/Ability)
│   │   └── Sword/
│   │       ├── Root_Weapon_Sword_Primary.json
│   │       ├── Root_Weapon_Sword_Secondary_Guard.json
│   │       └── Root_Weapon_Sword_Signature_Vortexstrike.json
│   └── Interactions/Weapons/            # Attack logic chains
│       └── Axe/Attacks/Swing_Left/
│           ├── Axe_Swing_Left.json           # Animation + hit detection
│           └── Axe_Swing_Left_Damage.json    # Damage application
└── Common/Characters/Animations/Items/  # Player character animations
    ├── One_Handed/
    └── Dual_Handed/
        └── Longsword/Attacks/Swing_Left/
            └── Swing_Left.blockyanim         # Body part keyframes
```

### Weapon Template Inheritance

Weapons use a `Parent` inheritance pattern to reduce duplication:

**Template (base configuration):**
```json
// Template_Weapon_Sword.json
{
  "TranslationProperties": { "Name": "server.items.Template_Weapon_Sword.name" },
  "PlayerAnimationsId": "Sword",
  "Reticle": "DefaultMelee",
  "Categories": ["Items.Weapons"],
  "Interactions": {
    "Primary": "Root_Weapon_Sword_Primary",
    "Secondary": "Root_Weapon_Sword_Secondary_Guard",
    "Ability1": "Root_Weapon_Sword_Signature_Vortexstrike"
  },
  "Tags": { "Type": ["Weapon"], "Family": ["Sword"] },
  "Weapon": {
    "EntityStatsToClear": ["SignatureEnergy"],
    "StatModifiers": {
      "SignatureEnergy": [{ "Amount": 20, "CalculationType": "Additive" }]
    }
  },
  "ItemSoundSetId": "ISS_Weapons_Blade_Large",
  "MaxDurability": 80,
  "DurabilityLossOnHit": 0.21
}
```

**Specific weapon (inherits and overrides):**
```json
// Weapon_Sword_Iron.json
{
  "Parent": "Template_Weapon_Sword",
  "TranslationProperties": { "Name": "server.items.Weapon_Sword_Iron.name" },
  "Model": "Items/Weapons/Sword/Iron.blockymodel",
  "Texture": "Items/Weapons/Sword/Iron_Texture.png",
  "Quality": "Uncommon",
  "Icon": "Icons/ItemsGenerated/Weapon_Sword_Iron.png",
  "ItemLevel": 20,
  "MaxDurability": 120,
  "InteractionVars": {
    "Swing_Left_Damage": {
      "Interactions": [{
        "Parent": "Weapon_Sword_Primary_Swing_Left_Damage",
        "DamageCalculator": { "BaseDamage": { "Physical": 9 } },
        "DamageEffects": {
          "WorldSoundEventId": "SFX_Sword_T2_Impact",
          "LocalSoundEventId": "SFX_Sword_T2_Impact"
        }
      }]
    },
    "Swing_Right_Damage": { ... },
    "Swing_Down_Damage": { ... },
    "Thrust_Damage": { ... }
  }
}
```

### Interaction Chain Architecture

Weapon attacks use a chain of interactions:

```
Item.Interactions.Primary
    → RootInteraction (cooldown, click handling)
        → Interaction chain (animation, timing)
            → Selector (hit detection)
                → Damage interaction
```

**1. Root Interaction (entry point):**
```json
// Root_Weapon_Sword_Primary.json
{
  "RequireNewClick": true,
  "ClickQueuingTimeout": 0.2,
  "Cooldown": { "Cooldown": 0.25 },
  "Interactions": ["Weapon_Sword_Primary"]
}
```

**2. Attack Interaction (animation + hit detection):**
```json
// Axe_Swing_Left.json
{
  "Type": "Simple",
  "Effects": { "ItemAnimationId": "SwingLeft" },
  "RunTime": 0.244,
  "Next": {
    "Type": "Parallel",
    "Interactions": [
      {
        "Interactions": [{
          "Type": "Selector",
          "RunTime": 0.133,
          "Selector": {
            "Id": "Horizontal",
            "Direction": "ToLeft",
            "TestLineOfSight": true,
            "ExtendTop": 0.5,
            "ExtendBottom": 0.5,
            "StartDistance": 0.1,
            "EndDistance": 2.5,
            "Length": 60,
            "YawStartOffset": -30
          },
          "HitBlock": { "Interactions": ["Block_Break_Adventure"] },
          "HitEntity": {
            "Interactions": [{
              "Type": "Replace",
              "DefaultValue": { "Interactions": ["Axe_Swing_Left_Damage"] },
              "Var": "Axe_Swing_Left_Damage"
            }]
          }
        }]
      }
    ]
  }
}
```

**3. Damage Interaction:**
```json
// Axe_Swing_Left_Damage.json
{
  "Type": "DamageEntity",
  "DamageCalculator": {
    "BaseDamage": { "Physical": 5 }
  },
  "DamageEffects": {
    "Knockback": {
      "Force": 0.5,
      "RelativeX": -5,
      "RelativeZ": -5,
      "VelocityY": 5
    },
    "WorldSoundEventId": "SFX_Sword_T2_Impact",
    "LocalSoundEventId": "SFX_Sword_T2_Impact",
    "WorldParticles": [{ "SystemId": "Impact_Blade_01" }]
  }
}
```

### Interaction Types

| Type | Purpose | Key Properties |
|------|---------|----------------|
| `Simple` | Basic timed interaction | `RunTime`, `Effects`, `Next` |
| `Parallel` | Run multiple interactions simultaneously | `Interactions[]` |
| `Selector` | Hit detection (sweep, cone, AOE) | `Selector`, `HitEntity`, `HitBlock` |
| `DamageEntity` | Apply damage + effects | `DamageCalculator`, `DamageEffects` |
| `Replace` | Variable substitution | `Var`, `DefaultValue` |
| `Condition` | Conditional branching | `Condition`, `True`, `False` |
| `Projectile` | Spawn projectile entity | `Config`, `RunTime`, `Effects` |
| `ApplyEffect` | Apply status effect to target | `EffectId` |
| `Explode` | AOE explosion with damage/knockback | `Parent`, `Config` (EntityDamage, BlockDamageRadius, Knockback) |
| `PlaceBlock` | Place block at location | `BlockTypeToPlace`, `RemoveItemInHand` |
| `ChangeStat` | Modify entity stats | `StatModifiers`, `Behaviour` |
| `RemoveEntity` | Despawn entity | `Entity` (`"User"` for projectile) |
| `Charging` | Bow/charge weapon with release thresholds | `Next` (charge level map), `AllowIndefiniteHold` |
| `ModifyInventory` | Consume/add items, with success/fail paths | `ItemToRemove`, `Next`, `Failed` |

### Charging with Ammo Consumption

For bows that consume arrows from inventory, place `ModifyInventory` inside each charge level's release:

```json
{
  "Type": "Charging",
  "AllowIndefiniteHold": true,
  "Effects": { "ItemAnimationId": "ShootCharging" },
  "Next": {
    "0.3": {
      "Type": "ModifyInventory",
      "ItemToRemove": { "Id": "Weapon_Arrow_Crude", "Quantity": 1 },
      "Next": {
        "Type": "Serial",
        "Interactions": [
          { "Type": "Simple", "Effects": { "ItemAnimationId": "ShootChargedRelease" } },
          { "Type": "Projectile", "Config": "Projectile_Config_Arrow" }
        ]
      },
      "Failed": { "Type": "Simple", "RunTime": 0.3, "Effects": { "WorldSoundEventId": "SFX_Bow_No_Ammo" } }
    },
    "1.0": { "...same pattern with stronger projectile..." }
  }
}
```

**Key points:**
- `ModifyInventory` must be INSIDE the charge level's `Next`, not before the `Charging` interaction
- Item IDs have **no namespace prefix** — use `"Weapon_Arrow_Fire"`, not `"pluginname:Weapon_Arrow_Fire"`
- Chain `Failed` paths to support multiple ammo types (try Fire → Poison → Explosive → Crude → no ammo)

### Explosions

Use `"Type": "Explode"` with `Parent: "Explode_Generic"` to inherit vanilla knockback and block damage specs:

```json
{
  "Type": "Explode",
  "Parent": "Explode_Generic",
  "RunTime": 0.1,
  "Config": {
    "DamageEntities": true,
    "DamageBlocks": true,
    "BlockDamageRadius": 2,
    "EntityDamage": 15.0,
    "EntityDamageRadius": 3
  },
  "Effects": {
    "WorldSoundEventId": "SFX_Goblin_Lobber_Bomb_Death",
    "LocalSoundEventId": "SFX_Goblin_Lobber_Bomb_Death",
    "Particles": [{ "SystemId": "Explosion_Medium", "TargetEntityPart": "Entity" }]
  }
}
```

**Key points:**
- Use `"Type": "Explode"`, not `"Type": "Selector"` with `AOECircle` — Selector is for melee hit detection
- **Wall explosions:** Set `Bounciness: 1.0` and handle `ProjectileBounce` with explosion — there's no wall-hit event, but bounce triggers on any surface contact
- `ProjectileMiss` only fires when projectile comes to rest (ground), not on wall contact

### Hit Detection (Selector)

The `Selector` type defines hitbox shapes for attacks:

```json
{
  "Type": "Selector",
  "Selector": {
    "Id": "Horizontal",           // Selector type
    "Direction": "ToLeft",        // Swing direction
    "TestLineOfSight": true,      // Require LOS to target
    "ExtendTop": 0.5,             // Hitbox vertical extension up
    "ExtendBottom": 0.5,          // Hitbox vertical extension down
    "StartDistance": 0.1,         // Min range (avoid self-hit)
    "EndDistance": 2.5,           // Max attack range
    "Length": 60,                 // Arc width in degrees
    "YawStartOffset": -30         // Arc offset from facing direction
  }
}
```

Selector types:
- `Horizontal` — horizontal arc sweep (melee)
- `Cone` — cone-shaped area
- `Line` — straight line (projectiles)
- `AOECircle` — circular area of effect (explosions), uses `Range` property

### Character Animation Format (.blockyanim)

Player character animations define keyframes for body parts:

```json
// Swing_Left.blockyanim
{
  "duration": 25,
  "holdLastKeyframe": true,
  "nodeAnimations": {
    "R-Arm": {
      "position": [
        { "time": 0, "delta": { "x": 0, "y": 0, "z": 0 }, "interpolationType": "smooth" },
        { "time": 15, "delta": { "x": 0.18, "y": 0.09, "z": 4.24 }, "interpolationType": "smooth" }
      ],
      "orientation": [
        { "time": 0, "delta": { "x": 0.47, "y": 0.11, "z": 0.64, "w": -0.60 }, "interpolationType": "smooth" },
        { "time": 15, "delta": { "x": -0.45, "y": 0.61, "z": -0.06, "w": 0.65 }, "interpolationType": "smooth" }
      ],
      "shapeStretch": [],
      "shapeVisible": [],
      "shapeUvOffset": []
    },
    "R-Forearm": { ... },
    "R-Hand": { ... },
    "Spine": { ... }
  }
}
```

Common body part node names:
- Arms: `R-Arm`, `R-Forearm`, `R-Hand`, `L-Arm`, `L-Forearm`, `L-Hand`
- Torso: `Spine`, `Chest`, `Neck`, `Head`
- Legs: `R-Thigh`, `R-Shin`, `R-Foot`, `L-Thigh`, `L-Shin`, `L-Foot`

Orientation uses quaternions (x, y, z, w). Use Blockbench to author these visually.

### Extracting Vanilla Assets

```bash
# List all weapon files
unzip -l server/Assets.zip | grep -iE "Weapon"

# Extract sword definitions
unzip server/Assets.zip "Server/Item/Items/Weapon/Sword/*" -d ./vanilla-ref/

# Extract interaction chains
unzip server/Assets.zip "Server/Item/RootInteractions/Weapons/Sword/*" -d ./vanilla-ref/
unzip server/Assets.zip "Server/Item/Interactions/Weapons/*" -d ./vanilla-ref/

# Extract character animations
unzip server/Assets.zip "Common/Characters/Animations/Items/*" -d ./vanilla-ref/
```

> **Note**: Add `vanilla-ref/` to `.gitignore` — these are copyrighted game assets.

### Key Classes Reference

| Class | Package | Purpose |
|-------|---------|---------|
| `ItemPlayerAnimations` | `server.core.asset.type.itemanimation.config` | Animation set definition |
| `AnimationUtils` | `server.core.entity` | Play animations on entities |
| `SimpleInstantInteraction` | `server.core.interaction` | Base for instant item actions |
| `WieldingInteraction` | `protocol` | Held/charged weapon behavior |
| `InteractionEffects` | `server.core.modules.interaction.interaction.config` | Animation/effect linkage |
| `Damage` | `server.core.modules.entity.damage` | Damage application |
| `CombatActionEvaluator` | `builtin.npccombatactionevaluator.evaluator` | NPC combat AI |

### Limitations & Evolving Areas

- **Player animation assets**: The `PlayerAnimationsId` maps to animation sets, but the registration mechanism for new animation IDs is unclear
- **Signature abilities**: How `SignatureEnergy` accumulates and triggers special attacks needs more documentation
- **Animation blending**: Cross-fade and layering between animation states
- **Damage modifiers**: How armor, resistances, and critical hits modify base damage
- **Custom selectors**: Whether new selector types can be defined beyond `Horizontal`, `Cone`, `Line`, `AOECircle`

> **Tip**: Extract vanilla assets from `Assets.zip` to understand patterns. The interaction chain system is JSON-driven and can be extended via asset packs.

---

## Projectiles & Status Effects

### Projectile System Architecture

Projectiles are spawned by interactions (bows, staves, thrown items) and have their own physics, hit detection, and impact behaviors.

```
Item → Interactions.Primary → Projectile Type → ProjectileConfig
                                                    ├→ Physics (gravity, velocity)
                                                    ├→ Model/Texture
                                                    └→ Interactions.ProjectileHit/ProjectileMiss
                                                        └→ ApplyEffect, DamageEntity, Explode, etc.
```

### Asset Locations

| Asset Type | Location | Purpose |
|------------|----------|---------|
| Projectile configs | `Server/ProjectileConfigs/` | Physics, interactions |
| Status effects | `Server/Entity/Effects/Status/` | Burn, Poison, Slow, Stun |
| Arrow items | `Server/Item/Items/Weapon/Arrow/` | Arrow item definitions |
| Projectile models | `Common/Items/Projectiles/` | 3D models and textures |

### Projectile Config Example

```json
// Server/ProjectileConfigs/Weapons/Arrows/Projectile_Config_Arrow_Crossbow.json
{
  "Parent": "Projectile_Config_Arrow_Base",
  "LaunchForce": 40,
  "Physics": {
    "Type": "Standard",
    "Gravity": 10,
    "TerminalVelocityAir": 50,
    "RotationMode": "VelocityDamped",
    "Bounciness": 0.0,
    "SticksVertically": true
  },
  "Interactions": {
    "ProjectileHit": {
      "Interactions": [
        "Weapon_Crossbow_Primary_Combo_Condition",
        { "Type": "Replace", "Var": "Standard_Projectile_Impact", ... },
        "Common_Projectile_Despawn"
      ]
    },
    "ProjectileMiss": {
      "Interactions": [ "Common_Projectile_Miss", "Common_Projectile_Despawn" ]
    }
  }
}
```

### Applying Effects on Projectile Hit

Use `ApplyEffect` in `ProjectileHit.Interactions` to apply status effects:

```json
// Example: Ice projectile that freezes target
{
  "Interactions": {
    "ProjectileHit": {
      "Interactions": [
        { "Type": "ApplyEffect", "EffectId": "Freeze" },
        { "Type": "DamageEntity", "DamageCalculator": { "BaseDamage": { "Physical": 10 } } },
        { "Type": "RemoveEntity", "Entity": "User" }
      ]
    }
  }
}
```

### Status Effect Definition

```json
// Server/Entity/Effects/Status/Burn.json
{
  "ApplicationEffects": {
    "EntityBottomTint": "#100600",
    "EntityTopTint": "#cf2302",
    "ScreenEffect": "ScreenEffects/Fire.png",
    "Particles": [{ "SystemId": "Effect_Fire" }],
    "ModelVFXId": "Burn"
  },
  "DamageCalculatorCooldown": 1,
  "DamageCalculator": { "BaseDamage": { "Fire": 5 } },
  "Duration": 3,
  "Debuff": true,
  "StatusEffectIcon": "UI/StatusEffects/Burn.png"
}
```

### AOE Explosion Example

```json
// Explosion with AOE damage and knockback
{
  "Type": "Selector",
  "Selector": { "Id": "AOECircle", "Range": 4 },
  "Effects": {
    "WorldParticles": [{ "SystemId": "Explosion_Medium" }],
    "WorldSoundEventId": "SFX_Goblin_Lobber_Bomb_Death"
  },
  "HitEntity": {
    "Interactions": [{
      "Parent": "DamageEntityParent",
      "DamageCalculator": { "BaseDamage": { "Physical": 20 } },
      "DamageEffects": {
        "Knockback": { "Type": "Point", "Force": 20, "VelocityType": "Set" }
      }
    }]
  }
}
```

### Arrow Item Structure

Arrows have `Tags.Family: ["Arrow"]` and reference a `ProjectileId`:

```json
// Server/Item/Items/Weapon/Arrow/Weapon_Arrow_Iron.json
{
  "Id": "Weapon_Arrow_Iron",
  "Tags": { "Type": ["Weapon"], "Family": ["Arrow"] },
  "MaxStack": 40,
  "InteractionVars": {
    "Knife_Throw_Charged_Projectile": {
      "Interactions": [{
        "Parent": "Knife_Throw_Charged_Projectile",
        "ProjectileId": "Arrow_HalfCharge"
      }]
    }
  }
}
```

### Vanilla Status Effects

| Effect | Duration | Damage | Notes |
|--------|----------|--------|-------|
| `Burn` | 3s | 5 Fire/tick | Overwrite on reapply |
| `Poison` | 16s | 10 Poison/5s | Extends on reapply |
| `Slow` | varies | — | Movement debuff |
| `Freeze` | varies | — | Movement + abilities disabled |
| `Stun` | varies | — | Full incapacitation |

---

## Documentation Coverage Map

The community documentation covers these major areas (via hytalemodding.dev):

### Guides Available
- **Java Basics** (13 modules): Variables, OOP, collections, exceptions, inheritance
- **Server Plugins** (30+ guides): Commands, events, sounds, chat, blocks, items, entities, NPCs, UI, worldgen
- **Entity Component System**: Fundamentals, theory, systems, example plugins
- **Gameplay Systems**: Prefabs

### Official Documentation (mirrored)
- NPC Documentation
- World Generation (biomes, generators, block masks, curves, density, patterns, props, scanners)
- Entities Reference
- Events Reference
- Sounds Reference

### Areas Still Evolving
- Visual scripting (planned, not yet released)
- Full server source code (planned ~March 2026)
- Node-graph editors for asset types
- WorldGen V2 public documentation (in progress)
- **Weapon/combat system**: `ItemPlayerAnimations` format, damage formulas
- **Player animation assets**: How to create new animation sets for items
- **Projectile system**: Bow/crossbow arrow selection override (vanilla hardcodes `Weapon_Arrow_Crude`)

---

## World Generation

WorldGen V2 is a major system with APIs that:
- Automatically work with the node editor
- Plug into other vanilla and modded features
- Are automatically multithreaded
- Provide full read access to surrounding world context

Official worldgen docs cover: Assignments, Block Masks, Curves, Density, Directionality, Material Providers, Patterns, Positions Providers, Props, Scanners, Vector Providers.

---

## Local Server Setup

### Downloading the Server

The Hytale dedicated server is distributed via the official `hytale-downloader` CLI tool:

1. Download `hytale-downloader-linux-amd64` (or platform equivalent) from https://support.hytale.com/hc/en-us/articles/45326769420827-Hytale-Server-Manual
2. Run it — it will prompt for OAuth2 device authorization via `https://accounts.hytale.com/device`
3. Once authorized, it downloads the server (~1.4 GB zip) and validates the checksum
4. Extract the zip — it contains `Server/HytaleServer.jar` and `Assets.zip`

### Running the Server

**Requires Java 25.** Verify with `java --version` (should show `openjdk 25.0.x`). Install via [Adoptium](https://adoptium.net/) if needed.

```bash
java -Xms4G -Xmx8G -jar Server/HytaleServer.jar --assets ./Assets.zip --bind 0.0.0.0:5520
```

The server uses **UDP port 5520** (QUIC protocol, not TCP). An AOT cache (`-XX:AOTCache=HytaleServer.aot`) can be used on subsequent runs for faster startups, but requires the JVM to have generated it on a prior run — omit it on first launch.

### Server Authentication (OAuth 2.0)

Before players can connect, the server must be authenticated:

1. In the server console, run: `/auth login device`
2. Visit the URL it provides and enter the device code
3. Authorize via your Hytale account in the browser
4. The server will receive a session token — players can now connect

#### Persisting Authentication

By default, authentication is lost on server restart. To persist it, run after authenticating:

```
/auth persistence Encrypted
```

This stores the token encrypted on disk, tied to the machine's `/etc/machine-id`. The server will automatically re-authenticate on subsequent starts. The token expires after **30 days** of inactivity.

> **Linux note**: If you get a "no encryption key" error, ensure `/etc/machine-id` exists:
> `cat /etc/machine-id`

Authentication details:
- Uses **OAuth 2.0** (RFC 6749, RFC 8628 device code flow)
- Tokens authorize server access to official API endpoints
- See: https://support.hytale.com/hc/en-us/articles/45328341414043-Server-Provider-Authentication-Guide

### Installing Mods

Drop `.jar` (plugins) or `.zip` (asset packs) into the `Server/mods/` directory and restart:

```
server/Server/                  # Note: mods go in Server/mods/, not server/mods/
├── HytaleServer.jar
├── Assets.zip
├── mods/
│   ├── YourPlugin.jar         # Java plugin
│   └── YourAssetPack.zip      # Asset pack
└── ...
```

> **Path gotcha**: The mods folder is `server/Server/mods/`, not `server/mods/`. This is a common mistake that causes plugins to not load.

### Connecting from the Client

In the Hytale game client, use **Direct Connect** and enter `localhost:5520` (or your server's IP).

---

## Community & Distribution

| Platform | URL | Purpose |
|----------|-----|---------|
| CurseForge | https://www.curseforge.com | Mod distribution |
| Hytale Hub | https://hytalehub.com | Forums, community |
| Discord | Official Hytale Discord | Direct dev access |
| GitHub | https://github.com/HytaleModding | Open-source tools |

---

## Key Differences from Minecraft Modding

| Aspect | Minecraft | Hytale |
|--------|-----------|--------|
| Mod location | Client + Server | **Server-only** (server-first architecture) |
| Client mods | Required for most mods | Not needed; server pushes content |
| Data assets | Limited data packs | **Broad JSON-driven system** (blocks, items, NPCs, worldgen) |
| Mod format | `.jar` (Forge/Fabric) | `.jar` (plugins) or `.zip` (packs) in `mods/` |
| Build system | ForgeGradle/Loom | Standard **Maven/Gradle** against `maven.hytale.com` |
| Language | Java | Java (JVM languages supported) |
| IDE | Any | **IntelliJ IDEA** recommended |
| Visual scripting | None (vanilla) | **Planned** node-graph editors |
| Asset editor | None (vanilla) | **Built-in** Hytale Asset Editor |
| Entity system | Custom | **ECS** (Entity Component System) |

---

## Updating This Document

When APIs change or new documentation is released:

1. **Check official sources**:
   - https://hytale.com/news (blog posts on modding updates)
   - https://hytalemodding.dev/en/docs (community-maintained, frequently updated)
   - https://britakee-studios.gitbook.io/hytale-modding-documentation (tested tutorials)

2. **Use Context7 to fetch latest**:
   ```
   resolve-library-id: "hytale" → get latest library IDs
   query-docs: libraryId="/websites/hytalemodding_dev_en", query="<topic>"
   query-docs: libraryId="/websites/britakee-studios_gitbook_io_hytale-modding-documentation", query="<topic>"
   query-docs: libraryId="/websites/rentry_co_gykiza2m", query="<topic>"
   ```

3. **Check for version changes**: The Maven artifact version (`2026.01.28-87d03be09`) updates with each game patch. Check `https://maven.hytale.com/release` for latest.

4. **Decompile the Server JAR as source of truth**: Community docs can be outdated. Use `javap` against the actual Server JAR to verify API signatures:
   ```bash
   JAR=".gradle-home/caches/modules-2/files-2.1/com.hypixel.hytale/Server/<version>/<hash>/Server-<version>.jar"
   # List all classes
   jar tf "$JAR" | grep '\.class$'
   # Decompile a specific class
   javap -p -classpath "$JAR" com.hypixel.hytale.server.core.plugin.JavaPlugin
   ```

5. **Monitor milestones**:
   - Full server source code release (~March 2026)
   - Visual scripting / node editor release
   - WorldGen V2 public documentation completion
