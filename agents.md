# CONTEXT.md — Landing Page · Arq. Celeste Celaya

Documento de referencia técnica y de diseño para el desarrollo y mantenimiento del proyecto. Establece las decisiones tomadas, convenciones adoptadas y el criterio estético que debe guiar cualquier modificación futura.

---

## 1. Stack Tecnológico

| Capa | Tecnología | Versión | Rol |
|---|---|---|---|
| Estructura | HTML5 semántico | — | Esqueleto de la página |
| Estilos | Vanilla CSS3 (inline en `<style>`) | — | Todo el diseño visual |
| Interactividad base | Vanilla JavaScript ES6+ | — | Lógica de eventos y animaciones propias |
| Animaciones / Scroll | **GSAP** + **ScrollTrigger** | 3.12.2 | Reveals, parallax, scroll horizontal, counters |
| 3D Hero | **Three.js** | r128 | Canvas WebGL en la sección hero |
| Fuentes | Google Fonts (CDN) | — | Tipografía display + mono |

### Dependencias externas (CDN)
```
https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js
https://cdnjs.cloudflare.com/ajax/libs/gsap/3.12.2/gsap.min.js
https://cdnjs.cloudflare.com/ajax/libs/gsap/3.12.2/ScrollTrigger.min.js
https://fonts.googleapis.com/css2?family=Cormorant+Garamond:ital,wght@0,300;0,400;0,600;1,300;1,400&family=DM+Mono:ital,wght@0,300;0,400;1,300
```

> **Regla:** No agregar librerías adicionales sin justificación. El peso actual es deliberadamente bajo para garantizar performance.

---

## 2. Arquitectura del Archivo

El proyecto vive en un único archivo `celeste-celaya.html`. Estructura interna:

```
<head>
  ├── Meta tags (charset, viewport, SEO title + description)
  ├── Google Fonts (preconnect + link)
  ├── CDN scripts (Three.js, GSAP, ScrollTrigger)
  └── <style> — todo el CSS del proyecto

<body>
  ├── .grain                   → overlay de textura fija
  ├── #cursor + #cursor-ring   → cursor personalizado
  ├── <nav>                    → navegación fixed
  ├── <section #hero>          → canvas 3D + contenido principal
  ├── .marquee-section         → banda de especialidades animada
  ├── <section #about>         → perfil, educación, skills, stats
  ├── <section #projects>      → scroll horizontal con 4 cards
  ├── <section #quote>         → cita de práctica
  ├── <section #services>      → grilla de 3 servicios
  ├── <section #experience>    → lista de experiencia profesional
  ├── <section #contact>       → datos de contacto
  └── <footer>

  └── <script>                 → toda la lógica JS
```

---

## 3. Sistema de Diseño

### 3.1 Paleta de Colores

Definida mediante variables CSS en `:root`. La paleta es **oscura, cálida y mineral** — inspirada en la tierra, la piedra y el metal de los Valles Calchaquíes.

| Variable | Hex | Uso |
|---|---|---|
| `--bg` | `#07080a` | Fondo principal de la página |
| `--bg2` | `#0d0f12` | Fondo secundario (reservado) |
| `--text` | `#e8e4dc` | Texto principal, títulos |
| `--muted` | `#5e5d5a` | Texto secundario, descripciones, labels |
| `--accent` | `#b8935a` | Color de acento — dorado tierra (principal) |
| `--accent-dim` | `#7a5e38` | Acento atenuado — scrollbar, hover sutil |
| `--accent-lt` | `#d4aa77` | Acento claro — hover en skill tags |
| `--border` | `#1a1c1f` | Líneas divisoras, bordes de cards |
| `--card` | `#0d0e11` | Fondo de tarjetas y bloques oscuros |

> **Criterio:** El dorado `#b8935a` es el color de marca. Evitar saturarlo — usarlo para énfasis editorial (cursivas, labels, líneas decorativas, números de acento). Nunca usarlo como fondo de bloques grandes.

### 3.2 Tipografía

Dos familias con roles bien diferenciados:

#### Cormorant Garamond — Display / Editorial
- **Uso:** H1, H2, títulos de proyectos, citas, datos de contacto, nombres de sección
- **Pesos usados:** 300 (light), 400 (regular), 600 (semibold) — principalmente 300
- **Estilo itálico:** usado para énfasis cromático + itálica (`<em>`) en títulos principales
- **Tamaños:** `clamp(4.5rem, 8.5vw, 9.5rem)` en hero · `clamp(2.5rem, 5vw, 5rem)` en secciones
- **Carácter:** Elegante, arquitectónico, con reminiscencias editoriales de revista de diseño

#### DM Mono — UI / Técnico
- **Uso:** Todo el cuerpo de texto, labels, navegación, tags, datos técnicos, metadata
- **Pesos usados:** 300 (light), 400 (regular)
- **Tamaños típicos:** `0.56rem`–`0.76rem` con `letter-spacing` amplio (`0.14em`–`0.38em`)
- **Carácter:** Técnico, preciso, evoca documentación de obra y planimetría

> **Regla de oro:** Títulos grandes = Cormorant + weight 300 + posible cursiva dorada. Cualquier texto pequeño, etiqueta o metadata = DM Mono + letter-spacing generoso + color `--muted`.

### 3.3 Espaciado y Ritmo Vertical

El diseño usa espaciado generoso para comunicar premium y respiración:

| Contexto | Valor |
|---|---|
| Padding de secciones principales | `padding: 13rem 5rem` |
| Padding horizontal base | `5rem` (desktop) · `2rem` (mobile ≤900px) |
| Gap entre elementos de grilla | `1px` (con `background: var(--border)` para efecto de línea) |
| Margen entre label de sección y título | `1.8rem` |
| Tamaño mínimo de tarjeta de proyecto | `min-width: 360px` |

### 3.4 Efectos Visuales Globales

| Efecto | Implementación | Descripción |
|---|---|---|
| Textura grain | `<div class="grain">` + SVG feTurbulence | Overlay fixed, `opacity: 0.32`, simula film fotográfico |
| Cursor personalizado | `#cursor` (punto) + `#cursor-ring` (anillo) | Reemplaza cursor nativo. El anillo tiene lag inercial |
| Scrollbar | `::-webkit-scrollbar` | 3px, color `--accent-dim` |
| Selection | `::selection` | Background `--accent`, texto `--bg` |

---

## 4. Sistema de Animaciones (GSAP)

### 4.1 Clase `.reveal`
Todos los elementos que aparecen al hacer scroll llevan la clase `.reveal`.

```css
.reveal { opacity: 0; transform: translateY(55px); }
```

El ScrollTrigger activa la animación cuando el elemento entra al 86% del viewport:
```js
gsap.to(el, {
  opacity: 1, y: 0, duration: .9, ease: 'power3.out',
  scrollTrigger: { trigger: el, start: 'top 86%', toggleActions: 'play none none reverse' }
});
```

> Para agregar un elemento con reveal, simplemente añadir la clase `reveal` al HTML.

### 4.2 Animaciones disponibles

| Animación | Trigger | Descripción |
|---|---|---|
| Hero entrance | Page load | Palabras del H1 suben desde `translateY(108%)`, stagger de 0.18s |
| Hero parallax | Scroll | El `.hero-content` sube y se desvanece al salir del viewport |
| Scroll horizontal | `#projects` pinneado | El track de proyectos se desplaza en X mientras el usuario scrollea verticalmente |
| About parallax | Scroll | `.about-frame` flota `-60px` al pasar por la sección |
| Counters | ScrollTrigger `top 82%` | Números animados con `gsap.to({ v: 0 }, { v: target })` |
| Quote reveal | ScrollTrigger | Fade + translateY de la cita |
| Experience hover | `mouseenter/mouseleave` | `paddingLeft` de `0` a `1.5rem` con GSAP |
| Card 3D tilt | `mousemove` en `.p-card` | `rotationX` + `rotationY` con `transformPerspective: 900` |

### 4.3 Scroll Horizontal — Proyectos

El scroll horizontal está implementado con `pin: true` en ScrollTrigger. El `end` se calcula dinámicamente:

```js
end: () => `+=${track.scrollWidth - innerWidth + 160}`
```

Al agregar o quitar tarjetas, el scroll se recalcula automáticamente gracias a `invalidateOnRefresh: true`.

---

## 5. Three.js — Hero 3D

### Composición de la escena

La escena representa abstractamente los proyectos de Celeste como volúmenes arquitectónicos en wireframe dorado:

| Elemento | Geometría | Referencia |
|---|---|---|
| Bloque principal | `BoxGeometry(3.8, 1.2, 2.6)` | Cubierta de la Bodega Albor |
| Cilindro Corten | `CylinderGeometry(.7, .7, 2.8, 16)` + `EdgesGeometry` | Cilindro central de Bodega Albor |
| Torre | `BoxGeometry(.9, 5.5, .9)` | Torre de viviendas Pinares |
| Volúmenes menores | Varios `BoxGeometry` pequeños | Escuela y conjunto vitivinícola |
| Grid del piso | `GridHelper(20, 18)` | Terreno / viñedos |
| Partículas | `PointsMaterial` (110 puntos) | Polvo de obra / atmósfera del Valle |

### Interactividad de la escena

- **Mouse:** El grupo rota suavemente según la posición del cursor (lerp con factor `0.045`)
- **Scroll:** `camera.position.y` baja levemente al hacer scroll, creando profundidad
- **Auto-rotación:** `grp.rotation.y += t * 0.04` — giro continuo lento

---

## 6. Estructura de Secciones

| ID / Selector | Componente | Notas clave |
|---|---|---|
| `<nav>` | Navegación fixed | Logo `C.C.` + 5 links. `::before` con gradiente para legibilidad sobre el hero |
| `#hero` | Sección principal | `height: 100vh`, canvas Three.js absoluto, contenido con `z-index: 10` |
| `.marquee-section` | Banda de texto animada | CSS `animation: marquee 26s linear infinite`. Duplicar items si se agregan más |
| `#about` | Perfil | Grid 2 columnas: texto+stats / visual+edu+skills |
| `#projects` | Portfolio horizontal | Scroll pinneado con GSAP. Cards de `aspect-ratio: 2/3` |
| `#quote` | Cita | Centrada, `max-width: 860px`, auto-margin |
| `#services` | Servicios | Grid 3 cols con `gap: 1px` + `background: var(--border)` como separador |
| `#experience` | Experiencia | Lista con grid 3 cols: año / descripción / tag |
| `#contact` | Contacto | Grid 2 cols: titular / items de datos |
| `<footer>` | Pie | Flex space-between: copyright / links sociales |

---

## 7. Tarjetas de Proyecto (`.p-card`)

Cada tarjeta tiene esta estructura interna:

```html
<div class="p-card">
  <div class="p-card-inner">
    <div class="p-bg p-bg-N"></div>        <!-- Fondo decorativo CSS -->
    <svg class="p-drawing">...</svg>        <!-- Plano técnico SVG -->
    <div class="p-overlay">
      <span class="p-num">0N</span>
      <div class="p-arrow">...</div>        <!-- Ícono visible on hover -->
      <div class="p-tag">Tipo — Año</div>
      <h3 class="p-title">Nombre</h3>
      <p class="p-info">Lugar · m² · Etapa</p>
      <p class="p-desc">Descripción breve</p>  <!-- Visible on hover -->
    </div>
  </div>
</div>
```

Los fondos `.p-bg-N` son gradientes CSS únicos por proyecto. Los SVGs son dibujos técnicos esquemáticos (plantas, cortes, axonometrías) que se vuelven más visibles al hacer hover (`opacity: 0.13` → `0.30`).

El desplazamiento vertical entre tarjetas (`margin-top`) es intencional para crear ritmo asimétrico: `0px / 100px / -20px / 130px`.

---

## 8. Responsive Design

El breakpoint principal es `max-width: 900px`:

- La navegación oculta los links (`.nav-links { display: none }`)
- Las grillas de 2 columnas pasan a 1 columna (`#about`, `#contact`, `.edu-skills`)
- La grilla de servicios pasa a 1 columna
- Los paddings horizontales se reducen de `5rem` a `2rem`
- Las tarjetas de proyecto tienen `min-width: 280px`
- El footer pasa a columna centrada

> Pendiente: implementar menú hamburguesa para mobile. Los links de nav están ocultos en mobile actualmente.

---

## 9. SEO

```html
<title>Celeste Celaya — Arquitecta | Salta, Argentina</title>
<meta name="description" content="Arq. Celeste Celaya — Arquitecta y Maestra Mayor de Obras graduada en la FAU-UCASAL 2025. Proyectos residenciales, agroindustriales y educativos en Salta, Argentina.">
```

- H1 único en la página: `Celeste Celaya` (con `<em>` en el apellido)
- Títulos jerárquicos: `<h1>` hero → `<h2>` secciones → `<h3>` tarjetas de proyecto
- Los SVGs de planos técnicos no tienen `alt` (son decorativos); agregar `aria-hidden="true"` si se requiere accesibilidad estricta

---

## 10. Convenciones de Código

### CSS
- Variables globales en `:root`
- Comentarios de bloque con `/* ── SECCIÓN ── */`
- Sin clases utilitarias genéricas (no Tailwind, no Bootstrap)
- Transiciones: preferir `cubic-bezier(.23,1,.32,1)` para movimientos arquitectónicos suaves

### JavaScript
- Todo el JS en un único `<script>` al final del `<body>`
- IIFEs `(function() { ... })()` para aislar el scope de Three.js
- Comentarios de sección con `// ── NOMBRE ──────`
- Animaciones GSAP siempre con `ease: 'power3.out'` como default (salvo scroll scrub que usa `'none'`)
- ScrollTrigger registrado con `gsap.registerPlugin(ScrollTrigger)` al inicio

---

## 11. Decisiones de Diseño Clave

| Decisión | Razón |
|---|---|
| Fondo muy oscuro (`#07080a`) | Hace que los SVGs dorados brillen, evoca la noche salteña y la cámara de fermentación |
| Sin imágenes fotográficas | El portfolio usa dibujos técnicos SVG como representación — más coherente con la práctica arquitectónica y evita depender de renders externos |
| Scroll horizontal en proyectos | Crea una experiencia de "desplazamiento por el proyecto" análoga a un panel de presentación académica |
| Cursor personalizado | Diferencia la experiencia, refuerza el carácter premium del sitio |
| Tipografía mono para UI | Evoca la documentación técnica de arquitectura (AutoCAD, planillas, memorias descriptivas) |
| Grain overlay | Añade materialidad al fondo plano, evita la frialdad digital |
| Three.js abstracto (no fotorrealista) | Representa los proyectos como volúmenes puros — coherente con el pensamiento arquitectónico de formas antes que materiales |