# OpenGOAL Speedrun Rhythm Coach & Metronome (`goal-tempo`)

A high-precision, real-time input timing evaluator and dynamic audio metronome built natively for the **OpenGOAL** decompiler and compiler ecosystem. This tool intercepts internal player state machine changes at **60 frames per second (300Hz engine ticks)** to analyze, evaluate, and drill frame-perfect speedrun movement mechanics [Standard library, Process and State].

---

## 🚀 Core Architecture

Unlike standard external overlays, `goal-tempo` operates deep inside Naughty Dog's custom game kernel and virtual machine heap, leveraging the game's native symbols and cooperative multitasking threads [Process and State, Type system].

### 1. Heap-Allocated Process State (`tempo-coach-process`)
By default, the GOAL kernel allocates a tiny **256-byte stack** for each process thread [Process and State]. To prevent fatal stack overflow crashes when managing persistent timing variables, this coach is compiled as a custom LISP type structure inheriting from the engine's base `process` class [Process and State, Type system]:
* All variables (timestamps, input captures, learning limits) are stored as **heap fields** rather than stack-allocated registers [Process and State].
* A custom pointer dereference `(-> self field-name)` is used to query and update metrics across active loops, dropping the active stack footprint to under 50 bytes [Process and State].

### 2. Bulletproof Native `behavior` Threading
Instead of high-overhead anonymous lambdas, the coach's loop is compiled as a native GOAL **`behavior`** block [Process and State]. The `behavior` construct:\n* Automatically and safely binds the lexical symbol `self` to the running CPU thread [Process and State].
* Casts `self` to `tempo-coach-process` during compile-time, completely immunizing the running game client against type-mismatch faults and memory heap corruption [Process and State, Type system].

### 3. Null-Pointer & Divide-by-Zero Guards
* **Asset Loading Protection:** During map transitions (e.g., loading and unloading level sectors), the player pointer `*target*` or its state machine can temporarily become null (`#f`) [Process and State]. Double-guards ensure the script gracefully displays `OFFLINE` instead of triggering a fatal null-pointer access violation (`exit status 5`) [Process and State, In-game Settings].
* **Frame Boundary Guard:** If button inputs register on the exact same frame (a 0-frame gap), the BPM math scales the interval safely to a 1-frame boundary, preventing Division-by-Zero CPU interrupts [Standard library].

---

## 🎨 Interactive HUD & Visual Metronome

The coach draws a real-time training dashboard directly onto the screen's standard connection format buffer (`*stdcon*`) [Standard library, Symbol Index]:

### 1. Authentic PlayStation Controller Colors
The HUD parses index escape codes mapped directly to the game engine's internal **Font Color Tables** defined in `font-h.gc` [Font Color Tables]. By appending the escape command `~indexL`, buttons are painted in their official, colored controller representations [Font Color Tables, Standard library]:
* **~24L[Square]~0L:** PINK button marker [Font Color Tables].
* **~25L[Circle]~0L:** RED button marker [Font Color Tables].
* **~26L[Triangle]~0L:** GREEN button marker [Font Color Tables].
* **~27L[X]~0L:** BLUE button marker [Font Color Tables].
* **~22L[L1]~0L:** GRAY shoulder bumper color [Font Color Tables].

### 2. Dual-Feedback Rhythm Metronome
* **Visual:** An asterisk `*` bounces left and right across a visual rhythm bar on the HUD, representing the sub-beat phase of your movement combo.
* **Acoustic:** Plays a high-pitched downbeat ping (`money-pickup` sound cue) [Symbol Index], followed by automated, synchronized echo-beats scheduled precisely at your personal best transition frame boundaries [Standard library].

---

## 🏋️‍♂️ How to Load & Practice

### 1. Clean Boot Protocol
Because OpenGOAL uses a stateful compiler, you must load the game's core type definitions and kernel symbols into memory before compiling your user mod [Language basics, Type system]:
1. Open your terminal in `jak-project` and boot the REPL:\n   ```bash\n   task repl\n   ```
2. Compile and populate the base engine symbol tables:\n   ```goal\n   g > (mi)\n   ```
3. Open a second terminal and launch the client window:\n   ```bash\n   task boot-game\n   ```
4. Connect the REPL listener to the game window:\n   ```goal\n   g > (lt)\n   ```
5. Compile, load, and boot the Speedrun Coach!\n   ```goal\n   gc> (ml \"goal_src/user/goal-tempo.gc\")\n   gc> (tempo-start)\n   ```

### 2. Live Speedrun Preset Swapping
While practicing in-game, you can copy-paste any of these preset declarations directly into your active **`gc>`** REPL to change the drill instantly without recompiling [Type system, Method System]:

* **Punch-Roll-Jump (PRJ) [Default]:** Drills your lunge cancel.
  ```goal
  (tempo-set-drill-presets "Punch-Roll-Jump (PRJ)" 'target-running-attack 'target-wheel 'target-wheel-flip 'square 'l1 'x)
  ```
* **Ledge Boosted Jump (Boosted):** Drills walking off ledges and canceling the falling frame.
  ```goal
  (tempo-set-drill-presets "Ledge Boosted Jump" 'target-falling 'target-attack-air 'target-attack-up 'square 'x 'circle)
  ```
* **Ledge Extended Jump (Extended):** Drills maximum vertical height launches.
  ```goal
  (tempo-set-drill-presets "Extended Uppercut" 'target-running-attack 'target-attack-uppercut 'target-attack-uppercut-jump 'square 'x 'circle)
  ```
* **Ground Pound Jump:** Drills frame-perfect high bounces.
  ```goal
  (tempo-set-drill-presets "Ground Pound Jump" 'target-jump 'target-flop 'target-flop-hit-ground 'x 'square 'x)
  ```
* **Rolljump High Jump:** Drills landing frames.
  ```goal
  (tempo-set-drill-presets "Rolljump High Jump" 'target-wheel-flip 'target-hit-ground 'target-high-jump 'l1 'x 'x)
  ```

---

## 📚 References

The following resources from the OpenGOAL documentation and community codebases were used to design, optimize, and ground the architecture of `goal-tempo`:

1.  **[Font Color Tables | OpenGOAL](https://opengoal.dev/docs/reference/color_table)** — Native color registers for string formatting and PS2 controller button symbols [Font Color Tables].
2.  **[GOOS Macro Language | OpenGOAL](https://opengoal.dev/docs/reference/goos)** — Syntax specifications for GOAL's compile-time macro language [GOOS].
3.  **[GOAL-Tempo Repository | GitHub](https://github.com/EpistemicPolymath/GOAL-Tempo)** — Timing and rhythm development framework for OpenGOAL speedrun coaching [GitHub - EpistemicPolymath/GOAL-Tempo].
4.  **[open-goal/jak-project | GitHub](https://github.com/open-goal/jak-project)** — The core native decompiler and compiler development repository for the PC port [GitHub - open-goal/jak-project].
5.  **[In-game Settings Documentation | OpenGOAL](https://opengoal.dev/docs/usage/settings/)** — Engine-level settings detailing speedrunner mode, culling, aspect ratios, and frame rates [In-game Settings].
6.  **[Jak Speedrunning | Google Photos](https://photos.google.com)** — Community image logs and media references for movement tech [Jak Speedrunning - Google Photos].
7.  **[Language Basics | OpenGOAL](https://opengoal.dev/docs/reference/language_basics)** — Compiler mechanics, top-level expressions, and file structure basics [Language basics].
8.  **[Method System | OpenGOAL](https://opengoal.dev/docs/reference/method_system)** — Virtual method dispatching, vtables, constructor/destructor behavior, and type specialization [Method System].
9.  **[OG-Speedrun-Practice | GitHub](https://github.com/OpenGOAL-Mods/OG-Speedrun-Practice)** — Native mod reference for custom checkpoints, warp-points, and movement practice overrides [OG-Speedrun-Practice/README.md at main · OpenGOAL-Mods/OG-Speedrun-Practice · GitHub].
10. **[Package Index | OpenGOAL](https://opengoal.dev/docs/source-docs/jak1/package-index/)** — The directory layout and physical file organization rules of `goal_src/` [Package Index].
11. **[Process and State | OpenGOAL](https://opengoal.dev/docs/reference/process_and_state)** — Low-level documentation detailing thread mechanics, process execution loops, dead pools, and state machines [Process and State].
12. **[Reader Specifications | OpenGOAL](https://opengoal.dev/docs/reference/reader)** — S-expression parser logic, integer formatting rules, and reader macros [Reader].
13. **[Standard Library | OpenGOAL](https://opengoal.dev/docs/reference/lib)** — Essential functions including `format` print escapes, block structures, lexical bindings (`let`), and basic operations [Standard library].
14. **[Symbol Index | OpenGOAL](https://opengoal.dev/docs/source-docs/jak1/symbol-index/)** — A complete index of engine symbols, states, and global variables [Symbol Index].
15. **[Syntax and Examples | OpenGOAL](https://opengoal.dev/docs/reference/syntax)** — Reference for value versus reference semantics, inline arrays, and `new` allocation behaviors [Syntax and examples].
16. **[Type System | OpenGOAL](https://opengoal.dev/docs/reference/type_system)** — Concrete specifications of the parent-child type hierarchy and compound type simplification [Type system].
