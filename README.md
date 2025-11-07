# ToastMasters Timer

A single-page web application that helps Toastmasters meeting timers track speeches, provide visual cues, and maintain a running log of performances.

## Features

- **Responsive Timer Display** – Large timer that adapts to screens of any size and is paired with green/yellow/red background cues when thresholds are crossed.
- **Preset Roles** – Quick presets for Table Topics, Evaluations, and Prepared Speeches; switch to Custom to enter your own thresholds.
- **Primary Controls** – Start/Pause, Reset, End Talk, and Download CSV buttons aligned in a single toolbar for both classic and modern themes.
- **Status Progression** – Automatic evaluation of each talk as Under Time, Within Time, or Over Time, with colored pill indicators and matching icons in the log.
- **Accessible Keyboard Shortcut** – Spacebar toggles Start/Pause when focus is not on an input field.
- **Embedded Cue Sound** – Built-in audio ding (with a Test Sound button) so no external file is needed.
- **Log & Export** – Each ended talk stores the speaker, topic, total time, and status; export the log as a CSV file for easy sharing or archival.
- **Modern UI Toggle** – Switch between the original look and a Bootstrap-inspired modern layout via the UI toggle bar.
- **Footer Credit** – A subtle footer credit that links to Tushar Vartak’s LinkedIn profile.

## Usage

1. Open `index.html` locally or host it via GitHub Pages (it’s named lowercase to be picked up automatically).
2. Enter optional speaker and topic information; the display updates live.
3. Pick a preset (or choose Custom) to control green/yellow/red thresholds.
4. Press **Start** (or hit Spacebar) to begin timing. The background changes color as thresholds are crossed.
5. Press **End Talk** when finished to log the result, reset the timer, and add the entry to the table.
6. Use **Download CSV** to export the log.

## Development Notes

- Primary functionality resides in `index.html` with inline CSS and JavaScript; there are no external dependencies.
- `ding.mp3` contains the embedded cue sound; the page converts it to base64 at load time.
- Styling includes both the legacy look and the modern Bootstrap-inspired theme; the toggle button at the top switches between them.

## License

This project is licensed under the Apache License 2.0. See [`LICENSE`](LICENSE) for details.

---
Created by [Tushar Vartak](https://www.linkedin.com/in/tusharvartak/).
