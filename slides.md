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
hideInToc: true
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

<Toc columns=2 />

---
transition: slide-up
---

# Czym jest SvelteKit?

SvelteKit to full-stack filesystem-based framework służący do budowania aplikacji internetowych

Jedną z kluczowych zalet jest wbudowane SSR (Server-Side Rendering), które zapewnia szybkie ładowanie stron i poprawia SEO, umożliwiając indeksowanie treści przez wyszukiwarki. SvelteKit obsługuje również generowanie statycznych stron, co jest idealne dla blogów czy stron statycznych

Nie posiada on wbudowanenego rozwiązania do zarządzania bazą danych, rejestracją i logowaniem, wysyłaniem maili etc

---
transition: slide-down
---

# Tworzenie aplikacji

```sh
npm create svelte@latest my-app
```
<div class="my-4"></div>

```sh
cd my-app
```
<div class="my-4"></div>

```sh
npm install
```
<div class="my-4"></div>

```sh
code .
```
<div class="my-4"></div>

```sh
npm run dev
```

---
transition: slide-left
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
		Aby dodać layout, należy utworzyć plik <code class="text-slate-100">+layout.svelte</code>
	</p>
  <p>
    Aby wskazać gdzie ma się wyświetlać właściwa zawartość <br />
    strony należy użyć znacznika  <code class="text-slate-100">&lt;slot &sol;&gt;</code>
  </p>
  <p>
    Fallback zawartości strony można utworzyć <br />
    w taki sposób <code class="text-slate-100">&lt;slot&gt;fallback&lt;&sol;slot&gt;</code>
  </p>
</div>

---
transition: slide-right
---

# Parametry stron

Aby dynamicznie pobierać parametry z linku, należy w nazwe folderu umieścić <br />
w `[]`, można uzyskać do nich dostęp w funkcji `load()` w pliku <br />
`page.ts` lub `page.server.ts`

```
src/routes/blog/[slug]/+page.server.ts
```
<div class="my-4"></div>

```ts
export const load: PageServerLoad = async ({ params }) => {
  const { slug } = params;
  return slug;
})
```

---
transition: slide-up
---

# Ładowanie danych z servera na stronie

Aby załadować dane z funkcji `load()` w plikach `+page.svelte` należy je zaimportować

```ts
// +page.server.ts
export const load: PageServerLoad = async ({ params }) => {
  const { slug } = params;
  return slug;
})
```
<div class="my-4"></div>

```svelte
<!-- +page.svelte -->
<script lang="ts">
	import type { PageData } from './$types';
  export let data: PageData;

  $: ({ slug } = data);
</script>

<h1>{slug}</h1>
```

To samo da się zrobić na layoucie w plikach `+layout.server.ts` i `+layout.svelte`

---
transition: slide-down
---

# Headery

Aby ustawić headery strony należy w funkcji `load()` użyć metody `setHeaders`

```ts
// +page.server.ts
export const load: PageServerLoad = async ({ setHeaders }) => {
  setHeaders({
    'Content-Type': 'application/json',
  });
})
```

Przydaje się to m.in. przy tworzeniu API

---
transition: fade-out
---

# Cookies

Aby odczytać lub ustwaić plik cookie należy użyć metod klasy `cookies`

```ts
// +page.server.ts
export const load: PageServerLoad = async ({ cookies }) => {
  const cookie = cookies.get('nazwa-ciasteczka');
  cookies.set('nazwa-ciasteczka', 'wartość', { ...opcje });
})
```
<img src="/gfx/cookies.jpg" alt="ciasteczka" class="mx-auto mt-12 w-11/12 h-36" />

---
transition: fade
---

# $lib

W folderze `src/lib` można trzymać pliki do których chcemy mieć dostęp w całej aplikacji za pomocą aliasu `$lib`

```svelte
<!-- +page.svelte -->
<script lang="ts">
  import Component from '$lib/Component.svelte' 
</script>
```

zamiast

```svelte
<!-- +page.svelte -->
<script lang="ts">
  import Component from '../../../Component.svelte' 
</script>
```

---

# Forms

Aby odbierać formy na serwerze, należy dodać plik `+page.server.ts` w tym samym folderze co strona. Potem zawartość inputów możemy pobrać z promisa `request.formData()` Zawartosć plików może wyglądać na przykład tak:

```svelte
<!-- +page.svelte -->
<form method="post">
    <input type="text" name="nickname">
    <button type="submit">Zapisz</button>
</form>
```
<div class="my-4"></div>

```ts
// +page.server.ts
export const actions = {
	default: async ({ cookies, request }) => {
		const data = await request.formData();
    const nickname = data.get('nickname');

    if (typeof nickname !== 'string') return fail(400); // najprostsza walidacja

    cookies.set('nickname', nickname, { path: '/' });
	}
};
```
---
hideInToc: true
---

## Wiele formów na jednej stronie

Aby móc korzystać z więcej niż 1 formularza i domyślnej akcji `default`, należy dodać je w następujący sposób:

<div class="grid grid-cols-2 gap-2 [&>*]:grid">

```svelte
<!-- +page.svelte -->
<form method="post" action="?/dodaj">
    <input type="text" name="zadanie">
    <button type="submit">Dodaj</button>
</form>

<form method="post" action="?/usun">
    <input type="text" name="zadanie">
    <button type="submit">Usun</button>
</form>
```

```javascript
// +page.server.ts
export const actions = {
	dodaj: async ({ cookies, request }) => {
		const data = await request.formData();
		console.log(data.get('zadanie'))
		console.log('dodawanie');
	},

	usun: async ({ cookies, request }) => {
		const data = await request.formData();
		console.log(data.get('zadanie'))
		console.log('usuwanie');
	}
};
```
</div>

---

# Svelte stores

Aby uzyskiwać dostęp do danych między przekierowaniami i zarządzać danymi aplikacji, używa się `svelte/store`. Aby ustawić wartość używamy funkcji `set` a żeby ją zmienić, funkcji `update`, w której możemy pobrać obecną zawartość i zwrócić nową. Aby modyfikować wartość bezpośredniu z inputa, trzeba dodać `$` przed nazwą. Przykład:

<div class="grid grid-cols-2 gap-2 [&>*]:grid">

```javascript
//plik src/lib/stores.js
import { writable } from 'svelte/store';
export const count = writable(0);

function decrement() {
		count.update((n) => n - 1);
	}
function increment() {
		count.update((n) => n + 1);
	}
function reset() {
		count.set(0);
	}

```

```svelte
//przykładowy plik +page.svelte
<script>
  import { count } from '$lib/stores.js';
</script>

<button on:click={decrement}>
<button on:click={increment}>
<button on:click={reset}>
<input type="number" bind:value={$count}>
```

</div>

---
transition: fade
---

# Customowe funkcje

`Stores` mogą exportwać własne funkcje, na przykład:

<div class="grid grid-cols-2 gap-2 [&>*]:grid">

```javascript
import { writable } from 'svelte/store';

function createCount() {
	const { subscribe, set, update } = writable(0);

	return {
		subscribe,
		increment: () => update((n) => n + 1),
		decrement: () => update((n) => n - 1),
		reset: () => set(0)
	};
}

export const count = createCount();

```

```svelte
<script>
	import { count } from '$lib/stores.js';
</script>

<h1>The count is {$count}</h1>

<button on:click={count.increment}>+</button>
<button on:click={count.decrement}>-</button>
<button on:click={count.reset}>reset</button>

```

</div>

---
transition: fade
---

# Błędy

W SvelteKit są 2 typy blędów, tzw. `expected` i `unexpected`. `Exprected error` to taki, który został stworzony przy użyciu helpera `error` np. by powiadomić o czymś użytkownika.

```javascript
import { error } from '@sveltejs/kit';

export function load() {
	throw error(420, 'Error');
}
```

`Unexpected error` jest traktowany jako bug.

```javascript
export function load() {
	throw new Error('awaria');
}
```

---
transition: fade
---

# Strony błędów

Aby dodać customową stronę błędu, możemy utworzyć plik `+error.svelte`

```svelte
<h1>{$page.status} {$page.error.message}</h1>
```

<!--
n
-->

---
transition: fade
---

# Przekierowania

Do przekierowań używamy helpera `redirect`

```javascript
import { redirect } from '@sveltejs/kit';

export function load() {
	throw redirect(307, '/register');
}
```

Najczęstrze kody przekierowań:

`303` — po zgłoszeniu forma

`307` — tymczasowe przekierowania

`308` — permamentne przekierowania, np podstrona `/userlist` to teraz `/users`


---
transition: fade
---

# API routes

Aby dodać ścieżki API tworzymy plik `+server.js` a nim odpowiednie funkcje, na przykład:

```javascript
import { json } from '@sveltejs/kit';

export function GET({request,cookies}) {
  	console.log(request)
	const number = Math.floor(Math.random() * 6) + 1;

	return json(number);
}

export function POST({ request, cookies }) {
	console.log(request)
	const number = Math.floor(Math.random() * 6) + 1;

	return json(number);
}
```

---
transition: fade
---

# ssr
Domyślnie strony w svelcie wykorzystują server-side rendering (ssr), generuje on kod HTML na serwerze i wysyła go do klienta. Poprawia to szybkość ładowania strony oraz SEO.

Dużym minusem ssr jest to że nie da się używać zmiennych globalnych przeglądarki (np. `window`)

Aby wyłączyć ssr należy użyć następującą linijke w pliku `+page.server.js`

```js
export const ssr = false;
```

---
transition: fade
---

# Preloading
Są dwa sposoby na preloadowanie strony w sveltekit, pierwszy z nich ładuje strone przy uruchomieniu serwera, a drugi na hoverze linka do tej podstrony

<div class="grid grid-cols-2 gap-2 [&>*]:grid">

```js
// +page.server.js
export const prerender = true;
```

```svelte
<!-- ../+page.svelte -->
<nav>
	<a href="/page" data-sveltekit-preload-data>link</a>
</nav>
```

</div>


---
transition: fade
---

# Zmienne środowiskowe
Domyślnie wszystkie zmienne środowiskowe w sveltekit są prywatne (są dostępne tylko w plikach serwerowych, np `+page.server.js`)

<div class="grid grid-cols-2 gap-2 [&>*]:grid">

```
APP_NAME="App name"
```

```js
// +page.server.js

import { APP_NAME } from '$env/static/private';

console.log(APP_NAME)
```

</div>

Aby móc używać zmiennej po stronie klienta należy przed jej nazwą napisać `PUBLIC_`

<div class="grid grid-cols-2 gap-2 [&>*]:grid">

```
PUBLIC_APP_NAME="App name"
```

```svelte
<!-- +page.svelte -->

<script>
	import { PUBLIC_APP_NAME } from '$env/static/public';
</script>

<h1>{PUBLIC_APP_NAME}</h1>
```

</div>

---
transition: fade
---

# Slidev
Ta prezentacja została zrobiona przy użyciu slidev. Jest to aplikacja do tworzenia prezaentacji oparta na node js. Nadaje się najbardziej do tworzenia prezentacji związanych z programowaniem.

```bash
npm init slidev@latest
```

---
transition: fade
---

# Slajdy
Slidy są tworzone w postaci plików markdown `.md`. Można łatwo dodawać bloki pokazujące kod, motywy oraz dodawać komponenty `Vue`.

```md
---
transition: fade
---

# Slajdy
Slidy są tworzone w postaci plików markdown `.md`. Można łatwo dodawać bloki pokazujące kod,
motywy oraz dodawać komponenty `Vue`.

```

---
transition: fade
---

# Spis treści
Można łatwo wygenerować spis treści dla prezentacji dodając:

```jsx
<Toc maxDepth="1" columns="3"></Toc>

```
<Toc columns="3" />

---
transition: fade
---

# Podświetlenia
Aby podświetlać linijki kodu w trakcie prezentacji, należy dodać odpowienie linie w nawiasach klamrowych, np `js {2,3}`.

```js {2,3}
function add (
	a: Ref<number> | number,
	b: Ref<number> | number
) {
	return computed(() => unref(a) + unref(b))
}
```

---
transition: fade
---


# Shiki Magic Move (animacje przy zmianie kodu)
````md magic-move
```js
console.log(`Step ${1}`)
```

```js
console.log(`Step ${1 + 1}`)
```

```ts
console.log(`Step ${3}` as string)
```
````

```md
''''md magic-move
'''js
console.log(`Step ${1}`)
'''

'''js
console.log(`Step ${1 + 1}`)
'''

'''ts
console.log(`Step ${3}` as string)
'''
''''
```

---
transition: fade
---

# Podkreślenia i animacje

<div v-click>To pokaże się po kliknieciu.</div>
<span v-mark.underline.orange>podkreślenia</span>

```html
<div v-click>To pokaże się po kliknieciu.</div>
<span v-mark.underline.orange>podkreślenia</span>
```