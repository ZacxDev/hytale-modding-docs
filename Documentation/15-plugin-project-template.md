# Plugin Project Template

A minimal, production-ready template for creating Hytale plugins with modern build tools and automated testing.

**GitHub Repository:** [realBritakee/hytale-template-plugin](https://github.com/realBritakee/hytale-template-plugin)

---

## Overview

The Hytale Plugin Template is a ready-to-use project structure that builds immediately without any modifications. It includes modern development tools, automated server testing, and CI/CD workflows to help you start developing plugins quickly.

### Key Features

✅ **Builds Out-of-the-Box** - Clone and run `./gradlew shadowJar` immediately  
✅ **Modern Build System** - Gradle 9.2.0 with Kotlin DSL  
✅ **Java 25 Support** - Latest Java features  
✅ **Automated Server Testing** - Custom Gradle plugin for one-command testing  
✅ **ShadowJar Integration** - Automatic dependency bundling  
✅ **CI/CD Ready** - GitHub Actions workflow included  
✅ **Minimal Structure** - Only essential files, write your own code  

---

## Quick Start

### Prerequisites

- **Java 25 JDK** - [Download here](https://www.oracle.com/java/technologies/downloads/)
- **IntelliJ IDEA** - [Download here](https://www.jetbrains.com/idea/download/) (Community Edition works)
- **Git** - [Download here](https://git-scm.com/)

### 1. Clone the Template

```bash
git clone https://github.com/realBritakee/hytale-template-plugin.git
cd hytale-template-plugin
```

### 2. Build Immediately (No Changes Needed!)

The template works out-of-the-box:

```bash
# Windows
gradlew.bat shadowJar

# Linux/Mac
./gradlew shadowJar
```

Your plugin JAR will be in: `build/libs/TemplatePlugin-1.0.0.jar`

### 3. Customize Your Plugin

When ready to customize, edit these files:

**`settings.gradle.kts`:**

```kotlin
rootProject.name = "your-plugin-name"
```

**`gradle.properties`:**

```properties
pluginGroup=com.yourname
pluginVersion=1.0.0
pluginDescription=Your plugin description
```

**`src/main/resources/manifest.json`:**

```json
{
  "Group": "YourName",
  "Name": "YourPluginName",
  "Main": "com.yourname.yourplugin.YourPlugin"
}
```

**Rename the main plugin class:**

- Rename `src/main/java/com/example/templateplugin/TemplatePlugin.java`
- Update package name to match your `pluginGroup`

### 4. Implement Your Plugin

Write your plugin code in `src/main/java/`:

- Commands
- Event listeners
- Services
- Storage
- Utilities

See [Advanced Plugin Patterns](12-advanced-plugin-patterns.md) and [Common Plugin Features](14-common-plugin-features.md) for examples.

---

## Project Structure

```
hytale-template-plugin/
├── .github/workflows/
│   └── build.yml                    # CI/CD workflow
├── buildSrc/
│   ├── build.gradle.kts             # Custom plugin configuration
│   └── src/main/kotlin/
│       └── RunHytalePlugin.kt       # Automated server testing
├── gradle/wrapper/
│   ├── gradle-wrapper.jar           # Gradle wrapper binary
│   └── gradle-wrapper.properties    # Gradle 9.2.0 configuration
├── src/main/
│   ├── java/com/example/templateplugin/
│   │   └── TemplatePlugin.java      # Minimal main class (example)
│   └── resources/
│       ├── manifest.json            # Plugin metadata
│       └── config.json              # Example configuration
├── .gitignore                       # Git ignore rules
├── build.gradle.kts                 # Build configuration
├── gradle.properties                # Project properties
├── gradlew                          # Gradle wrapper (Unix)
├── gradlew.bat                      # Gradle wrapper (Windows)
├── settings.gradle.kts              # Project settings
├── LICENSE                          # MIT License
└── README.md                        # Documentation
```

**Note:** This is a minimal template. Create your own folder structure as needed:

- `commands/` - For command implementations
- `listeners/` - For event listeners
- `services/` - For business logic
- `storage/` - For data persistence
- `utils/` - For utility classes
- `config/` - For configuration management

---

## Automated Server Testing

The template includes a custom Gradle plugin for automated server testing. This feature is documented in detail in [Gradle Automation & Testing](13-gradle-automation-testing.md).

### Quick Usage

**Configure in `build.gradle.kts`:**

```kotlin
runHytale {
    jarUrl = "url to hytale server jar"
}
```

**Run the server:**

```bash
# Windows
gradlew.bat runServer

# Linux/Mac
./gradlew runServer
```

### What It Does

1. ✅ Downloads the Hytale server JAR (cached for future runs)
2. ✅ Compiles your plugin with `shadowJar`
3. ✅ Copies the plugin to `run/plugins/`
4. ✅ Starts the server with interactive console
5. ✅ Supports debug mode: `./gradlew runServer -Pdebug`

### Features

- **Smart Caching** - Downloads server JAR once, reuses forever
- **Auto-Deploy** - Builds and deploys your plugin automatically
- **Interactive Console** - Type commands directly in the terminal
- **Debug Support** - Connect your IDE debugger to port 5005
- **Clean Workspace** - Server files in `run/` directory (gitignored)

---

## Development Workflow

### Building

```bash
# Compile only
./gradlew compileJava

# Build plugin JAR
./gradlew shadowJar

# Clean and rebuild
./gradlew clean shadowJar
```

### Testing

```bash
# Run server with your plugin
./gradlew runServer

# Run unit tests
./gradlew test

# Clean test server
rm -rf run/
```

### Debugging

```bash
# Run server in debug mode
./gradlew runServer -Pdebug

# Then connect your IDE debugger to localhost:5005
```

---

## Adding Dependencies

Edit `build.gradle.kts`:

```kotlin
dependencies {
    // Hytale API (provided by server)
    compileOnly(files("libs/hytale-server.jar"))
    
    // Your dependencies (will be bundled)
    implementation("com.google.code.gson:gson:2.10.1")
    implementation("org.jetbrains:annotations:24.1.0")
    
    // Test dependencies
    testImplementation("org.junit.jupiter:junit-jupiter:5.10.0")
    testRuntimeOnly("org.junit.platform:junit-platform-launcher")
}
```

### Dependency Relocation

To avoid conflicts with other plugins, relocate your dependencies:

```kotlin
tasks {
    shadowJar {
        // Relocate dependencies to avoid conflicts
        relocate("com.google.gson", "com.yourplugin.libs.gson")
        relocate("org.apache.commons", "com.yourplugin.libs.commons")
        
        // Minimize JAR size (removes unused classes)
        minimize()
    }
}
```

---

## CI/CD with GitHub Actions

The template includes a GitHub Actions workflow (`.github/workflows/build.yml`) that:

1. ✅ Builds your plugin on every push
2. ✅ Runs tests
3. ✅ Uploads artifacts
4. ✅ Creates releases when you tag

### Creating a Release

```bash
git tag v1.0.0
git push origin v1.0.0
```

GitHub Actions will automatically build and create a release with your plugin JAR.

### Workflow Features

- **Multi-OS Testing** - Builds on Ubuntu, Windows, and macOS
- **Java 25 Support** - Uses the latest Java version
- **Artifact Upload** - Saves built JARs for download
- **Release Automation** - Automatically creates GitHub releases

---

## Customization Examples

### Custom Build Configuration

**Change Java version:**

```kotlin
java {
    toolchain {
        languageVersion.set(JavaLanguageVersion.of(21))
    }
}

tasks {
    compileJava {
        options.release = 21
    }
}
```

**Add custom repositories:**

```kotlin
repositories {
    mavenLocal()
    mavenCentral()
    maven("https://repo.example.com/releases")
}
```

### Custom Gradle Tasks

**Add a custom task:**

```kotlin
tasks.register("copyToServer") {
    group = "deployment"
    description = "Copy plugin to local server"
    
    doLast {
        val serverDir = File("C:/HytaleServer/plugins")
        val pluginJar = tasks.shadowJar.get().outputs.files.singleFile
        pluginJar.copyTo(File(serverDir, pluginJar.name), overwrite = true)
        println("Plugin copied to server!")
    }
}
```

---

## Recommended Folder Structure

Once you start developing, organize your code like this:

```
src/main/java/com/yourname/yourplugin/
├── YourPlugin.java              # Main class
├── commands/
│   ├── CommandManager.java      # Command registration
│   ├── HelpCommand.java
│   └── ReloadCommand.java
├── listeners/
│   ├── PlayerJoinListener.java
│   └── BlockBreakListener.java
├── services/
│   ├── PlayerService.java       # Business logic
│   └── DataService.java
├── storage/
│   ├── PlayerStorage.java       # Data persistence
│   └── ConfigStorage.java
├── config/
│   ├── PluginConfig.java        # Configuration classes
│   └── Messages.java
└── utils/
    ├── TextUtils.java           # Utility methods
    └── TimeUtils.java
```

See [Advanced Plugin Patterns](12-advanced-plugin-patterns.md) for implementation details.

---

## Best Practices

### ✅ DO:

- Use the Service-Storage pattern for data management
- Write unit tests for your business logic
- Use structured logging (not `System.out.println`)
- Handle errors gracefully with try-catch blocks
- Document your public API with JavaDoc
- Version your releases semantically (1.0.0, 1.1.0, etc.)
- Use dependency injection where possible
- Keep your main class minimal

### ❌ DON'T:

- Hardcode configuration values
- Block the main thread with heavy operations
- Ignore exceptions or use empty catch blocks
- Use deprecated APIs
- Commit sensitive data (API keys, passwords)
- Mix business logic with storage logic
- Use static variables for state management

---

## Troubleshooting

### Build Fails

```bash
# Clean and rebuild
./gradlew clean build --refresh-dependencies

# Check Gradle version
./gradlew --version

# Update Gradle wrapper
./gradlew wrapper --gradle-version=9.2.0
```

### Server Won't Start

1. Check that `jarUrl` in `build.gradle.kts` is correct
2. Verify Java 25 is installed: `java -version`
3. Check logs in `run/logs/`
4. Ensure the server JAR is compatible

### Plugin Not Loading

1. Verify `manifest.json` has correct `Main` class path
2. Check server logs for errors
3. Ensure all dependencies are bundled in JAR
4. Verify the plugin JAR is in `run/plugins/`

### Gradle Daemon Issues

```bash
# Stop all Gradle daemons
./gradlew --stop

# Rebuild
./gradlew clean shadowJar
```

---

## Migration from Other Templates

If you're migrating from another template:

1. **Copy your source code** to `src/main/java/`
2. **Update `manifest.json`** with your plugin metadata
3. **Copy dependencies** to `build.gradle.kts`
4. **Update package names** to match your group
5. **Test the build** with `./gradlew shadowJar`

---

## Related Documentation

- [Getting Started with Plugins](07-getting-started-with-plugins.md) - Plugin basics
- [Advanced Plugin Patterns](12-advanced-plugin-patterns.md) - Architecture patterns
- [Common Plugin Features](14-common-plugin-features.md) - Practical features
- [Gradle Automation & Testing](13-gradle-automation-testing.md) - Server testing details

---

## Support and Resources

- **GitHub Repository:** [realBritakee/hytale-template-plugin](https://github.com/realBritakee/hytale-template-plugin)
- **Issues:** [GitHub Issues](https://github.com/realBritakee/hytale-template-plugin/issues)
- **Documentation:** [Hytale Modding Documentation](README.md)

---

## Credits

Created by the Hytale modding community.

Based on best practices from production Hytale plugins and modern Java development standards.

---

**Happy Modding! 🎮**
