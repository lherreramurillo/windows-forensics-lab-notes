# Hash-based auditing and filtering with hashdeep

**Date:** 2026-10-14
**Context:** Self-directed practice session
**Environment:** Windows 11 VM (VMware), PowerShell 5.1, hashdeep 4.4

## Goal

Verify the integrity of an entire folder against a previously recorded baseline, and identify which specific file changed without inspecting the files one by one. 

## Setup

Eight text files in a dedicated practice folder. Two of them share identical content under different names, to confirm that a file is identified by its content and not by its name. Two others contain the same visible text but were created with and without the `-NoNewline` parameter, so that the trailing line terminator Windows appends (`CR LF`, two bytes) shows up as a difference the hash detects even though nothing is visible on screen.

## What I did

### 1. Creating the test files

I created six text files manually (`Texto_1.txt` through `Texto_6.txt`), then added two more with the same visible text but written differently, so that the only difference between them would be the trailing line terminator:

```powershell
Set-Content -Path 'C:\Forensics\Practice\refuerzo\con_flag.txt' -Value 'prueba de avalancha' -Encoding ASCII -NoNewline
Set-Content -Path 'C:\Forensics\Practice\refuerzo\sin_flag.txt' -Value 'prueba de avalancha' -Encoding ASCII
```

### 2. Observing the avalanche effect

Both files hold the same text on screen, so one would reasonably expect them to be identical. They are not: without `-NoNewline`, Windows appends a `CR LF` line terminator, which adds two bytes that never show up in an
editor.

```powershell
Get-FileHash 'C:\Forensics\Practice\refuerzo\con_flag.txt' | Format-List
Get-FileHash 'C:\Forensics\Practice\refuerzo\sin_flag.txt' | Format-List
Get-ChildItem 'C:\Forensics\Practice\refuerzo'
```

| File | Size | SHA-256 |
|---|---|---|
| `con_flag.txt` | 19 bytes | `BA4A4FD47E7C067811F85EBC5EA8991D68E6DE685565BE5CF92854328BBE7075` |
| `sin_flag.txt` | 21 bytes | `111BE1EE5A6556F2C031EDF5EEFC81A3BE7071697EBC67A34779979CB9E1DD6C` |

Two bytes of difference produce two hashes with no recognizable similarity. This is the avalanche effect, and it is what makes hashing usable for integrity verification: there is no such thing as a small change producing a small difference in the hash.

It also explains why the lab material pins `-Encoding ASCII -NoNewline` when it expects a specific hash value. Without fixing those parameters, the bytes written to disk depend on the environment, and the result stops being reproducible.

### 3. Baseline generation

### 4. Audit with no changes

### 5. Audit after a single-byte modification

### 6. Filtering against a known-hash list

## Findings

## What I would do differently

## References