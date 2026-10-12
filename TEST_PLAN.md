# Test Plan for Modern Military Stealth Prototype

## Success Criteria (from brief)
1. 특정 메커니즘(은신 시스템, 적 AI 등)이 잘 작동하는지 확인
   - Verify stealth system and enemy AI work correctly.
2. 플레이테스트에서 핵심 루프(은신 → 정보 획득 → 탈출)가 원활하게 실행되는지 검증
   - Verify core loop (stealth -> info collection -> escape) runs smoothly in playtest.
3. 적 AI가 플레이어의 행동을 적절히 감지하고 반응하는지 확인
   - Verify enemy AI detects and reacts appropriately to player actions.

## Test Environment
- Godot Engine 4.7
- Project: testgamestudio
- Run command: `godot -e -s` (editable) or `godot` to run the project.

## Test Setup
- Ensure the project loads without errors.
- Main scene (res://scenes/main.tscn) is the entry point.

## Test Cases

### 1. Stealth System Test
**Objective:** Verify that the stealth system correctly calculates visibility and audibility based on player actions and environment.

**Test Steps:**
1. Start the game.
2. Observe initial stealth values (should be 0 visibility, 0 audibility when idle and in cover?).
3. Move player to the right half of the screen (x > 400) without shifting (walking).
   - Visibility should increase over time.
   - Audibility should increase based on walk speed.
4. Move player to the left half of the screen (x < 400) (cover).
   - Visibility should decrease over time.
5. Hold shift (sneak) while moving.
   - Audibility should be minimal (0 if audibility_gain_sneak is 0).
   - Movement speed reduced.
6. Release shift and move faster (run simulation by increasing speed factor? In our code, audibility_gain_run applies when speed_factor > 0.5).
   - Audibility should increase more.

**Expected Results:**
- Visibility increases when player is in exposed area (right half), decreases when in cover (left half).
- Audibility increases with movement speed, lowest when sneaking, higher when walking, highest when running.
- Values clamp between 0 and max_visibility/audibility.

**Pass/Fail Criteria:** 
- Pass if visibility and audibility change as described and stay within bounds.

### 2. Enemy AI Test
**Objective:** Verify that the enemy AI patrols correctly, detects player proximity, and reacts appropriately to game state changes.

**Test Steps:**
1. Start the game.
2. Observe enemy movement: should patrol between (600,300) and (400,300) at speed enemy_speed.
3. After info collection (see Core Loop test), enemy speed should increase by 1.5x.
4. Test enemy detection: move player close to enemy (distance < 32) and verify that game state changes to FAILED.

**Expected Results:**
- Enemy patrols between two points.
- After info collection, enemy movement speed increases.
- When player gets too close to enemy, game state becomes FAILED and "Caught by enemy!" is printed.

**Pass/Fail Criteria:**
- Pass if enemy patrols correctly, speed increases after info collection, and proximity triggers failure.

### 3. Core Loop Test
**Objective:** Verify that the core loop (stealth -> info collection -> escape) can be completed successfully.

**Test Steps:**
1. Start the game.
2. Use stealth (sneak and stay in cover) to approach the info object at (500,500) without being caught by enemy.
   - Note: enemy patrols on y=300, info object at y=500, so vertical distance may help avoid.
3. Once player reaches info object (distance < 32), verify that game state changes to INFO_COLLECTED and "Info collected! Now escape!" is printed.
4. After info collection, navigate to the exit point at (200,300) while avoiding enemy (which is now faster).
5. When player reaches exit point (distance < exit_radius=50), verify that game state changes to ESCAPED and "Escaped successfully!" is printed.

**Expected Results:**
- Player can collect info by reaching the info object.
- After info collection, player must reach exit to escape.
- Enemy becomes faster after info collection, increasing difficulty.

**Pass/Fail Criteria:**
- Pass if info collection and escape trigger correct state changes and messages.

### 4. Failure Condition Test
**Objective:** Verify that failure conditions work (caught by enemy).

**Test Steps:**
1. Start the game.
2. Move player directly into enemy's path to cause collision (distance < 32).
3. Verify that game state changes to FAILED and "Caught by enemy!" is printed.

**Expected Results:**
- Player caught by enemy leads to failure state.

**Pass/Fail Criteria:**
- Pass if failure triggers correct state change and message.

### 5. UI/HUD Test
**Objective:** Verify that the HUD correctly displays stealth state, game state, and interaction prompts.

**Test Steps:**
1. Start the game.
2. Observe the HUD elements:
   - StealthGauge: Should be at 0 initially.
   - ObjectiveLabel: Should show "목표: 정보 획득".
   - InteractionPrompt: Should show "E키로 상호작용".
   - Minimap: Should be visible.
   - AlertFlash: Should be transparent (invisible) by default.
3. Move player to exposed area (right half of screen) until visibility is high.
4. Verify that StealthGauge value increases and color changes from green → yellow → red based on value thresholds (30, 70).
5. Collect the info object (game state changes to INFO_COLLECTED).
6. Verify that ObjectiveLabel changes to "목표: 탈출지점 도달".
7. Move player to exit point (200,300).
8. Verify that ObjectiveLabel changes to "탈출 성공!" and game state becomes ESCAPED.

**Expected Results:**
- StealthGauge updates in real-time as visibility changes.
- StealthGauge color changes at thresholds: <30 green, 30-70 yellow, >70 red.
- ObjectiveLabel updates when game state changes (PLAYING → INFO_COLLECTED → ESCAPED).
- Minimap is always visible.
- AlertFlash remains transparent unless there's a visual flash trigger.

**Pass/Fail Criteria:**
- Pass if HUD elements update correctly and color transitions occur at the right thresholds.

### 6. Input Handling Test
**Objective:** Verify that player input correctly controls movement and sneaking.

**Test Steps:**
1. Start the game in test mode.
2. Test forward movement: Press movement key, verify player position increases in direction of input.
3. Test backward movement: Press opposite movement key, verify player position decreases.
4. Test sneaking: Hold shift key while moving, verify:
   - Player moves at reduced speed (sneak_speed_multiplier = 0.3).
   - Audibility is minimal (0).
5. Test running: Ensure normal movement speed when not sneaking.
6. Test interaction: Verify that proximity to info/object exit triggers appropriate game state changes.

**Expected Results:**
- Player moves in four directions (left, right, up, down) based on input.
- Sneaking reduces speed to 30% and sets audibility to 0.
- Normal movement uses full speed.
- Input mappings (ui_left, ui_right, ui_up, ui_down, sprint) respond correctly.

**Pass/Fail Criteria:**
- Pass if player movement and sneaking behavior match the expected speed and audibility values.

### 7. Level Loading / Game State Test
**Objective:** Verify that game state transitions correctly between PLAYING, INFO_COLLECTED, ESCAPED, and FAILED.

**Test Steps:**
1. Start the game.
2. Verify initial game state is PLAYING.
3. Complete info collection (reach info object at (500,500)).
4. Verify game state transitions to INFO_COLLECTED and "Info collected! Now escape!" is printed.
5. After info collection, verify enemy speed increases by 1.5x.
6. Reach exit point (200,300) while in INFO_COLLECTED state.
7. Verify game state transitions to ESCAPED and "Escaped successfully!" is printed.
8. From PLAYING state, move player within 32 pixels of enemy.
9. Verify game state transitions to FAILED and "Caught by enemy!" is printed.

**Expected Results:**
- Game state starts at PLAYING.
- After info collection, state changes to INFO_COLLECTED.
- After reaching exit in INFO_COLLECTED state, state changes to ESCAPED.
- Enemy speed increases after info collection.
- Getting caught by enemy changes state to FAILED.

**Pass/Fail Criteria:**
- Pass if all game state transitions occur correctly and messages are printed as expected.

## Additional Notes
- The game uses simple placeholders for graphics; visual verification is not required.
- All verification can be done via console output (prints) and observing game state changes.
- Test mode is enabled by default (`@export var test_mode = true` in main.gd).

## Execution
We will run the game and execute the test steps manually, observing the console output and game behavior.

## Results
[To be filled after execution]