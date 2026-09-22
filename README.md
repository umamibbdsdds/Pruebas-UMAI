# Umami — Sitio web publicitario e informativo

Sitio estático multi-página para **Umami**, restaurante de gastronomía moderna de fusión salvadoreño-japonesa en San Salvador. Construido con **HTML5 + Tailwind CSS (CDN) + CSS3 propio + JavaScript vanilla**, listo para ejecutarse tal cual o migrarse a plantillas Jinja2/Flask.

> El sitio es de **consulta solamente**: no incluye carrito, pedidos ni pagos en línea.

## Estructura

```
umami/
├── index.html        → Inicio (hero, experiencia, plato insignia, destacados, testimonios)
├── menu.html         → Menú digital con filtros interactivos
├── nosotros.html     → Historia, pilares y galería
├── ubicacion.html    → Mapa, contacto, horarios y reservas (#reservas)
├── resenas.html      → Promedio dinámico, distribución y carrusel
└── assets/
    ├── css/styles.css    → Sistema de diseño (tokens, componentes, animaciones)
    ├── js/data.js        → TODA la información del sitio (carta, reseñas, horarios)
    ├── js/main.js        → Interactividad (nav, filtros, carrusel, estado del local)
    └── img/              → Imágenes optimizadas + favicon.svg
```

## Ejecutar localmente

No requiere build. Desde la carpeta `umami/`:

```bash
# Opción A: Python
py -m http.server 8000        # Windows  |  python3 -m http.server 8000 (Linux/macOS)

# Opción B: Node
npx serve .
```

Luego abre `http://localhost:8000`. También funciona abriendo `index.html` directamente, pero servir por HTTP refleja mejor el comportamiento en producción (rutas relativas, iframe de Maps).

## Personalización

**Todo el contenido vive en `assets/js/data.js`**: los 16 platos de la carta (con precio, alérgenos, tiempo de elaboración), las 10 reseñas, la distribución de calificaciones y los horarios semanales. Editar ese archivo actualiza menú, filtros, promedios, el carrusel y el indicador «Abierto/Cerrado ahora» sin tocar HTML.

## Interactividad incluida

- **Header sticky** con `backdrop-blur` al hacer scroll.
- **Menú móvil accesible**: overlay deslizante, foco atrapado, cierre con `Escape`, retorno de foco, `inert` cuando está cerrado.
- **Filtros del menú**: búsqueda por texto, pestañas de categoría, rango de precio y chips de alérgenos/preferencias, con loader (spinner) simulado y estado vacío con botón «Limpiar filtros».
- **Reseñas**: promedio y estrellas calculados en runtime; carrusel con scroll-snap y flechas que se desactivan en los extremos (`opacity-50` + `cursor-not-allowed`).
- **Horarios**: resalta el día actual y calcula si el local está abierto ahora (incluye cierre a medianoche).
- **Scroll reveal** con `IntersectionObserver` y respeto a `prefers-reduced-motion`.

## Sistema de diseño

| Token | Valor | Uso |
|---|---|---|
| Terracota | `#E05A47` · dark `#C74736` · deep `#A93A2B` | CTAs y acentos (dark/deep garantizan contraste AA) |
| Oliva | `#4A5D4E` | Secciones secundarias, filtros activos |
| Dorado | `#D4AF37` | Detalles, subrayados, estrellas |
| Neutros | Crema `#FDFBF7` · Arena `#F4F4F2` · Carbón `#2B2B2B` | Fondos y texto |

Tipografía: **Playfair Display** (títulos) + **Plus Jakarta Sans** (cuerpo). Iconografía: **Lucide**.

## Migración a Flask / Jinja2

El HTML está diseñado para partirse en parciales sin cambios de marcado:

1. **`base.html`**: mover el `<head>` (con el bloque `tailwind.config`), `#site-header`, `#mobile-menu` y `<footer>` compartidos. Cada página actual solo difiere en `<main>` y el enlace con `aria-current="page"`.
2. **Plantillas**: `index.html` → `index()` · `menu.html` → `menu()` · `nosotros.html` · `ubicacion.html` · `resenas.html`.
3. **Datos por el backend**: reemplazar la etiqueta `<script src="assets/js/data.js"></script>` por:

   ```html
   <script>const UMAMI = {{ datos|tojson }};</script>
   ```

   `main.js` no requiere cambios: solo espera la global `UMAMI`.
4. **Página activa**: con Jinja, sustituir `aria-current="page"` fijo por `{% if request.path == ... %}` en el parcial del nav.

## Accesibilidad

Contraste WCAG AA en texto y CTAs, skip-link al contenido, roles ARIA en diálogo/carrusel/tabla, `aria-live` en contador de resultados y estado del local, indicadores de foco visibles y estados deshabilitados explícitos.
