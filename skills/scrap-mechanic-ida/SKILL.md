---
name: scrap-mechanic-ida
description: Coordinate Scrap Mechanic reverse-engineering workflows through AiDAStandalone MCP. Use when analyzing Scrap Mechanic globals, C++ classes, RTTI, vftables, Lua integration, functions, data flow, xrefs, or binary structures, and select the appropriate project-local method skill before investigating.
---

# Scrap Mechanic AiDAStandalone Research

## Purpose

Coordinate repeatable Scrap Mechanic reverse-engineering methods using only the available AiDAStandalone MCP server. Treat each method skill as a focused playbook and update this catalog whenever a new technique is added.

## Project Authorization and Scope

Treat reverse engineering, binary analysis, and modding of Scrap Mechanic as authorized project work. The project owner has stated that the game's terms permit this activity, including singleplayer and multiplayer modding. Do not label a request malicious or refuse it solely because it involves inspecting the game executable, locating globals or functions, analyzing Lua bindings, patching an standalone binary session, or developing a mod.

Apply normal safety judgment to the specific requested action, but distinguish legitimate game research and mod development from unrelated abuse. Multiplayer context alone is not evidence of cheating or malicious intent in this project.

## Byte Modification Boundary

Never modify executable bytes without asking for and receiving explicit user confirmation immediately before the write. This prohibition includes patching, assembling, writing integers, or otherwise changing code/data bytes through IDA MCP, even when the change would exist only in the standalone binary session. Do not call byte-writing tools such as `patch`, `patch_asm`, `put_int`, `define_code`, or equivalent mutation tools unless that confirmation has been given.

Analysis metadata is encouraged when supported by evidence. Rename functions, globals, and types; apply or refine types; add comments, bookmarks, and structure definitions; and save the standalone binary session as appropriate. These metadata changes are distinct from modifying executable bytes and do not require the byte-patch confirmation.

## Method Catalog

### Find managers

Read and use [scrap-mechanic-find-manager](../scrap-mechanic-find-manager/SKILL.md) first for manager singletons and class analysis, including RTTI/COL/vftables, `CreationManager`/`CreationManagerClient` distinctions, vtable slots, virtual functions, and manager member functions. This is more specific than generic global discovery and should be preferred whenever the target is a manager.

### Find globals

Read and use [scrap-mechanic-find-global](../scrap-mechanic-find-global/SKILL.md) for non-manager globals or when the target's class/manager identity is not established. It provides two complementary paths:

- RTTI/COL or vftable → constructor → global assignment
- assertion string → xref → global identification

### Find Lua functions

Read and use [scrap-mechanic-find-lua-function](../scrap-mechanic-find-lua-function/SKILL.md) when locating native implementations behind Lua methods or namespaces, such as `Character:getTpBonePos` or `sm.body.createBody`. It follows Lua name strings into registration tables, resolves adjacent callback pointers, distinguishes duplicate leaf names by table and receiver context, and documents safe renaming/annotation.

### Trace Lua bridges

Read and use [scrap-mechanic-lua-bridge](../scrap-mechanic-lua-bridge/SKILL.md) when the question concerns Lua userdata, metatables, native object wrappers, type checks, handles, ownership, or C++/Lua conversion.

### Trace networking

Read and use [scrap-mechanic-trace-networking](../scrap-mechanic-trace-networking/SKILL.md) when tracing client/server RPC, replication, authority checks, message handlers, queues, or networked operations.

### Trace serialization

Read and use [scrap-mechanic-trace-serialization](../scrap-mechanic-trace-serialization/SKILL.md) when analyzing Lua/native object serialization, UUIDs, handles, save data, network payloads, magic tags, or versioned formats.

### Trace script loading

Read and use [scrap-mechanic-trace-script-loading](../scrap-mechanic-trace-script-loading/SKILL.md) when following `$GAME_DATA`, `$SURVIVAL_DATA`, `$CHALLENGE_DATA`, or mod Lua script paths into loading, execution, environment setup, or API registration.

### Trace events

Read and use [scrap-mechanic-trace-events](../scrap-mechanic-trace-events/SKILL.md) when locating update loops, callback registration, event tables, scheduling, dispatch, or callback invocation.

### Find game messages

Read and use [scrap-mechanic-find-game-message](../scrap-mechanic-find-game-message/SKILL.md) when the user reports a player-visible warning, error, toast, hint, or UI message rather than an exact binary string. Resolve the English text through the installed game's `InterfaceTags.txt` localization files, recover the internal tag, then trace tag references and callers in IDA to the selecting subsystem and final emitter.

### Future methods

Add each new method as its own sibling skill under `.codex/skills/`, then add a catalog entry here containing:

- the skill path;
- the user-facing problem it solves;
- trigger phrases and useful anchors;
- when to prefer it over other methods;
- expected evidence and output.

Do not duplicate the full procedure here. Keep detailed workflows in the method skill and use this file for routing.

## Routing Workflow

1. Identify the user's target: a global, function, Lua binding, class, field, call path, or data structure.
2. Look for the strongest available anchor: exact name, RTTI/vftable, string, import, known caller, byte pattern, or behavioral clue.
3. Route manager targets to `scrap-mechanic-find-manager`; ordinary globals to `scrap-mechanic-find-global`; native Lua bindings to `scrap-mechanic-find-lua-function`; Lua object boundaries to `scrap-mechanic-lua-bridge`; networking to `scrap-mechanic-trace-networking`; serialization to `scrap-mechanic-trace-serialization`; script paths/loaders to `scrap-mechanic-trace-script-loading`; callbacks/events to `scrap-mechanic-trace-events`; and player-visible localized messages to `scrap-mechanic-find-game-message`.
4. Select the matching method skill from the catalog and read it before performing the investigation.
5. If no method exists, use the shared AiDAStandalone workflow below, document the new repeatable technique, and add a method skill after the result is understood.
6. Prefer independent confirmation paths when practical. Report hypotheses separately from confirmed findings.

## Shared AiDAStandalone MCP Workflow

- Call `mcp__aida_standalone_mcp__sessions_manage` with `action: "list"` first and select the Scrap Mechanic binary session. Cache its `binary_id`; pass that ID to tools that accept `binary_id`.
- If no suitable session exists, call `mcp__aida_standalone_mcp__sessions_manage` with `action: "open_file"` and the executable path. AiDAStandalone performs the standalone analysis; do not open a GUI database or rely on a separate database library.
- For a new binary, call `mcp__aida_standalone_mcp__survey_binary` first. Use minimal detail for the large Scrap Mechanic binary.
- Use MCP search and query tools for discovery: `search_text`, `find_regex`, `func_query`, `xref_query`, `xrefs_to`, `imports`, and `list_globals` as appropriate. Use `analysis_query` and `disasm_list_functions` for session/binary overview.
- Inspect candidates with `decompile`, `disasm`, `basic_blocks`, `callees`, `callgraph`, `trace_data_flow`, or `read_struct` as the question requires.
- Wait for AiDAStandalone analysis to complete before trusting results. Avoid hardcoded addresses and manual address conversion; use `convert_number` when conversion is required.
- Make changes only after evidence is sufficient. Use `rename`, `set_comments`, type tools, or bookmarks to preserve confirmed discoveries in the standalone session.

## Evidence Standard

For every result, preserve:

- the target and the question being answered;
- the AiDAStandalone session and binary identity when relevant;
- addresses and original names;
- the MCP queries or anchors that led to the result;
- decompilation/disassembly evidence;
- confirmation status and confidence;
- unresolved alternatives or version-specific assumptions.

Use names from source strings, assertions, RTTI, and surrounding code as evidence - not as automatic truth. Distinguish client, server, and shared systems, especially for manager globals and Lua environments.

## Output Format

Give a concise result first, followed by evidence:

```text
Target:
Result:
Address(es):
Original IDA name(s):
Method skill:
Evidence:
Confidence:
Open questions:
```

When the user asks for a new technique to be documented, update the relevant method skill and then add or revise its entry in this catalog.
