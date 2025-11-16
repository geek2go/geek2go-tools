# Geek2Go Repair Tools

A collection of browser-based tools for computer repair technicians. No installation required - just open the HTML files in your browser.

## Tools

### 1. Repair Checklist Generator
**File:** `Ticket Notes/geek_2_go_repair_checklist_html.html`

Interactive checklist for documenting phone, tablet, and game console repairs.

**Features:**
- Device-specific checklists (Phone, Tablet, Console)
- Pre-diagnostics and post-repair phases
- Customizable fields for customer info, device model, accessories
- Common ticket phrases for quick notes
- Copy-to-clipboard functionality
- Live output preview

**Use:** Open the file in a browser, fill in the form, and copy the generated checklist into your POS system.

### 2. PC-Doctor Report Summarizer
**File:** `Ticket Notes/pc_doctor_report_summarizer_bookmarklet_paste_parser.html`

Converts PC-Doctor diagnostic reports into customer-friendly summaries.

**Features:**
- Parses hardware specs (CPU, RAM, Storage, GPU, OS)
- Analyzes test results and identifies issues
- Add-on services tracking (Malwarebytes, Windows Updates, Software Installation)
- Preset workflows for common service bundles
- Works with or without PC-Doctor report
- Save/load your default settings

**Use:** Paste a PC-Doctor report, check any additional work performed, and generate a professional summary for your ticket.

## Usage

These are standalone HTML files with no dependencies:

1. **Download** the HTML files you need
2. **Open** them in any modern web browser (Chrome, Firefox, Edge, Safari)
3. **Bookmark** them for quick access
4. No internet connection required after downloading

## Live Demo

Visit the [GitHub Pages site](https://YOUR-USERNAME.github.io/geek2go-tools/) to use the tools online without downloading.

## Development

All tools are built with vanilla JavaScript - no frameworks, no build process. To modify:

1. Open the HTML file in a text editor
2. Edit the embedded CSS (in `<style>` tags) or JavaScript (in `<script>` tags)
3. Refresh your browser to see changes

See `CLAUDE.md` for detailed architecture documentation.

## Contributing

Contributions welcome! Feel free to:
- Report bugs or request features via Issues
- Submit pull requests with improvements
- Share feedback on the tools

## License

MIT License - free to use and modify for your repair business.
