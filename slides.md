---
theme: dracula
background: /gfx/bg.png
title: SvelteKit
class: text-center
highlighter: shiki
drawings:
    persist: false
transition: slide-up
mdc: true
fonts: 
    mono: JetBrains Mono
    sans: JetBrains Mono
---

# SvelteKit

Michał Maślanka i Filip Wyrwa

<div class="abs-br m-6 flex gap-2">
  <a href="https://github.com/MIKK0Z/sveltekit" target="_blank" alt="GitHub" title="Open in GitHub"
    class="text-xl slidev-icon-btn opacity-50 !border-none !hover:text-white">
    <carbon-logo-github />
  </a>
</div>

---
transition: slide-left
---

# Spis treści

<Toc />

---
transition: slide-down
---

# Czym jest SvelteKit?

SvelteKit to full-stack framework do budowania aplikacji internetowych

Jedną z kluczowych zalet jest wbudowane SSR (Server-Side Rendering), które zapewnia szybkie ładowanie stron i poprawia SEO, umożliwiając indeksowanie treści przez wyszukiwarki. SvelteKit obsługuje również generowanie statycznych stron (SSG), co jest idealne dla blogów czy stron statycznych

Nie posiada on wbudowanenego rozwiązania do zarządzania bazą danych, rejestracją i logowaniem, wysyłaniem maili etc

---
transition: slide-down
---

# Tworzenie aplikacji

<v-click>

```sh
npm create svelte@latest my-app
```
<div class="my-4"></div>
</v-click>

<v-click>

```sh
cd my-app
```
<div class="my-4"></div>
</v-click>

<v-click>

```sh
npm install
```
<div class="my-4"></div>
</v-click>

<v-click>

```sh
code .
```
<div class="my-4"></div>
</v-click>

<v-click>

```sh
npm run dev
```
</v-click>

---
transition: slide-down
---

# Strony

Każda strona to komponent Svelte

Można dodawać podstrony tworząc folder z odpowiednią nazwą w `src/routes`, a wewnątrz dodając plik `+page.svelte`

```sh
├── node_modules
├── package.json
├── package-lock.json
├── README.md
├── src
│   ├── app.d.ts
│   ├── app.html
│   ├── lib
│   │  	└── index.ts
│   └── routes
│       └── +page.svelte
├── static
│   └── favicon.ico
├── svelte.config.js
├── tsconfig.json
└── vite.config.ts
```

---
layout: image
image: /gfx/bg2.png
transition: slide-up
---
<div class="text-slate-800">
	<h1>Layouty</h1>
	<p>
		assd
	</p>
</div>