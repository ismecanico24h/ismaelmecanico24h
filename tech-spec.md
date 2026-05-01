# Tech Spec - Mecánico Isamel

## Dependencias

### Producción

| Paquete | Versión | Justificación |
|---------|---------|---------------|
| `react` | ^18.3.0 | Framework UI |
| `react-dom` | ^18.3.0 | Renderizado DOM |
| `framer-motion` | ^11.0.0 | Animaciones complejas (scroll, gestos, acordeones) |
| `lucide-react` | ^0.400.0 | Iconos (teléfono, WhatsApp, herramientas, check, etc.) |
| `tailwindcss` | ^3.4.0 | Estilos utilitarios |
| `@radix-ui/react-accordion` | ^1.2.0 | Base para FAQ acordeón accesible |

### Desarrollo

| Paquete | Versión | Justificación |
|---------|---------|---------------|
| `typescript` | ^5.4.0 | Tipado estático |
| `vite` | ^5.0.0 | Bundler / dev server |
| `@vitejs/plugin-react` | ^4.0.0 | Plugin React para Vite |
| `autoprefixer` | ^10.4.0 | Prefijos CSS automáticos |
| `postcss` | ^8.4.0 | Procesamiento CSS |

---

## Inventario de Componentes

### Layout

| Componente | Fuente | Reutilización | Notas |
|------------|--------|---------------|-------|
| `FloatingButtons` | Custom | Único | Botones flotantes fijos (WhatsApp + Llamar), visibles en toda la página |
| `EmergencyBanner` | Custom | Único | Barra sticky roja en top, cierrable |

### Secciones

| Componente | Fuente | Notas |
|------------|--------|-------|
| `HeroSection` | Custom | Background con overlay, CTAs, badge 24/7 |
| `ServicesSection` | Custom | Grid 2x2 de tarjetas de servicio |
| `AboutSection` | Custom | Dos columnas: texto + imagen |
| `EmergencySection` | Custom | Banner rojo de urgencia |
| `TestimonialsSection` | Custom | Grid 2x2 de opiniones |
| `PricingSection` | Custom | Grid 3 columnas de precios + servicios rápidos |
| `CoverageSection` | Custom | Lista de zonas + mapa embebido |
| `ContactSection` | Custom | Grid 3 columnas (teléfono, WhatsApp, email) + footer |

### Componentes Reutilizables

| Componente | Fuente | Usado en | Notas |
|------------|--------|----------|-------|
| `ServiceCard` | Custom | ServicesSection | Icono + título + descripción, hover con elevación |
| `TestimonialCard` | Custom | TestimonialsSection | Estrellas + cita + autor, borde lateral amarillo |
| `PricingCard` | Custom | PricingSection | Título + precio + lista de checks. Variante destacada con borde amarillo |
| `PrimaryButton` | Custom | Global | Pill shape amarillo, enlaza a tel: |
| `WhatsAppButton` | Custom | Global | Pill shape verde WhatsApp, enlaza a wa.me |
| `SectionTitle` | Custom | Todas las secciones | Label amarillo opcional + título principal |
| `AnimatedSection` | Custom | Todas las secciones | Wrapper con IntersectionObserver + fade-in |

### Hooks

| Hook | Descripción |
|------|-------------|
| `useScrollReveal` | IntersectionObserver para animaciones de entrada en scroll. Umbral 0.15, una sola ejecución |

---

## Plan de Animaciones

| Animación | Librería | Enfoque | Complejidad |
|-----------|----------|---------|-------------|
| Fade-in + translateY entrada secciones | Framer Motion | `motion.div` con `whileInView`, viewport once | Baja |
| Stagger entre elementos de sección | Framer Motion | `staggerChildren: 0.1` en container | Baja |
| Hero título + botones entrada | Framer Motion | Secuencia: label → título → subtítulo → CTAs, delay escalonado | Baja |
| Badge 24/7 pulse infinito | Framer Motion | `animate={{ scale: [1, 1.05, 1] }}` con `transition.repeat: Infinity` | Baja |
| Hover tarjetas (elevación + borde) | CSS Tailwind | `hover:-translate-y-1 hover:border-yellow-400 transition-all duration-300` | Baja |
| Hover botones (scale) | CSS Tailwind | `hover:scale-105 transition-transform duration-200` | Baja |
| Acordeón FAQ expand/colapsar | Framer Motion | `AnimatePresence` + `motion.div` height animate | Media |
| Emergency banner slide-down | Framer Motion | `initial={{ y: -40 }}` → `animate={{ y: 0 }}` | Baja |
| Sobre mí: texto izq / img der | Framer Motion | `initial={{ x: -30, opacity: 0 }}` / `initial={{ x: 30, opacity: 0 }}` | Baja |
| Zonas: lista izq / mapa der | Framer Motion | Mismo patrón que Sobre mí | Baja |

---

## Estado y Lógica

- **Sin state global**: La aplicación es una landing page estática. No se requiere Context, Redux ni Zustand.
- **Estado local mínimo**:
  - `EmergencyBanner`: `isVisible` para permitir cerrar el banner (persistir en sessionStorage opcional)
  - `Accordion` (FAQ futuro): `openItem` string | null para controlar item expandido
- **No lógica de negocio compleja**: Solo enlaces a tel:, mailto:, wa.me:, y scroll suave a anclas
- **No formularios**: El contacto es solo vía teléfono/WhatsApp/email directo

---

## Otras Decisiones Clave

### Imágenes

Las siguientes imágenes serán generadas por IA (tool `generate_image`):

| ID | Descripción | Ratio | Sección |
|----|-------------|-------|---------|
| `hero-bg` | Furgoneta de servicio mecánico negra con herramientas, estacionada en calle urbana de noche con faros encendidos, ambiente dramático con niebla y luces de ciudad difuminadas en fondo, estilo fotografía profesional industrial oscuro | 16:9 | Hero (background) |
| `about-mechanic` | Mecánico profesional con uniforme oscuro trabajando en motor de coche con capó abierto, iluminación de foco portátil en ambiente nocturno, rostro concentrado, fondo urbano difuminado, estilo fotografía documental oscura | 3:4 | Sobre Mí |

Las imágenes de servicios usarán iconos de Lucide en lugar de fotografías para mantener coherencia visual y reducir peso.

### Mapa

El mapa de la sección Zonas será un iframe embebido de Google Maps (URL estática centrada en Alicante, España). No se requiere librería de mapas compleja para una ubicación estática.

### Accesibilidad

- Todos los enlaces telefónicos deben tener `aria-label` descriptivo
- Contraste de colores verificado: amarillo `#FFD700` sobre negro `#0A0A0A` = ratio 12.5:1 (AAA)
- Animaciones respetan `prefers-reduced-motion`
