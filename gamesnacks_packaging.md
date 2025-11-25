# How to Package the Plugin

## Step-by-Step Packaging Instructions

### 1. Create the Folder Structure

Create a folder named `GameSnacks_SDK` with this exact structure:

```
GameSnacks_SDK/
├── addon.json
├── plugin.js
├── type.js
├── aces.json
├── icon.png
├── lang/
│   └── en-US.json
└── c3runtime/
    ├── plugin.js
    ├── instance.js
    ├── actions.js
    ├── conditions.js
    └── expressions.js
```

### 2. Copy All the Code Files

Copy each file I provided into the correct location:

**Root folder files:**
- `addon.json`
- `plugin.js`
- `type.js`
- `aces.json`

**lang folder:**
- `en-US.json`

**c3runtime folder:**
- `plugin.js`
- `instance.js`
- `actions.js`
- `conditions.js`
- `expressions.js`

### 3. Create an Icon

Create a 64x64 pixel PNG image named `icon.png` and place it in the root folder.

**Quick icon options:**
- Use any graphics software (Photoshop, GIMP, Paint.NET)
- Use online tools like Canva or Photopea
- Simple solid color with "GS" text works fine
- Must be exactly 64x64 pixels
- PNG format

### 4. Zip the Folder

**Important:** You must zip the CONTENTS of the folder, not the folder itself.

**Correct way:**
1. Open the `GameSnacks_SDK` folder
2. Select ALL files and folders inside it
3. Right-click → Send to → Compressed (zipped) folder
   - Or use 7-Zip: Right-click → 7-Zip → Add to archive

**The zip should contain:**
```
addon.json (at root level)
plugin.js (at root level)
type.js (at root level)
aces.json (at root level)
icon.png (at root level)
lang/ (folder)
c3runtime/ (folder)
```

**NOT like this (wrong):**
```
GameSnacks_SDK/
  ├── addon.json
  ├── plugin.js
  └── ...
```

### 5. Rename the Zip File

Rename the `.zip` file to `GameSnacks_SDK.c3addon`

Example:
- Before: `GameSnacks_SDK.zip`
- After: `GameSnacks_SDK.c3addon`

### 6. Install in Construct 3

1. Open Construct 3 (web or desktop)
2. Go to **Menu** → **View** → **Addon Manager**
3. Click **"Install new addon"**
4. Select your `GameSnacks_SDK.c3addon` file
5. Click **OK** to confirm
6. **Restart Construct 3** (important!)

### 7. Verify Installation

After restarting:
1. Create a new project or open existing one
2. Right-click in the Objects panel
3. Search for "GameSnacks"
4. You should see "GameSnacks SDK" in the list

## Testing the Plugin

### Test 1: Check if Plugin Loads
1. Add GameSnacks SDK object to your project
2. If no errors appear, the plugin loaded successfully

### Test 2: Check Actions/Conditions
1. Create an event
2. Add action → GameSnacks SDK
3. You should see all actions organized by category:
   - **Game Lifecycle** (Game ready, Pause, Resume)
   - **Advertisements** (Show interstitial, Show rewarded)
   - **Audio** (none - only conditions)
   - **Score** (Update score)
   - **Storage** (Set, Remove, Clear)

### Test 3: Check Console (Local Testing)
1. Run your project locally
2. Open browser console (F12)
3. You should see: `[GameSnacks SDK] GameSnacks object not found. Make sure your game is running on GameSnacks platform.`
4. This is NORMAL - the plugin only works on GameSnacks platform

## Common Issues

### Issue: "Failed to install addon"
**Solution:** Make sure your zip structure is correct. The files should be at the root of the zip, not inside a folder.

### Issue: Plugin doesn't appear after installation
**Solution:** 
- Restart Construct 3 completely
- Check that icon.png exists and is valid
- Verify addon.json is valid JSON (use JSONLint.com)

### Issue: Actions are empty or missing
**Solution:**
- Check that aces.json is in the root folder
- Check that all c3runtime/*.js files exist
- Verify aces.json is valid JSON

### Issue: "Cannot find module" errors
**Solution:**
- Make sure file names match exactly (case-sensitive)
- Verify all files from file-list in addon.json exist

## Delivery Checklist

Before sending to client, verify:

- [ ] All 9 files are included
- [ ] Icon.png is 64x64 pixels
- [ ] Zip contains files at root level (not in subfolder)
- [ ] File is renamed to .c3addon
- [ ] Plugin installs in Construct 3 without errors
- [ ] All actions/conditions appear correctly
- [ ] README.md documentation is included

## Client Instructions

Provide this to your client:

```
=== INSTALLATION INSTRUCTIONS ===

1. Download GameSnacks_SDK.c3addon file

2. Open Construct 3

3. Go to Menu → View → Addon Manager

4. Click "Install new addon"

5. Select GameSnacks_SDK.c3addon

6. Restart Construct 3

7. Add "GameSnacks SDK" object to your project

8. See README.md for usage examples
```

## File Size

Expected plugin size: ~15-20 KB (with icon)

If your .c3addon file is much larger, you may have included extra files. Make sure only the required files are in the zip.

## Support After Delivery

Provide client with:
1. The `.c3addon` file
2. `README.md` (usage documentation)
3. This `PACKAGING_GUIDE.md` (installation help)
4. Basic example .c3p project (optional but recommended)

## Creating an Example Project (Optional)

To make it easier for the client:

1. Create a simple Construct 3 project
2. Add GameSnacks SDK object
3. Create basic events showing:
   - Game ready on start
   - Show ad on button click
   - Update score when collecting item
   - Save/load with storage
4. Export as .c3p file
5. Include with delivery

This helps the client understand how to use the plugin immediately.