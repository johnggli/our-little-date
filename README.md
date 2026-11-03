# our-little-date 🌙

A small, interactive date invitation, built as a single HTML page. It is meant to be sent as a link (for example, over WhatsApp). The person opens it, says yes (the "no" button runs away), picks a day, a time and the kind of date, and sends the answer straight back to you on WhatsApp.

It's plain HTML, CSS and JavaScript. No framework, no build step and no dependencies. It works when you open the file directly and when you host it on GitHub Pages.

> The visible text is in Brazilian Portuguese. Everything is easy to translate or change (see [Customization](#customization)).

## The flow

1. **Opening.** A greeting and a big glowing moon that pulses slowly. Tapping the moon starts the background music and opens the invitation.
2. **Invitation.** "Quer sair comigo de novo?" with a **Sim** and a **Não** button. Every tap on **Não** makes it jump somewhere else on the screen with a new message, and **Sim** grows a little each time. Saying no is not really an option. 😏
3. **Celebration.** Confetti falls and a card appears with a picture and a short thank-you note.
4. **Step 1 of 3, day and time.** A calendar in Portuguese plus a grid of time slots. Past days are disabled. If today is picked, past time slots are disabled too.
5. **Step 2 of 3, kind of date.** Six cards: romantic dinner, Italian restaurant, cozy café, movie + dinner, sunset, or **Outro** ("other"), which shows a text field for her own idea.
6. **Step 3 of 3, summary.** Emojis fall from the top while a summary shows the choices, with three buttons:
   - **Me mandar pelo WhatsApp:** opens WhatsApp with a ready-made message to your number.
   - **Copiar o resumo:** copies the same message to the clipboard.
   - **Recomeçar:** goes back to the moon and resets everything. The music keeps playing.

A small music button stays in the bottom-right corner to pause or resume the song.

## Project structure

```
.
├── index.html            # the whole site: markup, styles and scripts
└── assets/
    ├── music/song.mp3    # background music (loops)
    └── images/photo.jpg  # picture shown on the celebration card and in link previews
```

Inside `index.html`:

| Part | What's there |
|------|--------------|
| `<head>` | Page title, link-preview tags (Open Graph), fonts, favicon |
| `<style>` | All styles. Colors are CSS variables in `:root` at the top |
| `<main>` | One `<section class="screen">` per screen, in flow order |
| `<script id="app-logic">` | `CONFIG` (everything you'll want to tweak) plus the date and message helpers |
| `<script id="app-ui">` | Event handlers and screen navigation |

## Running locally

Open `index.html` in a browser, or serve the folder so it behaves like the real site:

```bash
python3 -m http.server 8000
# then open http://localhost:8000
```

## Publishing on GitHub Pages

1. Push the repository to GitHub.
2. Go to **Settings → Pages**.
3. Under **Build and deployment**, choose **Deploy from a branch**, pick your branch (e.g. `master`) and the `/ (root)` folder, then save.
4. After a minute, the site is live at `https://<your-user>.github.io/<repo-name>/`.
5. Update the base URL used by the link preview (see [Link preview](#link-preview)).

Only `index.html` and `assets/` are needed for the site to work.

## Customization

Most things live in the `CONFIG` object at the top of `<script id="app-logic">`.

### Names and texts

All visible text is plain HTML inside the `<section class="screen">` blocks in `<main>`, one block per screen (`abertura`, `convite`, `comemoracao`, `dia`, `tipo`, `final`). Edit the text there: the greeting, the invitation question, the thank-you note, the step titles and so on.

### The runaway "no" button

- `CONFIG.noMessages`: the messages the button shows, in order. The list repeats when it runs out.
- `CONFIG.noPositions`: the spots it jumps to, as `[x%, y%]` of the screen. It is always kept fully inside the screen.
- How much **Sim** grows: the `#convite[data-yes-step="1…5"] #yesButton` rules in the CSS. It stops growing after 5 taps.

### Time slots

`CONFIG.times`: any list of `"HH:MM"` strings. The grid shows 3 per row.

### Date options

`CONFIG.venues`: each option has an `id`, an `emoji`, a `name` and a short `blurb`. Add, remove or reorder freely. Keep the option with `id: 'outro'` if you want the free-text "other" field.

### WhatsApp number and message

- `CONFIG.whatsappNumber`: digits only, with country code and area code (e.g. `5586999999999`).
- The message itself is built in the `buildMessage()` function, right below `CONFIG`. Edit the lines in the array to change the wording.
- The "Copiar o resumo" button copies the same message.

### Music

Replace `assets/music/song.mp3` with any MP3 and keep the same file name. The song title on the music button is the text `Blue - yung kai` in `index.html`. It appears in the `<em id="musicLabel">` element and inside the `playMusic()` function.

### Picture

Replace `assets/images/photo.jpg` with any image and keep the same file name. A square image works best, since it is shown in a circle.

### Colors and fonts

Change the CSS variables in `:root` at the top of `<style>`: `--ink` (background), `--purple`, `--pink`, `--blue`, `--soft`, the moon colors and so on. Fonts come from Google Fonts (Cormorant Garamond, DM Sans and Caveat). Change the `<link>` in `<head>` and the `--serif`, `--sans` and `--script` variables to swap them.

### Falling emojis and confetti

- `CONFIG.fallingEmojis`: the emojis that fall on the final screen.
- `CONFIG.confettiColors`: the confetti colors used on the celebration card.
- The floating symbols in the background are set in the `fillSky()` function.

### Link preview

When the link is shared on WhatsApp (and most other apps), it shows a preview card built from the tags in `<head>`:

- `<title>` and `og:title`: preview title
- `description` and `og:description`: preview text
- `og:image`: preview picture. This **must be an absolute URL**.

If you publish under a different address, update the base URL in **two places**: the `og:url` and `og:image` tags in `<head>`, and `CONFIG.siteUrl`.

## Good to know

- **Music on phones:** mobile browsers only allow audio to start from a tap, which is why the music begins when the moon is tapped. If a browser still blocks it, the music button shows "toque para ouvir" (also on phones) and plays on tap.
- **Link preview cache:** WhatsApp caches previews. If you change the title, text or image after sharing the link once, the old preview may stick around for a while. Adding a query string (e.g. `?v=2`) to the shared link forces a fresh preview.
- **Reduced motion:** if the device asks for reduced motion, the floating, pulsing and falling animations are turned off.
- **No data is stored or sent anywhere.** The only thing that leaves the page is the WhatsApp message, and only when the button is tapped.
