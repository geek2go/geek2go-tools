# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This is a collection of standalone HTML tools for Geek2Go computer repair technicians. Each tool is a self-contained single-file application that runs entirely in the browser with no external dependencies.

## Architecture

All tools are built using vanilla JavaScript with no frameworks or build processes. Each HTML file contains:

- Embedded CSS (using CSS custom properties for theming)
- Inline JavaScript (immediately invoked function expressions for scoping)
- Complete UI and business logic in a single file

### Design Patterns

**State Management**: Uses DOM state and localStorage for persistence. No external state libraries.

**Parsing Strategy**: Text parsing uses regular expressions and string manipulation to extract structured data from unformatted text (e.g., PC-Doctor reports).

**UI Pattern**: Two-column layouts with form inputs on the left and live output preview on the right. Forms update output in real-time.

**Data Flow**: User input → JavaScript processing → Text output formatted for copy/paste into POS systems.

## Files

### geek_2_go_repair_checklist_html.html

Interactive repair checklist generator for phones, tablets, and game consoles.

**Key Features**:
- Device-specific checklists with pre/post-repair phases
- Template-driven checklist generation (TEMPLATES object at line 192)
- Markdown-style output with checkbox notation `[x]` for completed, `[ ]` for incomplete
- Live output updates without explicit "generate" action

**Template Structure**:
```javascript
TEMPLATES = {
  Phone: { Pre: [...], Post: [...] },
  Tablet: { Pre: [...], Post: [...] },
  Console: { Pre: [...], Post: [...] }
}
```

**Output Format**: Plain text with headers, metadata, and checkbox lists suitable for ticket systems.

### pc_doctor_report_summarizer_bookmarklet_paste_parser.html

Parses PC-Doctor diagnostic reports and generates customer-friendly summaries with optional add-on services.

**Key Features**:
- Multi-section parser for hardware specifications (CPU, RAM, Storage, GPU, OS)
- Test result analysis with failure detection
- Preset workflows for common service bundles
- LocalStorage-based defaults system for technician preferences
- Dual-mode operation: works with or without PC-Doctor report

**Parser Functions** (lines 172-262):
- `parseCPU()`: Extracts CPU model, cores, threads
- `parseRAM()`: Detects total memory and module configurations (supports DDR4-SDRAM notation)
- `parseStorage()`: Finds model, capacity, and free space
- `parseGPU()`: Identifies up to 2 GPUs with deduplication
- `parseOS()`: Extracts OS version and build information
- `parseTests()`: Categorizes test results by component, handles special cases (e.g., "No Cable" is not a failure)

**Presets** (lines 326-363):
- Quick Clean, New PC Setup, Business Standard, Gaming Build
- Managed Care Lite/Plus packages
- Custom defaults save/load via localStorage

**Test Result Logic**: Avoids false positives by checking for actual failure tokens (`Failed`, `Error`, `Critical`, `Bad`, `SMART Warning`) rather than generic keywords. Special handling for network tests when Ethernet cable is not connected.

## Common Workflows

### Modifying Checklist Templates

Edit the `TEMPLATES` object (geek_2_go_repair_checklist_html.html:192-284). Each device type has Pre and Post arrays of checklist items.

### Adding Parser Support

For pc_doctor_report_summarizer_bookmarklet_paste_parser.html:
1. Create a new `parse*()` function following the pattern at lines 172-215
2. Add parsed data to `buildSummary()` output (lines 283-323)
3. Test with sample reports using the "Load sample" button

### Adding Service Presets

Add new preset cases in `applyPreset()` function (lines 326-363). Each preset calls `setChecked()` and `setVal()` to configure the form state.

## Styling

Both tools use CSS custom properties for theming:
- Dark theme: geek_2_go_repair_checklist_html.html (slate-900 background)
- Light theme: pc_doctor_report_summarizer_bookmarklet_paste_parser.html (gray-50 background)

Color variables are defined in `:root` (lines 8-17 and line 8 respectively).

## Testing

Open HTML files directly in a browser. No build step required.

For pc_doctor_report_summarizer_bookmarklet_paste_parser.html, use the "Load sample" button to populate test data.
