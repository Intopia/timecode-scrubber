# Timecode Scrubber

A single-page, browser-based tool that converts caption files (SRT or VTT) into a clean, timecode-free transcript, delivered as a downloadable Word document.

## What it does

Upload an `.srt` or `.vtt` caption file, and the tool strips out everything that isn't spoken text — timecodes, cue index numbers, VTT headers, and inline formatting tags — then reassembles each caption cue as a single line of text in a Word document you can download.

For example, this caption cue:

```
00:00:00.462 --> 00:00:04.721
To exit NVDA, press the
NVDA modifier key plus Q.
```

becomes a single transcript line:

```
To exit NVDA, press the NVDA modifier key plus Q.
```

## Features

- Accepts both `.srt` and `.vtt` caption files
- Removes timecodes, SRT index numbers, VTT `WEBVTT`/`NOTE` headers, and inline tags (e.g. `<i>`, `<b>`, `<c>`, karaoke-style timestamps)
- Collapses multi-line cues into a single line per cue
- Generates a genuine `.docx` Word file (not just a renamed text file)
- Shows the output filename and a dedicated download button
- Simple error handling for missing files, unsupported extensions, or empty caption content
- No server or backend required — everything runs client-side in the browser

## How it was built

- **Structure**: a single self-contained HTML file with inline CSS and JavaScript — no build step, no installation.
- **Parsing**: plain JavaScript scans the caption file line by line, using the presence of `-->` to identify timecode lines and treating the lines that follow (until a blank line) as that cue's text.
- **Word file generation**: Word's `.docx` format is a ZIP archive containing XML files (the OOXML format). The tool builds the required XML parts (`[Content_Types].xml`, relationships, `docProps`, and `word/document.xml` with one paragraph per caption cue) and zips them client-side using [JSZip](https://stuk.github.io/jszip/), loaded from a CDN.
- **Download flow**: the generated `.docx` is turned into a `Blob`, given an object URL, and offered to the user via a styled download button.

## Usage

1. Open `caption-to-transcript.html` in a browser.
2. Click "Select a caption file" and choose an `.srt` or `.vtt` file.
3. Click "Convert to document transcript."
4. Review the generated filename and click "Download" to save the `.docx` transcript.

## Limitations

- Designed for standard SRT/VTT cue structures; heavily nonstandard or malformed files may parse imperfectly.
- Output formatting is plain paragraphs — no headings, speaker labels, or styling are applied automatically.
