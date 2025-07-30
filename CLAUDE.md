# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

TCNOpen3 is a TRDP (Train Real-time Data Protocol) implementation - a real-time networking protocol for railway applications. This is a C-based implementation that supports cross-platform development across Linux, macOS, VxWorks, QNX, and Windows.

## Architecture

- **Core Library**: `src/common/` contains the main TRDP implementation
  - `trdp_pdcom.c/h` - Process Data communication
  - `trdp_mdcom.c/h` - Message Data communication  
  - `trdp_utils.c/h` - Utility functions
  - `tlc_if.c`, `tlp_if.c`, `tlm_if.c` - Interface layers (control, PD, MD)
- **VOS Layer**: `src/vos/` provides OS abstraction
  - Platform-specific implementations in subdirectories (posix, windows, vxworks)
- **API**: `src/api/` contains public headers
  - `trdp_if_light.h` - Main API interface
  - `trdp_types.h` - Type definitions
- **Examples**: `example/` directory with sample applications
- **Tests**: `test/` directory with comprehensive test suite

## Build System

### Configuration
First select a target configuration:
```bash
make LINUX_X86_64_config        # 64-bit Linux
make OSX_X86_64_config          # macOS 64-bit
make LINUX_X86_64_HP_config     # High performance Linux build
make VXWORKS_PPC_config         # VxWorks PowerPC
```

### Build Commands
```bash
make help                       # Show all available targets and options
make all                        # Build everything
make DEBUG=TRUE all             # Debug build
make clean                      # Clean build artifacts
make distclean                  # Clean everything including config

# Specific targets
make libtrdp                    # Core library only
make libtrdpap                  # Full library with XML/marshalling
make example                    # Example applications
make test                       # Test applications
make pdtest                     # PD communication tests
make mdtest                     # MD communication tests
make xml                        # XML parsing tests
make highperf                   # High performance tests
make vlan                       # VLAN interface tests
```

### Build Options
```bash
make MD_SUPPORT=0               # Disable message data support
make RT_THREADS=1               # Enable realtime scheduling
make TSN_SUPPORT=1              # Enable TSN support
make HIGH_PERF_INDEXED=1        # Enable high performance mode
```

## Key Files to Understand

- `Makefile` - Main build configuration
- `rules.mk` - Build rules and toolchain setup
- `config/` - Target-specific configurations
- `src/api/trdp_if_light.h` - Primary API interface
- `src/common/trdp_private.h` - Internal definitions

## Development Workflow

1. Select appropriate configuration file from `config/` directory
2. Run `make <TARGET>_config` to set up build environment
3. Build with `make` or specific targets
4. Test applications are built in `bld/output/<arch>/`
5. Use `make clean` between configuration changes

## Testing

The test suite includes:
- Unit tests in `test/diverse/`
- PD communication tests in `test/pdpatterns/`
- MD communication tests in `test/mdpatterns/`  
- XML functionality tests in `test/xml/`
- Local loopback tests in `test/localtest/`

## Documentation

- API documentation can be generated with `make doc` (requires doxygen)
- Reference manual: `doc/TCN-TRDP2-D-BOM-033-xx - TRDP Reference Manual.pdf`
- Architecture specification referenced in readme.txt