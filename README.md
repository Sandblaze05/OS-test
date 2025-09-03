# OS-test

This repository is a minimal test project for building a simple operating system kernel for the x86_64 architecture.

## Overview

The project provides scripts and code to assemble, link, and package a basic x86_64 OS kernel into a bootable ISO image. It uses GRUB as the bootloader and is compatible with the Multiboot2 specification.

## Build Environment

The build environment is set up using Docker. The Dockerfile (`buildenv/Dockerfile`) uses a cross-compiler image and installs tools such as:

- **nasm**: Assembler for compiling assembly source files.
- **xorriso**: For ISO image manipulation.
- **grub-pc-bin** and **grub-common**: For GRUB bootloader operations.

This ensures a consistent build setup and easy cross-compilation.

## Kernel and Boot Code

- The kernel's entry point is written in assembly (`src/impl/x86_64/boot/main.asm`).
- The multiboot2 header (`src/impl/x86_64/boot/header.asm`) allows GRUB to recognize and load the kernel.

## Build Process

- The `Makefile` automates the build:
  - Finds and compiles assembly files into object files.
  - Links object files into a kernel binary using a linker script (`targets/x86_64/linker.ld`), which sets memory layout and the entry point.
  - Packages the kernel into an ISO directory.
  - Uses `grub-mkrescue` to create a bootable ISO image.

## Linker Script

- The linker script (`targets/x86_64/linker.ld`) defines:
  - The kernel's entry point (`start`).
  - The memory layout (starting at 1MB).
  - Sections for the multiboot header and code.

## How to Build

1. Build the Docker image:
   ```sh
   docker build buildenv -t <your-tag>
   ```

2. Run the build environment:
   ```sh
   docker run --rm -it -v ${pwd}:/root/env <your-tag>
   ```

3. Execute the pipeline using makefile:
   ```sh
   make build-x86_64
   ```

4. The resulting bootable ISO will be in `dist/x86_64/kernel.iso`.

5. Boot the kernel iso using qemu:
   ```sh
   qemu-system-x86_64 -cdrom dist/x86_64/kernel.iso
   ```
   
