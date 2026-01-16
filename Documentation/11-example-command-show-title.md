# Example Command to Show Title

**Source:** [Official Hytale Resources](https://hytale.com/)

**Last Modified:** Friday, January 9, 2026 at 12:02 PM

---

## Overview

This tutorial demonstrates how to create custom commands that display event titles using Hytale's plugin API. Titles are a powerful way to provide visual feedback to players during events, achievements, or system announcements.

---

## Example Result

When executed, the command displays a high-impact title and subtitle directly on the player's screen.

---

## Command Implementation

### Complete Code Example:

```java
public class ModdedCommand extends CommandBase {
    
    public ModdedCommand() {
        // Name, Description, Requires OP
        super("moddedcommand", "A test command", false);
    }
    
    @Override
    protected void executeSync(@Nonnull CommandContext commandContext) {
        // Ensure the sender is a player before proceeding
        commandContext.senderAsPlayer().getWorld().execute(() -> {
            EventTitleUtil.showEventTitleToPlayer(
                commandContext.senderAsPlayer().getReference(),
                Message.raw("It's modded!"), // Main Title
                Message.raw("Yeppers"),      // Subtitle
                true,                        // Is Major (Shows gold bars/animation)
                commandContext.senderAsPlayer().getWorld().getEntityStore().getStore()
            );
        });
    }
}

```

---

## Code Breakdown

### 1. Command Constructor

The `super` call defines how players interact with the command in the chat console.

* **`"moddedcommand"`**: The trigger (e.g., `/moddedcommand`).
* **`"A test command"`**: The tooltip shown in the `/help` menu.
* **`false`**: Setting this to `true` would restrict the command to server operators.

### 2. The Execute Method

* **`executeSync`**: This method runs on the main server thread, making it safe to access world data.
* **`CommandContext`**: Contains data about the `sender`, the `world`, and any `arguments` passed.

### 3. EventTitleUtil Parameters

The `EventTitleUtil` class handles the packet logic for you:

* **Player Reference**: Obtained via `senderAsPlayer().getReference()`.
* **Main/Subtitle**: Wrapped in `Message.raw()` to handle plain text.
* **Is Major**: If set to `true`, the title displays with "major" styling (often including decorative bars and a more pronounced animation).
* **Entity Store**: Required for the server to track the display lifecycle relative to the player entity.

---

## Registering the Command

To activate your command, register it within the `onEnable()` method of your main plugin class.

```java
public class ExamplePlugin extends JavaPlugin {
    
    @Override
    public void onEnable() {
        // Register the command with the server's command manager
        getServer().getCommandManager().registerCommand(new ModdedCommand());
        
        getLogger().info("Modded Title Plugin has been enabled!");
    }
}

```

---

## Customization & Styling

### Using Colors

You can use standard Hytale color codes (e.g., `&6` for Gold, `&b` for Aqua) within your messages:

```java
Message.raw("&6CONGRATULATIONS!")
Message.raw("&bYou found a secret area.")

```

### Formatting the Duration

While the basic utility uses default timings, you can also specify fade-in, stay, and fade-out durations using advanced overloads of the `EventTitleUtil` (if available in your API version).

---

## Best Practices

* **Thread Safety**: Always wrap world-altering or visual logic in a `world.execute()` block to ensure it runs on the correct synchronization cycle.
* **Player Validation**: Before calling `senderAsPlayer()`, verify the sender is actually a player (and not the console) to avoid `NullPointerExceptions`.
* **Clarity**: Avoid using titles for frequent spam; they are best reserved for significant milestones to maintain their visual impact.

---

## Getting Help

**Official Channels:**

* **Discord:** [Official Hytale Discord](https://discord.gg/hytale)
* **Blog:** [Hytale News](https://hytale.com/news)