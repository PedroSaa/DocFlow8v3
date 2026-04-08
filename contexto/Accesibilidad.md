# ACCESSIBILIDAD — DocFlow8v3 (WCAG 2.1 AA)

**Fecha**: Abril 2026  
**Target**: WCAG 2.1 nivel AA  
**Alcance**: Todo el frontend Angular 17

---

## 1. PRINCIPIOS WCAG

### 1.1 Perceptible

| Criterio | Implementacion |
|----------|----------------|
| **1.1.1** Contenido no textual | Todo imagen debe tener `alt` descriptivo |
| **1.2.1** Solo audio/video grabados | Subtitulos/transcripcion cuando corresponda |
| **1.3.1** Informacion y relaciones | Semantica HTML correcta (header, main, nav, section) |
| **1.3.2** Secuencia significativa | Orden de tabulacion logico |
| **1.4.1** Uso del color | No usar solo color para indicar estado (伴text/icono) |
| **1.4.3** Contraste minimo | 4.5:1 para texto normal, 3:1 para texto grande |
| **1.4.4** Redimensionar texto | Soporta zoom 200% sin perdida de contenido |
| **1.4.10** Reflujo | Sin scroll horizontal a 320px |
| **1.4.11** Contraste no textual | 3:1 para componentes graficos |

### 1.2 Operable

| Criterio | Implementacion |
|----------|----------------|
| **2.1.1** Teclado | Toda funcionalidad accesible por teclado |
| **2.1.2** Sin trampas | Focus puede salir de todo componente |
| **2.4.1** Saltar bloques | Link "Saltar al contenido principal" al inicio |
| **2.4.2** Titulos de pagina | Titulos descriptivos por vista |
| **2.4.3** Focus secuencial | Recorrido logico de focus |
| **2.4.4** Proposito del enlace | Texto descriptivo (no "click aqui") |
| **2.4.6** Encabezados y etiquetas | Encabezados (h1-h6) y labels descriptivos |
| **2.4.7** Focus visible | Outline visible en todo elemento enfocable |
| **2.5.3** Etiqueta en nombre | Aria-label cuando label visible no existe |

### 1.3 Comprensible

| Criterio | Implementacion |
|----------|----------------|
| **3.1.1** Idioma de pagina | `<html lang="es">` |
| **3.2.1** En foco | No cambiar contexto al recibir focus |
| **3.2.2** Con entrada | No cambiar contexto al entrada de usuario |
| **3.3.1** Identificacion de errores | Mensajes de error claros y especificos |
| **3.3.2** Etiquetas o instrucciones | Labels en todos los campos de formulario |

### 1.4 Robusto

| Criterio | Implementacion |
|----------|----------------|
| **4.1.1** Parsing | HTML valido |
| **4.1.2** Nombre, rol, valor | ARIA para componentes complejos |

---

## 2. IMPLEMENTACION EN DOCFLOW

### 2.1 Skip Link (Saltar contenido)

```html
<!-- Primera linea del body -->
<a href="#main-content" class="skip-link">Saltar al contenido principal</a>
```

```scss
.skip-link {
  position: absolute;
  top: -40px;
  left: 0;
  background: var(--primary);
  color: white;
  padding: 8px 16px;
  z-index: 9999;
  transition: top 0.2s;
  
  &:focus {
    top: 0;
  }
}
```

### 2.2 Estructura Semantica

```html
<body>
  <a href="#main-content" class="skip-link">Saltar al contenido principal</a>
  
  <app-root>
    <header role="banner">
      <nav role="navigation" aria-label="Menu principal">
        <!-- Sidebar -->
      </nav>
    </header>
    
    <main id="main-content" role="main">
      <!-- Contenido principal -->
    </main>
    
    <footer role="contentinfo">
      <!-- Footer -->
    </footer>
  </app-root>
</body>
```

### 2.3 ARIA Labels

#### Sidebar
```html
<aside class="sidebar" role="navigation" aria-label="Menu de navegacion">
  <nav>
    <ul role="menubar">
      <li role="menuitem">
        <a href="#" aria-current="page">Dashboard</a>
      </li>
    </ul>
  </nav>
</aside>
```

#### Botones con iconos
```html
<button class="btn-icon" aria-label="Cerrar sesion">
  <svg aria-hidden="true">...</svg>
</button>

<button class="btn-icon" aria-label="Expandir menu">
  <svg aria-hidden="true">...</svg>
</button>
```

#### Formularios
```html
<label for="input-email">Correo electronico</label>
<input 
  type="email" 
  id="input-email" 
  aria-describedby="email-hint"
  required
>
<span id="email-hint" class="form-hint">
  Ingrese su correo institucional
</span>

<!-- Error state -->
<input 
  type="email" 
  id="input-email"
  aria-invalid="true"
  aria-describedby="email-error"
>
<span id="email-error" class="error-message" role="alert">
  El correo es requerido
</span>
```

### 2.4 Contraste de Colores

```scss
// Verificar: --gray-700 (text) sobre --gray-50 (background)
// Ratio: 4.5:1 minimum ✅

// Textos
--text-primary: var(--gray-800);    // 10.3:1 sobre white
--text-secondary: var(--gray-600);  // 7.5:1 sobre white
--text-muted: var(--gray-500);       // 4.5:1 sobre white (minimo)

// Links
--link-color: var(--primary);        // 4.5:1 sobre white

// Botones (text sobre fondo)
--btn-primary-bg: var(--primary);    // white on #1a56db = 5.3:1
--btn-secondary-bg: white;          // #374151 on white = 10.3:1
--btn-danger-bg: white;             // #e02424 on white = 4.5:1
```

### 2.5 Focus States

```scss
*:focus {
  outline: 2px solid var(--primary);
  outline-offset: 2px;
}

*:focus:not(:focus-visible) {
  outline: none;
}

*:focus-visible {
  outline: 2px solid var(--primary);
  outline-offset: 2px;
}

// Links
a:focus-visible {
  outline: 2px solid var(--primary);
  outline-offset: 2px;
  border-radius: var(--radius-sm);
}

// Skip link
.skip-link:focus {
  top: 0;
  outline: 2px solid var(--primary);
}
```

### 2.6 Formularios - Validacion

```html
<form (ngSubmit)="onSubmit()" #form="ngForm">
  <div class="form-group" [class.has-error]="email.invalid && email.touched">
    <label for="email">Correo *</label>
    <input 
      type="email" 
      id="email"
      [(ngModel)]="model.email"
      name="email"
      #email="ngModel"
      required
      email
      [attr.aria-invalid]="email.invalid && email.touched ? 'true' : null"
      [attr.aria-describedby]="email.invalid && email.touched ? 'email-error' : null"
    >
    <div *ngIf="email.invalid && email.touched" id="email-error" class="error-message" role="alert">
      <span *ngIf="email.errors?.['required']">El correo es requerido</span>
      <span *ngIf="email.errors?.['email']">Ingrese un correo valido</span>
    </div>
  </div>
  
  <button type="submit" [disabled]="form.invalid">
    Enviar
  </button>
</form>
```

```scss
.form-group.has-error {
  .form-input {
    border-color: var(--danger);
    
    &:focus {
      box-shadow: 0 0 0 3px rgba(224, 36, 36, 0.1);
    }
  }
  
  .form-label {
    color: var(--danger);
  }
}

.error-message {
  color: var(--danger);
  font-size: var(--text-sm);
  margin-top: 4px;
  display: flex;
  align-items: center;
  gap: 4px;
}
```

### 2.7 Navegacion por Teclado

```scss
// Tab order: sidebar → header → main content
// Skip link primero

// Arrow keys en menus
.nav-item {
  &::after {
    // Triangle indicator for nested menus
    border: none; 
  }
}

// Dropdowns
.dropdown {
  &[aria-expanded="true"] {
    // Show options
  }
}

// Modals - focus trap
.modal {
  // Close on Escape
  // Return focus to trigger on close
}

// Keyboard shortcuts
.keyboard-shortcut {
  display: inline-flex;
  align-items: center;
  gap: 4px;
  
  kbd {
    padding: 2px 6px;
    background: var(--gray-100);
    border: 1px solid var(--gray-300);
    border-radius: var(--radius-sm);
    font-family: monospace;
    font-size: var(--text-xs);
  }
}
```

### 2.8 Timeout y Sesiones

```typescript
// Advertencia antes de timeout
@Directive({ selector: '[appSessionTimeout]' })
export class SessionTimeoutDirective implements OnDestroy {
  private timeoutService = inject(TimeoutService);
  
  // Mostrar warning 5 min antes de expire
  // Ofrecer extender sesion
}
```

```html
<!-- Session timeout warning modal -->
<div class="modal" role="alertdialog" aria-labelledby="timeout-title" aria-describedby="timeout-desc">
  <h2 id="timeout-title">Sesion a punto de expirar</h2>
  <p id="timeout-desc">Su sesion expira en 5 minutos. Desea continuar?</p>
  <div class="modal-actions">
    <button (click)="extendSession()">Continuar sesion</button>
    <button (click)="logout()">Cerrar sesion</button>
  </div>
</div>
```

---

## 3. CHECKLIST DE IMPLEMENTACION

### Prioridad Alta (essenciales)
- [ ] Skip link al contenido principal
- [ ] Estructura semantica HTML5
- [ ] Contraste 4.5:1 minimo
- [ ] Focus visible en todo elemento
- [ ] Labels en todos los campos
- [ ] ARIA en componentes complejos

### Prioridad Media
- [ ] Titulos de pagina descriptivos
- [ ] Encabezados (h1-h6) correctos
- [ ] mensajes de error claros
- [ ] Focus trap en modals
- [ ] Keyboard navigation completa

### Prioridad Baja
- [ ] Shortcuts documentados
- [ ] Estados de loading accesibles
- [ ] Tooltips con ARIA
- [ ] Animaciones reducibles (prefers-reduced-motion)

---

## 4. HERRAMIENTAS DE TEST

### Lighthouse (Chrome DevTools)
```
Audits > Accessibility
Run audit > Verificar score > AA
```

### AXE (Accessibility Checker)
```javascript
// En testing
const axe = require('axe-core');
axe.run(document).then(results => {
  console.log(results.violations);
});
```

### WAVE (Web Accessibility Evaluation)
- https://wave.webaim.org/

### Validacion Manual
| Prueba | Accion |
|--------|--------|
| Screen reader | Navegar con NVDA/VoiceOver |
| Keyboard | Solo Tab/Enter/Escape |
| Zoom | 200% sin ruptura de layout |
| Color blind | Verificar no usar solo color |

---

## 5. ANGULAR - DIRECTIVES UTILES

```typescript
// Focus trap para modales
@Directive({
  selector: '[appFocusTrap]'
})
export class FocusTrapDirective implements AfterViewInit, OnDestroy {
  private elementRef = inject(ElementRef);
  private focusableElements: HTMLElement[] = [];
  
  ngAfterViewInit() {
    this.updateFocusableElements();
    this.focusableElements[0]?.focus();
  }
  
  private handleKeydown(event: KeyboardEvent) {
    if (event.key === 'Tab') {
      // Trap focus inside modal
    }
    if (event.key === 'Escape') {
      // Close modal
    }
  }
}

// Skip link target
@Directive({
  selector: '[appMainContent]'
})
export class MainContentDirective {
  @HostBinding('id') @Input() id = 'main-content';
  @HostBinding('tabindex') tabindex = -1;
}
```

---

## 6. EJEMPLO: COMPONENTE ACCESIBLE

### Badge/Tag
```html
<span 
  class="badge badge-success" 
  role="status"
  aria-label="Documento estado: activo"
>
  Activo
</span>

<!-- Con boton -->
<span class="badge-container">
  <span class="badge badge-warning">Pendiente</span>
  <button 
    class="badge-dismiss" 
    aria-label="Descartar notificacion"
  >
    ✕
  </button>
</span>
```

### Data Table
```html
<table class="doc-table" role="grid" aria-label="Lista de documentos">
  <thead>
    <tr role="row">
      <th scope="col" aria-sort="ascending">N° Documento</th>
      <th scope="col">Remitente</th>
      <th scope="col">Asunto</th>
      <th scope="col">Fecha</th>
      <th scope="col"><span class="sr-only">Acciones</span></th>
    </tr>
  </thead>
  <tbody>
    <tr role="row">
      <td><a href="/documents/1">245/2026</a></td>
      <td>Oficina de Partes</td>
      <td>Solicitud de informe</td>
      <td>07/04/2026</td>
      <td>
        <button aria-label="Ver documento 245/2026">Ver</button>
      </td>
    </tr>
  </tbody>
</table>
```

---

*Accesibilidad v1.0 — Abril 2026*  
*WCAG 2.1 AA Compliance*