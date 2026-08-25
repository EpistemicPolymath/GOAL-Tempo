# OpenGOAL Speedrun Rhythm Coach & Metronome (`goal-tempo`)

A high-precision, real-time input timing evaluator and dynamic audio metronome built natively for the **OpenGOAL** decompiler and compiler ecosystem. This tool intercepts internal player state machine changes at **60 frames per second (300Hz engine ticks)** to analyze, evaluate, and drill frame-perfect speedrun movement mechanics [Standard library, Process and State].

---

## 🚀 Advanced Movement Physics: Refined Mechanics

In *Jak & Daxter*, advanced ledge-glide maneuvers exploit the physics engine's momentum-preservation algorithms [Process and State]. When Jak executes a lunge punch (`target-running-attack`), his horizontal velocity spikes [Symbol Index]. Intercepting and canceling this state with a jump or spin allows speedrunners to carry massive horizontal speed across vast chasms [Process and State, In-game Settings].

Following precise community timing tests, the coach defines the two primary ledge-cancel mechanics as follows:

### 1. The Ledge Boosted Jump ("Boosted")
*   **Physical Execution:** Walking at a slow, controlled pace (lightly pushing the L-Stick), followed by a quick slide-input of **Square** (`target-running-attack`), an immediate jump cancel with **X** (`target-attack-uppercut-jump`), and a slightly delayed **Circle** spin (`target-attack-air`) to glide for maximum distance [Symbol Index].
*   **Engine Transition:** The punch cancel must occur **just before** gravity registers. If the physics engine triggers the `target-falling` state, Jak loses his ground-contact frames and the lunge lunge-jump window is locked out [Process and State, Symbol Index].
*   **Rhythm Profile:** 
    *   **Gap 1 (Punch -> Uppercut):** Ultra-fast, near-simultaneous slide input (1-2 frames).
    *   **Gap 2 (Uppercut -> Spin):** Slightly delayed to allow Jak to gain optimal height before extending the glide.

### 2. The Ledge Extended Jump ("Extended")
*   **Physical Execution:** Initiating a running lunge punch (**Square** / `target-running-attack`) early on the flat platform, extending the punch forward off the ledge, and then performing a rapid **X** (`target-attack-uppercut-jump`) and **Circle** (`target-attack-air`) cancel in mid-air to gain both horizontal distance and vertical height [Symbol Index].
*   **Rhythm Profile:**
    *   **Gap 1 (Punch -> Uppercut):** Broad, extended spacing where the punch is initiated early and held before transitioning.
    *   **Gap 2 (Uppercut -> Spin):** Fast, snappy double-tap off the edge of the platform.

---

## 🎨 Interactive HUD & Visual Metronome

The coach draws a real-time training dashboard directly onto the screen's standard connection format buffer (`*stdcon*`) [Standard library, Symbol Index]:

### 1. Authentic PlayStation Controller Colors
The HUD parses index escape codes mapped directly to the game engine's internal **Font Color Tables** defined in `font-h.gc` [Font Color Tables]. By appending the escape command `~indexL`, buttons are painted in their official, colored controller representations [Font Color Tables, Standard library]:
*   **~24L[Square]~0L:** PINK button marker [Font Color Tables].
*   **~25L[Circle]~0L:** RED button marker [Font Color Tables].
*   **~26L[Triangle]~0L:** GREEN button marker [Font Color Tables].
*   **~27L[X]~0L:** BLUE button marker [Font Color Tables].
*   **~22L[L1]~0L:** GRAY shoulder bumper color [Font Color Tables].

### 2. Dual-Feedback Rhythm Metronome
*   **Visual:** An asterisk `*` bounces left and right across a visual rhythm bar on the HUD, representing the sub-beat phase of your movement combo.
*   **Acoustic:** Plays a high-pitched downbeat ping (`money-pickup` sound cue) [Symbol Index], followed by automated, synchronized echo-beats scheduled precisely at your personal best transition frame boundaries [Standard library].

---

## 🏋️‍♂️ How to Load & Practice

### 1. Clean Boot Protocol
Because OpenGOAL uses a stateful compiler, you must load the game's core type definitions and kernel symbols into memory before compiling your user mod [Language basics, Type system]:
1. Open your terminal in `jak-project` and boot the REPL:
   ```bash
   task repl
   ```
2. Compile and populate the base engine symbol tables:
   ```goal
   g > (mi)
   ```
3. Open a second terminal and launch the client window:
   ```bash
   task boot-game
   ```
4. Connect the REPL listener to the game window:
   ```goal
   g > (lt)
   ```
5. Compile, load, and boot the Speedrun Coach!
   ```goal
   gc> (ml "goal_src/user/goal-tempo.gc")
   gc> (tempo-start)
   ```

### 2. Live Speedrun Preset Swapping
While practicing in-game, you can copy-paste any of these preset declarations directly into your active **`gc>`** REPL to change the drill instantly without recompiling [Type system, Method System]:

*   **Ledge Boosted Jump (Boosted) [Refined]:**
    ```goal
    (tempo-set-drill-presets "Ledge Boosted Jump" 'target-running-attack 'target-attack-uppercut-jump 'target-attack-air 'square 'x 'circle)
    ```
*   **Ledge Extended Jump (Extended) [Refined]:**
    ```goal
    (tempo-set-drill-presets "Extended Uppercut" 'target-running-attack 'target-attack-uppercut-jump 'target-attack-air 'square 'x 'circle)
    ```
*   **Punch-Roll-Jump (PRJ) [Default]:**
    ```goal
    (tempo-set-drill-presets "Punch-Roll-Jump (PRJ)" 'target-running-attack 'target-wheel 'target-wheel-flip 'square 'l1 'x)
    ```
*   **Ground Pound Jump:**
    ```goal
    (tempo-set-drill-presets "Ground Pound Jump" 'target-jump 'target-flop 'target-flop-hit-ground 'x 'square 'x)
    ```
*   **Rolljump High Jump:**
    ```goal
    (tempo-set-drill-presets "Rolljump High Jump" 'target-wheel-flip 'target-hit-ground 'target-high-jump 'l1 'x 'x)
    ```

---

## 📚 Project References & Sources

1.  **[Font Color Tables | OpenGOAL](https://opengoal.dev/docs/reference/color_table)** — Native color registers for string formatting and PS2 controller button symbols.
2.  **[GOOS Macro Language | OpenGOAL](https://opengoal.dev/docs/reference/goos)** — Syntax specifications for GOAL's compile-time macro language.
3.  **[open-goal/jak-project | GitHub](https://github.com/open-goal/jak-project)** — The core native decompiler and compiler development repository for the PC port.
4.  **[In-game Settings Documentation | OpenGOAL](https://opengoal.dev/docs/usage/settings/)** — Engine-level settings detailing speedrunner mode, culling, aspect ratios, and frame rates.
5.  **[Language Basics | OpenGOAL](https://opengoal.dev/docs/reference/language_basics)** — Compiler mechanics, top-level expressions, and file structure basics.
6.  **[Method System | OpenGOAL](https://opengoal.dev/docs/reference/method_system)** — Virtual method dispatching, vtables, constructor/destructor behavior, and type specialization.
7.  **[OG-Speedrun-Practice | GitHub](https://github.com/OpenGOAL-Mods/OG-Speedrun-Practice)** — Native mod reference for custom checkpoints, warp-points, and movement practice overrides.
8.  **[Package Index | OpenGOAL](https://opengoal.dev/docs/source-docs/jak1/package-index/)** — The directory layout and physical file organization rules of `goal_src/`.
9.  **[Process and State | OpenGOAL](https://opengoal.dev/docs/reference/process_and_state)** — Low-level documentation detailing thread mechanics, process execution loops, dead pools, and state machines.
10. **[Reader Specifications | OpenGOAL](https://opengoal.dev/docs/reference/reader)** — S-expression parser logic, integer formatting rules, and reader macros.
11. **[Standard Library | OpenGOAL](https://opengoal.dev/docs/reference/lib)** — Essential functions including `format` print escapes, block structures, lexical bindings (`let`), and basic operations.
12. **[Symbol Index | OpenGOAL](https://opengoal.dev/docs/source-docs/jak1/symbol-index/)** — A complete index of engine symbols, states, and global variables.
13. **[Syntax and Examples | OpenGOAL](https://opengoal.dev/docs/reference/syntax)** — Reference for value versus reference semantics, inline arrays, and `new` allocation behaviors.
14. **[Type System | OpenGOAL](https://opengoal.dev/docs/reference/type_system)** — Concrete specifications of the parent-child type hierarchy and compound type simplification.
