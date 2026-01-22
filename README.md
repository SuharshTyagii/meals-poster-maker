# 🍽️ Meal Poster Maker

A beautiful, responsive Vue 3 application for creating and managing meal categories and recipes. Create your personalized meal posters and export them as PDF!

## ✨ Features

- **Category Management**: Create, edit, and delete food categories (Curries, Breads, Smoothies, etc.)
- **Meal Management**: Add meals with:
  - Name
  - Ingredients list
  - Optional image upload
- **Responsive Design**: Fully responsive with mobile-optimized hamburger menu
- **PDF Export with Preview**: 
  - Live preview before exporting
  - Adjust items per row (1-4 columns)
  - Customize title size (12-24px)
  - Customize text size (10-18px)
  - Choose spacing (compact/normal/relaxed)
  - Real-time preview updates
- **Dev Mode Tools** (when enabled):
  - Export all data as JSON
  - Import data from JSON file
  - Clear all data
- **Local Storage**: All data is saved locally in your browser (no backend needed)
- **Beautiful UI**: Modern design with Tailwind CSS and smooth animations

## 🚀 Getting Started

### Prerequisites
- Node.js (v16 or higher)

### Installation

1. Install dependencies:
```bash
npm install
```

2. Start the development server:
```bash
npm run dev
```

3. Open your browser and navigate to `http://localhost:5173`

### Build for Production

```bash
npm run build
```

The built files will be in the `dist` folder.

## 📱 Usage

1. **Add a Category**: Click "Add Category" and enter a name (e.g., "Curries", "Breads")
2. **Add Meals**: Click "Add Meal", select a category, enter the meal name, list ingredients (one per line), and optionally upload an image
3. **Edit/Delete**: Use the edit (✏️) and delete (🗑️) buttons on categories and meals
4. **Export PDF**: 
   - Click "Export PDF" to open the preview modal
   - Adjust settings with sliders (items per row, font sizes, spacing)
   - Preview updates in real-time
   - Click "Export PDF" button in the modal to download

## 🛠️ Dev Mode

Dev mode is enabled by default in the source code. To enable/disable:

1. Open `src/App.vue`
2. Find the line: `const devMode = ref(true)`
3. Set to `true` to enable dev tools, `false` to hide them

When enabled, a purple 🛠️ button appears in the menu with options to:
- **Export JSON**: Download all your categories and meals as a JSON file
- **Import JSON**: Upload a previously exported JSON file to restore data
- **Clear All Data**: Delete all categories and meals (with confirmation)

## 🎨 Mobile Experience

On mobile devices (screen width < 768px), the menu automatically transforms into a hamburger menu for better usability.

## 💾 Data Storage

All data is stored in your browser's localStorage, so:
- No server or backend needed
- Data persists between sessions
- Data is private to your browser
- Clear browser data will delete your meals

## 🛠️ Technologies Used

- **Vue 3** - Progressive JavaScript framework
- **Tailwind CSS** - Utility-first CSS framework
- **Vite** - Next-generation frontend tooling
- **jsPDF** - PDF generation library
- **html2canvas** - HTML to canvas rendering

## 📄 License

MIT

---

Made with ❤️ for meal planning enthusiasts!
