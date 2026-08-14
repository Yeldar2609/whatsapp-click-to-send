# WhatsApp Click-to-Send

A single-file, zero-dependency HTML page that turns a list of phone numbers into a
clean click-to-send worklist for WhatsApp: it detects each number's country from its
dialing code, flags numbers that are missing one, personalizes your message per person,
and tracks who you've already opened.

**Live demo:** https://yeldar2609.github.io/whatsapp-click-to-send/

It does **not** send anything automatically. Every message is one deliberate click plus a
paste by you. See [Limits](#limits).

---

## Use it

Open the live demo, or download `index.html` and double-click it. No install, no build
step, no server, no account.

1. **Write your message.** Put `{name}` anywhere and it's replaced with each recipient's
   name (or `there` when the name is blank).
2. **Paste your recipients**, one per line, as `number,name`. The name is optional:

   ```
   77011234567,Aigerim
   12025551234,John
   919812345678,Priya
   971501234567,Ahmed
   77019876543
   ```

   Anything that isn't a digit is stripped, so `+7 (701) 123-45-67` works fine.
3. **Click "Build list."** You get a table with a country badge per number, summary
   chips, a country filter and a sort control. Bad numbers are flagged and can't be
   clicked.
4. **For each person:** `Copy message` → `Open chat` → click WhatsApp's message box →
   `Ctrl+V` (`Cmd+V` on Mac) → Enter.

   Copy-paste rather than a pre-filled `wa.me/?text=` link is intentional: pre-filled
   links mangle emoji and long messages in several WhatsApp clients.

Rows dim once you've opened them so you can tell where you stopped. `Reset sent marks`
clears that.

## Number format — the one thing that trips everyone up

Every number must include its **country code, without a `+`**:

| Country | Correct | Wrong |
| --- | --- | --- |
| Kazakhstan | `77011234567` | `87011234567` (local trunk prefix) |
| US | `12025551234` | `2025551234` |
| India | `919812345678` | `9812345678` |
| UAE | `971501234567` | `0501234567` |

A locally-formatted number **cannot be repaired automatically** — `8701…` and `202…` are
each ambiguous across countries, so guessing would risk messaging a stranger. Those get
flagged `no country code` and are excluded until you fix them by hand.

## Limits

- **Nothing is sent for you.** By design. One copy + one chat open + your own paste and
  Enter, per person. This is a worklist, not a bulk sender.
- **You need WhatsApp already logged in** on the same browser (WhatsApp Web) or the
  desktop app, so `wa.me` links can open a chat.
- **Country detection is a prefix heuristic**, not a phone-number parser. The dial-code
  table (~110 entries in `index.html`) covers common countries but is not exhaustive; an
  unlisted code is flagged rather than guessed. `+7` is split by the rule *starts with
  `77` → Kazakhstan, otherwise Russia*, which is right for mobile numbers but not a full
  numbering-plan implementation. If you need real validation, swap `detectCountry()` for
  a library like `libphonenumber-js`.
- **Validation is shallow:** 8–15 digits plus a recognized country code. It cannot tell
  you a well-formed number is real, active, or on WhatsApp.
- **No persistence.** Your message, list and "sent" marks live in the page only — a
  reload clears everything. Keep the source list in your own file.
- **No data leaves your browser.** There's no backend, no analytics, no network call
  except the `wa.me` link you click. That also means no logs and no delivery reports.
- **Duplicates aren't detected**; the same number twice gives you two rows.

## Responsible use

Only message people who gave you their number and expect to hear from you. WhatsApp
restricts and bans accounts for unsolicited bulk messaging, and that's on the sender, not
the tool. The manual, one-at-a-time flow here is a feature.

## Customize

Everything is in `index.html` — one file, plain HTML/CSS/JS, no dependencies.

- **Add a country:** add e.g. `["250","Rwanda"]` to the `DIAL_CODES` array. Longest prefix
  wins, so ordering doesn't matter.
- **Change the look:** the CSS variables in `:root` at the top control the whole theme.
- **Change the default message/recipients:** edit the two `<textarea>` bodies.

## License

MIT — see [LICENSE](LICENSE).
