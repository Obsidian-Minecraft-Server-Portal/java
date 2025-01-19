# Java Runtime Management Library for Obsidian Server Portal

[![License: LGPL-3.0](https://img.shields.io/badge/License-LGPL--3.0-blue.svg)](https://opensource.org/licenses/LGPL-3.0)

This library is part of the **Obsidian Server Portal**, a Minecraft server management tool. It provides robust Java runtime version management, helping with detection, downloading, installation, uninstallation, and executing commands for Java runtimes used by the Minecraft server environment. The library is designed to work as a Git dependency and integrates seamlessly into the Obsidian ecosystem.

---

## Features

- **Java Version Management**:
    - Lists all available Java runtimes versions for the supported operating systems.
    - Fetches installed Java runtimes on the system.
    - Installs specific runtime versions efficiently.
    - Uninstalls Java versions safely with cleanup.

- **Cross-Platform Support**:
    - Detects the operating system and architecture.
    - Supports **Linux**, **Windows**, and **macOS**, along with their common architectures (*x86*, *x64*, *ARM*).

- **Java Command Execution**:
    - Provides an interface for running commands directly via Java executables.
    - Supports async operations with live output processing.

- **Streams and Callbacks**:
    - Uses asynchronous operations.
    - Supports progress callbacks for installs with real-time feedback.

---

## Installation

Since this library is a part of the Obsidian Server Portal ecosystem and not published on crates.io, it can be added as a Git dependency in your project's `Cargo.toml`.

```toml
[dependencies]
java = { git = "https://github.com/Obsidian-Minecraft-Server-Portal/java-runtime-lib.git" }
```

---

## Usage

This library provides APIs for working with Java runtime versions. Below is a high-level example of how you can interact with it.

### Listing Available Java Versions

```rust
use java::versions::JavaVersion;

#[tokio::main]
async fn main() {
    match JavaVersion::list().await {
        Ok(versions) => {
            for version in versions {
                println!("Found Java version: {}", version.version);
            }
        }
        Err(e) => eprintln!("Error fetching Java versions: {}", e),
    }
}
```

### Installing a Java Runtime Version

```rust
use java::versions::JavaVersion;

#[tokio::main]
async fn main() {
    let version_id = "17"; // Specify the version of Java you want to install
    match JavaVersion::from_version(version_id).await {
        Ok(version) => {
            version
                .install(|progress| {
                    // Handle installation progress (e.g., update UI, log progress)
                    for file in progress {
                        println!("Installing file {} - Completed: {}", file.file, file.completed);
                    }
                })
                .await
                .expect("Failed to install Java");
        }
        Err(e) => eprintln!("Error fetching version {}: {}", version_id, e),
    }
}
```

### Uninstalling a Java Runtime Version

```rust
use java::versions::JavaVersion;

#[tokio::main]
async fn main() {
    let version_id = "17"; // Specify the version of Java you want to uninstall
    match JavaVersion::from_version(version_id).await {
        Ok(version) => {
            if let Err(e) = version.uninstall() {
                eprintln!("Error uninstalling Java {}: {}", version.version, e);
            } else {
                println!("Java {} uninstalled successfully", version.version);
            }
        }
        Err(e) => eprintln!("Error fetching version {}: {}", version_id, e),
    }
}
```

### Running a Java Command

```rust
use java::versions::JavaVersion;

#[tokio::main]
async fn main() {
    let version_id = "17"; // Specify the installed Java version to use
    match JavaVersion::from_version(version_id).await {
        Ok(mut version) => {
            version
                .execute_command("-version", |line| {
                    println!("Output: {}", line);
                })
                .await
                .expect("Failed to execute Java command");
        }
        Err(e) => eprintln!("Error fetching version {}: {}", version_id, e),
    }
}
```

---

## API Overview

The library exposes the following core functionality through the `JavaVersion` API:

1. **Version Listing**:
    - `JavaVersion::list`: Lists all available versions supported by Mojang's API.
    - `JavaVersion::get_installed_versions`: Fetches all Java versions currently installed on the system.

2. **Version Lookup**:
    - `JavaVersion::from_version`: Finds a specific Java version by its name (e.g., `17`, `1.8`).
    - `JavaVersion::from_runtime`: Finds a Java version by its runtime category (e.g., `alpha`, `beta`).

3. **Management**:
    - `install`: Downloads and installs specific Java runtime versions.
    - `uninstall`: Removes an installed Java runtime version.
    - `get_installation_files`: Fetches the installation files for a specific version.

4. **Command Execution**:
    - `execute_command`: Executes commands using the Java runtime and streams live output.

---

## License

This project is licensed under the **LGPL-3.0**. See the [LICENSE](https://opensource.org/licenses/LGPL-3.0) file for details.

---

## Contributing

Feel free to open issues or submit pull requests to improve this library.

---

## Notes

- The library relies on **Mojang's Piston Meta API** for runtime version information.
- Only supports async operations, and **Tokio runtime** is required.
- Recommended for managing Minecraft servers using the **Obsidian Server Portal** panel.