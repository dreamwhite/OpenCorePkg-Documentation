---
layout: default
title: Coding Conventions
parent: Setup
nav_order: 4
---
# Coding Conventions

As with any other project, we have conventions that we follow during development. All third-party contributors are advised to adhere to the conventions listed below before submitting patches. To minimise abortive work and the potential rejection of submissions, third-party contributors should initially raise issues to the [Acidanthera Bugtracker](https://github.com/acidanthera/bugtracker) for feedback before submitting patches.

**Organisation.** The codebase is contained in the `OpenCorePkg` repository, which is the primary EDK II package.

- Whenever changes are required in multiple repositories, separate pull requests should be sent to each.
- Committing the changes should happen firstly to dependent repositories, secondly to primary repositories to
avoid automatic build errors.
- Each unique commit should compile with XCODE5 and preferably with other toolchains. In the majority of the
cases it can be checked by accessing the [CI interface](https://travis-ci.com/acidanthera). Ensuring that static analysis finds no warnings is preferred.
- External pull requests and tagged commits must be validated. That said, commits in master may build but may
not necessarily work.
- Internal branches should be named as follows: `author-name-date`, e.g. `vit9696-ballooning-20191026`.
- Commit messages should be prefixed with the primary module (e.g. library or code module) the changes were
made in. For example, `OcGuardLib: Add OC_ALIGNED macro`. For non-library changes `Docs` or `Build` prefixes are used.

**Design.** The codebase is written in a subset of freestanding C11 (C17) supported by most modern toolchains used by 10
 
EDK II. Applying common software development practices or requesting clarification is recommended if any particular case is not discussed below.
- Never rely on undefined behaviour and try to avoid implementation defined behaviour unless explicitly covered below (feel free to create an issue when a relevant case is not present).
- Use `OcGuardLib` to ensure safe integral arithmetics avoiding overflows. Unsigned wraparound should be relied on with care and reduced to the necessary amount.
- Check pointers for correct alignment with OcGuardLib and do not rely on the architecture being able to dereference unaligned pointers.
- Use flexible array members instead of zero-length or one-length arrays where necessary.
- Use static assertions (`STATIC_ASSERT`) for type and value assumptions, and runtime assertions (`ASSERT`) for precondition and invariant sanity checking. Do not use runtime assertions to check for errors as they should never
alter control flow and potentially be excluded.
- Assume `UINT32/INT32` to be int-sized and use `%u`, `%d`, and `%x` to print them.
- Assume `UINTN/INTN` to be of unspecified size, and cast them to `UINT64/INT64` for printing with `%Lu`, `%Ld` and so
on as normal.
- Do not rely on integer promotions for numeric literals. Use explicit casts when the type is implementation-
dependent or suffixes when type size is known. Assume U for UINT32 and ULL for UINT64.
- Do ensure unsigned arithmetics especially in bitwise maths, shifts in particular.
- `sizeof` operator should take variables instead of types where possible to be error prone. Use `ARRAY_SIZE` to
obtain array size in elements. Use `L_STR_LEN` and `L_STR_SIZE` macros from `OcStringLib` to obtain string literal
sizes to ensure compiler optimisation.
- Do not use `goto` keyword. Prefer early `return`, `break`, or `continue` after failing to pass error checking instead of
nesting conditionals.
- Use `EFIAPI`, force UEFI calling convention, only in protocols, external callbacks between modules, and functions
with variadic arguments.
- Provide inline documentation to every added function, at least describing its inputs, outputs, precondition,
postcondition, and giving a brief description.
- Do not use `RETURN_STATUS`. Assume `EFI_STATUS` to be a matching superset that is to be always used when
`BOOLEAN` is not enough.
- Security violations should halt the system or cause a forced reboot.

**Codestyle.** The codebase follows the [EDK II codestyle](https://github.com/tianocore/tianocore.github.io/wiki/Code-Style-C) with a few changes and clarifications.
- Write inline documentation for the functions and variables only once: in headers, where a header prototype is available, and inline for `static` variables and functions.
- Use line length of 120 characters or less, preferably 100 characters.
- Use spaces after casts, e.g. `(VOID *)(UINTN) Variable`.
- Use two spaces to indent function arguments when splitting lines.
- Prefix public functions with either Oc or another distinct name.
- Do not prefix private `static` functions, but prefix private `non-static` functions with `Internal`.
- Use SPDX license headers as shown in [acidanthera/bugtracker#483](https://github.com/acidanthera/bugtracker/issues/483).