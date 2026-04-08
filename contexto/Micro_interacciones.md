# MICRO-INTERACCIONES — DocFlow8v3

**Fecha**: Abril 2026  
**Alcance**: Todo el frontend Angular 17

---

## 1. PRINCIPIOS

### 1.1 Tipo de animaciones
| Tipo | Duracion | Easing |
|------|----------|--------|
| **Instantanea** | < 100ms | - |
| **Rapida** | 150ms | ease-out |
| **Normal** | 200-250ms | ease |
| **Lenta** | 300-400ms | ease-in-out |

### 1.2 Propiedades a animer
- `opacity` - transiciones de visibilidad
- `transform` - movimiento (translate, scale, rotate)
- `background-color` - cambios de estado
- `box-shadow` - elevacion

### 1.3 NO animaciones
- `width` / `height` - usar transform en cambio
- `margin` / `padding` - usar transform
- `font-size` - cambiar abruptly

---

## 2. ANIMACIONES POR COMPONENTE

### 2.1 Transiciones de pantalla

```scss
// Fade in/out entre pantallas
@keyframes fadeIn {
  from { opacity: 0; }
  to { opacity: 1; }
}

@keyframes fadeOut {
  from { opacity: 1; }
  to { opacity: 0; }
}

.page-enter {
  animation: fadeIn 200ms ease forwards;
}

.page-leave {
  animation: fadeOut 150ms ease forwards;
}
```

### 2.2 Sidebar - Expandir/Colapsar

```scss
.sidebar {
  transition: width 250ms ease;
  
  .nav-label, .nav-badge, .sidebar-brand-text {
    transition: opacity 200ms ease, width 200ms ease;
  }
}

// Hover en nav items
.nav-item {
  transition: background 150ms ease, color 150ms ease;
  
  &:hover {
    background: rgba(255, 255, 255, 0.08);
    color: white;
  }
  
  &:active {
    transform: scale(0.98);
  }
}
```

### 2.3 Botones

```scss
.btn {
  transition: all 150ms ease;
  
  &:hover {
    transform: translateY(-1px);
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
  }
  
  &:active {
    transform: translateY(0) scale(0.98);
  }
  
  &:disabled {
    opacity: 0.5;
    transform: none;
    box-shadow: none;
  }
}

// Loading spinner en boton
.btn.loading {
  .btn-text { display: none; }
  .spinner { 
    display: block; 
    animation: spin 0.6s linear infinite; 
  }
}
```

### 2.4 Cards y Lists

```scss
// Hover en card KPI
.kpi-card {
  transition: transform 200ms ease, box-shadow 200ms ease;
  
  &:hover {
    transform: translateY(-2px);
    box-shadow: 0 8px 24px rgba(0, 0, 0, 0.12);
  }
}

// Hover en item de lista
.recent-item {
  transition: background 150ms ease;
  
  &:hover {
    background: var(--gray-50);
  }
}

// Selection de item
.recent-item.selected {
  transition: background 150ms ease;
  background: var(--primary-light);
}
```

### 2.5 Inputs y Forms

```scss
.form-input {
  transition: border-color 150ms ease, box-shadow 150ms ease;
  
  &:focus {
    border-color: var(--primary);
    box-shadow: 0 0 0 3px rgba(var(--primary-rgb), 0.1);
  }
  
  // Error state animation
  &.error {
    animation: shake 300ms ease;
    
    @keyframes shake {
      0%, 100% { transform: translateX(0); }
      20%, 60% { transform: translateX(-4px); }
      40%, 80% { transform: translateX(4px); }
    }
  }
}

// Checkbox custom
.form-checkbox input {
  transition: transform 150ms ease;
  
  &:checked {
    transform: scale(1.1);
  }
}
```

### 2.6 Tabs

```scss
.tab-btn {
  transition: all 150ms ease;
  
  &:hover {
    color: var(--gray-700);
  }
  
  &.active {
    transition: all 200ms ease;
    background: white;
    color: var(--primary);
    box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
  }
}

// Content transition
.tab-content {
  animation: fadeIn 200ms ease;
}
```

### 2.7 Modals

```scss
.modal-overlay {
  transition: opacity 200ms ease;
  
  &.entering {
    opacity: 0;
  }
  &.entered {
    opacity: 1;
  }
  &.exiting {
    opacity: 1;
  }
  &.exited {
    opacity: 0;
  }
}

.modal {
  transition: transform 250ms ease, opacity 250ms ease;
  
  &.entering {
    transform: scale(0.95) translateY(10px);
    opacity: 0;
  }
  &.entered {
    transform: scale(1) translateY(0);
    opacity: 1;
  }
}
```

### 2.8 Toasts

```scss
.toast {
  transition: transform 300ms ease, opacity 300ms ease;
  
  &.show {
    transform: translateX(-50%) translateY(0);
    opacity: 1;
  }
  
  &.hiding {
    transform: translateX(-50%) translateY(20px);
    opacity: 0;
  }
}
```

### 2.9 Sidebar Tabs (Bandeja)

```scss
.bandeja-tab {
  transition: background 150ms ease, color 150ms ease;
  
  &:hover {
    background: var(--gray-50);
  }
  
  &.active {
    &::before {
      animation: slideIn 200ms ease;
    }
    
    @keyframes slideIn {
      from { transform: scaleY(0); }
      to { transform: scaleY(1); }
    }
  }
}
```

### 2.10 Panel Atender (Flotante)

```scss
.attend-panel {
  transition: width 250ms ease;
  
  &.open {
    animation: slideInRight 250ms ease;
  }
  
  @keyframes slideInRight {
    from {
      transform: translateX(100%);
    }
    to {
      transform: translateX(0);
    }
  }
}

.attend-tab {
  transition: color 150ms ease, background 150ms ease;
  
  &.active {
    transition: all 200ms ease;
  }
}
```

### 2.11 Checklist (Progreso)

```scss
.progress-fill {
  transition: width 300ms ease;
}

// Checkbox animation
.check-item input {
  transition: transform 200ms ease;
  
  &:checked {
    animation: checkBounce 300ms ease;
    
    @keyframes checkBounce {
      0% { transform: scale(1); }
      50% { transform: scale(1.2); }
      100% { transform: scale(1); }
    }
  }
}

.check-item.completed {
  .check-item-text {
    transition: text-decoration 200ms ease, color 200ms ease;
  }
}
```

---

## 3. LOADING STATES

### 3.1 Skeleton Loading

```scss
.skeleton {
  background: linear-gradient(
    90deg,
    var(--gray-200) 25%,
    var(--gray-100) 50%,
    var(--gray-200) 75%
  );
  background-size: 200% 100%;
  animation: skeleton-pulse 1.5s ease-in-out infinite;
  
  @keyframes skeleton-pulse {
    0% { background-position: 200% 0; }
    100% { background-position: -200% 0; }
  }
}

.skeleton-text {
  height: 16px;
  border-radius: var(--radius-sm);
  margin-bottom: 8px;
}

.skeleton-title {
  height: 24px;
  width: 60%;
  border-radius: var(--radius-sm);
  margin-bottom: 12px;
}
```

### 3.2 Spinner

```scss
.spinner {
  width: 20px;
  height: 20px;
  border: 2px solid rgba(255, 255, 255, 0.3);
  border-top-color: white;
  border-radius: 50%;
  animation: spin 0.6s linear infinite;
}

@keyframes spin {
  to { transform: rotate(360deg); }
}
```

### 3.3 Progress Bar

```scss
.progress-bar {
  .progress-fill {
    transition: width 300ms ease;
    
    // Animated stripes
    background-image: linear-gradient(
      45deg,
      rgba(255, 255, 255, 0.15) 25%,
      transparent 25%,
      transparent 50%,
      rgba(255, 255, 255, 0.15) 50%,
      rgba(255, 255, 255, 0.15) 75%,
      transparent 75%,
      transparent
    );
    background-size: 1rem 1rem;
    animation: progress-stripes 1s linear infinite;
  }
}

@keyframes progress-stripes {
  from { background-position: 1rem 0; }
  to { background-position: 0 0; }
}
```

---

## 4. FEEDBACK VISUAL

### 4.1 Hover States

```scss
// Links
a {
  transition: color 150ms ease;
  
  &:hover {
    text-decoration: underline;
  }
}

// Icon buttons
.icon-btn {
  transition: background 150ms ease, color 150ms ease, transform 150ms ease;
  
  &:hover {
    background: var(--gray-100);
    transform: scale(1.05);
  }
  
  &:active {
    transform: scale(0.95);
  }
}
```

### 4.2 Click/Tap Feedback

```scss
// Ripple effect para buttons
.btn {
  position: relative;
  overflow: hidden;
  
  &::after {
    content: '';
    position: absolute;
    top: 50%;
    left: 50%;
    width: 0;
    height: 0;
    background: rgba(255, 255, 255, 0.3);
    border-radius: 50%;
    transform: translate(-50%, -50%);
    transition: width 300ms ease, height 300ms ease;
  }
  
  &:active::after {
    width: 200%;
    height: 200%;
  }
}

// Touch feedback en mobile
@media (hover: none) {
  .btn:active {
    transform: scale(0.96);
  }
}
```

### 4.3 Selection States

```scss
// Table row selection
.doc-table tr {
  transition: background 150ms ease;
  
  &:hover {
    background: var(--gray-50);
  }
  
  &.selected {
    transition: background 150ms ease;
    background: var(--primary-light);
  }
}
```

---

## 5. UTILITY CLASSES

```scss
// Duraciones
.duration-150 { transition-duration: 150ms; }
.duration-200 { transition-duration: 200ms; }
.duration-250 { transition-duration: 250ms; }
.duration-300 { transition-duration: 300ms; }

// Easing
.ease-out { transition-timing-function: ease-out; }
.ease-in { transition-timing-function: ease-in; }
.ease-in-out { transition-timing-function: ease-in-out; }

// Propiedades
.transition-all { transition: all 150ms ease; }
.transition-colors { transition: background-color 150ms ease, color 150ms ease, border-color 150ms ease; }
.transition-transform { transition: transform 150ms ease; }
.transition-opacity { transition: opacity 150ms ease; }

// Animations
.animate-fade-in { animation: fadeIn 200ms ease; }
.animate-fade-out { animation: fadeOut 150ms ease; }
.animate-slide-in { animation: slideIn 250ms ease; }
.animate-spin { animation: spin 0.6s linear infinite; }
.animate-pulse { animation: pulse 2s ease-in-out infinite; }
.animate-bounce { animation: bounce 300ms ease; }
.animate-shake { animation: shake 300ms ease; }
```

---

## 6. PREFERENCIAS DEL USUARIO

### 6.1 Reduced Motion

```scss
@media (prefers-reduced-motion: reduce) {
  *,
  *::before,
  *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
  }
}
```

### 6.2 Angular Implementation

```typescript
// service/preference.service.ts
import { Injectable, signal } from '@angular/core';

@Injectable({ providedIn: 'root' })
export class PreferenceService {
  reducedMotion = signal(
    window.matchMedia('(prefers-reduced-motion: reduce)').matches
  );
  
  constructor() {
    window.matchMedia('(prefers-reduced-motion: reduce)')
      .addEventListener('change', (e) => {
        this.reducedMotion.set(e.matches);
      });
  }
}

// Usage in component
@Component({...})
export class CardComponent {
  private prefs = inject(PreferenceService);
  
  get animationClass() {
    return this.reducedMotion() ? 'no-animations' : 'animate-fade-in';
  }
}
```

---

## 7. PERFORMANCE

### 7.1 Reglas
- Usar `transform` y `opacity` - GPU accelerated
- Evitar animaciones en elementos grandes
- Usar `will-change` sparingly
- Animar elementos composites, no toda la pagina

### 7.2 GPU Acceleration

```scss
.gpu-accelerated {
  will-change: transform;
  transform: translateZ(0);
}

// Ejemplo: sidebar
.sidebar {
  transform: translateZ(0);
  backface-visibility: hidden;
}
```

---

## 8. RESUMEN RAPIDO

| Elemento | Animation | Duracion | Easing |
|----------|-----------|----------|--------|
| Botones hover | translateY(-1px) + shadow | 150ms | ease |
| Botones click | scale(0.98) | 100ms | ease |
| Sidebar collapse | width | 250ms | ease |
| Cards hover | translateY(-2px) | 200ms | ease |
| Tabs switch | opacity + background | 150ms | ease |
| Modal open | scale + opacity | 250ms | ease |
| Toast show | translateY + opacity | 300ms | ease |
| Input focus | border + shadow | 150ms | ease |
| Checkbox | scale | 200ms | ease |
| Progress bar | width | 300ms | ease |

---

*Micro-interacciones v1.0 — Abril 2026*