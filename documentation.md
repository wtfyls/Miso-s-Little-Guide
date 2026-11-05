# Miso's Little Guide — Documentation

A quick guide for modpack creators. No Java needed.

Miso's Little Guide lets you make a welcome guide for your modpack: dialogue, a mascot, buttons, sounds, starter gifts and community links. You can use Miso or bring your own character.

## 1. Get started

1. Put the mod JAR in your `mods/` folder.
2. Launch Minecraft and enter a world once.
3. Open `config/misos_guide/guidepacks/`.
4. Edit the included `lunchtime/` example, or make your own pack.
5. Run `/miso reload`, then `/miso preview` to test it.

Can't find your instance folder? In CurseForge, right-click the profile and choose **Open Folder**. In Prism Launcher or MultiMC, choose **Folder**.

## 2. Where everything goes

After the first launch, the folder should look like this:

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

| File | What it's for |
|---|---|
| `misos_guide-client.toml` | Client options and the active guide pack. |
| `misos_guide-common.toml` | Server options, intro settings and welcome gifts. |
| `pack.json` | Pack name, mascot, theme, layout, buttons and sounds. |
| `pages.json` | The actual dialogue and page buttons. |
| `rewards.json` | Optional starter gifts. |
| `chat_messages.json` | Optional clickable links shown in chat. |

Your guide-pack files are not overwritten when Minecraft restarts or the mod updates.

## 3. Make your own guide pack

You can either edit the example or export a fresh pack:

```text
/miso export my_pack
/miso export my_pack lunchtime
```

The first creates a clean starter pack. The second copies the example.

Open `misos_guide-client.toml` and set:

```toml
activeGuidePack = "my_pack"
```

The value must match the `"id"` in your pack's `pack.json`.

**Keep the pack ID the same after publishing.** It's used to remember who has already seen the guide. You can change the pack's `version` when you update it.

## 4. Write your pages

Edit `pages.json`. Pages appear in order, and each page needs its own unique `id`.

Here's a small example:

```json
[
  {
    "id": "welcome",
    "title": "Welcome!",
    "text": "Hey! I'm Miso. Let me show you around.",
    "expression": "happy",
    "animation": "wave"
  },
  {
    "id": "quests",
    "title": "Your Quest Book",
    "text": "Use your quest book to find things to do.",
    "condition": { "type": "mod_loaded", "modid": "embersquesting" }
  }
]
```

Useful page fields:

| Field | What it does |
|---|---|
| `id` | Required. Unique page ID. |
| `title`, `subtitle` | Heading and smaller heading. |
| `text` | Dialogue. Use a short string or an array of paragraphs. |
| `expression` | Mascot expression from `pack.json`. |
| `animation` | `talk`, `idle`, `bounce`, `celebrate` or `wave`. |
| `hideMascot` | Hides the mascot on this page. |
| `icon` | Item icon or a PNG from your pack. |
| `condition` | Shows the page only when a condition is true. |
| `buttons` | Custom buttons for this page. |

### Show a keybind

Use `{key:key_name}` in a page's title, subtitle or text. It shows the player's actual keybind.

For example:

```text
Press {key:key.embersquesting.open} to open your Quest Book.
```

You can find key names in the instance's `options.txt`. Copy the name after `key_`.

Built-in shortcuts include `{key:inventory}`, `{key:quests}`, `{key:skills}`, `{key:fieldguide}`, `{key:cookbook}`, `{key:map}`, `{key:claims}` and `{key:guide}`.

## 5. Change the mascot and look

Put transparent PNGs in `assets/mascot/`. Keep paths relative to your guide-pack folder. Images can be up to 2048 × 2048 and 10 MB.

The mascot is set in `pack.json`. Here's the basic shape:

```json
{
  "schemaVersion": 1,
  "id": "my_pack",
  "title": "Welcome!",
  "defaultMascot": "miso",
  "mascots": {
    "miso": {
      "name": "Miso",
      "width": 64,
      "height": 64,
      "alignment": "left",
      "defaultExpression": "idle",
      "talkingExpression": "talking",
      "idle": "assets/mascot/miso_idle.png",
      "happy": "assets/mascot/miso_happy.png",
      "talking": {
        "frames": [
          "assets/mascot/miso_talk_1.png",
          "assets/mascot/miso_talk_2.png"
        ],
        "frameDurationMs": 150
      }
    }
  }
}
```

You can add more mascots under `mascots`. Use `"character": "helper"` on a page to make another mascot speak. Set `"alignment": "right"` to place them on the other side.

### Themes

The built-in themes are:

- `cozy` — warm cream, cocoa and soft green.
- `farmers_delight` — rustic cream and leafy green.
- `rpg` — parchment and gold.
- `create` — andesite, brass and copper.

Set one in `pack.json`:

```json
{ "theme": "cozy" }
```

You can override individual colors and layout values in `pack.json`. Keep the preset and only change what you need.

## 6. Add buttons and conditions

Pages have **Skip** and **Continue** buttons by default. You can add your own buttons in a page:

```json
{
  "id": "choose",
  "title": "Where to first?",
  "text": "Pick a topic or jump straight in!",
  "buttons": [
    { "text": "Farming", "action": "goto", "target": "farming" },
    { "text": "Open Quests", "action": "open_menu", "target": "quests" },
    { "text": "Join Discord", "action": "open_url", "target": "https://discord.gg/your-invite" },
    { "text": "Let's go!", "action": "finish" }
  ]
}
```

Button actions include `next`, `goto`, `back`, `restart`, `finish`, `skip`, `open_menu` and `open_url`.

You can also show a page or button only when a mod or item exists:

```json
{ "type": "mod_loaded", "modid": "create" }
```

Other condition types include `item_exists`, `rewards_available`, `not`, `and` and `or`.

Supported menu targets include `inventory`, `quests`, `skills`, `cookbook` and `fieldguide`. The relevant mod or menu needs to be installed.

## 7. Sounds, starter gifts and chat links

- Put custom OGG Vorbis sounds in `assets/sounds/`. Keep each file under 2 MB.
- Add optional starter items in `rewards.json`. Gifts are given by the server, once per player per world.
- Add optional clickable community links in `chat_messages.json`. Players can see them after the guide or with `/miso links`.

For multiplayer starter gifts, install the mod on the server and copy the guide pack into the server's `config/misos_guide/guidepacks/` folder too.

## 8. Commands

| Command | What it does |
|---|---|
| `/miso` or `/miso replay` | Continue a paused guide or replay it. |
| `/miso reload` | Reload JSON, PNG and OGG files without restarting. |
| `/miso preview [pack] [page]` | Preview a pack or jump to a page. |
| `/miso validate [pack]` | Check a pack for errors. |
| `/miso export <id> [template]` | Create a new guide pack. |
| `/miso packs` | List loaded packs. |
| `/miso reset` | Reset the local first-time-seen record for testing. |
| `/miso skip` | Close the open guide. |
| `/miso links` | Show the pack's chat links again. |
| `/miso help` | List commands. |

**Quick testing loop:** edit a file → `/miso reload` → `/miso preview`. Run `/miso validate` if something looks wrong.

## 9. Before you add it to your modpack

- [ ] Include the mod JAR in `mods/`.
- [ ] Include `config/misos_guide/` and your guide pack.
- [ ] Include your mascot images, icons and sounds.
- [ ] Test the guide in a fresh instance.

**Don't include `.misos_guide/` from your instance folder.** That's where local player progress is stored. Including it can make players look like they've already seen the intro.

## 10. Quick fixes

| Problem | Try this |
|---|---|
| My changes aren't showing | Run `/miso reload`, then `/miso preview`. |
| The wrong pack loads | Check `activeGuidePack` and the pack `id`. |
| JSON errors | Run `/miso validate [pack]`. |
| The intro won't play | You may have already seen it. Run `/miso reset` to test again. |
| Mascot is missing | Check the PNG path, spelling and capitalization. |
| Server gifts don't work | Install the mod on the server and copy the guide pack to its config folder. |
| A sound won't play | Use an OGG Vorbis file, not an MP3 or WAV renamed to `.ogg`. |

Still stuck? [Open a GitHub ticket](https://github.com/wtfyls/Miso-s-Little-Guide/issues/new) and include your Minecraft version, mod version and logs.

---

[CurseForge](https://www.curseforge.com/minecraft/mc-mods/misos-little-guide) · [GitHub issues](https://github.com/wtfyls/Miso-s-Little-Guide/issues) · [Lunchtime Discord](https://discord.gg/68yhDdFpnx)
