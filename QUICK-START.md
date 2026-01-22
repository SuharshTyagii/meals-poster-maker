# Quick Start Guide - New Features

## 1️⃣ Enabling Dev Mode

The dev mode is **already enabled** by default! You'll see a purple 🛠️ button in your menu bar.

To disable it later:
```javascript
// In src/App.vue, line ~11
const devMode = ref(false)  // Change true to false
```

---

## 2️⃣ Using Dev Tools

### Export Your Data
1. Click the purple 🛠️ button
2. Click "📤 Export JSON"
3. A file will download: `meal-poster-data-[timestamp].json`
4. Keep this as a backup!

### Import Data
1. Click the purple 🛠️ button
2. Click "📥 Import JSON"
3. Select your previously exported JSON file
4. Confirm to replace current data
5. Your data is restored!

### Clear Everything
1. Click the purple 🛠️ button
2. Click "🗑️ Clear All Data"
3. Confirm TWICE (it's permanent!)
4. All data is deleted

---

## 3️⃣ Using PDF Preview & Export

### Step-by-Step Process

1. **Open Preview**
   - Click "Export PDF" button (blue button in menu)
   - Preview modal opens with your meals displayed

2. **Customize Layout**
   
   **Items per Row** slider (1-4):
   - `1`: Single column (great for detailed lists)
   - `2`: Two columns (default, balanced)
   - `3`: Three columns (compact)
   - `4`: Four columns (very dense)

   **Title Size** slider (12-24px):
   - Smaller values: More content per page
   - Larger values: More readable titles
   - Default: 18px

   **Text Size** slider (10-18px):
   - Smaller values: Fit more ingredients
   - Larger values: Better readability
   - Default: 14px

   **Spacing** dropdown:
   - `Compact`: Minimal space, max content
   - `Normal`: Balanced (default)
   - `Relaxed`: More white space, easier to read

3. **Watch the Preview**
   - As you adjust sliders, the preview updates instantly
   - Scroll through the preview to see all pages
   - The preview shows exact A4 page size

4. **Export or Cancel**
   - Click "📄 Export PDF" to download your file
   - Or click "Cancel" to close without saving

---

## 💡 Pro Tips

### For Printing
- Use **2 items per row** with **normal spacing**
- Increase **title size to 20px** for clarity
- Use **text size 14-16px** for readability

### For Digital Sharing
- Use **3-4 items per row** for compact layout
- **Compact spacing** to fit more on screen
- Smaller fonts (title: 16px, text: 12px)

### For Detailed Reference
- Use **1 item per row**
- **Relaxed spacing**
- Larger fonts (title: 22px, text: 16px)

### Data Management
1. **Regular Backups**: Export JSON weekly
2. **Before Big Changes**: Always export first
3. **Testing**: Import on a different browser to test
4. **Sharing**: Share your JSON files with friends!

---

## 🎯 Example Workflow

1. Create some categories (Breakfast, Lunch, Dinner)
2. Add meals with ingredients and photos
3. **Export JSON** as backup
4. Click **Export PDF** to preview
5. Try different layouts:
   - 2 columns, normal spacing → save as "meal-poster-normal.pdf"
   - 4 columns, compact spacing → save as "meal-poster-dense.pdf"
6. Choose your favorite layout!

---

## 🚀 Your App is Ready!

Visit: **http://localhost:5173/**

Everything is working and ready to use! Start creating your meal posters! 🍽️
