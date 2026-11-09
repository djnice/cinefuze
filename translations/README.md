# CineFuze translations

The interface texts of CineFuze 1.3 as Android string resources:

- `en/` – the English source texts
- `sv/` – Swedish (machine translated, to be reviewed)
- `zh-CN/` – Simplified Chinese (machine translated, to be reviewed)

Each folder has `strings.xml`, `strings_audio.xml` and `strings_help.xml`. To review a language, edit the text between
the tags and keep the rest as it is:

- the `name="..."` attributes,
- the placeholders (`%1$s`, `%2$d`, `%%`),
- the escapes: an apostrophe is written `\'`, a line break `\n`.

Send the corrected files in an issue or a pull request; they go into the next release.
