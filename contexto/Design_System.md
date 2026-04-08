# DESIGN SYSTEM — DocFlow8v3

**Fecha**: Abril 2026  
**Estado**: Completo  
**Basado en**: Demos HTML (06-10) + Decisiones UI

---

## 1. TOKENS DE DISEÑO (Design Tokens)

### 1.1 Colores

#### Paleta Primaria
```scss
// Primary
--primary: #1a56db;           // Azul default
--primary-light: #e8effc;     // Fondo highlight
--primary-dark: #1240a8;     // Hover
--primary-rgb: 26, 86, 219;  // Para rgba() 
```

#### Colores Semánticos
```scss
// Estados
--success: #0d9f6e;           // Verde - exitos, completado
--success-light: #ecfdf5;   // Fondo success
--warning: #e3a008;          // Amarillo - warning, pendiente
--warning-light: #fefce8;    // Fondo warning
--danger: #e02424;           // Rojo - error, urgencia
--danger-light: #fef2f2;     // Fondo danger
--info: #3f83f8;             // Azul - informacion
--info-light: #e0f2fe;       // Fondo info
```

#### Escala de Grises
```scss
--gray-50: #f9fafb;   // Fondo pagina
--gray-100: #f3f4f6;  // Fondo cards, inputs disabled
--gray-200: #e5e7eb;  // Borders default
--gray-300: #d1d5db;  // Borders input focus
--gray-400: #9ca3af; // Placeholder text
--gray-500: #6b7280;  // Secondary text
--gray-600: #4b5563; // Body text
--gray-700: #374151; // Headings
--gray-800: #1f2937; // Sidebar dark
--gray-900: #111827; // Textos oscuros
```

#### Colores Badge de Estados
```scss
// Documentos
--badge-recibido: #e0f2fe;     // cyan #0369a1
--badge-derivacion: #ecfdf5;  // verde #047857
--badge-leido: #f3f4f6;       // gris #6b7280
--badge-urgente: #fef2f2;     // rojo #e02424

// Expedientes
--badge-activo: #ecfdf5;       // verde #047857
--badge-cerrado: #f3f4f6;     // gris
--badge-por-revisar: #fefce8; // amarillo #d97706

// Vencimiento
--vencimiento-vigente: #ecfdf5;
--vencimiento-vencido: #fef2f2;
```

---

### 1.2 Tipografía

```scss
// Familia
--font-family: 'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;

// Tamaños
--text-xs: 11px;    // Badges pequeños, metadata
--text-sm: 12px;    // Labels, hints
--text-base: 14px; // Body default
--text-md: 15px;   // Card titles
--text-lg: 16px;  // Section titles
--text-xl: 18px;  // Page titles
--text-2xl: 22px; // H1, headers grandes

// Pesos
--font-normal: 400;
--font-medium: 500;
--font-semibold: 600;
--font-bold: 700;

// Line heights
--leading-tight: 1.25;
--leading-normal: 1.5;
--leading-relaxed: 1.75;
```

---

### 1.3 Espaciado (Spacing)

```scss
// Escala base (4px)
--space-1: 4px;
--space-2: 8px;
--space-3: 12px;
--space-4: 16px;
--space-5: 20px;
--space-6: 24px;
--space-8: 32px;
--space-10: 40px;
--space-12: 48px;

// Layout
--sidebar-width: 260px;
--sidebar-collapsed: 72px;
--header-height: 64px;
--content-padding: 24px;
--card-padding: 20px;
--input-padding: 10px 14px;
```

---

### 1.4 Radios (Border Radius)

```scss
// Inputs, botones pequeños
--radius-sm: 4px;

// Buttons, badges
--radius-md: 6px;

// Cards, modals
--radius-lg: 8px;
--radius-xl: 12px;

// Avatars
--radius-full: 50%;
--radius-avatar: 10px;
```

---

### 1.5 Sombras (Shadows)

```scss
// Base
--shadow-sm: 0 1px 2px rgba(0, 0, 0, 0.05);
--shadow-md: 0 1px 3px rgba(0, 0, 0, 0.06);
--shadow-lg: 0 4px 12px rgba(0, 0, 0, 0.08);
--shadow-xl: 0 8px 24px rgba(0, 0, 0, 0.12);

// Especificos
--shadow-card: 0 1px 3px rgba(0, 0, 0, 0.06);
--shadow-dropdown: 0 4px 12px rgba(0, 0, 0, 0.08);
--shadow-modal: 0 8px 24px rgba(0, 0, 0, 0.12);
--shadow-sidebar: 0 0 10px rgba(0, 0, 0, 0.1);
```

---

### 1.6 Transiciones

```scss
--transition-fast: 0.15s ease;
--transition-base: 0.2s ease;
--transition-slow: 0.3s ease;

// Duraciones
--duration-150: 150ms;
--duration-200: 200ms;
--duration-250: 250ms;
```

---

## 2. COMPONENTES BASE

### 2.1 Botones (Buttons)

#### Variantes
```html
<!-- Primary -->
<button class="btn btn-primary">Label</button>

<!-- Secondary -->
<button class="btn btn-secondary">Label</button>

<!-- Danger -->
<button class="btn btn-danger">Label</button>

<!-- Ghost (sin fondo) -->
<button class="btn btn-ghost">Label</button>
```

#### Estados
```scss
// CSS
.btn {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
  padding: 10px 20px;
  border-radius: var(--radius-md);
  font-size: var(--text-base);
  font-weight: var(--font-semibold);
  cursor: pointer;
  transition: all var(--transition-fast);
  border: none;
  font-family: var(--font-family);
}

.btn-primary {
  background: var(--primary);
  color: white;
  &:hover { background: var(--primary-dark); }
  &:active { transform: scale(0.98); }
  &:disabled { opacity: 0.5; cursor: not-allowed; }
}

.btn-secondary {
  background: white;
  border: 1px solid var(--gray-300);
  color: var(--gray-700);
  &:hover { background: var(--gray-50); }
}

.btn-danger {
  background: white;
  border: 1px solid var(--danger);
  color: var(--danger);
  &:hover { background: var(--danger-light); }
}
```

#### Tamaños
```scss
.btn-sm   { padding: 6px 12px; font-size: var(--text-sm); }
.btn-md   { padding: 10px 20px; font-size: var(--text-base); }
.btn-lg   { padding: 12px 24px; font-size: var(--text-md); }
```

#### Con Iconos
```html
<button class="btn btn-primary">
  <svg width="16" height="16">...</svg>
  Guardar
</button>
```

---

### 2.2 Inputs (Form Elements)

#### Text Input
```html
<div class="form-group">
  <label class="form-label">Label <span class="required">*</span></label>
  <input type="text" class="form-input" placeholder="Placeholder">
</div>
```

```scss
.form-group {
  display: flex;
  flex-direction: column;
  gap: 6px;
}

.form-label {
  font-size: var(--text-sm);
  font-weight: var(--font-semibold);
  color: var(--gray-700);
  
  .required { color: var(--danger); }
}

.form-input {
  padding: var(--input-padding);
  border: 1px solid var(--gray-300);
  border-radius: var(--radius-md);
  font-size: var(--text-base);
  color: var(--gray-800);
  background: white;
  transition: all var(--transition-fast);
  
  &::placeholder { color: var(--gray-400); }
  &:focus {
    outline: none;
    border-color: var(--primary);
    box-shadow: 0 0 0 3px rgba(var(--primary-rgb), 0.1);
  }
  &:disabled { background: var(--gray-100); }
}
```

#### Select
```scss
.form-select {
  padding: var(--input-padding);
  border: 1px solid var(--gray-300);
  border-radius: var(--radius-md);
  font-size: var(--text-base);
  background: white;
  cursor: pointer;
  
  &:focus {
    outline: none;
    border-color: var(--primary);
  }
}
```

#### Textarea
```scss
.form-textarea {
  @extend .form-input;
  min-height: 120px;
  resize: vertical;
}
```

#### Checkbox y Radio
```scss
.form-checkbox {
  display: flex;
  align-items: center;
  gap: 8px;
  cursor: pointer;
  
  input[type="checkbox"] {
    width: 18px;
    height: 18px;
    accent-color: var(--primary);
  }
}
```

---

### 2.3 Tablas (Tables)

```html
<table class="doc-table">
  <thead>
    <tr>
      <th>Columna 1</th>
      <th>Columna 2</th>
      <th>Columna 3</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Contenido</td>
      <td>Contenido</td>
      <td>Contenido</td>
    </tr>
  </tbody>
</table>
```

```scss
.doc-table {
  width: 100%;
  border-collapse: collapse;
  background: white;
  border-radius: var(--radius-lg);
  overflow: hidden;
  box-shadow: var(--shadow-card);
  
  th {
    background: var(--gray-50);
    padding: 12px 16px;
    text-align: left;
    font-size: var(--text-xs);
    font-weight: var(--font-semibold);
    color: var(--gray-600);
    text-transform: uppercase;
    letter-spacing: 0.3px;
    border-bottom: 1px solid var(--gray-200);
  }
  
  td {
    padding: 14px 16px;
    border-bottom: 1px solid var(--gray-100);
    font-size: var(--text-base);
  }
  
  tr:hover {
    background: var(--gray-50);
    cursor: pointer;
  }
  
  tr.selected {
    background: var(--primary-light);
  }
}
```

---

### 2.4 Cards

```html
<div class="card">
  <div class="card-header">
    <span class="card-title">Titulo</span>
    <a class="card-action">Ver todos</a>
  </div>
  <div class="card-body">
    Contenido
  </div>
</div>
```

```scss
.card {
  background: white;
  border-radius: var(--radius-xl);
  box-shadow: var(--shadow-card);
  border: 1px solid var(--gray-200);
  overflow: hidden;
}

.card-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 16px 20px;
  border-bottom: 1px solid var(--gray-100);
}

.card-title {
  font-size: var(--text-md);
  font-weight: var(--font-semibold);
  color: var(--gray-800);
}

.card-action {
  font-size: var(--text-sm);
  color: var(--primary);
  text-decoration: none;
  font-weight: var(--font-medium);
  cursor: pointer;
  
  &:hover { text-decoration: underline; }
}

.card-body {
  padding: 20px;
}
```

---

### 2.5 Badges / Tags

```html
<span class="badge badge-success">Activo</span>
<span class="badge badge-warning">Pendiente</span>
<span class="badge badge-danger">Urgente</span>
<span class="badge badge-info">Recibido</span>
```

```scss
.badge {
  display: inline-flex;
  align-items: center;
  padding: 4px 10px;
  border-radius: var(--radius-md);
  font-size: var(--text-xs);
  font-weight: var(--font-medium);
}

.badge-success   { background: var(--success-light); color: #047857; }
.badge-warning   { background: var(--warning-light); color: #d97706; }
.badge-danger    { background: var(--danger-light); color: var(--danger); }
.badge-info      { background: var(--info-light); color: #0369a1; }
.badge-neutral   { background: var(--gray-100); color: var(--gray-600); }

// Badge con icono
.badge-with-icon {
  display: inline-flex;
  align-items: center;
  gap: 6px;
}
```

---

### 2.6 Tabs

```html
<div class="tabs">
  <button class="tab active">Tab 1</button>
  <button class="tab">Tab 2</button>
  <button class="tab">Tab 3</button>
</div>
```

```scss
.tabs {
  display: flex;
  background: var(--gray-100);
  border-radius: var(--radius-md);
  padding: 4px;
  gap: 4px;
}

.tab {
  flex: 1;
  padding: 10px 16px;
  border: none;
  background: transparent;
  font-size: var(--text-sm);
  font-weight: var(--font-medium);
  color: var(--gray-500);
  cursor: pointer;
  border-radius: var(--radius-sm);
  transition: all var(--transition-fast);
  font-family: var(--font-family);
  
  &:hover { color: var(--gray-700); }
  &.active {
    background: white;
    color: var(--primary);
    box-shadow: var(--shadow-sm);
  }
}
```

---

### 2.7 Modals

```html
<div class="modal-overlay">
  <div class="modal">
    <div class="modal-header">
      <span class="modal-title">Titulo</span>
      <button class="modal-close">✕</button>
    </div>
    <div class="modal-body">
      Contenido
    </div>
    <div class="modal-footer">
      <button class="btn btn-secondary">Cancelar</button>
      <button class="btn btn-primary">Confirmar</button>
    </div>
  </div>
</div>
```

```scss
.modal-overlay {
  position: fixed;
  inset: 0;
  background: rgba(0, 0, 0, 0.5);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1000;
}

.modal {
  background: white;
  border-radius: var(--radius-xl);
  box-shadow: var(--shadow-modal);
  width: 100%;
  max-width: 500px;
  max-height: 90vh;
  overflow: hidden;
}

.modal-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 16px 20px;
  border-bottom: 1px solid var(--gray-200);
}

.modal-title {
  font-size: var(--text-lg);
  font-weight: var(--font-semibold);
}

.modal-body {
  padding: 20px;
  overflow-y: auto;
}

.modal-footer {
  display: flex;
  justify-content: flex-end;
  gap: 12px;
  padding: 16px 20px;
  border-top: 1px solid var(--gray-200);
}
```

---

### 2.8 Toasts / Notifications

```html
<div class="toast toast-success" id="toast">
  <span>✓</span>
  <span>Mensaje</span>
</div>
```

```scss
.toast {
  position: fixed;
  bottom: 24px;
  left: 50%;
  transform: translateX(-50%) translateY(100px);
  background: var(--gray-800);
  color: white;
  padding: 12px 20px;
  border-radius: var(--radius-lg);
  font-size: var(--text-base);
  display: flex;
  align-items: center;
  gap: 10px;
  opacity: 0;
  transition: all var(--duration-250);
  z-index: 1000;
  
  &.show {
    transform: translateX(-50%) translateY(0);
    opacity: 1;
  }
  &.success { background: var(--success); }
  &.error { background: var(--danger); }
  &.warning { background: var(--warning); }
}
```

---

## 3. COMPONENTES DE LAYOUT

### 3.1 Sidebar

```scss
.sidebar {
  width: var(--sidebar-width);
  background: var(--gray-900);
  color: white;
  position: fixed;
  top: 0;
  left: 0;
  bottom: 0;
  z-index: 100;
  display: flex;
  flex-direction: column;
  transition: width var(--duration-250);
  
  &.collapsed {
    width: var(--sidebar-collapsed);
    
    .nav-label, .nav-badge, .sidebar-brand-text, 
    .user-info, .sidebar-footer-text { 
      opacity: 0; width: 0; overflow: hidden; 
    }
  }
}

.sidebar-brand {
  height: var(--header-height);
  display: flex;
  align-items: center;
  padding: 0 16px;
  gap: 12px;
  border-bottom: 1px solid rgba(255,255,255,0.08);
}

.sidebar-logo {
  width: 36px; height: 36px;
  background: var(--primary);
  border-radius: var(--radius-lg);
  display: flex;
  align-items: center;
  justify-content: center;
}

.sidebar-brand-text {
  font-size: var(--text-lg);
  font-weight: var(--font-bold);
  white-space: nowrap;
}

.sidebar-user {
  padding: 16px;
  border-bottom: 1px solid rgba(255,255,255,0.08);
}

.sidebar-user-card {
  display: flex;
  align-items: center;
  gap: 12px;
  padding: 12px;
  background: rgba(255,255,255,0.05);
  border-radius: var(--radius-lg);
  cursor: pointer;
}

.sidebar-avatar {
  width: 40px; height: 40px;
  border-radius: var(--radius-avatar);
  background: var(--primary);
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: var(--text-sm);
  font-weight: var(--font-bold);
}

.sidebar-nav {
  flex: 1;
  overflow-y: auto;
  padding: 8px 0;
}

.sidebar-section {
  padding: 8px 12px;
}

.sidebar-section-title {
  font-size: var(--text-xs);
  font-weight: var(--font-semibold);
  text-transform: uppercase;
  letter-spacing: 0.5px;
  color: var(--gray-500);
  padding: 8px 8px 4px;
}

.nav-item {
  display: flex;
  align-items: center;
  gap: 12px;
  padding: 10px 12px;
  color: var(--gray-300);
  text-decoration: none;
  border-radius: var(--radius-md);
  cursor: pointer;
  margin: 2px 8px;
  transition: all var(--transition-fast);
  
  &:hover {
    background: rgba(255,255,255,0.08);
    color: white;
  }
  &.active {
    background: var(--primary);
    color: white;
  }
}

.nav-icon {
  width: 20px;
  height: 20px;
  flex-shrink: 0;
}

.nav-label {
  flex: 1;
  font-size: var(--text-base);
  white-space: nowrap;
}

.nav-badge {
  font-size: var(--text-xs);
  padding: 2px 8px;
  border-radius: 10px;
  background: var(--danger);
  color: white;
  font-weight: var(--font-semibold);
}

.nav-nested {
  display: none;
  
  &.show { display: block; }
  
  .nav-item {
    padding-left: 44px;
    font-size: var(--text-sm);
  }
}

.sidebar-footer {
  padding: 12px 16px;
  border-top: 1px solid rgba(255,255,255,0.08);
}

.sidebar-footer-btn {
  display: flex;
  align-items: center;
  gap: 10px;
  width: 100%;
  padding: 10px;
  background: none;
  border: none;
  color: var(--gray-400);
  font-size: var(--text-base);
  cursor: pointer;
  border-radius: var(--radius-md);
  font-family: var(--font-family);
  
  &:hover {
    background: rgba(255,255,255,0.05);
    color: white;
  }
}
```

---

### 3.2 Header

```scss
.header {
  height: var(--header-height);
  background: white;
  border-bottom: 1px solid var(--gray-200);
  display: flex;
  align-items: center;
  padding: 0 24px;
  gap: 16px;
  position: sticky;
  top: 0;
  z-index: 50;
}

.header-toggle {
  width: 36px; height: 36px;
  display: flex;
  align-items: center;
  justify-content: center;
  border: none;
  background: none;
  cursor: pointer;
  border-radius: var(--radius-md);
  color: var(--gray-600);
  
  &:hover { background: var(--gray-100); }
}

.header-title {
  font-size: var(--text-xl);
  font-weight: var(--font-semibold);
}

.header-actions {
  margin-left: auto;
  display: flex;
  gap: 8px;
}

.header-btn {
  width: 40px; height: 40px;
  display: flex;
  align-items: center;
  justify-content: center;
  border: none;
  background: none;
  cursor: pointer;
  border-radius: var(--radius-md);
  color: var(--gray-500);
  position: relative;
  
  &:hover {
    background: var(--gray-100);
    color: var(--gray-700);
  }
  
  .badge {
    position: absolute;
    top: 6px; right: 6px;
    width: 8px; height: 8px;
    background: var(--danger);
    border-radius: 50%;
  }
}
```

---

## 4. COMPONENTES ESPECÍFICOS (DocFlow)

### 4.1 KPI Card (Dashboard)

```html
<div class="kpi-card">
  <div class="kpi-icon pending">
    <svg>...</svg>
  </div>
  <div class="kpi-content">
    <div class="kpi-label">Docs pendientes</div>
    <div class="kpi-value">23</div>
    <div class="kpi-trend up">+5 esta semana</div>
  </div>
</div>
```

```scss
.kpi-card {
  background: white;
  border-radius: var(--radius-xl);
  padding: 20px;
  box-shadow: var(--shadow-card);
  border: 1px solid var(--gray-200);
  display: flex;
  align-items: flex-start;
  gap: 16px;
  transition: all var(--transition-base);
  cursor: pointer;
  
  &:hover {
    transform: translateY(-2px);
    box-shadow: var(--shadow-lg);
  }
}

.kpi-icon {
  width: 48px; height: 48px;
  border-radius: var(--radius-lg);
  display: flex;
  align-items: center;
  justify-content: center;
  
  &.pending { background: var(--danger-light); color: var(--danger); }
  &.control { background: #ecfdf5; color: #047857; }
  &.execute { background: #f0fdfa; color: #0d9488; }
  &.unread { background: var(--warning-light); color: var(--warning); }
}

.kpi-content {
  flex: 1;
}

.kpi-label {
  font-size: var(--text-sm);
  color: var(--gray-500);
  margin-bottom: 4px;
}

.kpi-value {
  font-size: 28px;
  font-weight: var(--font-bold);
  color: var(--gray-900);
  line-height: 1.1;
}

.kpi-trend {
  display: inline-flex;
  align-items: center;
  gap: 4px;
  font-size: var(--text-xs);
  margin-top: 8px;
  padding: 4px 8px;
  border-radius: var(--radius-sm);
  
  &.up { background: #ecfdf5; color: #047857; }
  &.down { background: var(--danger-light); color: var(--danger); }
  &.neutral { background: var(--gray-100); color: var(--gray-600); }
}
```

---

### 4.2 Checklist (Expediente)

```html
<div class="checklist">
  <div class="progress-bar">
    <div class="progress-fill" style="width: 66%;"></div>
  </div>
  <span class="progress-text">66% (4 de 6)</span>
</div>

<div class="checklist-items">
  <div class="check-item completed">
    <input type="checkbox" checked>
    <span class="check-item-text">Item completado</span>
    <button class="check-item-delete">🗑️</button>
  </div>
  <div class="check-item">
    <input type="checkbox">
    <span class="check-item-text">Item pendiente</span>
    <button class="check-item-delete">🗑️</button>
  </div>
</div>
```

```scss
.checklist-progress {
  display: flex;
  align-items: center;
  gap: 16px;
  margin-bottom: 20px;
}

.progress-bar {
  flex: 1;
  height: 8px;
  background: var(--gray-200);
  border-radius: 4px;
  overflow: hidden;
}

.progress-fill {
  height: 100%;
  background: var(--primary);
  border-radius: 4px;
  transition: width var(--duration-300);
}

.progress-text {
  font-size: var(--text-base);
  font-weight: var(--font-semibold);
  color: var(--gray-700);
}

.checklist-items {
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.check-item {
  display: flex;
  align-items: center;
  gap: 12px;
  padding: 12px 16px;
  background: white;
  border: 1px solid var(--gray-200);
  border-radius: var(--radius-md);
  
  input[type="checkbox"] {
    width: 18px;
    height: 18px;
    accent-color: var(--primary);
  }
  
  &.completed .check-item-text {
    text-decoration: line-through;
    color: var(--gray-500);
  }
}

.check-item-text {
  flex: 1;
  font-size: var(--text-base);
}

.check-item-delete {
  width: 28px;
  height: 28px;
  display: flex;
  align-items: center;
  justify-content: center;
  border: none;
  background: none;
  color: var(--gray-400);
  cursor: pointer;
  border-radius: var(--radius-sm);
  
  &:hover {
    background: var(--danger-light);
    color: var(--danger);
  }
}
```

---

### 4.3 Bandeja Tabs

```html
<div class="bandeja-tabs">
  <button class="bandeja-tab active">
    <svg>...</svg>
    Todos
    <span class="badge">12</span>
  </button>
  <button class="bandeja-tab">
    Recibidos
    <span class="badge">5</span>
  </button>
  ...
</div>
```

```scss
.bandeja-tabs {
  width: 220px;
  background: white;
  border-right: 1px solid var(--gray-200);
  padding: 16px 0;
  display: flex;
  flex-direction: column;
  gap: 4px;
}

.bandeja-tab {
  display: flex;
  align-items: center;
  gap: 10px;
  padding: 10px 16px;
  cursor: pointer;
  border: none;
  background: none;
  font-size: var(--text-base);
  font-weight: var(--font-medium);
  color: var(--gray-600);
  text-align: left;
  font-family: var(--font-family);
  transition: all var(--transition-fast);
  position: relative;
  
  &:hover {
    background: var(--gray-50);
    color: var(--gray-800);
  }
  &.active {
    background: var(--primary-light);
    color: var(--primary);
  }
  &.active::before {
    content: '';
    position: absolute;
    left: 0; top: 0; bottom: 0;
    width: 3px;
    background: var(--primary);
    border-radius: 0 2px 2px 0;
  }
}

.bandeja-tab-badge {
  margin-left: auto;
  font-size: var(--text-xs);
  padding: 2px 8px;
  border-radius: 10px;
  background: var(--gray-100);
  color: var(--gray-600);
  
  .active & {
    background: var(--primary);
    color: white;
  }
}
```

---

### 4.4 Panel Flotante (Atender)

```scss
.attend-panel {
  width: 0;
  background: white;
  border-left: 1px solid var(--gray-200);
  display: flex;
  flex-direction: column;
  overflow: hidden;
  transition: width var(--duration-250);
  
  &.open { width: 420px; }
}

.attend-tabs {
  display: flex;
  border-bottom: 1px solid var(--gray-200);
  background: var(--gray-50);
}

.attend-tab {
  flex: 1;
  padding: 12px;
  border: none;
  background: none;
  font-size: var(--text-sm);
  font-weight: var(--font-medium);
  color: var(--gray-500);
  cursor: pointer;
  position: relative;
  font-family: var(--font-family);
  
  &:hover { color: var(--gray-700); }
  &.active {
    background: white;
    color: var(--primary);
  }
  &.active::after {
    content: '';
    position: absolute;
    bottom: -1px; left: 0; right: 0;
    height: 2px;
    background: var(--primary);
  }
}

.attend-content {
  flex: 1;
  overflow-y: auto;
  padding: 20px;
}

.attend-section {
  margin-bottom: 20px;
}

.attend-actions {
  padding: 16px 20px;
  border-top: 1px solid var(--gray-200);
  display: flex;
  gap: 12px;
}
```

---

## 5. GRILLA RESPONSIVA

```scss
// Breakpoints
$breakpoint-sm: 640px;
$breakpoint-md: 768px;
$breakpoint-lg: 1024px;
$breakpoint-xl: 1200px;
$breakpoint-2xl: 1440px;

// Grid system
.kpi-grid {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 20px;
  
  @media (max-width: $breakpoint-xl) {
    grid-template-columns: repeat(2, 1fr);
  }
  @media (max-width: $breakpoint-sm) {
    grid-template-columns: 1fr;
  }
}

.dashboard-grid {
  display: grid;
  grid-template-columns: 2fr 1fr;
  
  @media (max-width: $breakpoint-lg) {
    grid-template-columns: 1fr;
  }
}

.form-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 20px;
  
  @media (max-width: $breakpoint-xl) {
    grid-template-columns: repeat(2, 1fr);
  }
  @media (max-width: $breakpoint-sm) {
    grid-template-columns: 1fr;
  }
}
```

---

## 6. PATRONES DE USO

### 6.1 Navegación entre pantallas
- Sidebar → navegar entre modulos
- Breadcrumb → navegar dentro de modulo
- Botones de accion → operaciones especificas

### 6.2 Formularios
- Labels siempre visibles
- Required marcado con *
- Validation inline debajo del campo
- Submit deshabilitado si invalid

### 6.3 Listas/Tablas
- Hover highlight en rows
- Click abre detalle
- Checkbox para seleccion multiple
- Sorting por click en header

### 6.4 Confirmaciones
- Modals para acciones destructivas
- Toasts para exitos
- Loading states durante operaciones

---

*Design System v1.0 — Abril 2026*
*Basado en demos HTML 06-10*