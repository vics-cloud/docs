Níže naleznete jednoduchý krok za krokem návod, jak pomocí Next.js (moderního frameworku pro React) a MDX/Markdown automaticky načítat obsah složek a souborů (.md) z GitHub repozitáře [vics-cloud/docs](https://github.com/vics-cloud/docs).

---

## 1. Vytvoření nového projektu

Nejdříve je potřeba mít nainstalovaný [Node.js](https://nodejs.org/). Poté vytvoříte nový Next.js projekt:

```bash
npx create-next-app docs-loader
cd docs-loader
```

---

## 2. Instalace potřebných závislostí

Pro práci s Markdown obsahem (a případně MDX) nainstalujte potřebné balíčky:

```bash
npm install @next/mdx @mdx-js/loader remark remark-html
```

---

## 3. Konfigurace Next.js pro podporu MDX/Markdown

Upravte (nebo vytvořte) soubor `next.config.js` tak, aby Next.js rozpoznával soubory s příponou `.md` nebo `.mdx`:

```js
const withMDX = require('@next/mdx')({
  extension: /\.mdx?$/,
})

module.exports = withMDX({
  pageExtensions: ['js', 'jsx', 'md', 'mdx'],
})
```

---

## 4. Dynamické načítání obsahu z GitHub

### a) Dynamické routování

V adresáři `pages` vytvořte složku (například `docs`) a uvnitř soubor s dynamickou routou. Pro jednoduchý příklad použijeme soubor `pages/docs/[slug].js`, který bude zobrazovat obsah jednotlivých Markdown souborů.

### b) Načítání seznamu souborů

Pomocí funkce `getStaticPaths` načtěte pomocí GitHub API seznam souborů z hlavního adresáře repozitáře, který obsahuje Markdown soubory. Například:

```js
// pages/docs/[slug].js

export async function getStaticPaths() {
  const res = await fetch('https://api.github.com/repos/vics-cloud/docs/contents')
  const files = await res.json()

  // Filtrování pouze souborů končících .md
  const paths = files
    .filter(file => file.name.endsWith('.md'))
    .map(file => ({
      params: { slug: file.name.replace('.md', '') },
    }))

  return { paths, fallback: false }
}
```

### c) Načítání obsahu souboru

Ve funkci `getStaticProps` načtěte konkrétní Markdown soubor (například z hlavní větve `main`). Použijte GitHub raw URL a knihovnu [remark](https://github.com/remarkjs/remark) pro převod Markdown na HTML:

```js
import remark from 'remark'
import html from 'remark-html'

export async function getStaticProps({ params }) {
  const res = await fetch(`https://raw.githubusercontent.com/vics-cloud/docs/main/${params.slug}.md`)
  const fileContent = await res.text()

  const processedContent = await remark()
    .use(html)
    .process(fileContent)
  const contentHtml = processedContent.toString()

  return {
    props: {
      contentHtml,
    },
  }
}
```

### d) Zobrazení obsahu

V samotné komponentě vykreslete HTML obsah pomocí `dangerouslySetInnerHTML`:

```js
export default function Doc({ contentHtml }) {
  return <div dangerouslySetInnerHTML={{ __html: contentHtml }} />
}
```

---

## 5. Rozšíření na složky a vnořené struktury

Pokud potřebujete načítat obsah nejen z kořenového adresáře, ale i z vnořených složek, postupujte následovně:

- **Rekurzivní načítání adresářové struktury:**  
  Pomocí GitHub API můžete načítat obsah složek (každý adresář má své vlastní URL v API). Implementujte rekurzivní funkci, která projde strukturu repozitáře a získá seznam všech Markdown souborů i v podsložkách.

- **Dynamické routování pro více parametrů:**  
  Místo `[slug].js` můžete vytvořit soubor s volitelnými parametry, např. `pages/docs/[[...slug]].js`. Tento soubor umožní načítat cesty jako `/docs/slozka/podstranka` a v `getStaticPaths` budete pracovat s polem parametrů.

---

## 6. Vylepšení a nasazení

- **Navigační menu:**  
  Vytvořte si komponentu, která při načtení stránky získá strukturu repozitáře (adresáře a soubory) a dynamicky vytvoří navigaci.

- **Styling a další funkce:**  
  Přidejte vlastní styly, SEO optimalizaci, nebo jiné funkce dle potřeby.

- **Nasazení:**  
  Projekt můžete nasadit na platformu [Vercel](https://vercel.com/), která je optimalizována pro Next.js aplikace.

---

## Shrnutí

Tento návod vám ukazuje, jak:
- Vytvořit nový Next.js projekt,
- Konfigurovat podporu Markdown/MDX,
- Načítat seznam a obsah Markdown souborů přímo z GitHub repozitáře pomocí GitHub API,
- Dynamicky generovat stránky pomocí `getStaticPaths` a `getStaticProps`.

Tento přístup vám umožní mít dokumentaci, která se automaticky aktualizuje dle obsahu repozitáře. Další úpravy (např. rekurzivní načítání složek, dynamické routování pro více úrovní) lze implementovat podle konkrétních potřeb projektu.

Tento jednoduchý návod je základem, který můžete dále rozšiřovat a přizpůsobovat. Pokud máte další dotazy či potřebujete upřesnit nějaký krok, dejte vědět!
