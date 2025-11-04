# Wellness Dashboard

Dashboard personal de seguimiento de hábitos de salud y bienestar desarrollado con React + Tailwind CSS.

## 📋 Descripción General

Aplicación web standalone (archivo HTML único) para seguimiento semanal de:
- Rutinas de ejercicio (natación/fuerza)
- Calidad nutricional (3 comidas/día)
- Hidratación (vasos de agua)
- Mediciones corporales (peso, cintura, IMC)
- Actividades de bienestar (familia, sueño, aire libre)

## 🚀 Inicio Rápido

### Ejecutar el Dashboard

**Opción 1: Abrir directamente**
```bash
start wellness-dashboard.html
```

**Opción 2: Servidor local (recomendado para desarrollo)**
```bash
# Python 3
python -m http.server 8000

# Node.js
npx http-server
```

Luego abrir: `http://localhost:8000/wellness-dashboard.html`

### 🌐 Hosting Online con Sincronización en la Nube

**¿Quieres acceder desde múltiples dispositivos?**

Ver [IMPLEMENTATION_LOG.md](IMPLEMENTATION_LOG.md) para la guía completa de implementación de:
- Hosting online (GitHub Pages)
- Sincronización en tiempo real (Supabase)
- Acceso desde cualquier dispositivo
- Persistencia de datos en la nube

**Estado actual:** localStorage solo (datos en navegador local)
**Estado futuro:** Supabase + GitHub Pages (datos en la nube, acceso desde cualquier lugar)

### Archivos del Proyecto

```
Wellness_dash/
├── wellness-dashboard.html        # Aplicación principal (USAR ESTE)
├── wellness-dashboard-backup.html # Versión backup con layout optimizado
├── test.html                      # Archivo de prueba React
├── CLAUDE_CODE_PROMPT.md         # Especificaciones completas del proyecto
└── README.md                      # Esta documentación
```

## 🏗️ Arquitectura Técnica

### Stack Tecnológico

- **React 18.2.0** - Framework UI (CDN: unpkg)
- **Tailwind CSS 3.3.0** - Estilos utility-first
- **Chart.js 4.4.0** - Visualizaciones de datos
- **Babel Standalone** - Compilación JSX en navegador
- **localStorage** - Persistencia de datos

### Componentes React

```javascript
<App/>                      // Componente principal
├── <DaySelector/>         // Navegación entre días
├── <DayCard/>            // Vista detalle del día
│   ├── <CheckboxToggle/> // Actividades booleanas
│   ├── <MealQualityButton/> // Calidad de comidas
│   └── <WaterCounter/>   // Contador de agua
├── <MonthlyStatsPanel/>  // Estadísticas mensuales
└── <ChartsPanel/>        // 5 gráficos de análisis
    ├── Peso & Cintura (12 semanas)
    ├── Índice Calidad Alimenticia (8 semanas)
    ├── Distribución de Comidas (donut)
    ├── Hidratación Semanal (barras)
    └── Evolución IMC (línea + zonas)
```

## 📊 Funcionalidades Principales

### 1. Sistema de Seguimiento Semanal

**Tipos de Día:**
- 🏊 Natación (Lun, Jue) - 5:00 AM
- 💪 Fuerza (Mar, Vie) - 5:00 AM
- 🧘 Descanso Activo (Mié) - Movimiento ligero
- 🌳 Fin de Semana (Sáb, Dom) - Aire libre, meal prep

### 2. Calidad Alimenticia

**Sistema de 4 Estados:**
- ⏸️ Pendiente (sin registrar)
- ✅ Saludable = 3 puntos
- ⚠️ Regular = 2 puntos
- ❌ Mala = 1 punto

**Índice Semanal:** 0-100% con mensajes motivacionales

### 3. Hidratación Inteligente

- Contador: 0-15 vasos/día
- Metas dinámicas:
  - 7 vasos (días descanso)
  - 9 vasos (días ejercicio)
- Barra de progreso visual
- Total semanal acumulado

### 4. Mediciones Corporales

- **Peso** (kg, con decimales)
- **Cintura** (cm, con decimales)
- **Estatura** (para cálculo IMC)
- **IMC Automático** con clasificación:
  - Normal: 18.5-24.9
  - Sobrepeso: 25-29.9
  - Obesidad I, II, III

### 5. Persistencia de Datos

**localStorage Keys:**
```javascript
wellness_currentWeek  // Semana actual
wellness_historical   // Archivo histórico
wellness_config       // Configuración usuario (altura, nombre)
```

- Auto-guardado en cada cambio
- Sin pérdida de datos al refrescar
- Exportación CSV/JSON disponible

### 6. Exportación de Datos

**CSV Export:**
- Tabla completa con todas las semanas
- Formato: Semana, Día, Actividades, Comidas, Agua, Peso, Cintura

**JSON Backup:**
- Backup completo con timestamp
- Importación con protección de sobreescritura

### 7. Estadísticas y Análisis

**Panel de Estadísticas Mensuales:**
- Total entrenamientos
- Calidad alimenticia promedio
- Consumo agua promedio diario
- Conteo de semanas
- Progreso corporal (peso, cintura, IMC)

**5 Gráficos Interactivos:**
1. Evolución Peso & Cintura (dual-axis)
2. Índice Calidad Alimenticia (barras coloreadas)
3. Distribución Comidas (donut chart)
4. Hidratación Semanal (barras vs meta)
5. Evolución IMC (con zonas de referencia)

## 💾 Estructura de Datos

### Objeto de Semana

```javascript
{
  weekNumber: 45,           // Semana del año
  year: 2025,
  startDate: "2025-11-03",  // Lunes de esa semana
  peso: "75.5",            // kg
  cintura: "85.0",         // cm
  estatura: "175",         // cm (opcional)
  lunes: {
    tipo: "natacion",
    ejercicio: true,
    pausa1: true,
    pausa2: true,
    desayuno: "bueno",
    almuerzo: "regular",
    cena: "bueno",
    vasos: 9
  },
  martes: { /* ... */ },
  // ... miércoles a domingo
  archivedDate: "2025-11-10T12:00:00Z"  // Si está archivada
}
```

### Objeto de Día

```javascript
{
  tipo: "natacion" | "fuerza" | "descanso" | "finDeSemana",

  // Actividades booleanas
  ejercicio: false,        // Entrenamiento principal
  movimiento: false,       // Movimiento ligero (miércoles)
  pausa1: false,          // Break 10am
  pausa2: false,          // Break 3pm
  aireLibre: false,       // Actividad outdoor (fines de semana)
  familia: false,         // Tiempo en familia
  sueno: false,           // Dormir temprano
  mealPrep: false,        // Meal prep (solo domingo)
  prep: false,            // Preparación semanal (solo domingo)

  // Comidas (4 estados)
  desayuno: "pendiente" | "bueno" | "regular" | "malo",
  almuerzo: "pendiente" | "bueno" | "regular" | "malo",
  cena: "pendiente" | "bueno" | "regular" | "malo",

  // Hidratación
  vasos: 0  // 0-15 vasos
}
```

## 🎨 Diseño y UX

### Principios HIG Aplicados

- ✅ Targets táctiles mínimos: 44px
- ✅ Espaciado en grid de 8px
- ✅ Contraste de color accesible (WCAG AA)
- ✅ Indicadores de foco visibles
- ✅ Transiciones suaves (0.2s ease)
- ✅ Feedback visual inmediato
- ✅ Navegación por teclado
- ✅ Labels ARIA para screen readers

### Sistema de Colores

```css
/* Estados */
Pendiente: bg-gray-100, text-gray-400
Bueno:     bg-green-500, text-white
Regular:   bg-yellow-500, text-white
Malo:      bg-red-500, text-white

/* Acciones */
Primario:  bg-blue-600 hover:bg-blue-700
Éxito:     bg-green-600 hover:bg-green-700
Peligro:   bg-red-600 hover:bg-red-700
```

### Responsive Layout

- **Móvil:** 1 columna (< 640px)
- **Tablet:** 2 columnas (640px - 1024px)
- **Desktop:** 3-4 columnas (> 1024px)

## 🔧 Configuración y Personalización

### Modificar Metas de Agua

En el código, buscar `getWaterGoal()`:

```javascript
const getWaterGoal = (dayType) => {
  if (dayType === 'descanso') return 7;
  return 9;  // Cambiar aquí para días de ejercicio
};
```

### Cambiar Días de Entrenamiento

En `getDayType()`:

```javascript
case 'lunes': return 'natacion';  // Cambiar tipo aquí
case 'martes': return 'fuerza';
// ...
```

### Ajustar Sistema de Puntos

En `getMealPoints()`:

```javascript
if (value === 'bueno') return 3;    // Ajustar puntos
if (value === 'regular') return 2;
if (value === 'malo') return 1;
```

## 📈 Casos de Uso

### Flujo Diario Típico

1. **Mañana:** Registrar ejercicio realizado
2. **10am:** Marcar pausa1 + registrar desayuno
3. **3pm:** Marcar pausa2 + actualizar agua
4. **Noche:** Registrar almuerzo, cena, agua final
5. **Antes dormir:** Marcar familia, sueño

### Flujo Semanal

- **Lunes:** Ingresar peso y cintura nuevos
- **Domingo:** Marcar meal prep y preparación semanal
- **Fin de semana:** Dashboard archiva semana automáticamente

### Análisis Mensual

1. Ir a tab "Estadísticas"
2. Revisar tendencias mensuales
3. Ir a tab "Gráficos"
4. Analizar evolución peso/cintura
5. Verificar consistencia calidad alimenticia
6. Exportar datos para análisis externo

## 🐛 Troubleshooting

### El dashboard no guarda datos

- Verificar que localStorage esté habilitado en el navegador
- Comprobar que no estés en modo incógnito
- Revisar consola de desarrollo (F12) para errores

### Los gráficos no aparecen

- Verificar conexión a internet (Chart.js se carga de CDN)
- Esperar a que se carguen las bibliotecas
- Refrescar la página (F5)

### Datos perdidos o corruptos

1. Intentar recuperar de backup JSON si existe
2. Revisar `localStorage` en DevTools:
   - Application > Local Storage > wellness_*
3. Restaurar desde export CSV si está disponible

### La aplicación se ve rota

- Limpiar caché del navegador (Ctrl + F5)
- Verificar que Tailwind CSS se cargue (CDN)
- Comprobar consola para errores de red

## 🔒 Privacidad y Seguridad

- ✅ **100% Local:** Todos los datos en localStorage
- ✅ **Sin Servidor:** No hay backend ni APIs externas
- ✅ **Sin Tracking:** No se envía información a terceros
- ✅ **Offline First:** Funciona sin internet (después de primera carga)
- ⚠️ **Backup Manual:** Exportar regularmente a JSON/CSV

## 🚧 Limitaciones Conocidas

1. **No hay sincronización:** Datos solo en un navegador
2. **Límite localStorage:** ~5-10MB (suficiente para años)
3. **Sin multi-usuario:** Diseñado para uso personal
4. **Requiere navegador moderno:** Chrome 90+, Firefox 88+, Safari 14+

## 📝 Próximas Mejoras Sugeridas

- [ ] Modo offline completo (Service Worker)
- [ ] Sincronización en la nube (opcional)
- [ ] Notificaciones push para recordatorios
- [ ] Modo oscuro
- [ ] Más tipos de ejercicio personalizables
- [ ] Integración con dispositivos wearables
- [ ] Comparación con semanas anteriores
- [ ] Objetivos y logros gamificados

## 📚 Referencias

- [Documentación completa del proyecto](CLAUDE_CODE_PROMPT.md)
- [React Docs](https://react.dev)
- [Chart.js Docs](https://www.chartjs.org)
- [Tailwind CSS](https://tailwindcss.com)

## 🤝 Mantenimiento

### Para retomar el proyecto en el futuro:

1. Leer este README.md
2. Revisar CLAUDE_CODE_PROMPT.md para especificaciones detalladas
3. Abrir wellness-dashboard.html en navegador
4. Inspeccionar con DevTools (F12) para ver estructura React
5. Modificar código dentro del `<script type="text/babel">` del HTML

### Estructura del código en HTML:

```html
<!DOCTYPE html>
<html>
<head>
  <!-- CDN links: React, Tailwind, Chart.js -->
</head>
<body>
  <div id="root"></div>

  <script type="text/babel">
    // Custom Hook: useLocalStorage

    // UI Components:
    // - Button, Input, CheckboxToggle
    // - MealQualityButton, WaterCounter
    // - DaySelector, DayCard
    // - MonthlyStatsPanel, ChartsPanel

    // Main Component: App

    // ReactDOM.render
  </script>
</body>
</html>
```

---

**Versión:** 1.0
**Última actualización:** Octubre 2025
**Autor:** Sistema de Wellness Personal
**Licencia:** Uso Personal
