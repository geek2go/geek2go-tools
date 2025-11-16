# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This is a collection of standalone HTML tools for Geek2Go computer repair technicians. Each tool is a self-contained application that runs entirely in the browser with minimal dependencies.

## Architecture

All tools are built using vanilla JavaScript with no frameworks or build processes. The architecture includes:

- **Shared CSS** (`shared.css`): Common styling patterns and reusable components
- **Tool-specific CSS**: Theme variables and unique styles embedded in each HTML file
- **Vanilla JavaScript**: Immediately invoked function expressions for scoping
- **Index page** (`index.html`): Landing page with links to all tools

### Design Patterns

**State Management**: Uses DOM state and localStorage for persistence. No external state libraries.

**Parsing Strategy**: Text parsing uses regular expressions and string manipulation to extract structured data from unformatted text (e.g., PC-Doctor reports).

**UI Pattern**: Two-column layouts with form inputs on the left and live output preview on the right. Forms update output in real-time.

**Data Flow**: User input → JavaScript processing → Text output formatted for copy/paste into POS systems.

## Files

### index.html

Landing page providing access to all repair tools with a clean, dark-themed interface.

### repair-checklist.html

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

### pc-doctor-summarizer.html

Parses PC-Doctor diagnostic reports and generates customer-friendly summaries with optional add-on services.

**Key Features**:
- Simplified parser for hardware specifications (CPU, RAM, Storage, GPU, OS)
- Test result analysis with failure detection
- Preset workflows for common service bundles
- LocalStorage-based defaults system for technician preferences
- Dual-mode operation: works with or without PC-Doctor report

**Parser Functions** (simplified for better maintainability):
- `parseCPU()`: Extracts CPU model using single regex pattern for Intel/AMD processors
- `parseRAM()`: Detects total memory and module configurations with clear, readable logic
- `parseStorage()`: Finds model, capacity, and free space
- `parseGPU()`: Identifies up to 2 GPUs with deduplication
- `parseOS()`: Extracts OS version and build information
- `parseTests()`: Simplified test detection with clear component categorization and failure checking

**Presets** (lines 326-363):
- Quick Clean, New PC Setup, Business Standard, Gaming Build
- Managed Care Lite/Plus packages
- Custom defaults save/load via localStorage

**Test Result Logic**: Avoids false positives by checking for actual failure tokens (`Failed`, `Error`, `Critical`, `Bad`, `SMART Warning`) rather than generic keywords. Special handling for network tests when Ethernet cable is not connected.

## Common Workflows

### Modifying Checklist Templates

Edit the `TEMPLATES` object in `repair-checklist.html`. Each device type has Pre and Post arrays of checklist items.

### Adding Parser Support

For `pc-doctor-summarizer.html`:
1. Create a new `parse*()` function following the simplified pattern used in existing parsers
2. Add parsed data to `buildSummary()` output
3. Test with sample reports using the "Load sample" button

### Updating Shared Styles

Edit `shared.css` to modify styles that apply to all tools. Each tool can override shared styles using CSS custom properties defined in their `:root` selector.

### Adding Service Presets

Add new preset cases in `applyPreset()` function (lines 326-363). Each preset calls `setChecked()` and `setVal()` to configure the form state.

## Styling

**Shared Styles**: `shared.css` contains common patterns (form elements, buttons, cards, grid layouts)

**Theme Customization**: Each tool defines CSS custom properties in `:root`:
- `repair-checklist.html`: Dark theme (slate-900 background, green accents)
- `pc-doctor-summarizer.html`: Light theme (gray-50 background, blue accents)
- `index.html`: Dark theme (slate-900 background, green accents)

## Testing

Open HTML files directly in a browser. No build step required.

For `pc-doctor-summarizer.html`, use the "Load sample" button to populate test data and verify parser functionality.
