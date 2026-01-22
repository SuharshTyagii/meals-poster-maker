# New Features Added

## 🛠️ Dev Mode Tools

A dev mode flag has been added to enable developer tools for data management.

### How to Enable/Disable
- Open `src/App.vue`
- Find the line: `const devMode = ref(true)`
- Set to `true` to enable, `false` to disable

### Features When Enabled
A purple 🛠️ button appears in the top menu (or hamburger menu on mobile) with the following options:

1. **Export JSON** 📤
   - Downloads all categories and meals as a JSON file
   - File is named with timestamp: `meal-poster-data-[timestamp].json`
   - Includes export timestamp in the data

2. **Import JSON** 📥
   - Upload a previously exported JSON file
   - Replaces all existing data (with confirmation)
   - Validates JSON format before importing
   - Error handling for invalid files

3. **Clear All Data** 🗑️
   - Deletes all categories and meals
   - Double confirmation required
   - Clears localStorage completely

## 📄 PDF Preview & Customization

The PDF export now includes a comprehensive preview modal with real-time customization options.

### Preview Modal Features

1. **Live Preview**
   - Shows exactly what will be exported
   - A4 page format (210mm width)
   - Scrollable preview area
   - Updates in real-time as you adjust settings

2. **Customization Options**

   **Items per Row** (Slider: 1-4)
   - Control how many meals appear in each row
   - Options: 1, 2, 3, or 4 columns
   - Default: 2 columns

   **Title Size** (Slider: 12-24px)
   - Adjust the size of meal names
   - Default: 18px
   - Allows for compact or prominent titles

   **Text Size** (Slider: 10-18px)
   - Adjust the size of ingredient text
   - Default: 14px
   - Makes ingredients more or less prominent

   **Spacing** (Dropdown)
   - Compact: Minimal spacing between categories
   - Normal: Standard spacing (default)
   - Relaxed: More breathing room between sections

3. **Workflow**
   - Click "Export PDF" button in top menu
   - Preview modal opens automatically
   - Adjust settings using sliders and dropdown
   - See changes reflected immediately in preview
   - Click "Export PDF" in modal to download
   - Or click "Cancel" to close without exporting

### Benefits
- No surprises - see exactly what you'll get
- Customize layout for different purposes (printing, sharing, etc.)
- Dense layouts for more content per page
- Spacious layouts for better readability
- Perfect for fine-tuning before final export

## 🎨 UI Improvements

- Added purple gradient button style for dev tools
- Dev menu dropdown with clean styling
- Large, scrollable PDF preview modal (95vh)
- Settings panel with clear labels showing current values
- Real-time value display on sliders
- Improved modal layouts for better UX

## 🔧 Technical Details

- Dev menu closes when clicking outside
- PDF settings are reactive and update preview instantly
- Hidden PDF content div synced with preview
- File input properly resets after import
- JSON export includes metadata (timestamp)
- Comprehensive error handling for imports
- Multiple confirmation dialogs for destructive actions
