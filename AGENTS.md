# Git Repository Guide for AI Agents

This repository contains the source code for Git, the distributed version control system. This guide provides context for AI agents working with this codebase.

## Repository Overview

This is the official Git source code repository containing:

- Core Git implementation in C
- Built-in commands (`builtin/` directory)
- Comprehensive test suite (`t/` directory)
- Documentation (`Documentation/` directory)
- Shell scripts and utilities

## Building Git from Source

### Prerequisites

- C compiler (GCC or Clang)
- Make
- Development headers for curl, zlib, openssl
- autotools (for `make configure`)

### Build Process

```bash
# Generate configure script
make configure

# Build all components
make all

# Optional: Install to system
make install

# Build documentation (requires asciidoc/xmlto)
make doc
```

### Build Targets

- `make all` - Build Git binaries and scripts
- `make test` - Run the test suite
- `make doc` - Build documentation
- `make install` - Install to system

## Testing Framework

### Test Structure

- Tests are located in `t/` directory
- Test files follow pattern `tNNNN-description.sh`
- Each test file is executable and self-contained

### Running Tests

```bash
# Run all tests
make test

# Run specific test file
cd t && sh ./t4000-diff-format.sh

# Run test with verbose output
cd t && sh ./t4000-diff-format.sh -v

# Run specific test numbers
cd t && sh ./t4000-diff-format.sh --run="1,3,5"

# Run tests matching pattern
cd t && sh ./t4000-diff-format.sh --run="*basic*"
```

### Writing Tests

Tests use the TAP (Test Anything Protocol) format:

```bash
#!/bin/sh
test_description='Description of what this tests'

. ./test-lib.sh

test_expect_success 'test name' '
    # Test commands here
    test_when_finished "cleanup commands" &&
    echo "content" >file &&
    git add file &&
    git commit -m "message" &&
    test_cmp expected actual
'

test_done
```

### Test Utilities

- `test_expect_success` - Main test function
- `test_when_finished` - Cleanup after test
- `test_cmp` - Compare files
- `test_must_fail` - Expect command to fail
- `test_write_lines` - Create multi-line files

## Code Organization

### Key Directories

- `builtin/` - Built-in Git commands (git-add, git-commit, etc.)
- `t/` - Test suite
- `Documentation/` - Man pages and guides in AsciiDoc format
- `contrib/` - Contributed scripts and tools
- `templates/` - Default Git templates

### Core Files

- `git.c` - Main Git program entry point
- `diff.c` - Core diff functionality
- `revision.c` - Revision walking and parsing
- `cache.h` - Main header with common definitions
- `builtin.h` - Built-in command declarations

### Diff Subsystem

- `diff.c` - Core diff engine
- `diffcore.c` - Diff transformations (rename/copy detection)
- `builtin/diff.c` - `git diff` command implementation
- `Documentation/diff-options.adoc` - Diff option documentation

## Documentation

### Format

Documentation uses AsciiDoc format (`.adoc` files).

### Key Documentation Files

- `Documentation/git-<command>.adoc` - Individual command documentation
- `Documentation/diff-options.adoc` - Diff-related options
- `Documentation/gitdiffcore.adoc` - Technical diff internals
- `Documentation/CodingGuidelines` - Code style guidelines

### Building Documentation

```bash
make doc          # Build all documentation
make man          # Build man pages only
make html         # Build HTML documentation
```

## Development Workflow

### Remote Configuration

For contributors working with forks:

```bash
# Set up upstream and fork remotes
git remote add upstream https://github.com/git/git
git remote set-url --push upstream no-push  # Prevent accidental pushes
```

### Branch Management

- `master` - Main development branch
- `maint` - Maintenance branch for stable releases
- Feature branches should be based on `master`

### Commit Guidelines

- Clear, descriptive commit messages
- Separate logical changes into separate commits
- Follow existing code style (see `Documentation/CodingGuidelines`)

## Common Development Tasks

### Adding a New Test

1. Choose appropriate test file number (check existing `t/tNNNN-*.sh`)
2. Create test file in `t/` directory
3. Make executable: `chmod +x t/tNNNN-new-test.sh`
4. Run test to verify it works
5. Add to commit with descriptive message

### Modifying Diff Functionality

1. Core logic: `diff.c`, `diffcore.c`
2. Command interface: `builtin/diff.c`
3. Documentation: `Documentation/diff-options.adoc`
4. Tests: `t/t40*.sh` files
5. Ensure all tests pass: `make test`

### Debugging Tests

```bash
# Run single test with debug info
cd t && sh ./tNNNN-test.sh -d

# Keep test directories for inspection
cd t && sh ./tNNNN-test.sh -d --no-clean

# Run with shell tracing
cd t && sh ./tNNNN-test.sh -x
```

## Important Notes for AI Agents

- Always run tests after making changes: `make test`
- Follow existing code patterns and style
- Update documentation when changing behavior
- Test files use shell scripting with Git-specific test utilities
- The test framework creates temporary directories for isolation
- Use `test_when_finished` for cleanup in tests
- Copy detection requires `-C` flags to be enabled before `--diff-filter=C` works
- Documentation is built from AsciiDoc sources, not the C code comments

## Resources

- [Git Contributing Guide](https://github.com/git/git/blob/master/CONTRIBUTING.md)
- [Coding Guidelines](Documentation/CodingGuidelines)
- [Test README](t/README)
- [Git Developer Documentation](Documentation/technical/)
