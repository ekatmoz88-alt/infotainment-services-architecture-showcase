# Infotainment Service Layer – Architecture Showcase

## Overview
The **Infotainment Service Layer** is a C/C++‑based middleware that provides a collection of reusable services for vehicle multimedia platforms. It is part of a larger automotive software stack and is built using **CMake** with a modular project layout.

## Technology Stack
- **Languages:** C++14/17 (mixed with C where required)
- **Build System:** CMake 3.6+ (the root `CMakeLists.txt` shown below)
- **Key External Dependencies**:
  - **POCO** v1.7.5 (custom patches) – networking, JSON, util libraries
  - **GTest** – if `ENABLE_TESTS` is set, for unit‑test support
  - **SSL** – OpenSSL, WolfSSL or Botan, selected per platform (`CONFIG_MappingService_Project`)
  - **Other third‑party libraries** stored under `Dependencies/` (pre‑built for many platforms)
- **Platform Considerations:** Configurable per target (Platform-A, Platform-B, Platform-C, etc.) with compiler flags, safe‑SEH handling, coverage options, and child‑process debugging.
- **Testing:** GTest based tests can be enabled via `ENABLE_TESTS`; coverage via `CodeCoverage` when `GCC_COVERAGE_OPTION` is set.

## CMake Module Structure
The repository is organised into several top‑level directories that are included from the root `CMakeLists.txt`:

| Directory | Purpose |
|-----------|---------|
| **Projects/** | Sub‑project CMake files (`CMakeLists.txt`) that define individual targets (Platform-A, Platform-B, simulations, etc.). |
| **Internals/** | Internal implementation details, platform‑specific code, and adapters (e.g., `VehicleFunctionsAdapter`, `VectorXL`). |
| **Simulations/** | Mock services and test harnesses (web‑adapter, OEM simulations, web‑socket testers). |
| **Dependencies/** | Pre‑built third‑party libraries (POCO, SSL, etc.) used by the various targets. |
| **cmake/** | Custom CMake modules, macros (`ServiceMacros`) and presets. |
| **GeneratedOEMCMakeLists.txt** | OEM‑specific generated CMake snippets (included if present). |

The root `CMakeLists.txt` (excerpt) performs the following:
- Sets minimum CMake version, project name `Platform-A`.
- Defines output directories (`bin`, `lib`).
- Includes custom macros and presets.
- Sets search paths for public/private headers (`include`, `src`, `src-gen` …).
- Processes `Projects/CMakeLists.txt` to add all sub‑project targets.
- Conditionally adds **POCO** if `Poco_Sources` is defined.
- Emits build‑type, compiler flags, and pre‑processor defines at the end.

## Directory Layout (high‑level)
``` infotainment-services-architecture-showcase/
├─ CMakeLists.txt               # root CMake configuration (see summary above)
├─ Projects/                    # per‑target CMakeLists.txt files
├─ Internals/                   # internal adapters & platform code
├─ Simulations/                 # mock services & test harnesses
├─ Dependencies/                # third‑party libraries (POCO, SSL, GTest)
├─ cmake/                       # custom CMake modules & macros
└─ README.md                    # this file
```

## Build Instructions (generic)
```bash
# From the root of the repository
mkdir build && cd build
cmake ..                # configure – you may need to set Platform-A_, Platform-B_, SSL_ paths
make                  # build all targets
make test             # run GTest suite if ENABLE_TESTS is on
```
For platform‑specific builds, consult the per‑target `CMakeLists.txt` files under `Projects/` and the macro files under `cmake/`.

*This showcase does **not** contain the `.git` folder or any company‑specific identifiers. All code snippets and descriptions are derived from the public source tree.*