<template>
  <div
    class="min-h-screen bg-gradient-to-br from-orange-50 via-white to-green-50"
  >
    <!-- Top Bar -->
    <nav class="bg-white shadow-lg sticky top-0 z-50">
      <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
        <div class="flex justify-between items-center h-16">
          <div class="flex items-center">
            <h1
              class="text-2xl font-bold text-transparent bg-clip-text bg-gradient-to-r from-orange-500 to-green-500"
            >
              🍽️ Meal Poster Maker
            </h1>
          </div>

          <!-- Desktop Menu -->
          <div class="hidden md:flex space-x-4">
            <button @click="toggleFilters" class="btn-filter">
              <span class="mr-2">🔍</span> Filters
            </button>
            <button @click="openCategoryModal()" class="btn-primary">
              <span class="mr-2">📁</span> Add Category
            </button>
            <button @click="openMealModal()" class="btn-secondary">
              <span class="mr-2">🍽️</span> Add Meal
            </button>
            <button @click="openIngredientModal()" class="btn-ingredient">
              <span class="mr-2">🥕</span> Add Ingredient
            </button>
            <button @click="openPDFPreviewModal" class="btn-accent">
              <span class="mr-2">📄</span> Export PDF
            </button>
            <button
              v-if="devMode"
              @click="toggleDevMenu"
              class="btn-dev"
              title="Dev Tools"
            >
              🛠️
            </button>
          </div>

          <!-- Mobile Hamburger -->
          <div class="md:hidden">
            <button
              @click="menuOpen = !menuOpen"
              class="text-gray-700 hover:text-orange-500 focus:outline-none"
            >
              <svg
                class="h-6 w-6"
                fill="none"
                stroke="currentColor"
                viewBox="0 0 24 24"
              >
                <path
                  v-if="!menuOpen"
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  stroke-width="2"
                  d="M4 6h16M4 12h16M4 18h16"
                />
                <path
                  v-else
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  stroke-width="2"
                  d="M6 18L18 6M6 6l12 12"
                />
              </svg>
            </button>
          </div>
        </div>
      </div>

      <!-- Mobile Menu -->
      <div v-if="menuOpen" class="md:hidden bg-white border-t border-gray-200">
        <div class="px-4 py-3 space-y-2">
          <button
            @click="
              toggleFilters();
              menuOpen = false;
            "
            class="btn-filter w-full"
          >
            <span class="mr-2">🔍</span> Filters
          </button>
          <button
            @click="
              openCategoryModal();
              menuOpen = false;
            "
            class="btn-primary w-full"
          >
            <span class="mr-2">📁</span> Add Category
          </button>
          <button
            @click="
              openMealModal();
              menuOpen = false;
            "
            class="btn-secondary w-full"
          >
            <span class="mr-2">🍽️</span> Add Meal
          </button>
          <button
            @click="
              openIngredientModal();
              menuOpen = false;
            "
            class="btn-ingredient w-full"
          >
            <span class="mr-2">🥕</span> Add Ingredient
          </button>
          <button
            @click="
              openPDFPreviewModal();
              menuOpen = false;
            "
            class="btn-accent w-full"
          >
            <span class="mr-2">📄</span> Export PDF
          </button>
          <button
            v-if="devMode"
            @click="
              toggleDevMenu();
              menuOpen = false;
            "
            class="btn-dev w-full"
          >
            🛠️ Dev Tools
          </button>
        </div>
      </div>

      <!-- Filters Section -->
      <div v-if="showFilters" class="border-t border-gray-200 bg-gray-50">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-4">
          <!-- Search Bar -->
          <div class="mb-4">
            <input
              v-model="searchQuery"
              type="text"
              placeholder="Search meals or ingredients..."
              class="input-field w-full"
            />
          </div>

          <!-- Category Filters -->
          <div class="mb-4">
            <h3 class="text-sm font-semibold text-gray-700 mb-2">
              Filter by Categories:
            </h3>
            <div class="flex flex-wrap gap-2">
              <label
                v-for="category in categories"
                :key="category.id"
                class="inline-flex items-center px-3 py-2 rounded-lg cursor-pointer transition-all"
                :class="
                  selectedCategories.includes(category.id)
                    ? 'bg-orange-500 text-white'
                    : 'bg-white text-gray-700 border border-gray-300'
                "
              >
                <input
                  type="checkbox"
                  :value="category.id"
                  v-model="selectedCategories"
                  class="mr-2"
                />
                {{ category.name }}
              </label>
            </div>
          </div>

          <!-- Ingredient Filters -->
          <div class="mb-4">
            <div class="flex justify-between items-center mb-2">
              <h3 class="text-sm font-semibold text-gray-700">
                Filter by Ingredients:
              </h3>
              <div class="space-x-2">
                <button
                  @click="selectAllIngredients"
                  class="text-xs text-blue-600 hover:text-blue-800"
                >
                  Select All
                </button>
                <button
                  @click="deselectAllIngredients"
                  class="text-xs text-red-600 hover:text-red-800"
                >
                  Deselect All
                </button>
              </div>
            </div>
            <div
              class="max-h-48 overflow-y-auto bg-white rounded-lg border border-gray-300 p-3"
            >
              <div class="grid grid-cols-2 sm:grid-cols-3 md:grid-cols-4 gap-2">
                <label
                  v-for="ingredient in sortedIngredients"
                  :key="ingredient.id"
                  class="inline-flex items-center text-sm cursor-pointer hover:bg-gray-50 px-2 py-1 rounded"
                >
                  <input
                    type="checkbox"
                    :value="ingredient.name"
                    v-model="selectedIngredients"
                    class="mr-2"
                  />
                  {{ ingredient.name }}
                </label>
              </div>
            </div>
          </div>

          <!-- Filter Summary -->
          <div class="mt-3 text-sm text-gray-600">
            Showing {{ filteredMeals.length }} of {{ meals.length }} meals
            <span
              v-if="selectedIngredients.length < ingredients.length"
              class="text-orange-600"
            >
              ({{ ingredients.length - selectedIngredients.length }} ingredients
              filtered out)
            </span>
          </div>
        </div>
      </div>

      <!-- Dev Menu Dropdown -->
      <div
        v-if="showDevMenu"
        class="absolute right-4 top-20 bg-white shadow-xl rounded-lg border border-gray-200 p-4 z-50 w-64"
      >
        <h3 class="font-bold text-gray-800 mb-3">🛠️ Dev Tools</h3>
        <div class="space-y-2">
          <button
            @click="exportJSON"
            class="w-full text-left px-3 py-2 hover:bg-gray-100 rounded flex items-center"
          >
            <span class="mr-2">💾</span> Export JSON
          </button>
          <label
            class="w-full text-left px-3 py-2 hover:bg-gray-100 rounded flex items-center cursor-pointer"
          >
            <span class="mr-2">📥</span> Import JSON
            <input
              type="file"
              @change="importJSON"
              accept=".json"
              class="hidden"
            />
          </label>
          <button
            @click="clearAllData"
            class="w-full text-left px-3 py-2 hover:bg-red-100 text-red-600 rounded flex items-center"
          >
            <span class="mr-2">🗑️</span> Reset to default
          </button>
        </div>
      </div>
    </nav>

    <!-- Main Content -->
    <main class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-8">
      <div v-if="categories.length === 0" class="text-center py-20">
        <div class="text-6xl mb-4">🍽️</div>
        <h2 class="text-2xl font-semibold text-gray-700 mb-2">
          No categories yet
        </h2>
        <p class="text-gray-500 mb-6">
          Start by creating your first food category!
        </p>
        <button @click="openCategoryModal()" class="btn-primary">
          <span class="mr-2">📁</span> Create Category
        </button>
      </div>

      <div v-else class="space-y-8">
        <div
          v-for="category in visibleCategories"
          :key="category.id"
          class="category-card"
        >
          <div class="flex justify-between items-center mb-4">
            <h2 class="text-2xl font-bold text-gray-800">
              {{ category.name }}
            </h2>
            <div class="flex space-x-2">
              <button
                @click="openCategoryModal(category)"
                class="btn-icon"
                title="Edit Category"
              >
                ✏️
              </button>
              <button
                @click="deleteCategory(category.id)"
                class="btn-icon text-red-600 hover:bg-red-50"
                title="Delete Category"
              >
                🗑️
              </button>
            </div>
          </div>

          <div
            v-if="getFilteredCategoryMeals(category.id).length === 0"
            class="text-center py-8 bg-gray-50 rounded-lg border-2 border-dashed border-gray-300"
          >
            <p class="text-gray-500 mb-3">
              {{
                getCategoryMeals(category.id).length === 0
                  ? "No meals in this category yet"
                  : "No meals match your filter"
              }}
            </p>
            <button
              v-if="getCategoryMeals(category.id).length === 0"
              @click="openMealModal(null, category.id)"
              class="btn-sm"
            >
              Add Meal
            </button>
          </div>

          <div
            v-else
            class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-4"
          >
            <div
              v-for="meal in getFilteredCategoryMeals(category.id)"
              :key="meal.id"
              class="meal-card"
            >
              <div
                v-if="meal.image"
                class="mb-3 rounded-lg overflow-hidden h-40 bg-gray-200"
              >
                <img
                  :src="meal.image"
                  :alt="meal.name"
                  class="w-full h-full object-cover"
                />
              </div>
              <h3 class="text-lg font-semibold text-gray-800 mb-2">
                {{ meal.name }}
              </h3>
              <div class="mb-3">
                <p class="text-sm font-medium text-gray-600 mb-1">
                  Ingredients:
                </p>
                <div class="flex flex-wrap gap-1">
                  <span
                    v-for="(item, idx) in meal.items"
                    :key="idx"
                    class="ingredient-tag"
                  >
                    {{ item }}
                  </span>
                </div>
              </div>
              <div class="flex space-x-2 mt-4">
                <button
                  @click="openMealModal(meal, category.id)"
                  class="btn-sm flex-1"
                >
                  Edit
                </button>
                <button
                  @click="deleteMeal(meal.id)"
                  class="btn-sm bg-red-100 text-red-700 hover:bg-red-200 flex-1"
                >
                  Delete
                </button>
              </div>
            </div>
          </div>

          <button
            @click="openMealModal(null, category.id)"
            class="mt-4 btn-secondary w-full sm:w-auto"
          >
            <span class="mr-2">➕</span> Add Meal to {{ category.name }}
          </button>
        </div>
      </div>
    </main>

    <!-- Category Modal -->
    <div
      v-if="showCategoryModal"
      class="modal-overlay"
      @click.self="closeCategoryModal"
    >
      <div class="modal-content">
        <div class="flex justify-between items-center mb-6">
          <h2 class="text-2xl font-bold text-gray-800">
            {{ editingCategory ? "Edit Category" : "Add Category" }}
          </h2>
          <button
            @click="closeCategoryModal"
            class="text-gray-500 hover:text-gray-700 text-2xl"
          >
            ×
          </button>
        </div>
        <form @submit.prevent="saveCategory">
          <div class="mb-4">
            <label class="block text-sm font-medium text-gray-700 mb-2"
              >Category Name</label
            >
            <input
              v-model="categoryForm.name"
              type="text"
              required
              class="input-field"
              placeholder="e.g., Curries, Breads, Smoothies"
            />
          </div>
          <div class="flex space-x-3">
            <button type="submit" class="btn-primary flex-1">
              {{ editingCategory ? "Update" : "Create" }}
            </button>
            <button
              type="button"
              @click="closeCategoryModal"
              class="btn-secondary flex-1"
            >
              Cancel
            </button>
          </div>
        </form>
      </div>
    </div>

    <!-- Meal Modal -->
    <div
      v-if="showMealModal"
      class="modal-overlay"
      @click.self="closeMealModal"
    >
      <div class="modal-content max-w-2xl">
        <div class="flex justify-between items-center mb-6">
          <h2 class="text-2xl font-bold text-gray-800">
            {{ editingMeal ? "Edit Meal" : "Add Meal" }}
          </h2>
          <button
            @click="closeMealModal"
            class="text-gray-500 hover:text-gray-700 text-2xl"
          >
            ×
          </button>
        </div>
        <form @submit.prevent="saveMeal">
          <div class="mb-4">
            <label class="block text-sm font-medium text-gray-700 mb-2"
              >Category</label
            >
            <select v-model="mealForm.categoryId" required class="input-field">
              <option value="">Select a category</option>
              <option v-for="cat in categories" :key="cat.id" :value="cat.id">
                {{ cat.name }}
              </option>
            </select>
          </div>
          <div class="mb-4">
            <label class="block text-sm font-medium text-gray-700 mb-2"
              >Meal Name</label
            >
            <input
              v-model="mealForm.name"
              type="text"
              required
              class="input-field"
              placeholder="e.g., Matar Paneer"
            />
          </div>
          <div class="mb-4 relative">
            <label class="block text-sm font-medium text-gray-700 mb-2"
              >Ingredients</label
            >
            <div class="relative">
              <input
                v-model="currentIngredientInput"
                @input="handleIngredientInput"
                @keydown="handleIngredientKeydown"
                @focus="handleIngredientInput"
                type="text"
                class="input-field"
                placeholder="Type ingredient name and press Enter..."
                autocomplete="off"
              />
              <!-- Autocomplete Dropdown -->
              <div
                v-if="
                  showIngredientSuggestions &&
                  filteredIngredientSuggestions.length > 0
                "
                class="absolute z-50 w-full mt-1 bg-white border border-gray-300 rounded-lg shadow-lg max-h-48 overflow-y-auto"
              >
                <button
                  v-for="(ingredient, idx) in filteredIngredientSuggestions"
                  :key="ingredient.id"
                  type="button"
                  @click="selectIngredient(ingredient.name)"
                  @mousedown.prevent
                  class="w-full text-left px-4 py-2 hover:bg-orange-50 transition-colors"
                  :class="{ 'bg-orange-100': idx === selectedSuggestionIndex }"
                >
                  {{ ingredient.name }}
                </button>
              </div>
            </div>
            <!-- Selected Ingredients Display -->
            <div class="mt-2 flex flex-wrap gap-2">
              <span
                v-for="(item, idx) in mealForm.items"
                :key="idx"
                class="inline-flex items-center px-3 py-1 bg-green-100 text-green-700 rounded-full text-sm"
              >
                {{ item }}
                <button
                  type="button"
                  @click="removeIngredient(idx)"
                  class="ml-2 text-green-900 hover:text-red-600"
                >
                  ×
                </button>
              </span>
            </div>
            <p class="text-xs text-gray-500 mt-2">
              Type an ingredient and press Enter to add it. Suggestions will
              appear as you type.
            </p>
          </div>
          <div class="mb-4">
            <label class="block text-sm font-medium text-gray-700 mb-2"
              >Image (optional)</label
            >
            <input
              type="file"
              @change="handleImageUpload"
              accept="image/*"
              class="input-field"
            />
            <div v-if="mealForm.image" class="mt-3">
              <img
                :src="mealForm.image"
                alt="Preview"
                class="h-32 rounded-lg border border-gray-300"
              />
              <button
                type="button"
                @click="mealForm.image = ''"
                class="text-sm text-red-600 hover:text-red-800 mt-2"
              >
                Remove Image
              </button>
            </div>
          </div>
          <div class="flex space-x-3">
            <button type="submit" class="btn-primary flex-1">
              {{ editingMeal ? "Update" : "Create" }}
            </button>
            <button
              type="button"
              @click="closeMealModal"
              class="btn-secondary flex-1"
            >
              Cancel
            </button>
          </div>
        </form>
      </div>
    </div>

    <!-- Ingredient Modal -->
    <div
      v-if="showIngredientModal"
      class="modal-overlay"
      @click.self="closeIngredientModal"
    >
      <div class="modal-content">
        <div class="flex justify-between items-center mb-6">
          <h2 class="text-2xl font-bold text-gray-800">
            {{ editingIngredient ? "Edit Ingredient" : "Add Ingredient" }}
          </h2>
          <button
            @click="closeIngredientModal"
            class="text-gray-500 hover:text-gray-700 text-2xl"
          >
            ×
          </button>
        </div>
        <form @submit.prevent="saveIngredient">
          <div class="mb-4">
            <label class="block text-sm font-medium text-gray-700 mb-2"
              >Ingredient Name</label
            >
            <input
              v-model="ingredientForm.name"
              type="text"
              required
              class="input-field"
              placeholder="e.g., Tomatoes, Onion, Paneer"
            />
          </div>
          <div class="flex space-x-3">
            <button type="submit" class="btn-primary flex-1">
              {{ editingIngredient ? "Update" : "Create" }}
            </button>
            <button
              type="button"
              @click="closeIngredientModal"
              class="btn-secondary flex-1"
            >
              Cancel
            </button>
          </div>
        </form>

        <!-- Existing Ingredients List -->
        <div v-if="ingredients.length > 0" class="mt-6 border-t pt-4">
          <h3 class="text-sm font-semibold text-gray-700 mb-3">
            Existing Ingredients ({{ ingredients.length }})
          </h3>
          <div class="max-h-60 overflow-y-auto space-y-1">
            <div
              v-for="ingredient in sortedIngredients"
              :key="ingredient.id"
              class="flex justify-between items-center px-3 py-2 bg-gray-50 rounded hover:bg-gray-100"
            >
              <span class="text-sm">{{ ingredient.name }}</span>
              <div class="flex space-x-2">
                <button
                  type="button"
                  @click="openIngredientModal(ingredient)"
                  class="text-xs text-blue-600 hover:text-blue-800"
                >
                  Edit
                </button>
                <button
                  type="button"
                  @click="deleteIngredient(ingredient.id)"
                  class="text-xs text-red-600 hover:text-red-800"
                >
                  Delete
                </button>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- Hidden PDF Content -->
    <div id="pdf-content" class="hidden">
      <div class="p-8 bg-white">
        <h1 class="text-4xl font-bold text-center mb-8 text-gray-800">
          Meal Poster
        </h1>
        <div
          v-for="category in visibleCategories"
          :key="category.id"
          class="mb-8"
        >
          <h2
            class="text-2xl font-bold mb-4 text-orange-600 border-b-2 border-orange-300 pb-2"
          >
            {{ category.name }}
          </h2>
          <div
            class="grid gap-4"
            :style="`grid-template-columns: repeat(${pdfSettings.itemsPerRow}, 1fr);`"
          >
            <div
              v-for="meal in getFilteredCategoryMeals(category.id)"
              :key="meal.id"
              class="border border-gray-300 rounded p-3"
            >
              <h3
                :style="`font-size: ${pdfSettings.titleSize}px;`"
                class="font-bold mb-2"
              >
                {{ meal.name }}
              </h3>
              <ul :style="`font-size: ${pdfSettings.textSize}px;`">
                <li v-for="(item, idx) in meal.items" :key="idx" class="ml-4">
                  • {{ item }}
                </li>
              </ul>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- PDF Preview Modal -->
    <div
      v-if="showPDFPreview"
      class="modal-overlay"
      @click.self="closePDFPreviewModal"
    >
      <div
        class="modal-content max-w-full max-h-[95vh] overflow-hidden flex flex-col"
      >
        <div class="flex justify-between items-center mb-4 pb-4 border-b">
          <h2 class="text-2xl font-bold text-gray-800">
            PDF Preview & Settings
          </h2>
          <button
            @click="closePDFPreviewModal"
            class="text-gray-500 hover:text-gray-700 text-2xl"
          >
            ×
          </button>
        </div>

        <!-- Settings Panel -->
        <div class="bg-gray-50 rounded-lg p-4 mb-4 space-y-4">
          <div>
            <label class="block text-sm font-medium text-gray-700 mb-2">
              Items per Row:
              <span class="text-orange-600 font-bold">{{
                pdfSettings.itemsPerRow
              }}</span>
            </label>
            <input
              v-model.number="pdfSettings.itemsPerRow"
              type="range"
              min="1"
              max="8"
              class="w-full"
            />
          </div>

          <div class="grid grid-cols-1 sm:grid-cols-2 gap-4">
            <div>
              <label class="block text-sm font-medium text-gray-700 mb-2">
                Title Size:
                <span class="text-orange-600 font-bold"
                  >{{ pdfSettings.titleSize }}px</span
                >
              </label>
              <input
                v-model.number="pdfSettings.titleSize"
                type="range"
                min="12"
                max="24"
                class="w-full"
              />
            </div>

            <div>
              <label class="block text-sm font-medium text-gray-700 mb-2">
                Text Size:
                <span class="text-orange-600 font-bold"
                  >{{ pdfSettings.textSize }}px</span
                >
              </label>
              <input
                v-model.number="pdfSettings.textSize"
                type="range"
                min="10"
                max="18"
                class="w-full"
              />
            </div>
          </div>

          <div>
            <label class="block text-sm font-medium text-gray-700 mb-2">
              Spacing:
              <span class="text-orange-600 font-bold">{{
                pdfSettings.spacing
              }}</span>
            </label>
            <select v-model="pdfSettings.spacing" class="input-field">
              <option value="compact">Compact</option>
              <option value="normal">Normal</option>
              <option value="relaxed">Relaxed</option>
            </select>
          </div>
        </div>

        <!-- Preview Area -->
        <div
          class="flex-1 overflow-y-auto border-2 border-gray-300 rounded-lg bg-gray-100 p-4"
        >
          <div
            class="bg-white shadow-lg mx-auto"
            style="width: 210mm; min-height: 297mm"
          >
            <div class="p-8">
              <h1 class="text-4xl font-bold text-center mb-8 text-gray-800">
                Meal Poster
              </h1>
              <div
                v-for="category in visibleCategories"
                :key="category.id"
                :class="
                  pdfSettings.spacing === 'compact'
                    ? 'mb-6'
                    : pdfSettings.spacing === 'relaxed'
                      ? 'mb-12'
                      : 'mb-8'
                "
              >
                <h2
                  class="text-2xl font-bold mb-4 text-orange-600 border-b-2 border-orange-300 pb-2"
                >
                  {{ category.name }}
                </h2>
                <div
                  class="grid gap-4"
                  :style="`grid-template-columns: repeat(${pdfSettings.itemsPerRow}, 1fr);`"
                >
                  <div
                    v-for="meal in getFilteredCategoryMeals(category.id)"
                    :key="meal.id"
                    class="border border-gray-300 rounded p-3"
                  >
                    <h3
                      :style="`font-size: ${pdfSettings.titleSize}px;`"
                      class="font-bold mb-2"
                    >
                      {{ meal.name }}
                    </h3>
                    <ul :style="`font-size: ${pdfSettings.textSize}px;`">
                      <li
                        v-for="(item, idx) in meal.items"
                        :key="idx"
                        class="ml-4"
                      >
                        • {{ item }}
                      </li>
                    </ul>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>

        <!-- Action Buttons -->
        <div class="flex space-x-3 mt-4 pt-4 border-t">
          <button @click="exportToPDF" class="btn-primary flex-1">
            <span class="mr-2">📄</span> Export PDF
          </button>
          <button @click="closePDFPreviewModal" class="btn-secondary flex-1">
            Cancel
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted, watch } from "vue";
import jsPDF from "jspdf";
import html2canvas from "html2canvas";

// Dev Mode - Set to true to enable dev tools
const devMode = ref(true);

// State
const menuOpen = ref(false);
const showDevMenu = ref(false);
const showFilters = ref(false);
const categories = ref([]);
const meals = ref([]);
const ingredients = ref([]);
const showCategoryModal = ref(false);
const showMealModal = ref(false);
const showIngredientModal = ref(false);
const showPDFPreview = ref(false);
const editingCategory = ref(null);
const editingMeal = ref(null);
const editingIngredient = ref(null);

// Filter State
const searchQuery = ref("");
const selectedCategories = ref([]);
const selectedIngredients = ref([]);

// Ingredient Autocomplete State
const currentIngredientInput = ref("");
const showIngredientSuggestions = ref(false);
const selectedSuggestionIndex = ref(-1);

// PDF Settings
const pdfSettings = ref({
  itemsPerRow: 4,
  titleSize: 18,
  textSize: 14,
  spacing: "normal",
});

const categoryForm = ref({
  name: "",
});

const mealForm = ref({
  categoryId: "",
  name: "",
  items: [],
  image: "",
});

const ingredientForm = ref({
  name: "",
});

const itemsText = ref("");

// Computed Properties
const filteredIngredientSuggestions = computed(() => {
  if (!currentIngredientInput.value) return [];
  const query = currentIngredientInput.value.toLowerCase().trim();
  return ingredients.value
    .filter((ing) => ing.name.toLowerCase().includes(query))
    .slice(0, 10);
});

const sortedIngredients = computed(() => {
  return [...ingredients.value].sort((a, b) => a.name.localeCompare(b.name));
});

const filteredMeals = computed(() => {
  let filtered = meals.value;

  // Filter by search query
  if (searchQuery.value) {
    const query = searchQuery.value.toLowerCase();
    filtered = filtered.filter(
      (meal) =>
        meal.name.toLowerCase().includes(query) ||
        meal.items.some((item) => item.toLowerCase().includes(query)),
    );
  }

  // Filter by selected categories
  if (selectedCategories.value.length > 0) {
    filtered = filtered.filter((meal) =>
      selectedCategories.value.includes(meal.categoryId),
    );
  }

  // Filter by selected ingredients - only show meals that have ALL their ingredients selected
  if (
    selectedIngredients.value.length > 0 &&
    selectedIngredients.value.length < ingredients.value.length
  ) {
    filtered = filtered.filter((meal) =>
      meal.items.every((item) =>
        selectedIngredients.value.includes(item.toLowerCase()),
      ),
    );
  }

  return filtered;
});

const visibleCategories = computed(() => {
  // Only show categories that have filtered meals
  return categories.value.filter(
    (category) => getFilteredCategoryMeals(category.id).length > 0,
  );
});

// Load data from localStorage or database.json on first run
const loadData = async () => {
  const isInitialized = localStorage.getItem("mealDataInitialized");

  if (!isInitialized) {
    // First time load - fetch from database.json
    try {
      const response = await fetch("/database.json");
      if (response.ok) {
        const data = await response.json();
        if (data.categories && data.meals) {
          categories.value = data.categories;
          meals.value = data.meals;
          ingredients.value = data.ingredients || [];

          // Initialize selected categories with all categories
          selectedCategories.value = data.categories.map((c) => c.id);

          // Initialize selected ingredients with all ingredients
          selectedIngredients.value = (data.ingredients || []).map(
            (ing) => ing.name,
          );

          // Mark as initialized so we don't load from file again
          localStorage.setItem("mealDataInitialized", "true");
          saveData();
          console.log("Data loaded from database.json");
          return;
        }
      }
    } catch (error) {
      console.error("Error loading database.json:", error);
    }
    // If fetch fails, mark as initialized anyway to avoid repeated attempts
    localStorage.setItem("mealDataInitialized", "true");
  }

  // Subsequent loads or if fetch failed - use localStorage
  const savedCategories = localStorage.getItem("mealCategories");
  const savedMeals = localStorage.getItem("meals");
  const savedIngredients = localStorage.getItem("ingredients");

  if (savedCategories) {
    categories.value = JSON.parse(savedCategories);
    // Initialize selected categories with all categories
    selectedCategories.value = JSON.parse(savedCategories).map((c) => c.id);
  }

  if (savedMeals) {
    meals.value = JSON.parse(savedMeals);
  }

  if (savedIngredients) {
    ingredients.value = JSON.parse(savedIngredients);
    // Initialize selected ingredients with all ingredients
    selectedIngredients.value = JSON.parse(savedIngredients).map(
      (ing) => ing.name,
    );
  }
};

// Save data to localStorage
const saveData = () => {
  localStorage.setItem("mealCategories", JSON.stringify(categories.value));
  localStorage.setItem("meals", JSON.stringify(meals.value));
  localStorage.setItem("ingredients", JSON.stringify(ingredients.value));
};

// Watch for changes and save
watch(
  [categories, meals, ingredients],
  () => {
    saveData();
  },
  { deep: true },
);

// Watch for category changes and update selected categories
watch(categories, (newCategories) => {
  // Add newly created categories to selected list
  newCategories.forEach((cat) => {
    if (!selectedCategories.value.includes(cat.id)) {
      selectedCategories.value.push(cat.id);
    }
  });
  // Remove deleted categories from selected list
  selectedCategories.value = selectedCategories.value.filter((id) =>
    newCategories.some((cat) => cat.id === id),
  );
});

// Watch for ingredient changes and update selected ingredients
watch(ingredients, (newIngredients) => {
  // Add newly created ingredients to selected list
  newIngredients.forEach((ing) => {
    if (!selectedIngredients.value.includes(ing.name)) {
      selectedIngredients.value.push(ing.name);
    }
  });
  // Remove deleted ingredients from selected list
  selectedIngredients.value = selectedIngredients.value.filter((name) =>
    newIngredients.some((ing) => ing.name === name),
  );
});

// Filter Functions
const toggleFilters = () => {
  showFilters.value = !showFilters.value;
};

const selectAllIngredients = () => {
  selectedIngredients.value = ingredients.value.map((ing) => ing.name);
};

const deselectAllIngredients = () => {
  selectedIngredients.value = [];
};

// Category Functions
const openCategoryModal = (category = null) => {
  if (category) {
    editingCategory.value = category;
    categoryForm.value.name = category.name;
  } else {
    editingCategory.value = null;
    categoryForm.value.name = "";
  }
  showCategoryModal.value = true;
};

const closeCategoryModal = () => {
  showCategoryModal.value = false;
  editingCategory.value = null;
  categoryForm.value.name = "";
};

const saveCategory = () => {
  if (editingCategory.value) {
    // Update existing category
    const index = categories.value.findIndex(
      (c) => c.id === editingCategory.value.id,
    );
    categories.value[index].name = categoryForm.value.name;
  } else {
    // Create new category
    categories.value.push({
      id: Date.now(),
      name: categoryForm.value.name,
    });
  }
  closeCategoryModal();
};

const deleteCategory = (id) => {
  if (
    confirm(
      "Are you sure you want to delete this category? All meals in this category will also be deleted.",
    )
  ) {
    categories.value = categories.value.filter((c) => c.id !== id);
    meals.value = meals.value.filter((m) => m.categoryId !== id);
  }
};

// Ingredient Functions
const openIngredientModal = (ingredient = null) => {
  if (ingredient) {
    editingIngredient.value = ingredient;
    ingredientForm.value.name = ingredient.name;
  } else {
    editingIngredient.value = null;
    ingredientForm.value.name = "";
  }
  showIngredientModal.value = true;
};

const closeIngredientModal = () => {
  showIngredientModal.value = false;
  editingIngredient.value = null;
  ingredientForm.value.name = "";
};

const saveIngredient = () => {
  const ingredientName = ingredientForm.value.name.toLowerCase().trim();

  // Check if ingredient already exists
  const existingIngredient = ingredients.value.find(
    (ing) => ing.name.toLowerCase() === ingredientName,
  );

  if (existingIngredient && !editingIngredient.value) {
    alert("This ingredient already exists!");
    return;
  }

  if (editingIngredient.value) {
    // Update existing ingredient
    const index = ingredients.value.findIndex(
      (ing) => ing.id === editingIngredient.value.id,
    );
    ingredients.value[index].name = ingredientName;
  } else {
    // Create new ingredient
    ingredients.value.push({
      id: Date.now(),
      name: ingredientName,
    });
  }

  ingredientForm.value.name = "";
  if (!editingIngredient.value) {
    // Keep modal open for adding more
    return;
  }
  closeIngredientModal();
};

const deleteIngredient = (id) => {
  if (confirm("Are you sure you want to delete this ingredient?")) {
    ingredients.value = ingredients.value.filter((ing) => ing.id !== id);
  }
};

// Ingredient Autocomplete Functions
const handleIngredientInput = () => {
  showIngredientSuggestions.value = currentIngredientInput.value.length > 0;
  selectedSuggestionIndex.value = -1;
  console.log("Ingredient input:", currentIngredientInput.value);
  console.log("Showing suggestions:", showIngredientSuggestions.value);
  console.log(
    "Filtered suggestions:",
    filteredIngredientSuggestions.value.length,
  );
};

const handleIngredientKeydown = (event) => {
  if (event.key === "Enter") {
    event.preventDefault();
    if (
      selectedSuggestionIndex.value >= 0 &&
      filteredIngredientSuggestions.value[selectedSuggestionIndex.value]
    ) {
      selectIngredient(
        filteredIngredientSuggestions.value[selectedSuggestionIndex.value].name,
      );
    } else if (currentIngredientInput.value.trim()) {
      addNewIngredient(currentIngredientInput.value.trim());
    }
  } else if (event.key === "ArrowDown") {
    event.preventDefault();
    if (
      selectedSuggestionIndex.value <
      filteredIngredientSuggestions.value.length - 1
    ) {
      selectedSuggestionIndex.value++;
    }
  } else if (event.key === "ArrowUp") {
    event.preventDefault();
    if (selectedSuggestionIndex.value > 0) {
      selectedSuggestionIndex.value--;
    }
  } else if (event.key === "Escape") {
    showIngredientSuggestions.value = false;
  }
};

const selectIngredient = (ingredientName) => {
  const lowerName = ingredientName.toLowerCase().trim();
  if (!mealForm.value.items.includes(lowerName)) {
    mealForm.value.items.push(lowerName);
  }
  currentIngredientInput.value = "";
  showIngredientSuggestions.value = false;
  selectedSuggestionIndex.value = -1;
};

const addNewIngredient = (ingredientName) => {
  const lowerName = ingredientName.toLowerCase().trim();

  // Add to meal items
  if (!mealForm.value.items.includes(lowerName)) {
    mealForm.value.items.push(lowerName);
  }

  // Add to ingredients list if it doesn't exist
  const existingIngredient = ingredients.value.find(
    (ing) => ing.name.toLowerCase() === lowerName,
  );
  if (!existingIngredient) {
    ingredients.value.push({
      id: Date.now() + Math.random(), // Ensure unique ID
      name: lowerName,
    });
  }

  currentIngredientInput.value = "";
  showIngredientSuggestions.value = false;
};

const removeIngredient = (index) => {
  mealForm.value.items.splice(index, 1);
};

// Meal Functions
const openMealModal = (meal = null, categoryId = null) => {
  if (meal) {
    editingMeal.value = meal;
    mealForm.value = {
      categoryId: meal.categoryId,
      name: meal.name,
      items: [...meal.items],
      image: meal.image,
    };
    itemsText.value = meal.items.join("\n");
  } else {
    editingMeal.value = null;
    mealForm.value = {
      categoryId: categoryId || "",
      name: "",
      items: [],
      image: "",
    };
    itemsText.value = "";
  }
  currentIngredientInput.value = "";
  showIngredientSuggestions.value = false;
  showMealModal.value = true;
};

const closeMealModal = () => {
  showMealModal.value = false;
  editingMeal.value = null;
  mealForm.value = {
    categoryId: "",
    name: "",
    items: [],
    image: "",
  };
  itemsText.value = "";
  currentIngredientInput.value = "";
  showIngredientSuggestions.value = false;
};

const saveMeal = () => {
  if (mealForm.value.items.length === 0) {
    alert("Please add at least one ingredient!");
    return;
  }

  if (editingMeal.value) {
    // Update existing meal
    const index = meals.value.findIndex((m) => m.id === editingMeal.value.id);
    meals.value[index] = {
      ...meals.value[index],
      categoryId: mealForm.value.categoryId,
      name: mealForm.value.name,
      items: mealForm.value.items,
      image: mealForm.value.image,
    };
  } else {
    // Create new meal
    meals.value.push({
      id: Date.now(),
      categoryId: mealForm.value.categoryId,
      name: mealForm.value.name,
      items: mealForm.value.items,
      image: mealForm.value.image,
    });
  }
  closeMealModal();
};

const deleteMeal = (id) => {
  if (confirm("Are you sure you want to delete this meal?")) {
    meals.value = meals.value.filter((m) => m.id !== id);
  }
};

const handleImageUpload = (event) => {
  const file = event.target.files[0];
  if (file) {
    const reader = new FileReader();
    reader.onload = (e) => {
      mealForm.value.image = e.target.result;
    };
    reader.readAsDataURL(file);
  }
};

const getCategoryMeals = (categoryId) => {
  return meals.value.filter((m) => m.categoryId === categoryId);
};

const getFilteredCategoryMeals = (categoryId) => {
  return filteredMeals.value.filter((m) => m.categoryId === categoryId);
};

// Dev Tools Functions
const toggleDevMenu = () => {
  showDevMenu.value = !showDevMenu.value;
};

const exportJSON = () => {
  const data = {
    ingredients: ingredients.value,
    categories: categories.value,
    meals: meals.value,
    exportedAt: new Date().toISOString(),
  };

  const dataStr = JSON.stringify(data, null, 2);
  const dataBlob = new Blob([dataStr], { type: "application/json" });
  const url = URL.createObjectURL(dataBlob);
  const link = document.createElement("a");
  link.href = url;
  link.download = `meal-poster-data-${Date.now()}.json`;
  link.click();
  URL.revokeObjectURL(url);

  showDevMenu.value = false;
  alert("Data exported successfully!");
};

const importJSON = (event) => {
  const file = event.target.files[0];
  if (!file) return;

  const reader = new FileReader();
  reader.onload = (e) => {
    try {
      const data = JSON.parse(e.target.result);

      if (data.categories && data.meals) {
        if (confirm("This will replace all existing data. Continue?")) {
          categories.value = data.categories;
          meals.value = data.meals;
          ingredients.value = data.ingredients || [];
          saveData();
          alert("Data imported successfully!");
        }
      } else {
        alert("Invalid JSON format. Expected categories and meals.");
      }
    } catch (error) {
      alert("Error parsing JSON file: " + error.message);
    }
  };
  reader.readAsText(file);
  showDevMenu.value = false;
  event.target.value = ""; // Reset file input
};

const clearAllData = () => {
  if (
    confirm(
      "Are you sure you want to delete ALL categories and meals? This cannot be undone!",
    )
  ) {
    if (confirm("Really delete everything? Last chance!")) {
      categories.value = [];
      meals.value = [];
      ingredients.value = [];
      localStorage.clear();
      showDevMenu.value = false;
      alert("All data cleared! Refresh the page to reload from database.json");
    }
  }
};

// PDF Preview Functions
const openPDFPreviewModal = () => {
  if (categories.value.length === 0) {
    alert("Please add some categories and meals before exporting!");
    return;
  }
  if (filteredMeals.value.length === 0) {
    alert("No meals to export with current filters!");
    return;
  }
  showPDFPreview.value = true;
};

const closePDFPreviewModal = () => {
  showPDFPreview.value = false;
};

// PDF Export
const exportToPDF = async () => {
  const element = document.getElementById("pdf-content");
  element.classList.remove("hidden");

  try {
    const canvas = await html2canvas(element, {
      scale: 1.5,
      useCORS: true,
      logging: false,
      backgroundColor: "#ffffff",
    });

    const imgData = canvas.toDataURL("image/jpeg", 0.8);
    const pdf = new jsPDF("p", "mm", "a4");
    const imgWidth = 210; // A4 width in mm
    const imgHeight = (canvas.height * imgWidth) / canvas.width;

    let heightLeft = imgHeight;
    let position = 0;

    pdf.addImage(imgData, "JPEG", 0, position, imgWidth, imgHeight);
    heightLeft -= 297; // A4 height in mm

    while (heightLeft > 0) {
      position = heightLeft - imgHeight;
      pdf.addPage();
      pdf.addImage(imgData, "JPEG", 0, position, imgWidth, imgHeight);
      heightLeft -= 297;
    }

    pdf.save("meal-poster.pdf");
    closePDFPreviewModal();
    alert("PDF exported successfully!");
  } catch (error) {
    console.error("Error generating PDF:", error);
    alert("Error generating PDF. Please try again.");
  } finally {
    element.classList.add("hidden");
  }
};

onMounted(() => {
  loadData();

  // Close dev menu when clicking outside
  document.addEventListener("click", (e) => {
    if (
      showDevMenu.value &&
      !e.target.closest(".btn-dev") &&
      !e.target.closest(".absolute")
    ) {
      showDevMenu.value = false;
    }
  });
});
</script>

<style scoped>
.btn-primary {
  @apply px-4 py-2 bg-gradient-to-r from-orange-500 to-orange-600 text-white rounded-lg hover:from-orange-600 hover:to-orange-700 transition-all duration-200 shadow-md hover:shadow-lg font-medium;
}

.btn-secondary {
  @apply px-4 py-2 bg-gradient-to-r from-green-500 to-green-600 text-white rounded-lg hover:from-green-600 hover:to-green-700 transition-all duration-200 shadow-md hover:shadow-lg font-medium;
}

.btn-accent {
  @apply px-4 py-2 bg-gradient-to-r from-blue-500 to-blue-600 text-white rounded-lg hover:from-blue-600 hover:to-blue-700 transition-all duration-200 shadow-md hover:shadow-lg font-medium;
}

.btn-dev {
  @apply px-4 py-2 bg-gradient-to-r from-purple-500 to-purple-600 text-white rounded-lg hover:from-purple-600 hover:to-purple-700 transition-all duration-200 shadow-md hover:shadow-lg font-medium;
}

.btn-filter {
  @apply px-4 py-2 bg-gradient-to-r from-indigo-500 to-indigo-600 text-white rounded-lg hover:from-indigo-600 hover:to-indigo-700 transition-all duration-200 shadow-md hover:shadow-lg font-medium;
}

.btn-ingredient {
  @apply px-4 py-2 bg-gradient-to-r from-yellow-500 to-yellow-600 text-white rounded-lg hover:from-yellow-600 hover:to-yellow-700 transition-all duration-200 shadow-md hover:shadow-lg font-medium;
}

.btn-icon {
  @apply p-2 hover:bg-gray-100 rounded-lg transition-colors duration-200;
}

.btn-sm {
  @apply px-3 py-1.5 bg-orange-100 text-orange-700 rounded-lg hover:bg-orange-200 transition-all duration-200 text-sm font-medium;
}

.category-card {
  @apply bg-white rounded-xl shadow-lg p-6 border border-gray-100 hover:shadow-xl transition-shadow duration-200;
}

.meal-card {
  @apply bg-gradient-to-br from-white to-gray-50 rounded-lg shadow-md p-4 border border-gray-200 hover:shadow-lg transition-all duration-200 hover:-translate-y-1;
}

.ingredient-tag {
  @apply px-2 py-1 bg-green-100 text-green-700 text-xs rounded-full;
}

.modal-overlay {
  @apply fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50 p-4;
}

.modal-content {
  @apply bg-white rounded-xl shadow-2xl p-6 w-full max-w-md max-h-[90vh] overflow-y-auto;
}

.input-field {
  @apply w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-orange-500 focus:border-transparent outline-none transition-all duration-200;
}
</style>
