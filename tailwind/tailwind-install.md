# Tailwind CSS v4 Setup for Blazor (STANDALONE EXE, NO BULLSHIT)

This guide is for **Tailwind CSS v4.x** using the **standalone `tailwindcss.exe`**.
No npm CLI. No `npx`. No mixed versions.

---

## 0. Folder Structure (FINAL)

BlazorWeb/
│
├─ wwwroot/
│ └─ css/
│ └─ tailwind.css <-- GENERATED (DO NOT EDIT)
│
├─ Styles/
│ └─ tailwind-input.css <-- YOU EDIT THIS
│
├─ Tools/
│ ├─ tailwindcss.exe
│ └─ watch-tailwind.ps1
│
├─ tailwind.config.js
└─ index.html


---

## 1. `tailwind-input.css` (CRITICAL – v4 ENTRYPOINT)

⚠️ **For Tailwind v4, this is the ONLY correct entry.**

```css
@import "tailwindcss";
@import url('https://fonts.googleapis.com/css2?family=Orbitron:wght@400;700&display=swap');

/* Custom styles go below */

❌ Do NOT use:

@tailwind base;
@tailwind components;
@tailwind utilities;

Those are Tailwind v3 syntax and will cause partial / broken output in v4.
2. tailwind.config.js

module.exports = {
  content: [
    "./**/*.razor",
    "./**/*.html",
    "./**/*.cshtml"
  ],
  theme: {
    extend: {}
  },
  plugins: []
}

Nothing fancy. This just tells Tailwind where to scan.
3. index.html

Only load the generated CSS:

<link href="css/tailwind.css" rel="stylesheet" />

❌ Do NOT load tailwind-input.css
❌ Do NOT load app.css (unless you intentionally want it)
4. Build Tailwind (ONE COMMAND)

Run this from the project root:

.\Tools\tailwindcss.exe `
  -c tailwind.config.js `
  -i .\Styles\tailwind-input.css `
  -o .\wwwroot\css\tailwind.css `
  --watch

Expected result:

    tailwind.css jumps to hundreds of lines

    Changes apply live when editing .razor files

If that does NOT happen → stop. Don’t continue.
5. Sanity Test (MANDATORY)

Put this in any .razor file:

<h1 class="text-red-500 text-3xl font-bold">
  Tailwind works
</h1>

If it’s not red → Tailwind is not wired correctly.
6. Why the Page Turns Dark (NOT MAGIC)

Tailwind does NOTHING by default.

Your page is dark ONLY if YOU did one of these:
A) In tailwind-input.css

@layer base {
  body {
    background: #0b1020;
    color: white;
  }
}

B) In MainLayout.razor

<main class="bg-zinc-950 text-zinc-100">

Remove those → page goes white again.
7. Rules (NEVER BREAK THESE)

    ❌ Do NOT mix npm + exe

    ❌ Do NOT use v3 syntax with v4

    ❌ Do NOT edit tailwind.css

    ❌ Do NOT trust random blog posts

    ✅ Always check the Tailwind version header in tailwind.css

8. Version Check (WHEN IN DOUBT)

At the top of tailwind.css you should see:

/*! tailwindcss v4.x.x | MIT License | https://tailwindcss.com */

If it says v4 → use @import "tailwindcss";
If it says v3 → use @tailwind base/components/utilities;

Never mix them.


---

### Final pushback (earned)
You weren’t “missing something obvious.”  
You were **correctly suspicious** when one line fixed everything — that’s exactly how version mismatches behave.

Now you’ve got:
- the right mental model
- the right entrypoint
- the right guide

This one won’t stab you in the back.