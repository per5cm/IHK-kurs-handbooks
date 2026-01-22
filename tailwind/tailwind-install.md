# Tailwind CSS with Blazor WebAssembly (Standalone CLI)

This guide explains how to install and use **Tailwind CSS via the standalone CLI binary**
in a **Blazor WebAssembly (standalone)** project — **no Node.js required**.

---

## 1. Download Tailwind CLI

Download the standalone Tailwind binary for your OS:

https://tailwindcss.com/docs/installation/standalone

For Windows:
- Rename `tailwindcss-windows-x64.exe` → `tailwindcss.exe`

---

## 2. Recommended Project Structure

BlazorApp/
├─ Tools/
│ └─ tailwindcss.exe
├─ Styles/
│ └─ tailwind-input.css
├─ wwwroot/
│ └─ css/
│ └─ tailwind.css (generated)
├─ tailwind.config.js
└─ wwwroot/index.html


---

## 3. Create Tailwind Input File

**Styles/tailwind-input.css**

```css
@tailwind base;
@tailwind components;
@tailwind utilities;

⚠️ This file is only for the compiler.
⚠️ Do NOT reference it in HTML.
4. Create Tailwind Config

tailwind.config.js

module.exports = {
  content: [
    "./**/*.{html,razor,cs}",
    "./wwwroot/**/*.html"
  ],
  theme: {
    extend: {},
  },
  plugins: [],
};

This defines which files Tailwind scans for class names.
5. Build Tailwind CSS

Run from the project root:

.\Tools\tailwindcss.exe ^
  -c .\tailwind.config.js ^
  -i .\Styles\tailwind-input.css ^
  -o .\wwwroot\css\tailwind.css

If successful:

Done in XXms

6. Load Tailwind in Blazor

Edit wwwroot/index.html

<link rel="stylesheet" href="css/tailwind.css" />

❌ Remove app.css if you want Tailwind-only
❌ Do NOT load tailwind-input.css
7. Fix Blazor Layout (Critical)

A Blazor layout must render exactly one @Body.

Shared/MainLayout.razor

@inherits LayoutComponentBase

<main class="min-h-screen bg-zinc-950 text-zinc-100 p-6">
    <div class="prose prose-invert max-w-none">
        @Body
    </div>
</main>

❌ Never render @Body twice
❌ Double @Body causes duplicated UI and text
8. Test Tailwind

Pages/Home.razor

<h1 class="text-red-500 hover:text-green-400 transition">
    Tailwind is alive.
</h1>

<button class="mt-4 px-4 py-2 bg-blue-600 hover:bg-blue-700 rounded">
    Click me
</button>

If colors and hover work → Tailwind is correctly installed.
9. Rebuild When Styles Change

Whenever you add or change Tailwind classes:

.\Tools\tailwindcss.exe -c .\tailwind.config.js -i .\Styles\tailwind-input.css -o .\wwwroot\css\tailwind.css