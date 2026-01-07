# Learning Note: Developing in AEM Edge Delivery Services (EDS)

This guide is designed for developers familiar with **Traditional AEM (Sites)** who are transitioning to **AEM Edge Delivery Services (EDS)**.

---

## 1. Core Concepts Mapping

| Concept | Traditional AEM (Sites) | AEM Edge Delivery Services (EDS) |
| :--- | :--- | :--- |
| **Component Logic** | **HTL (Sightly) & Java Models** | **JavaScript Blocks** (`decorate` function). You manipulate the DOM directly client-side rather than rendering a template server-side. |
| **Component Styling** | **ClientLibs** (Less/Sass) | **CSS Files** per block. Loaded *only* when the block is on the page (lazy-loading built-in). |
| **Authoring Dialog** | **Dialog XML** (`_cq_dialog`) | **Model JSON** (`_block.json`). Defines fields for the Universal Editor. |
| **Page Structure** | **Page Templates** (Editable Templates) | **Sections**. A page is just a sequence of "Sections" (divs). You use `metadata` blocks to define page-level properties. |
| **Content Source** | **JCR Nodes** | **Documents** (Word/Google Docs) OR **Universal Editor** (JSON-based). The structure is flat and document-based. |
| **Assets** | **DAM (AEM Assets)** | **AEM Assets** (referenced by URL) or direct image references in the document. |

---

## 2. The "Block" (Your New Component)

In EDS, everything is a "Block".

### Structure

A folder in `/blocks/` containing:

* `blockname.js`: The logic (replaces HTL/Java).
* `blockname.css`: The styles (replaces ClientLibs).
* `_blockname.json`: The dialog definition (Universal Editor model).

### How it works

1. The browser receives raw HTML (tables/divs) from the content source.
2. Your JS `decorate(block)` function runs client-side.
3. It transforms that raw HTML into your desired UI component (Slider, Hero, Accordion).

---

## 3. HTML & Rendering (Goodbye HTL, Hello DOM)

**Old Way (HTL):**

```html
<div data-sly-use...>${item.title}</div>
```

**New Way (JS):**

```javascript
export default function decorate(block) {
  // 1. Read content from the raw block
  const [titleRow] = block.children; 
  
  // 2. Render new HTML structure
  // Tip: Use Template Literals (backticks) to write HTML strings
  block.innerHTML = `
    <div class="my-title">
        <h1>${titleRow.textContent}</h1>
    </div>
  `; 
}
```

---

## 4. Styling (Scoped & Lazy)

**Old Way:** Global `clientlib-site` loaded on every page.

**New Way:**

* `styles/styles.css`: Global variables (colors, fonts) and reset styles.
* `blocks/blockname/blockname.css`: Styles *specific* to that block.
  * **Lazy Loading:** EDS automatically loads this file *only* if the block exists on the page.
  * **Scoping:** Use `.block-name` as your main selector to keep styles scoped.

---

## 5. Dialogs (The Universal Editor Model)

**Old Way:** Nested XML nodes (`cq:dialog`, `tab1`, `textfield`).

**New Way:** A flat JSON file (`blocks/blockname/_blockname.json`).

* You define `fields` (text, image, reference, select).
* **Build Step:** Run `npm run build:json` to combine these into the master `component-models.json`.
* The Universal Editor reads this to generate the authoring UI.

---

## 6. Page Templates & Layout

**Old Way:** Complex Template Editor with locked/unlocked structure components.

**New Way:**

* **Metadata Block:** A special table in your document (or `head.html`) that sets page title, description, and "template" name.
* **Sections:** A page is just a list of Sections (horizontal stripes).
* **Section Metadata:** A special block used to style specific sections (e.g., "Style: Highlight" adds a `.highlight` class to the section wrapper).
* **Headers/Footers:** Managed as separate documents (`/nav`, `/footer`) and loaded dynamically via `scripts/scripts.js`.

---

## 7. Development Workflow

1. **Code:** Edit local files in VS Code.
2. **Preview:** Run `aem up` (the local proxy) to see your local code running against *live* content.
3. **Content:** Edit content in Word/Google Docs or Universal Editor.
4. **Deploy:** Commit to GitHub. The "Helix Bot" automatically deploys changes to the preview/live environment.

---

## 8. Key Files to Know

* `scripts/scripts.js`: The "Main" entry point. Handles loading headers, footers, and global logic.
* `scripts/aem.js`: The "Kernel". Contains the core EDS logic (image optimization, block loading). **Do not edit this unless necessary.**
* `styles/styles.css`: Your global theme (variables, typography).
* `fstab.yaml`: Maps your content source (SharePoint/Google Drive) to the site.

---

## Practice Exercise: Build a "Teaser" Block

1. **Folder:** Create `blocks/teaser/`.
2. **Files:** Add `teaser.js`, `teaser.css`, `_teaser.json`.
3. **JSON:** Define fields for `Image`, `Title` (text), and `Link` (url).
4. **JS:**
    * Read the image and text from `block.children`.
    * Create a DOM structure: `<div class="teaser-card"><img src.../><a href...>Title</a></div>`.
    * Replace `block.innerHTML`.
5. **CSS:** Style `.teaser-card` to look nice.
6. **Build:** Run `npm run build:json`.
7. **Test:** Add a "Teaser" block in the Universal Editor or Word Doc and see it render.

---

## Links

### Basic Concepts

<https://www.youtube.com/watch?v=2_fXhIOFb3Q&list=PLEaEQSM_Y4tlrOCheSWep4r6yTjA3eL_0&index=1>
<https://www.aem.live/developer/anatomy-of-a-project>
<https://www.aem.live/developer/ue-tutorial>
<https://www.aem.live/developer/universal-editor-blocks>
<https://www.aem.live/developer/block-collection>

### Other Useful References

<https://www.aem.live/docs/dev-collab-and-good-practices#css>
<https://www.aem.live/docs/dev-collab-and-good-practices#javascript>
<https://experienceleague.adobe.com/en/docs/experience-manager-cloud-service/content/implementing/developing/universal-editor/component-definition>
<https://experienceleague.adobe.com/en/docs/experience-manager-cloud-service/content/implementing/developing/universal-editor/field-types>
<https://experienceleague.adobe.com/en/docs/experience-manager-cloud-service/content/implementing/developing/universal-editor/filtering>
