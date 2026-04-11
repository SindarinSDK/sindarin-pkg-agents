# sindarin-pkg-agents

AI-agent primer for programming in [Sindarin](https://github.com/SindarinSDK). Install once, then link this file from your project's `CLAUDE.md` / `AGENT.md` so the agent can pull in language references on demand.

## Install

```bash
sn --install https://github.com/SindarinSDK/sindarin-pkg-agents.git
```

This package will then live at `./.sn/sindarin-pkg-agents/`. All paths below are relative to the project root (where `sn.yaml` lives), so an agent can follow them directly.

**Link from `CLAUDE.md` / `AGENT.md`:**

```markdown
See ./.sn/sindarin-pkg-agents/README.md for Sindarin language and tooling reference.
```

## Sindarin Compiler (`sn`)

Sindarin compiles `.sn` source to C, then to a native executable. Intermediate C is removed by default.

```bash
sn file.sn                 # compile + run
sn file.sn -o out          # compile to named binary
sn file.sn -g              # debug build (symbols + ASAN)
sn file.sn -v              # verbose compilation steps
sn file.sn -l 4            # log level 0=none .. 4=verbose
sn file.sn --emit-c        # emit generated C, don't build
sn file.sn --keep-c        # keep intermediate C alongside binary
sn file.sn --emit-model    # emit JSON IR
sn file.sn -O0 | -O1 | -O2 # optimization level (default -O2)
sn --format [--check]      # format .sn files in place (or verify)
sn --version
```

Run `sn --help` for the full flag list.

## Package Management

Sindarin projects are defined by an `sn.yaml` manifest. Dependencies are Git repos, resolved to `./.sn/<pkg-name>/`. Transitive deps are flattened into the same `./.sn/` directory.

```bash
sn --init                  # create sn.yaml in cwd
sn --install               # install all deps from sn.yaml
sn --install <git-url>     # add + install a single package
sn --clear-cache           # clear global cache (~/.sn-cache)
sn --clean                 # remove local build cache (.sn/build/)
```

**`sn.yaml` example:**

```yaml
name: my-project
dependencies:
- name: sindarin-pkg-sdk
  git: https://github.com/SindarinSDK/sindarin-pkg-sdk.git
  branch: main
- name: sindarin-pkg-agents
  git: https://github.com/SindarinSDK/sindarin-pkg-agents.git
  branch: main
```

**Rules of thumb:**
- `sn --install` **overwrites** `./.sn/<pkg>/` from Git. Never hand-edit files under `./.sn/` — changes belong in the upstream package repo.
- To pick up upstream package changes: `sn --clear-cache && rm -rf .sn && sn --install`.
- See `./.sn/sindarin-pkg-agents/docs/packages.md` for imports, project layout, and built-in functions.

## Language Reference

Read **only the topic(s) relevant to the current task** — do not load them all up front.

| Topic | File | When to read |
|-------|------|--------------|
| Program structure | `./.sn/sindarin-pkg-agents/docs/program-structure.md` | `main()`, comments, globals, forward declarations, `return` |
| Types & conversions | `./.sn/sindarin-pkg-agents/docs/types.md` | Primitives, `nil`, type inference, explicit `.toX()` conversions |
| Operators | `./.sn/sindarin-pkg-agents/docs/operators.md` | Arithmetic, comparison, logical, `+=`/`-=`/`*=`, `++`/`--`, unary, special |
| Functions & lambdas | `./.sn/sindarin-pkg-agents/docs/functions.md` | Functions, lambdas, recursion, `native fn`, variadic natives |
| Closures | `./.sn/sindarin-pkg-agents/docs/closures.md` | Capture semantics, mutation, struct fields, threads |
| Structs | `./.sn/sindarin-pkg-agents/docs/structs.md` | Fields, methods, static methods, operator overloading, packed structs, handle fields |
| Memory | `./.sn/sindarin-pkg-agents/docs/memory.md` | `as ref` / `as val`, pointer restrictions, `copyOf`, `using` / `dispose`, return ownership |
| Control flow | `./.sn/sindarin-pkg-agents/docs/control-flow.md` | if/else, `match` (statement vs expression, multi-value arms), for, while, break/continue |
| Strings & arrays | `./.sn/sindarin-pkg-agents/docs/strings-and-arrays.md` | Interpolation, multi-line `|` / `$|`, ranges, slicing, spread `...`, byte encodings |
| Generics & interfaces | `./.sn/sindarin-pkg-agents/docs/generics.md` | Generic structs/functions, `interface`, `Self`, constraints (`T: A & B`), iterators, operators |
| Serialization | `./.sn/sindarin-pkg-agents/docs/serialization.md` | `@serializable`, `@alias` field mapping, encode/decode |
| Threading | `./.sn/sindarin-pkg-agents/docs/threading.md` | `sync var`, `lock` block, thread spawn (`&`), join (`!`), detach (`~`), fire-and-forget |
| Native C interop | `./.sn/sindarin-pkg-agents/docs/native-interop.md` | `@source` / `@alias` / `@include` / `@link` (and `#pragma` forms), `.sn.c`, C type mapping |
| Imports & multi-file | `./.sn/sindarin-pkg-agents/docs/imports.md` | `import`, `import as`, diamond deps, transitive protection, collision handling |
| Packages & tooling | `./.sn/sindarin-pkg-agents/docs/packages.md` | `sn.yaml`, CLI, built-in functions, project layout |
| Common errors | `./.sn/sindarin-pkg-agents/docs/common-errors.md` | Compile error quick-reference: struct, pointer, thread, import, interface errors |

**Critical pitfall:** Sindarin has **no `as` cast operator** — `as` is only for memory qualifiers (`as ref` / `as val`). All type conversions are explicit method calls (`x.toDouble()`, `s.toInt()`, `bytes.toString()`, …). See `./.sn/sindarin-pkg-agents/docs/types.md`.
