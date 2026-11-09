# Contributing

Thanks for your interest in *La Mort aux Trousses!*

**Contributions are welcome, but please expect slow replies.** This is a personal side project maintained in spare time. Issues and pull requests may sit for a while, and some may never get a response. Nothing is wrong when that happens. If you want to take the game somewhere new, forking it is perfectly fine and encouraged under the license.

## Good ways to help

- **Bug reports:** the device and browser, the level and route height, and what you expected vs. what happened. A screenshot or short screen recording helps a lot.
- **Feel and balance:** a jump that seems impossible, a hazard you can't read, a level that drags.
- **Translations:** fixes to the French or English text, or a new language.
- **New ideas:** please open an issue to discuss before building something large, so your effort isn't wasted.

## Ground rules for changes

- **Keep it one file.** The game is a single `index.html` with no build step and no dependencies. Please don't add frameworks, bundlers or external assets. Draw in code, synthesise sounds, inline everything.
- **Test on a phone and a desktop.** Portrait touch play is the main target, but keyboard play should keep working.
- **Keep both languages in sync.** Every on-screen string goes through the `I18N` table and `T()`. Add any new text in both `fr` and `en`.
- **Keep changes small and focused.** One fix or feature per pull request is much easier to review.
- **Match the existing style:** plain JavaScript, short helpers, labelled `// ===== section =====` blocks. See [docs/DESIGN.md](docs/DESIGN.md) for a map of the file.

## License

By contributing, you agree that your contributions are licensed under the [Apache License 2.0](LICENSE), the same as the rest of the project.
