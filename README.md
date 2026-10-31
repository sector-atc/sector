# SECTOR

> [!WARNING]
> **Project status: Early development / architecture and prototyping.**
>
> SECTOR is not yet an operational VATSIM controller client. Live network connectivity and voice integration are planned features subject to the applicable VATSIM development and approval processes.

**An open-source, cross-platform air traffic control workstation.**

SECTOR is an early-stage air traffic control (ATC) client project designed for the VATSIM network. It aims to bring modern interface design, high-performance graphics, extensibility, and seamless multi-device workflows into a unified controller workstation.

Built with **Rust, TypeScript, React, WebGPU, and WebAssembly**, SECTOR is designed to operate across Windows, macOS, and Linux, with planned companion interfaces for iPadOS, Android, and modern web browsers.

---

## Vision

Air traffic control software should be powerful without being unnecessarily complicated.

SECTOR's goal is to combine the flexibility and operational capabilities expected from established ATC clients with the performance, accessibility, and design standards of modern software.

The project is built around five principles:

- **Performance first:** Hardware-accelerated rendering, efficient state management, and low-latency interactions.
- **Truly cross-platform:** A consistent experience across major desktop operating systems, with dedicated touch-friendly companion interfaces.
- **Open and extensible:** Transparent development, modular components, documented interfaces, and community contributions.
- **One connected workspace:** Seamless interaction between desktop workstations, tablets, and additional displays.

## Planned Features

### Radar & Surveillance

- GPU-accelerated radar rendering using WebGPU
- Interactive aircraft labels and track visualization
- Configurable radar symbology and display settings
- Aircraft history trails and trajectory visualization
- Sector boundaries, airways, waypoints, and navigation data
- Ground surveillance and detailed airport layouts
- Multiple radar views and independent windows
- High-DPI and multi-monitor support

### Flight Data & Controller Tools

- Flight-plan visualization and editing
- Electronic flight progress strips
- Controller coordination and handoff workflows
- Departure and arrival management tools
- ATIS and controller information
- Configurable panels and workspaces
- Keyboard shortcuts and command-based interactions

### Touch & Stylus

SECTOR is being designed with touch and pen input as first-class interaction methods.

Planned capabilities include:

- Apple Pencil and compatible Android stylus support
- Handwritten annotations on electronic flight strips
- Pressure-sensitive drawing where supported
- Multitouch gestures for navigation and zooming
- Touch-friendly flight-strip manipulation
- Chart annotations and taxi-route visualization
- Unified mouse, keyboard, touchscreen, and stylus input

Device-specific capabilities will depend on operating-system and browser support.

### SECTOR Companion

SECTOR Companion is the planned browser-based interface for tablets, phones, and secondary devices.

Rather than establishing an independent VATSIM connection, companion devices will connect to the primary workstation through a secure local gateway.

Potential workflows include:

- Using an iPad as a dedicated electronic flight-strip board
- Displaying airport diagrams on an Android tablet
- Viewing synchronized aircraft and flight-plan information
- Managing selected workstation panels from another device
- Sharing annotations and controller workspace state

The desktop workstation will remain the authoritative owner of the controlling session. Companion commands will be subject to authentication, permissions, and validation.

Interactive network use will require the necessary VATSIM approvals.

## Technology Stack

| Component | Technology |
|---|---|
| Frontend | React, TypeScript |
| Frontend tooling | Vite |
| Desktop runtime | Electron (provisional) |
| Core engine | Rust |
| Asynchronous services | Tokio (planned) |
| Graphics | WebGPU, WGSL |
| Browser computation | Rust / WebAssembly |
| Mobile companion | Progressive Web App |
| Styling | CSS / Tailwind CSS (proposed) |
| Local storage | SQLite (planned) |
| Device communication | WebSocket, with shared protocol definitions (planned) |

The architecture intentionally separates the application interface from the ATC engine.

Performance-critical graphics are rendered through GPU-accelerated surfaces, while conventional application controls can use accessible HTML and CSS components.

Rust provides the shared domain model and native services. Selected browser-compatible components can also be compiled to WebAssembly.

## Architecture

```text
                       SECTOR
                          |
             +------------+------------+
             |                         |
      Desktop Workstation       SECTOR Companion
      Electron + React          Browser / PWA
             |                         |
      WebGPU Renderer           WebGPU Renderer
             |                         |
      Native Rust Core          Rust / WASM
             |                         |
             +------ Gateway ----------+
                        |
               ATC Session Engine
                        |
           +------------+------------+
           |            |            |
       Networking     Voice       Navigation
           |            |            |
           +------------+------------+
                        |
                  VATSIM Network
             (future approved integration)
```

The main architectural layers are:

1. **Presentation:** User interfaces, workspaces, flight strips, radar interaction, and responsive layouts.
2. **Rendering:** GPU-accelerated radar graphics, maps, symbols, and annotations.
3. **Domain:** Aircraft, flight plans, controller state, geometry, and operational rules.
4. **Integration:** Networking, voice, navigation data, persistence, and platform-specific services.
5. **Companion Gateway:** Device authentication, state synchronization, and validated remote interactions.

The domain engine must remain independent of any particular UI framework or desktop runtime.

## Repository Structure

SECTOR is organized as a monorepo using pnpm for TypeScript packages and Cargo for Rust crates.

The following structure is the intended repository layout; some modules will be introduced progressively.

```text
sector/
├── apps/
│   ├── desktop/              # Desktop ATC workstation
│   └── companion/            # Browser and tablet companion
│
├── packages/
│   ├── ui/                   # Shared UI components
│   ├── renderer/             # WebGPU renderer and shaders
│   ├── input/                # Mouse, touch and stylus input
│   ├── protocol/             # TypeScript protocol bindings
│   └── config/               # Shared tooling configuration
│
├── crates/
│   ├── sector-core/          # ATC domain model
│   ├── sector-geometry/      # Geodesy and spatial operations
│   ├── sector-protocol/      # Shared protocol definitions
│   ├── sector-gateway/       # Companion synchronization
│   ├── sector-wasm/          # WebAssembly bindings
│   ├── sector-fsd/           # Future network integration
│   └── sector-audio/         # Future voice integration
│
├── schemas/                  # Shared data specifications
├── assets/                   # Application assets
├── fixtures/                 # Synthetic and test datasets
├── tests/                    # Integration and end-to-end tests
├── tools/                    # Development utilities
├── docs/
│   ├── architecture/
│   └── adr/                  # Architecture Decision Records
│
├── .github/
├── Cargo.toml
├── pnpm-workspace.yaml
├── package.json
└── README.md
```

## Getting Started

SECTOR is in early development. The following setup applies once the planned application scaffolding and root workspace scripts have been initialized.

### Prerequisites

- Git
- Node.js (current supported LTS release)
- pnpm
- Rust and Cargo (via rustup)
- Platform-specific native build tools

For macOS, install the Xcode Command Line Tools:

```bash
xcode-select --install
```

For browser-side Rust development, install the WebAssembly compilation target:

```bash
rustup target add wasm32-unknown-unknown
```

### Clone the Repository

```bash
git clone https://github.com/sector-atc/sector.git sector
cd sector
```

See [implementation status](docs/architecture/STATUS.md) for the boundary between starter code and planned functionality.

### Install Dependencies

```bash
pnpm install
cargo check --workspace
```

### Start the Desktop Application

```bash
pnpm dev:desktop
```

### Start the Companion Interface

The companion is not implemented yet; no development command is provided.

### Run Rust Tests

```bash
cargo test --workspace
```

### Validate Rust Formatting

```bash
cargo fmt --all -- --check
```

Additional build instructions and operating-system requirements will be documented as platform support is implemented.

## Development Roadmap

### Phase 1 — Foundation

- [ ] Initialize the monorepo and development tooling
- [ ] Establish the shared Rust domain model
- [ ] Implement an offline synthetic traffic generator
- [ ] Build the initial WebGPU radar renderer
- [ ] Implement basic aircraft labels and selection
- [ ] Create the desktop application shell
- [ ] Establish cross-platform CI and automated testing

### Phase 2 — Controller Workspace

- [ ] Implement flight-plan and aircraft information panels
- [ ] Build the electronic flight-strip system
- [ ] Implement mouse, keyboard, touch, and stylus interactions
- [ ] Add configurable layouts and multiple windows
- [ ] Load supported sector and airport data
- [ ] Implement radar display customization

### Phase 3 — Companion

- [ ] Establish the companion gateway
- [ ] Implement secure device pairing
- [ ] Synchronize aircraft and flight-plan state
- [ ] Introduce tablet-optimized layouts
- [ ] Support handwritten annotations and multitouch gestures
- [ ] Implement validated companion commands and reconnection handling

### Phase 4 — Network Integration

Subject to the relevant VATSIM approvals and technical requirements:

- [ ] Implement approved VATSIM connectivity
- [ ] Integrate supported voice communication
- [ ] Implement controller coordination and handoffs
- [ ] Support live flight-plan operations
- [ ] Validate operational behavior and network compatibility

### Phase 5 — Extended Capabilities

- [ ] Advanced ground-control workflows
- [ ] Departure and arrival management tools
- [ ] Replay and training scenarios
- [ ] Plugin and extension infrastructure
- [ ] Expanded customization and accessibility
- [ ] Further platform integrations

This roadmap describes development goals, not committed release dates or guaranteed network capabilities.

## Design Principles

**Operational clarity over decoration.**

SECTOR aims to provide a modern visual experience without compromising information density, responsiveness, or the clarity of controller workflows.

The interface will prioritize:

- Readable typography and clearly differentiated aircraft states
- Consistent interaction patterns
- Low-latency feedback
- Purposeful animation
- Keyboard accessibility
- Customizable workspace density
- High-resolution and multi-monitor usability
- Touch-friendly layouts without sacrificing desktop efficiency

Operational colors and warnings will be designed around their meaning and readability, independently of the project's branding palette.

## Performance & Reliability

Performance and correctness are foundational engineering requirements.

SECTOR will use representative synthetic traffic scenarios to benchmark:

- Frame times and rendering latency
- Aircraft update processing
- Radar label density and interaction
- Memory and CPU utilization
- GPU resource consumption
- Input responsiveness
- Multi-device synchronization
- Recovery from interrupted connections

The authoritative ATC state will remain separate from presentation and rendering. UI stalls must not directly interrupt native session processing.

Tests and benchmarks will be published as the implementation progresses.

## VATSIM Integration & Disclaimer

SECTOR is an independent, community-oriented software project intended for use with the VATSIM flight simulation network.

**SECTOR is not affiliated with, endorsed by, or officially approved by VATSIM.**

VATSIM connectivity, voice integration, and interactive controlling features will be developed only in accordance with applicable VATSIM policies, technical requirements, and necessary approvals.

SECTOR must not be considered certified software for real-world air traffic control.

The initial development environment will use synthetic traffic and offline test data.

For official VATSIM information, visit [vatsim.net](https://vatsim.net).

## Contributing

SECTOR is intended to become a community-driven open-source project.

Contributions in the following areas will be particularly valuable:

- Rust systems programming
- TypeScript and React development
- WebGPU and shader programming
- Cross-platform application development
- Aviation data formats and navigation
- VATSIM operations and ATC workflows
- UI/UX design and accessibility
- Testing, documentation, and developer tooling

Shared contribution guidelines and community defaults live in the organization’s [.github repository](https://github.com/sector-atc/.github). Governance is currently maintainer-led.

Until then, prospective contributors can follow the repository and participate in discussions once the project's collaboration channels are available.

## License

SECTOR-authored software is licensed under **GNU AGPL version 3 only (`AGPL-3.0-only`)**. See [LICENSE](LICENSE) and [licensing scope](LICENSING.md).

Documentation prose, branding, and datasets require separate content licensing. Dependencies and starter-template material retain their upstream terms. VATSIM approval and dataset rights are separate from source licensing.

---

**SECTOR** — *A new perspective on control.*

An open ATC workstation, built for the next generation of virtual air traffic control.
