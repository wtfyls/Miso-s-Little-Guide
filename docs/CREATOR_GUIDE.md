# Miso's Little Guide — Creator Guide

Customize the guide for your modpack without writing Java or recompiling the mod. You can edit JSON files, replace the mascot with your own PNG artwork, create dialogue pages and choices, and adjust the guide's theme.

This guide describes the guide-pack format and commands supplied for Miso's Little Guide. Keep a backup of your guide-pack folder before making large changes.

## Contents

- [1. How guide packs work](#1-how-guide-packs-work)
- [2. Find the configuration folder](#2-find-the-configuration-folder)
- [3. Activate a guide pack](#3-activate-a-guide-pack)
- [4. Customize the mascot](#4-customize-the-mascot)
- [5. Create dialogue pages](#5-create-dialogue-pages)
- [6. Add buttons and navigation](#6-add-buttons-and-navigation)
- [7. Use dynamic keybindings](#7-use-dynamic-keybindings)
- [8. Customize the theme](#8-customize-the-theme)
- [9. Use conditions for optional content](#9-use-conditions-for-optional-content)
- [10. Test changes in Minecraft](#10-test-changes-in-minecraft)
- [11. Troubleshooting](#11-troubleshooting)
- [12. Creator checklist](#12-creator-checklist)

---

## 1. How guide packs work

A **guide pack** is a set of JSON configuration files and image assets that define one onboarding experience. The mod provides the guide engine; the guide pack provides the content and appearance.

A guide pack can define:

- The pack's identity and general settings.
- One or more mascots, including expressions and animation frames.
- Dialogue pages and page navigation.
- Buttons and supported actions.
- Theme colors and visual settings.
- Optional rewards and other pack settings supported by the installed version.

You can keep Miso, replace Miso with your own character, or define several characters and use different ones on different pages.

The examples below use the configuration fields supplied for the guide-pack format. For fields not demonstrated here—especially optional reward settings and parameterized actions—copy the structure from the sample guide pack shipped with your installed version rather than guessing field names.

## 2. Find the configuration folder

Guide packs live in the Minecraft instance's `config` directory:

```text
config/
└── misos_guide/
    ├── guidepacks/
    │   └── <your_pack_id>/
    │       ├── manifest.json
    │       ├── pages.json
    │       └── assets/
    │           └── mascot/
    │               ├── idle.png
    │               ├── talk_1.png
    │               └── talk_2.png
    └── ...
```

Replace `<your_pack_id>` with a short, unique folder name, for example `my_modpack`. Use the same ID when selecting the pack in the client configuration.

### Finding the right instance

The location of `config` depends on your launcher and installation:

- **CurseForge App:** open the modpack's profile menu and choose the option to open the instance folder.
- **Prism Launcher:** right-click the instance and choose **Folder**.
- **ATLauncher:** open the instance's folder from its instance management screen.
- **Default Minecraft launcher:** open the game directory configured for that installation.

Do not edit a different Minecraft instance by mistake. Each instance can have its own configuration and active guide pack.

## 3. Activate a guide pack

Open:

```text
config/misos_guide-client.toml
```

Under `[general]`, set `activeGuidePack` to the folder ID of your guide pack:

```toml
[general]
activeGuidePack = "my_modpack"
```

Make sure the value matches the folder name exactly. Keep the quotation marks.

If the file already contains other settings, change only `activeGuidePack`; do not replace the entire file.

## 4. Customize the mascot

Mascot definitions are stored in the `"mascots"` section of `manifest.json`. Texture paths are relative to your guide-pack folder.

### Option A: Use one static PNG

Add a mascot definition like this under `"mascots"`:

```json
{
  "my_mascot": {
    "texture": "assets/mascot/idle.png",
    "width": 64,
    "height": 64,
    "alignment": "left",
    "offsetX": 0,
    "offsetY": 0
  }
}
```

Use your own filename if it differs from `idle.png`. The `width` and `height` values control the configured display size. `alignment` can be `"left"` or `"right"`; offsets let you fine-tune placement.

### Option B: Add expressions and talking frames

You can define expressions such as idle, talking, and happy:

```json
{
  "my_mascot": {
    "texture": "assets/mascot/idle.png",
    "width": 64,
    "height": 64,
    "alignment": "left",
    "offsetX": 0,
    "offsetY": 0,
    "expressions": {
      "idle": {
        "texture": "assets/mascot/idle.png"
      },
      "talking": {
        "frames": [
          "assets/mascot/talk_1.png",
          "assets/mascot/talk_2.png"
        ],
        "frameDurationMs": 200
      },
      "happy": {
        "texture": "assets/mascot/happy.png"
      }
    }
  }
}
```

In this example, the talking expression alternates between two images. `frameDurationMs` controls how long each frame is displayed.

### Mascot tips

- Use PNG files, especially when your character needs a transparent background.
- Keep all frames for one animation consistent in canvas size and character placement to avoid visible jumping.
- Keep the character inside the image canvas; leave transparent padding where needed.
- Use clear filenames such as `idle.png`, `happy.png`, and `talk_1.png`.
- Match the expression names used in your page definitions.
- Check capitalization carefully: `Talk.png` and `talk.png` are different filenames on many systems.

### Left or right placement

Set:

```json
"alignment": "right"
```

The mascot is positioned on the right side, with the dialogue bubble tail flipped by the guide UI.

### Hide the mascot on one page

Set `"mascotHidden": true` on a page when you want the text or menu explanation to use the available space without a character.

### Use multiple characters

Define more than one character in `"mascots"`, for example `"miso"`, `"blacksmith"`, or `"wizard"`. Then select the character on a page with its `"character"` value:

```json
{
  "id": "meet_the_blacksmith",
  "title": "Meet the Blacksmith",
  "text": "I can help you get started with crafting!",
  "character": "blacksmith",
  "expression": "happy"
}
```

The character ID must match a key defined in `manifest.json`.

### Missing image fallback

If a mascot image path is missing or invalid, the guide is designed to fall back to the default Miso mascot instead of crashing. Still fix missing-file warnings: a fallback means the guide can recover, not that the custom asset is working.

## 5. Create dialogue pages

Pages are defined as a JSON array in `pages.json`. Each page should have a unique `id`.

Here is a small example:

```json
[
  {
    "id": "welcome",
    "title": "Welcome, Explorer!",
    "text": "Welcome to our modpack! Take your time, explore, and have fun.",
    "character": "my_mascot",
    "expression": "happy",
    "animation": "talk",
    "buttons": [
      {
        "text": "Skip",
        "action": "skip",
        "style": "ghost"
      },
      {
        "text": "Continue",
        "action": "next",
        "style": "primary"
      }
    ]
  },
  {
    "id": "goodbye",
    "title": "Have fun!",
    "text": "That's everything for now. Enjoy your adventure!",
    "character": "my_mascot",
    "expression": "happy",
    "buttons": [
      {
        "text": "Finish",
        "action": "finish",
        "style": "primary"
      }
    ]
  }
]
```

### Page fields used in the examples

| Field | Purpose |
|---|---|
| `id` | Unique identifier for the page. |
| `title` | Page heading. |
| `text` | Dialogue text shown on the page. |
| `character` | Mascot ID to show on this page. |
| `expression` | Expression ID for that mascot. |
| `animation` | Animation state to use, where configured. |
| `mascotHidden` | Set to `true` to hide the mascot on this page. |
| `buttons` | Buttons displayed on the page. |
| `condition` | Optional condition controlling whether the page is available. |

Use the exact field names and values from your installed version's sample guide pack. Unknown fields may be ignored or rejected by the validator.

### Writing readable dialogue

- Keep each page focused on one idea.
- Prefer short sentences and paragraphs.
- Split long explanations across multiple pages.
- Give every page a unique ID.
- Make sure every navigation target points to an existing page.
- Avoid putting too many buttons on one page.
- Keep important instructions understandable without requiring the player to open another menu.

## 6. Add buttons and navigation

Buttons are configured inside each page's `"buttons"` array.

A button has a visible label, an action, and an optional style. For example:

```json
"buttons": [
  {
    "text": "Continue",
    "action": "next",
    "style": "primary"
  },
  {
    "text": "Skip",
    "action": "skip",
    "style": "ghost"
  }
]
```

### Supported action names

| Action | Purpose |
|---|---|
| `next` | Continue to the next page. |
| `goto` | Navigate to a specific page; provide its page ID as `target`. |
| `back` | Return to the previous page. |
| `finish` | Finish and close the guide. |
| `skip` | Skip the guide. |
| `restart` | Return to the beginning. |
| `open_menu` | Open a supported Minecraft menu or feature. |
| `open_url` | Open an external HTTPS link. |

### Link to a specific page

Use `goto` with a `target` that matches another page's `id`:

```json
{
  "text": "Learn about cooking",
  "action": "goto",
  "target": "cooking_page",
  "style": "secondary"
}
```

The destination page must exist. A typo in the target can create a broken navigation path; run the validator after editing pages.

### Create a choice page

```json
{
  "id": "choose_path",
  "title": "Choose Your Path",
  "text": "What would you like to learn first?",
  "buttons": [
    {
      "text": "Cooking",
      "action": "goto",
      "target": "cooking_page",
      "style": "secondary"
    },
    {
      "text": "Machinery",
      "action": "goto",
      "target": "tech_page",
      "style": "primary"
    }
  ]
}
```

Create pages with IDs `cooking_page` and `tech_page` for these choices to work.

### Open a menu or external link

The `open_menu` and `open_url` actions are supported action types. Their required target/URL fields are action-specific; copy the exact parameter structure from the sample guide pack or the action reference shipped with your mod version. External links must use HTTPS. Do not use these actions to run arbitrary commands or code.

## 7. Use dynamic keybindings

You can show the player's actual configured key in dialogue instead of hardcoding a key such as J.

Use this token format:

```text
Press {key:key.ftbquests.quests} anytime to view your Quest Book!
```

The guide resolves the keybinding token and displays the player's current binding (for example, `[J]` or `[SPACE]`).

Use the registered keybinding ID, not the key the player may have changed it to. The displayed key should reflect the player's settings.

If a keybinding is unbound or unavailable, test the result in-game and phrase the instruction so the player still has another way to find the feature.

## 8. Customize the theme

Theme colors and visual settings are configured in the `"theme"` section of `manifest.json`.

Example:

```json
{
  "primaryTextColor": "#2C1810",
  "titleTextColor": "#6B3A1E",
  "accentColor": "#D66E26",
  "buttonTextColor": "#FFFFFF",
  "buttonHoverTextColor": "#FFE6A0",
  "sliceBorder": 6
}
```

### Theme fields

| Field | Purpose |
|---|---|
| `primaryTextColor` | Main dialogue text color. |
| `titleTextColor` | Page title color. |
| `accentColor` | Main accent color. |
| `buttonTextColor` | Button label color. |
| `buttonHoverTextColor` | Button label color while hovered. |
| `sliceBorder` | Border slice size used by the UI texture. |

Use six-digit hexadecimal color values in the `#RRGGBB` format. Choose text and hover colors with enough contrast to remain readable. Keep the `sliceBorder` value appropriate for the actual border texture so corners do not stretch incorrectly.

## 9. Use conditions for optional content

Conditions let you include pages only when a supported feature is available.

For example, show a Create-related page only when the Create mod is loaded:

```json
{
  "id": "create_intro",
  "title": "Build Something Amazing",
  "text": "Create lets you build machines and automate tasks.",
  "condition": {
    "type": "mod_loaded",
    "modid": "create"
  },
  "buttons": [
    {
      "text": "Continue",
      "action": "next",
      "style": "primary"
    }
  ]
}
```

Use the real mod ID, not its display name. Conditions should describe optional integrations so a guide remains usable in modpacks that do not include every mod.

After adding conditional pages, validate the guide and test with the relevant mod installed and absent. Do not assume a condition works just because the JSON parses.

## 10. Test changes in Minecraft

The mod provides commands for checking and previewing guide packs. Run them in-game:

### Reload guide files

```text
/miso reload
```

Reloads the guide-pack JSON files and textures so you can inspect edits without restarting Minecraft.

### Preview a guide pack

```text
/miso preview [pack_id]
```

Opens the selected guide in Preview Mode. The `[PREVIEW]` badge identifies the preview. Preview Mode is intended to avoid changing the player's completion state or granting rewards.

Examples:

```text
/miso preview my_modpack
```

### Validate a guide pack

```text
/miso validate [pack_id]
```

Checks guide configuration, navigation links, button targets, and textures, and reports issues in chat.

Example:

```text
/miso validate my_modpack
```

Run validation after every significant edit. Fix errors before sharing the pack with players.

## 11. Troubleshooting

### My guide pack does not load

- Confirm the pack folder is inside `config/misos_guide/guidepacks/`.
- Confirm `activeGuidePack` matches the folder name exactly.
- Check that both `manifest.json` and `pages.json` exist.
- Run `/miso validate my_modpack`.
- Check the game log for JSON parsing or asset-loading errors.

### My mascot is not appearing

- Confirm the file exists at the path written in `manifest.json`.
- Check the filename and capitalization.
- Check that the path is relative to the guide-pack folder.
- Confirm the page's `character` matches a mascot ID in the manifest.
- Check the game log and validator output for missing textures.

### My animation does not play

- Confirm the expression name on the page matches the expression definition.
- Confirm every animation frame path exists.
- Check that the frame list contains the intended images.
- Keep frame dimensions and character placement consistent.
- Test again with `/miso reload`.

### A button does nothing

- Confirm its `action` is a supported action.
- For `goto`, confirm `target` matches an existing page ID exactly.
- For `open_menu` and `open_url`, confirm you used the required action-specific parameters from the sample/reference.
- Run the validator and inspect its output.

### My keybinding text shows the wrong thing

- Use the registered keybinding ID inside `{key:...}`.
- Do not put the player's current key (such as J) in place of the keybinding ID.
- Check the keybinding ID against the mod that registers the feature.

### My colors are difficult to read

- Increase contrast between text and background.
- Check both normal and hover button states.
- Test at the GUI scale players are likely to use.

## 12. Creator checklist

Before shipping your guide pack:

- [ ] Give the pack a unique folder ID.
- [ ] Set `activeGuidePack` to that exact ID.
- [ ] Confirm `manifest.json` and `pages.json` are valid JSON.
- [ ] Check every mascot texture path and expression.
- [ ] Make sure every page ID is unique.
- [ ] Make sure every `goto` target exists.
- [ ] Check all button labels and supported actions.
- [ ] Test dynamic keybindings with a changed key and an unbound key.
- [ ] Test conditional pages with the relevant mod installed and absent.
- [ ] Check text and button contrast.
- [ ] Run `/miso reload`.
- [ ] Run `/miso validate <your_pack_id>`.
- [ ] Run `/miso preview <your_pack_id>`.
- [ ] Confirm preview mode does not grant rewards or change completion state.
- [ ] Test the guide at more than one GUI scale and resolution.
- [ ] Keep a backup of the guide pack you are shipping.

---

## The important idea

**The guide engine is the mod; the guide pack is your content.** You should be able to give your modpack its own mascot, dialogue, pages, buttons, and colors by editing the guide-pack files—not by changing Java source code or rebuilding the mod.
