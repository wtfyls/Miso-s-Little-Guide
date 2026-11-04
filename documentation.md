# Miso's Little Guide — Documentation

A quick guide for modpack creators.

Miso's Little Guide is an onboarding mod. When someone joins a world for the first time, a mascot appears in a speech bubble above the hotbar and walks them through your modpack.

You can change the mascot, dialogue, buttons, themes, sounds, starter gifts and chat links.

## Start here

1. Put the mod JAR in your `mods/` folder.
2. Launch Minecraft and enter a world once.
3. Open `config/misos_guide/guidepacks/`.
4. Edit the `lunchtime/` example pack, or make your own pack (see [Create your own pack](#create-your-own-pack)).
5. Run `/miso reload`, then `/miso preview` to test.

**Can't find the folder?** In CurseForge, right-click your profile and choose **Open Folder**. In Prism Launcher or MultiMC, use **Folder**.

## What happens in-game?

- The intro starts about two seconds after the player enters a world for the first time.
- The guide remembers that it has been seen in `.misos_guide/client_progress.json` in the instance folder. It won't keep popping up on every launch or every world.
- Players can use the on-screen buttons, or press **Enter / Space** to move through dialogue. **Escape** pauses the guide.
- Press **H** (rebindable) or type `/miso` to continue a paused guide.
- Pages can open supported menus. The guide hides while the menu is open and resumes on the same page when it closes.
- Press **H** or use `/miso replay` to replay the guide. Use `/miso links` to show the community links again.
- It works in single-player and multiplayer. If the server doesn't have the mod, the client-side guide still works; server-granted gifts won't be available.

## Where are the files?

After the first launch, you'll find:

```text
config/misos_guide/
├── misos_guide-client.toml
├── misos_guide-common.toml
└── guidepacks/
    └── lunchtime/
        ├── pack.json
        ├── pages.json
        ├── rewards.json
        ├── chat_messages.json
        └── assets/
            ├── mascot/
            ├── icons/
            └── sounds/
```

- **`misos_guide-client.toml`** — client settings, including `activeGuidePack`.
- **`misos_guide-common.toml`** — server settings, including automatic intro and welcome gifts.
- **`pack.json`** — mascot, theme, layout, sounds, default buttons and UI text.
- **`pages.json`** — dialogue, expressions, icons, conditions and page buttons.
- **`rewards.json`** — optional starter gifts.
- **`chat_messages.json`** — optional chat messages and clickable links after the guide.

**Your files are not overwritten** when the game restarts or the mod updates.

## Commands

All `/miso` commands are client-side and don't require OP permissions.

| Command | What it does |
|---|---|
| `/miso` or `/miso replay` | Continue a paused guide or replay it from the start. |
| `/miso reload` | Reload JSON, PNG and OGG files without restarting Minecraft. |
| `/miso preview [pack] [page]` | Preview a pack. You can give a page ID to jump straight to it. |
| `/miso validate [pack]` | Check a pack for errors and show the file, line and field to fix. |
| `/miso export <new_id> [template\|lunchtime]` | Create a new guide-pack folder. |
| `/miso packs` | List loaded packs and show the active one. |
| `/miso reset` | Clear the local first-time-seen record so you can test the intro again. |
| `/miso skip` | Close the open guide. |
| `/miso links` | Show the pack's chat links again. |
| `/miso help` | List the commands. |

**Fast testing loop:** edit a file → run `/miso reload` → run `/miso preview`. Use `/miso validate` if something doesn't work.

## Create your own pack

You can edit `guidepacks/lunchtime/` or create a separate pack:

```text
/miso export my_pack
/miso export my_pack lunchtime
```

The first creates a clean starter pack. The second copies the Lunchtime example for you to customize.

Then open `misos_guide-client.toml` and set:

```toml
activeGuidePack = "my_pack"
```

Make sure this matches the `"id"` in your pack's `pack.json`.

**Important:** Keep the pack `id` the same after release. The mod uses it to track who has seen the guide. You can change `"version"` when you update the pack without resetting player progress.

## Edit the dialogue — `pages.json`

Pages are shown in order. Each page needs a unique `id`.

```json
[
  {
    "id": "welcome",
    "title": "Welcome!",
    "text": "Hi, I'm Pip! Let me show you around.",
    "expression": "happy",
    "animation": "wave"
  },
  {
    "id": "quests",
    "title": "Your Quest Book",
    "subtitle": "Goals & rewards",
    "text": "Your Quest Book gives you goals and rewards as you play.",
    "expression": "excited",
    "icon": { "type": "item", "id": "embersquesting:quest_book" },
    "condition": { "type": "mod_loaded", "modid": "embersquesting" },
    "cardTitle": "Open Quest Book",
    "actionKey": "key.embersquesting.open",
    "targetMenu": "quests"
  }
]
```

### Page fields

| Field | What it does |
|---|---|
| `id` | **Required.** Unique page ID. Use letters, numbers, `_`, `.` or `-`. |
| `title`, `subtitle` | Main heading and optional smaller heading. |
| `text` | A string or an array of paragraph strings. Keep pages short and easy to read. |
| `character` / `mascot` | Mascot speaking on this page. Defaults to `defaultMascot`. |
| `expression` | Mascot expression defined in `pack.json`. |
| `animation` | `talk`, `idle`, `bounce`, `celebrate` or `wave`. |
| `hideMascot` | Set `true` to hide the mascot and center the bubble. |
| `showName` | Set `false` to hide the mascot's name tag. |
| `image` | Show a specific PNG in the mascot's place on this page. |
| `icon` | Item or custom PNG icon beside the title. |
| `cardTitle`, `actionKey`, `targetMenu` | Add a clickable menu card with the player's live keybind. |
| `sound`, `typingSound` | Override the page-turn or typing sound. |
| `condition` | Only show the page when its condition is met. |
| `buttons` | Add custom buttons to this page. |
| `theme` | Override the theme for this page. |

An item icon looks like:

```json
{ "type": "item", "id": "minecraft:wheat" }
```

A custom PNG icon looks like:

```json
{ "type": "image", "path": "assets/icons/star.png", "width": 12, "height": 12 }
```

Supported menu targets:

- `inventory` — vanilla inventory
- `quests` — Ember's Questing or FTB Quests
- `skills` — Pufferfish's Skills
- `cookbook` — Lunchtime's Little Cookbook
- `fieldguide` — Field Guide

### Show the player's actual keybind

Use `{key:key_name}` in a page's `text`, `title` or `subtitle`. It displays the player's real binding, such as `[E]` or `[Not Bound]`.

To find a key name, open the instance's `options.txt` and copy the name after `key_`. For example:

```text
key_key.embersquesting.open:key.keyboard.k
```

Use it in your page like this:

```text
Press {key:key.embersquesting.open} to open your Quest Book.
```

Built-in shortcuts include `{key:inventory}`, `{key:quests}`, `{key:skills}`, `{key:fieldguide}`, `{key:cookbook}`, `{key:map}`, `{key:claims}` and `{key:guide}`.

## Change the mascot — `pack.json`

Put transparent PNGs in `assets/mascot/`. Images can be up to **2048 × 2048** and **10 MB**. For crisp pixel art, use image dimensions that scale cleanly to the display size (for example, 128 × 128 for a 64 × 64 display).

Example:

```json
{
  "schemaVersion": 1,
  "id": "my_pack",
  "title": "Welcome to Pinecrest",
  "defaultMascot": "pip",
  "mascots": {
    "pip": {
      "name": "Pip",
      "width": 64,
      "height": 64,
      "alignment": "left",
      "offsetX": 0,
      "offsetY": 0,
      "defaultExpression": "idle",
      "talkingExpression": "talking",
      "idle": "assets/mascot/pip_idle.png",
      "happy": "assets/mascot/pip_happy.png",
      "talking": {
        "frames": [
          "assets/mascot/pip_talk_1.png",
          "assets/mascot/pip_talk_2.png"
        ],
        "frameDurationMs": 150
      }
    }
  }
}
```

- An expression can be one PNG or an animation with `frames` and `frameDurationMs` (50–10000 ms).
- While dialogue types, the guide uses a talking expression if one is configured, then returns to the page expression.
- Add more mascots under `mascots`. Set `"character": "helper"` on a page to make that mascot speak. Use `"alignment": "right"` to place them on the other side.
- Miso has 12 built-in expressions: `idle`, `welcome`, `happy`, `excited`, `celebrate`, `thinking`, `confused`, `talking`, `blushing`, `sleeping`, `sad` and `cozy`.

## Themes and layout — `pack.json`

Choose a preset:

```json
{ "theme": "cozy" }
```

Available presets:

- `cozy` — warm cream, cocoa outlines, peach and olive accents (default).
- `farmers_delight` — rustic cream, walnut outlines and leafy green buttons.
- `rpg` — parchment, dark ink outlines and gold accents.
- `create` — pale andesite, iron outlines, brass and copper accents.

You can override colors, position and background effects:

```json
{
  "theme": {
    "preset": "rpg",
    "bubbleColor": "#FFF4E0",
    "borderColor": "#4A2E1B",
    "primaryTextColor": "#2B1B10",
    "titleTextColor": "#4A2E1B",
    "accentColor": "#2E6FD8",
    "buttonColor": "#E5C494",
    "buttonHoverColor": "#4F8BE8",
    "buttonPressedColor": "#2458B0",
    "buttonTextColor": "#2B1B10",
    "bubbleOpacity": 1.0,
    "textAlign": "left",
    "showIcons": true,
    "showMascotName": true
  },
  "layout": {
    "position": "above_hotbar",
    "width": 300,
    "offsetX": 0,
    "offsetY": 0,
    "buttonAlign": "right"
  },
  "background": {
    "effect": "blur",
    "blur": "subtle",
    "dimColor": "#000000",
    "dimOpacity": 0.15
  }
}
```

- Layout positions: `above_hotbar`, `center`, `top_center`, `bottom_left`, `bottom_right` or `custom`. For `custom`, set `anchorX` and `anchorY` from `0.0` to `1.0`.
- Background effects: `none`, `dim` or `blur`. Blur strength can be `subtle`, `medium` or `strong`. With Oculus/Iris shaders active, blur switches to a dim overlay.
- More color overrides are available for bubble shading, button states, text, keycaps and progress indicators. Keep the preset and override only the values you want to change.

## Buttons, branching and conditions

By default, pages use **Skip** and **Continue** (or **Finish** on the last page). You can change the default labels and styles in `pack.json`:

```json
{
  "buttons": {
    "showSkip": true,
    "showBack": false,
    "continue": { "text": "Continue", "style": "primary" },
    "skip": { "text": "Skip", "style": "ghost" },
    "finish": { "text": "Let's Go!", "style": "primary" }
  }
}
```

Or add buttons to an individual page (up to 8):

```json
{
  "id": "choose",
  "title": "Where to first?",
  "text": "Pick a topic or jump straight in!",
  "buttons": [
    { "text": "Farming", "action": "goto", "target": "farming", "style": "secondary" },
    { "text": "Open Quests", "action": "open_menu", "target": "quests" },
    { "text": "Join Discord", "action": "open_url", "target": "https://discord.gg/your-invite", "style": "ghost" },
    { "text": "Start Playing", "action": "finish", "style": "primary" }
  ]
}
```

Button actions:

| Action | Result |
|---|---|
| `next` | Go to the next visible page; finish if it's the last one. |
| `goto` | Jump to the page with the matching ID. |
| `back` | Go to the previous page in history. |
| `restart` | Return to the first page. |
| `finish` | Finish the guide and request the welcome gift. |
| `skip` | Close the guide. Gift behavior depends on `giveRewardsOnSkip`. |
| `open_menu` | Open a supported menu, then resume the guide. |
| `open_url` | Open an HTTPS link after Minecraft's confirmation prompt. |

Styles: `primary`, `secondary`, `ghost` and `danger`. Buttons can also have an icon, tooltip, condition and custom colors.

### Conditions

Conditions can be used on pages, buttons and reward items.

```json
{ "type": "mod_loaded", "modid": "create" }
```

Other condition types:

```json
{ "type": "item_exists", "item": "numismatics:spur" }
{ "type": "rewards_available" }
{ "type": "not", "condition": { "type": "mod_loaded", "modid": "create" } }
{ "type": "and", "conditions": [ ... ] }
{ "type": "or", "conditions": [ ... ] }
```

- `mod_loaded` — show only if the mod is installed. Use its actual mod ID.
- `item_exists` — show only if the item exists.
- `rewards_available` — show only if server gifts are enabled and the player can still claim them.
- `not`, `and`, `or` — combine or invert conditions.

## Sounds, gifts and chat links

### Sounds

Set sounds in `pack.json`. You can use built-in sounds, vanilla sounds or your own OGG files in `assets/sounds/`. Custom files must be **Ogg Vorbis** and under **2 MB**. Use an empty string (`""`) to mute a sound.

```json
{
  "sounds": {
    "volume": 1.0,
    "open": "misos_guide:miso_appear",
    "typing": { "sound": "assets/sounds/blip.ogg", "volume": 0.5 },
    "click": "misos_guide:button_click",
    "hover": "misos_guide:button_hover",
    "pageTurn": "misos_guide:chapter_transition",
    "finish": "misos_guide:miso_farewell"
  }
}
```

Built-in sounds: `miso_appear`, `dialogue_blip`, `button_click`, `button_hover`, `chapter_transition` and `miso_farewell` (all under `misos_guide:`).

### Welcome gifts — `rewards.json`

```json
{
  "enabled": true,
  "trigger": "finish_or_skip",
  "oneTime": true,
  "rewards": [
    { "item": "minecraft:bread", "count": 5 },
    {
      "item": "numismatics:spur",
      "count": 8,
      "condition": { "type": "mod_loaded", "modid": "numismatics" }
    }
  ]
}
```

- Gifts are given by the **server**, once per player per world.
- Set `"enabled": false` to disable gifts.
- Set `"trigger": "finish"` if skipping should not give the gift.
- For multiplayer gifts, install the mod on the server and copy the guide pack into the server's `config/misos_guide/guidepacks/` folder too.

### Chat links — `chat_messages.json`

Show clickable community links after the guide closes, or whenever the player runs `/miso links`.

```json
{
  "enabled": true,
  "messages": [
    {
      "id": "community",
      "trigger": "finish",
      "once": true,
      "lines": [
        [{ "text": "Thanks for playing! ", "color": "#F2A65A", "bold": true }],
        [
          {
            "text": "[Join our Discord]",
            "color": "#7289DA",
            "url": "https://discord.gg/your-invite",
            "hoverText": "Chat with us"
          }
        ]
      ]
    }
  ]
}
```

Triggers: `finish`, `skip`, `finish_or_skip` or `manual` (only shown with `/miso links`). Set `"enabled": false` to turn chat messages off.

### Other pack settings

These also go in `pack.json`:

```json
{
  "settings": {
    "autoStart": true,
    "allowReplay": true,
    "giveRewardsOnSkip": true,
    "typingSpeed": 1.0,
    "soundEnabled": true,
    "showProgress": true
  },
  "text": {
    "paused": "I'll wait here. Press {key} or type /miso to continue.",
    "skipped": "See you later! Press {key} or type /miso to open the guide again.",
    "giftReceived": "Starter gift: {items}"
  }
}
```

## Before you publish your modpack

Include:

- [ ] The Miso's Little Guide JAR in `mods/`.
- [ ] `config/misos_guide/`, including your guide pack and both TOML files.
- [ ] Your custom mascot images, icons and sounds inside the guide pack.

**Do not include `.misos_guide/` from the instance's root folder.** It stores local guide progress. If you include it, players may appear to have already seen the intro.

## Quick fixes

| Problem | Try this |
|---|---|
| My edits aren't showing | Run `/miso reload`, then `/miso preview`. |
| The wrong pack loads | Check `activeGuidePack` in `misos_guide-client.toml` and the `id` in `pack.json`. Run `/miso packs`. |
| JSON errors | Run `/miso validate [pack]`. |
| The intro won't start | You may have already seen it on this instance. Run `/miso reset` to test again. |
| Mascot is missing | Check the PNG path, spelling and capitalization. Paths are relative to the pack folder. |
| No gifts on a server | Install the mod server-side and copy the guide pack to the server config folder. |
| Custom sound won't play | Make sure it's an actual Ogg Vorbis `.ogg` file under 2 MB, not an MP3 or WAV renamed to .ogg. |

## Links

- [GitHub](https://github.com/wtfyls/Miso-s-Little-Guide)
- [Discord](https://discord.gg/fTGSfMsjB5)
- [Patreon](https://patreon.com/cw/Fierolam)
