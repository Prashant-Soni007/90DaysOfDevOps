# File I/O Practice

## Commands Run

**1. `touch notes.txt`**
Creates an empty file if it doesn't already exist.

**2. `echo "Line 1" > notes.txt`**
Writes "Line 1" into the file, overwriting anything already there.
```
Line 1
```

**3. `echo "Line 2" >> notes.txt`**
Appends "Line 2" as a new line without erasing existing content.
```
Line 1
Line 2
```

**4. `echo "Line 3" | tee -a notes.txt`**
Appends "Line 3" to the file *and* prints it to the terminal at the same time.
```
Line 3
```

**5. `cat notes.txt`** — read the full file
```
Line 1
Line 2
Line 3
```

**6. `head -n 2 notes.txt`** — read the first 2 lines
```
Line 1
Line 2
```

**7. `tail -n 2 notes.txt`** — read the last 2 lines
```
Line 2
Line 3
```

## What I Learned
- `>` overwrites a file, `>>` appends — mixing them up is a common way to accidentally wipe a config or log file.
- `tee -a` is useful when I want to write to a file *and* see the output immediately, instead of writing then cat-ing separately.
- `head`/`tail` are faster than `cat` for large files when I only need the start or end (e.g., checking the latest log lines).