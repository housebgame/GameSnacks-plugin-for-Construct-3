# GameSnacks SDK Plugin for Construct 3

Integration plugin for Google GameSnacks SDK, providing access to ads, audio controls, game lifecycle, score tracking, and cloud storage.

## Installation

1. **Create the plugin folder structure:**
   ```
   GameSnacks_SDK/
   ├── addon.json
   ├── plugin.js
   ├── type.js
   ├── aces.json
   ├── icon.png (create a 64x64 icon)
   ├── lang/
   │   └── en-US.json
   └── c3runtime/
       ├── plugin.js
       ├── instance.js
       ├── actions.js
       ├── conditions.js
       └── expressions.js
   ```

2. **Add an icon.png** (64x64 pixels) to the root folder

3. **Create a .c3addon file:**
   - Zip the entire `GameSnacks_SDK` folder
   - Rename the .zip file to `GameSnacks_SDK.c3addon`

4. **Install in Construct 3:**
   - Open Construct 3
   - Go to Menu → View → Addon Manager
   - Click "Install new addon"
   - Select your `.c3addon` file
   - Restart Construct 3

## Usage

### Basic Setup

1. Add the GameSnacks SDK object to your project
2. On game start, call "First frame ready" action when your game is fully loaded and ready for user interaction
3. Optionally, call "Game ready" action after the game is completely ready

```
Example Event Sheet:
On start of layout (when game is ready for interaction)
  → GameSnacks SDK: First frame ready

On game fully initialized
  → GameSnacks SDK: Game ready
```

### Game Lifecycle

**Actions:**
- `First frame ready` - Tell GameSnacks the first frame is ready to be shown (call when game is loaded and ready for user interaction)
- `Game ready` - Tell GameSnacks your game is fully ready (call after complete initialization)
- `Game over` - Notify GameSnacks of game over events (death, level failure, restart, exit)
- `Pause game` - Manually pause the game (Note: GameSnacks SDK uses an onPause event handler)
- `Resume game` - Manually resume the game (Note: GameSnacks SDK uses an onResume event handler)

**Conditions (Triggers):**
- `On game paused` - Triggered when GameSnacks pauses your game
- `On game resumed` - Triggered when GameSnacks resumes your game

**Conditions:**
- `Is game paused` - Check if game is currently paused

**Example:**
```
GameSnacks SDK: On game paused
  → System: Set time scale to 0
  → PauseMenu: Set visible

GameSnacks SDK: On game resumed
  → System: Set time scale to 1
  → PauseMenu: Set invisible

Player: On destroyed
  → GameSnacks SDK: Game over
  → Go to GameOver layout
```

### Audio Controls

**Conditions:**
- `Is audio enabled` - Check if audio is currently enabled
- `On audio changed` - Triggered when user toggles audio

**Expressions:**
- `AudioEnabled` - Returns 1 if enabled, 0 if disabled

**Example:**
```
GameSnacks SDK: On audio changed
  → Audio: Set muted to NOT GameSnacks_SDK.AudioEnabled
```

### Advertisements

**Actions:**
- `Show interstitial ad` - Display fullscreen ad (Note: uses GameSnacks.ad.showInterstitial())
- `Show rewarded ad` - Display rewarded video ad

**Conditions (Triggers):**
- `On ad shown` - Triggered when ad successfully displays
- `On ad failed` - Triggered when ad fails to load
- `On before reward` - Triggered before showing rewarded ad (show reward prompt here)
- `On ad viewed` - Triggered when player watched full rewarded ad (grant reward here)
- `On ad dismissed` - Triggered when player dismissed rewarded ad early (no reward)

**Example - Interstitial Ad:**
```
Button: On clicked
  → GameSnacks SDK: Show interstitial ad

GameSnacks SDK: On ad shown
  → Text: Set text to "Thanks for watching!"

GameSnacks SDK: On ad failed
  → Text: Set text to "Ad not available"
```

**Example - Rewarded Ad:**
```
WatchAdButton: On clicked
  → GameSnacks SDK: Show rewarded ad

GameSnacks SDK: On before reward
  → RewardPrompt: Set visible
  → RewardText: Set text to "Watch ad to get 100 coins?"

GameSnacks SDK: On ad viewed
  → Coins: Add 100
  → RewardPrompt: Set invisible
  → Text: Set text to "You earned 100 coins!"

GameSnacks SDK: On ad dismissed
  → RewardPrompt: Set invisible
  → Text: Set text to "Ad cancelled - no reward"
```

### Score Tracking

**Actions:**
- `Update score` - Send current score to GameSnacks (uses `GameSnacks.score.update()`)

**Requirements:**
- If your game has scores, you MUST call this action with any new score
- If your game doesn't have scores, call this with alternative progress metrics (e.g., levels completed)
- GameSnacks uses this data for features like high scores and leaderboards

**Example:**
```
Enemy: On destroyed
  → Score: Add 10
  → GameSnacks SDK: Update score to Score.Value
```

### Storage (Cloud Save)

**Actions:**
- `Get storage item` - Retrieve data by key and trigger "On item loaded from storage" event
- `Set storage item` - Save data (key, value)
- `Remove storage item` - Delete saved data by key
- `Clear all storage` - Remove all saved data

**Conditions (Triggers):**
- `On item loaded from storage` - Triggered after "Get storage item" completes (use LoadedStorageKey and LoadedStorageValue expressions)

**Expressions:**
- `StorageGet(key)` - Retrieve saved data synchronously (returns string, or empty string if key doesn't exist)
- `LoadedStorageKey` - Get the key from the last "Get storage item" action (use in "On item loaded from storage" event)
- `LoadedStorageValue` - Get the value from the last "Get storage item" action (use in "On item loaded from storage" event)

**Example - Save High Score (Method 1 - Using Expression):**
```
On start of layout
  → HighScore: Set text to GameSnacks_SDK.StorageGet("highscore")

Score > int(GameSnacks_SDK.StorageGet("highscore"))
  → GameSnacks SDK: Set storage "highscore" to str(Score.Value)
```

**Example - Save High Score (Method 2 - Using Action + Event):**
```
On start of layout
  → GameSnacks SDK: Get storage item "highscore"

GameSnacks SDK: On item loaded from storage
  → HighScore: Set text to GameSnacks_SDK.LoadedStorageValue

Score > int(GameSnacks_SDK.LoadedStorageValue)
  → GameSnacks SDK: Set storage "highscore" to str(Score.Value)
```

**Example - Save Game State:**
```
System: Every 30 seconds
  → GameSnacks SDK: Set storage "level" to str(CurrentLevel)
  → GameSnacks SDK: Set storage "coins" to str(Coins)

On start of layout
  → GameSnacks SDK: Get storage item "level"
  → GameSnacks SDK: Get storage item "coins"

GameSnacks SDK: On item loaded from storage
  → System: Compare variable: GameSnacks_SDK.LoadedStorageKey = "level"
    → Global variable CurrentLevel = int(GameSnacks_SDK.LoadedStorageValue)
  → System: Compare variable: GameSnacks_SDK.LoadedStorageKey = "coins"
    → Global variable Coins = int(GameSnacks_SDK.LoadedStorageValue)
```

## Complete Example Flow

```
=== GAME START ===
On start of layout
  → Load saved data using StorageGet expressions
  → Setup audio: Audio: Set muted to NOT GameSnacks_SDK.AudioEnabled
  → GameSnacks SDK: First frame ready
  → GameSnacks SDK: Game ready (optional, after full initialization)

=== DURING GAMEPLAY ===
Player collects coin
  → Add to score
  → GameSnacks SDK: Update score to Score.Value

Every 30 seconds
  → Save progress using Storage Set actions

=== LEVEL COMPLETE ===
Player reaches goal
  → GameSnacks SDK: Show interstitial ad

GameSnacks SDK: On ad shown
  → Go to next level

=== PAUSE/RESUME ===
GameSnacks SDK: On game paused
  → System: Set time scale to 0
  → Show pause menu

GameSnacks SDK: On game resumed
  → System: Set time scale to 1
  → Hide pause menu

=== AUDIO TOGGLE ===
GameSnacks SDK: On audio changed
  → Audio: Set muted to NOT GameSnacks_SDK.AudioEnabled
```

## Important Notes

1. **SDK Availability:** The plugin checks if `window.GameSnacks` exists. If not found, it logs a warning and does nothing. This allows you to test locally.

2. **Testing Locally:** The plugin will not work outside the GameSnacks platform. All actions will silently fail with console warnings.

3. **Storage Limits:** Keep saved data under 3 MB (recommended under 500 KB)

4. **Storage Format:** All storage values are strings. Use `int()` and `str()` to convert:
   - Save: `str(MyNumber)`
   - Load: `int(GameSnacks_SDK.StorageGet("key"))`

5. **Required Integration:** According to GameSnacks requirements, you MUST:
   - Call `First frame ready` when game is ready for user interaction (uses `GameSnacks.game.firstFrameReady()`)
   - Optionally call `Game ready` when game is fully initialized (uses `GameSnacks.game.ready()`)
   - Call `Game over` when player reaches a game over state (uses `GameSnacks.game.gameOver()`)
   - Handle `On game paused` and `On game resumed` events (automatic via `onPause`/`onResume` callbacks)
   - Handle `On ad failed` event properly, including for `frequencyCapped` status (when ads are rate-limited)
   - Use only GameSnacks storage (no localStorage/sessionStorage) - localStorage is no longer supported
   - Update score during gameplay if your game has scores or progress metrics
   - Integrate audio controls

6. **Pause/Resume Behavior:**
   - GameSnacks SDK uses event handlers (`onPause`/`onResume`) to notify your game
   - The "Pause game" and "Resume game" actions only update internal state
   - There are no direct GameSnacks API methods to pause/resume - only event listeners

7. **Ads Best Practice:**
   - Aim for at least one ad opportunity per minute (interstitial or rewarded)
   - Use `adBreak` API for rewarded ads with proper callbacks
   - Handle all ad callbacks: `beforeAd`, `afterAd`, `beforeReward`, `adViewed`, `adDismissed`, `adBreakDone`

## Troubleshooting

**Plugin doesn't appear in Construct 3:**
- Make sure all files are in correct folder structure
- Check that addon.json is valid JSON
- Restart Construct 3 after installation

**Actions do nothing:**
- Check browser console for warnings
- Make sure you're testing on GameSnacks platform
- Verify `window.GameSnacks` exists in browser console

**Storage not persisting:**
- Ensure you're calling `Set storage` action
- Check that key names are consistent
- Verify total storage is under 3 MB

## API Reference

### Actions
| Action | Parameters | Description | GameSnacks API |
|--------|-----------|-------------|----------------|
| First frame ready | None | Announce first frame is ready to be shown (call when game is ready for user interaction) | `GameSnacks.game.firstFrameReady()` |
| Game ready | None | Announce game is fully ready (call after complete initialization) | `GameSnacks.game.ready()` |
| Game over | None | Announce game over event | `GameSnacks.game.gameOver()` |
| Pause game | None | Manually set internal pause state | Internal state only |
| Resume game | None | Manually set internal resume state | Internal state only |
| Show interstitial ad | None | Display fullscreen ad | `GameSnacks.ad.break({type: 'next'})` |
| Show rewarded ad | None | Display reward video ad | `adBreak({type: 'reward'})` |
| Update score | score (number) | Send score to GameSnacks | `GameSnacks.score.update(score)` |
| Get storage item | key (string) | Retrieve data and trigger "On item loaded from storage" event | `GameSnacks.storage.getItem(key)` |
| Set storage | key (string), value (string) | Save data (max 3MB, recommended <500KB) | `GameSnacks.storage.setItem(key, value)` |
| Remove storage item | key (string) | Delete saved data | `GameSnacks.storage.removeItem(key)` |
| Clear all storage | None | Remove all saved data | `GameSnacks.storage.clear()` |

### Conditions
| Condition | Type | Description |
|-----------|------|-------------|
| Is audio enabled | Check | True if audio is enabled |
| Is game paused | Check | True if game is paused |
| On audio changed | Trigger | When audio toggles |
| On game paused | Trigger | When game is paused |
| On game resumed | Trigger | When game is resumed |
| On ad shown | Trigger | When ad displays successfully |
| On ad failed | Trigger | When ad fails to load |
| On before reward | Trigger | Before showing rewarded ad |
| On ad viewed | Trigger | When rewarded ad watched completely |
| On ad dismissed | Trigger | When rewarded ad dismissed early |
| On item loaded from storage | Trigger | After "Get storage item" action completes |

### Expressions
| Expression | Returns | Description |
|------------|---------|-------------|
| AudioEnabled | number | 1 if enabled, 0 if disabled |
| StorageGet(key) | string | Get saved data by key (synchronous) |
| LoadedStorageKey | string | Key from last "Get storage item" action |
| LoadedStorageValue | string | Value from last "Get storage item" action |

## Support

- GameSnacks Developer Docs: https://developers.google.com/gamesnacks/developer/sdk
- Construct 3 Plugin SDK: https://www.construct.net/en/make-games/manuals/addon-sdk

## Version History

**v1.0.2** - Critical bug fixes for GameSnacks QA requirements
- **FIXED:** Added separate "First frame ready" action using `GameSnacks.game.firstFrameReady()` for GAME_READY event
- **FIXED:** Added separate "Game ready" action using `GameSnacks.game.ready()` for complete initialization
- **FIXED:** Corrected "Game over" action to use `GameSnacks.game.gameOver()` instead of incorrect `GameSnacks.gameOver()`
- **FIXED:** Ad system now properly handles `frequencyCapped` breakStatus by triggering "On ad failed" event
- This ensures game resumes properly when ads are rate-limited and prevents game freezing after game over
- Updated all documentation to reflect the two separate ready actions

**v1.0.1** - Bug fixes and API corrections
- Fixed score submission to use correct API method `GameSnacks.score.update()` instead of `score.submit()`
- Updated documentation with accurate GameSnacks API method names
- Added comprehensive API reference mapping Construct 3 actions to GameSnacks SDK methods
- Clarified pause/resume behavior (event-based, not direct API calls)
- Added ads best practices and callback requirements

**v1.0.0** - Initial release
- Complete GameSnacks SDK integration
- All required features: ads, audio, game lifecycle, score, storage
- Category-organized actions
- Comprehensive error handling

## License

This plugin is provided as-is for use with GameSnacks platform. Refer to GameSnacks developer agreement for terms.s.