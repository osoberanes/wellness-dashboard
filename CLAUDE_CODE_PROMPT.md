# PROMPT PARA CLAUDE CODE: Dashboard de Bienestar Personal

## CONTEXTO DEL PROYECTO
Crear una aplicación web standalone de seguimiento de bienestar personal con persistencia local, exportación de datos y análisis estadístico. El usuario necesita una herramienta para trackear hábitos diarios, calidad alimenticia, mediciones corporales y visualizar su progreso semanal/mensual.

---

## REQUISITOS TÉCNICOS

### Stack Tecnológico
- **Frontend**: React 18+ con hooks
- **Estilos**: Tailwind CSS
- **Persistencia**: localStorage del navegador
- **Formato**: Archivo HTML standalone único (todo en uno)
- **Librerías de gráficos**: Recharts o Chart.js (embebido via CDN)
- **Sin backend**: Solución 100% cliente

### Estructura del Archivo
- HTML único con React embebido vía CDN (unpkg)
- Babel standalone para JSX
- Tailwind CSS vía CDN
- Toda la lógica en un solo archivo para fácil distribución

---

## FUNCIONALIDADES PRINCIPALES

### 1. SEGUIMIENTO SEMANAL DE ACTIVIDADES

#### Estructura de Datos por Semana
```javascript
{
  weekNumber: number,        // Número de semana del año
  year: number,             // Año
  startDate: string,        // Fecha de inicio (formato ISO)
  peso: string,             // Peso en kg (decimal)
  cintura: string,          // Cintura en cm (decimal)
  estatura: string,         // Estatura en cm (para cálculo IMC)
  
  // 7 días de la semana
  lunes: { /* ver estructura día */ },
  martes: { /* ver estructura día */ },
  miercoles: { /* ver estructura día */ },
  jueves: { /* ver estructura día */ },
  viernes: { /* ver estructura día */ },
  sabado: { /* ver estructura día */ },
  domingo: { /* ver estructura día */ }
}
```

#### Estructura de Datos por Día
```javascript
{
  // Tipo de día
  tipo: 'natacion' | 'fuerza' | 'descanso' | 'finDeSemana',
  
  // Actividades (boolean)
  ejercicio: boolean,       // Solo en días de natación/fuerza
  movimiento: boolean,      // Solo en día de descanso (miércoles)
  pausa1: boolean,          // Pausa 10:00 am + snack
  pausa2: boolean,          // Pausa 3:00 pm + snack
  aireLibre: boolean,       // Solo fines de semana
  familia: boolean,         // Tiempo familiar
  sueno: boolean,           // Sueño antes de 10 pm (o adecuado en fines de semana)
  
  // Actividades especiales domingo
  mealPrep: boolean,        // Meal prep semanal (solo domingo)
  prep: boolean,            // Preparación para la semana (solo domingo)
  
  // Alimentación (string: 'pendiente' | 'bueno' | 'regular' | 'malo')
  desayuno: string,
  almuerzo: string,
  cena: string,
  
  // Hidratación (number: 0-15)
  vasos: number
}
```

#### Distribución Semanal Específica
- **Lunes**: Natación 5:00 AM
- **Martes**: Fuerza (gimnasio) 5:00 AM
- **Miércoles**: Descanso activo (sin ejercicio estructurado, solo movimiento ligero)
- **Jueves**: Natación 5:00 AM
- **Viernes**: Fuerza (gimnasio) 5:00 AM
- **Sábado**: Fin de semana (actividad al aire libre informal)
- **Domingo**: Fin de semana + meal prep + preparación semanal

---

### 2. SISTEMA DE CALIDAD ALIMENTICIA

#### Estados de Comida (ciclar al hacer clic)
1. **Pendiente** (⏸️): Aún no se come o no se registra
2. **Saludable** (✅): Proteína + vegetales + carbohidrato complejo
3. **Regular** (⚠️): Algo procesado o falta balance
4. **Malo** (❌): Comida rápida, mucha grasa o azúcar

#### Cálculo de Índice de Calidad Semanal
```
Puntuación por comida:
- Saludable = 3 puntos
- Regular = 2 puntos
- Malo = 1 punto
- Pendiente = no cuenta

Índice = (Total puntos / Total comidas registradas * 3) * 100
```

#### Sistema de Mensajes Motivacionales
- ≥85%: "¡Excelente calidad!" 🌟 (verde)
- 70-84%: "Muy buena semana" 💪 (azul)
- 55-69%: "Bien, puedes mejorar" 👍 (amarillo)
- <55%: "Enfócate en calidad" ⚠️ (naranja)

---

### 3. SEGUIMIENTO DE HIDRATACIÓN

#### Sistema de Contador
- Cada día tiene un contador de vasos de agua (0-15)
- Botones: +1, -1, Reset
- Meta diaria:
  - **7 vasos**: Días sin ejercicio
  - **9 vasos**: Días con ejercicio (lunes, martes, jueves, viernes)

#### Indicadores Visuales
- Barra de progreso que muestra X/7 o X/9
- Color cyan cuando cumple meta
- Contador total semanal en el header

---

### 4. MEDICIONES CORPORALES

#### Campos de Entrada Semanal
1. **Peso (kg)**: Input numérico con decimales (step="0.1")
2. **Cintura (cm)**: Input numérico con decimales (step="0.5")
3. **Estatura (cm)**: Input numérico para cálculo de IMC (guardar en configuración permanente)

#### Cálculo Automático de IMC
```javascript
IMC = peso (kg) / (estatura (m))²

Clasificación:
- < 18.5: Bajo peso
- 18.5-24.9: Normal
- 25-29.9: Sobrepeso
- 30-34.9: Obesidad I
- 35-39.9: Obesidad II
- ≥ 40: Obesidad III
```

#### Mejores Prácticas de Medición
- Lunes por la mañana
- En ayunas
- Después de ir al baño
- Cintura a la altura del ombligo

---

### 5. PERSISTENCIA DE DATOS

#### LocalStorage Structure
```javascript
{
  currentWeek: { /* objeto semana actual */ },
  historicalData: [ /* array de semanas pasadas */ ],
  userConfig: {
    estatura: number,  // Guardar estatura permanentemente
    nombre: string     // Opcional
  }
}
```

#### Auto-guardado
- Guardar en localStorage en cada cambio (useEffect)
- Cargar datos al iniciar la aplicación
- No perder datos al refrescar/cerrar navegador

---

### 6. GESTIÓN DE SEMANAS

#### Botón "Nueva Semana"
- Guardar semana actual en `historicalData`
- Resetear `currentWeek` con estructura inicial
- Incrementar número de semana automáticamente
- Agregar timestamp de guardado

#### Función de Reinicio
```javascript
- Confirmar antes de ejecutar
- Archivar semana con fecha de guardado
- Copiar peso/cintura como sugerencia para nueva semana (opcional)
- Iniciar nueva semana limpia
```

---

### 7. EXPORTACIÓN DE DATOS

#### Exportar a CSV
**Formato del archivo:**
```csv
Semana,Año,Fecha Inicio,Peso (kg),Cintura (cm),IMC,Día,Tipo Día,Ejercicio,Pausa 10am,Pausa 3pm,Familia,Sueño,Desayuno,Almuerzo,Cena,Vasos Agua,Índice Calidad
```

**Contenido:**
- Una fila por cada día de cada semana
- Incluir semana actual + historial
- Valores booleanos como "Sí"/"No"
- Calidad de comidas como texto: pendiente/bueno/regular/malo

#### Backup JSON
**Formato:**
```json
{
  "exportDate": "2025-01-15T10:30:00Z",
  "currentWeek": { /* datos semana */ },
  "historicalData": [ /* array semanas */ ],
  "userConfig": { /* configuración */ }
}
```

#### Importar Datos
- Input file tipo JSON
- Validar estructura antes de cargar
- Sobreescribir datos actuales
- Mostrar confirmación/error

---

### 8. ESTADÍSTICAS Y ANÁLISIS

#### Estadísticas Semanales (Header Principal)
1. **Índice de Calidad**: Porcentaje con color
2. **Entrenamientos**: X/4 completados
3. **Vasos de Agua**: Total semanal
4. **Semanas Guardadas**: Contador de historial

#### Estadísticas Mensuales (Panel Expandible)
1. **Entrenamientos**: Total del mes
2. **Calidad Alimenticia**: Promedio mensual
3. **Hidratación**: Promedio vasos/día
4. **Semanas Completas**: Cantidad en el mes

#### Progreso Corporal Mensual
1. **Peso**:
   - Peso inicial del mes
   - Peso actual
   - Diferencia en kg (verde si bajó, naranja si subió)
   
2. **Cintura**:
   - Medida inicial del mes
   - Medida actual
   - Diferencia en cm (verde si bajó, rosa si subió)

3. **IMC**:
   - IMC inicial
   - IMC actual
   - Diferencia
   - Clasificación actual

#### Filtrado Mensual
```javascript
- Filtrar historicalData por mes actual
- Usar campo startDate para determinar mes
- Calcular promedios y totales
- Mostrar solo si hay datos (≥1 semana)
```

---

### 9. GRÁFICOS VISUALES

#### Gráfico 1: Evolución de Peso y Cintura
**Tipo:** Línea doble (line chart)
**Datos:**
- Eje X: Número de semana
- Eje Y izquierdo: Peso (kg)
- Eje Y derecho: Cintura (cm)
- Dos líneas de diferentes colores
- Mostrar últimas 12 semanas (3 meses)

#### Gráfico 2: Índice de Calidad Alimenticia
**Tipo:** Barras (bar chart)
**Datos:**
- Eje X: Número de semana
- Eje Y: Índice de calidad (0-100%)
- Colores según rango:
  - Verde: ≥85%
  - Azul: 70-84%
  - Amarillo: 55-69%
  - Naranja: <55%
- Mostrar últimas 8 semanas

#### Gráfico 3: Distribución Semanal de Comidas
**Tipo:** Donut/Pie chart
**Datos:**
- Comidas saludables (verde)
- Comidas regulares (amarillo)
- Comidas malas (rojo)
- Basado en semana actual

#### Gráfico 4: Hidratación Semanal
**Tipo:** Barras horizontales
**Datos:**
- 7 barras (una por día)
- Mostrar vasos consumidos vs meta
- Color cyan si cumple meta, gris si no

#### Gráfico 5: IMC a lo largo del tiempo
**Tipo:** Línea con zonas de referencia
**Datos:**
- Eje X: Semanas
- Eje Y: IMC
- Zonas de colores de fondo:
  - Verde claro: 18.5-24.9 (normal)
  - Amarillo: 25-29.9 (sobrepeso)
  - Naranja: 30+ (obesidad)

#### Ubicación de Gráficos
- Sección colapsable "📊 Gráficos" junto a "Estadísticas"
- Grid responsive: 2 columnas en desktop, 1 en móvil
- Scroll suave a la sección al expandir

---

### 10. INTERFAZ DE USUARIO

#### Layout Principal
```
┌─────────────────────────────────────────┐
│ Header                                   │
│ - Título y semana actual                │
│ - Botones: Exportar, Backup, Importar, │
│   Estadísticas, Gráficos, Nueva Semana │
│ - Inputs: Peso, Cintura, Estatura      │
│ - Métricas rápidas (4 cards)           │
└─────────────────────────────────────────┘
┌─────────────────────────────────────────┐
│ Panel Estadísticas (colapsable)         │
│ - Estadísticas mensuales                │
│ - Progreso corporal                     │
└─────────────────────────────────────────┘
┌─────────────────────────────────────────┐
│ Panel Gráficos (colapsable)             │
│ - 5 gráficos en grid                    │
└─────────────────────────────────────────┘
┌─────────────────────────────────────────┐
│ Grid de 7 Tarjetas de Días              │
│ - Lunes a Domingo                       │
│ - Cada tarjeta con todas sus actividades│
└─────────────────────────────────────────┘
┌─────────────────────────────────────────┐
│ Footer con Instrucciones                │
└─────────────────────────────────────────┘
```

#### Tarjeta de Día (Card)
```
┌─────────────────────────┐
│ 📅 [Día]      [% Progress]│
│ ─────────────────────── │
│ 🏊 Natación 5:00 AM     │
│ ─────────────────────── │
│ ⏰ Pausa 10am + snack ☑ │
│ ⏰ Pausa 3pm + snack ☐  │
│ ─────────────────────── │
│ ☕ Desayuno: ✅          │
│ 🍽️ Almuerzo: ⚠️         │
│ 🍽️ Cena: ⏸️             │
│ ─────────────────────── │
│ 💧 Agua: 6/7            │
│ [  -  ] [  +  ]         │
│ ─────────────────────── │
│ 👨‍👩‍👧 Familia ☑            │
│ 🌙 Sueño ☑              │
└─────────────────────────┘
```

#### Paleta de Colores
- **Azul**: Progreso general, ejercicio
- **Verde**: Completado, saludable, positivo
- **Amarillo**: Regular, advertencia
- **Rojo**: Malo, negativo
- **Cyan**: Hidratación
- **Naranja**: Peso, mediciones
- **Rosa**: Cintura
- **Morado**: Historial, archivo

#### Responsividad
- **Desktop (xl)**: 4 columnas de días
- **Laptop (lg)**: 3 columnas
- **Tablet (md)**: 2 columnas
- **Móvil**: 1 columna

---

### 11. EXPERIENCIA DE USUARIO

#### Interacciones
1. **Click en actividad booleana**: Toggle on/off con feedback visual
2. **Click en comida**: Ciclar entre 4 estados
3. **Click en botones de agua**: +1, -1, reset
4. **Input de mediciones**: Actualización en tiempo real
5. **Expandir/colapsar**: Animación suave (fade-in)

#### Feedback Visual
- Colores cambian según estado
- Iconos/emojis para quick recognition
- Barras de progreso animadas
- Confirmaciones antes de acciones destructivas

#### Mensajes y Validaciones
- Confirmar antes de "Nueva Semana"
- Confirmar antes de importar (sobreescribe datos)
- Validar formato JSON en importación
- Mostrar errores de forma amigable
- Success messages después de exportar

#### Accesibilidad
- Labels claros en inputs
- Botones con texto descriptivo
- Tooltips en botones (atributo title)
- Contraste adecuado
- Tamaños de fuente legibles

---

### 12. INSTRUCCIONES PARA EL USUARIO

#### Flujo de Uso Semanal
1. **Lunes mañana**:
   - Pesarse y medir cintura en ayunas
   - Ingresar valores en los campos
   - Marcar entrenamiento de natación al completarlo

2. **Durante la semana**:
   - Ir marcando actividades conforme se completan
   - Calificar cada comida después de comer
   - Registrar vasos de agua durante el día

3. **Domingo noche**:
   - Marcar meal prep si se hizo
   - Marcar preparación de semana
   - Revisar progreso general

4. **Inicio de nueva semana**:
   - Click en "Nueva Semana"
   - Confirmar para archivar
   - Ingresar nuevas mediciones

#### Recomendaciones de Backup
- Exportar CSV: Mensual (para análisis)
- Backup JSON: Mensual (seguridad)
- Guardar archivos en carpeta segura (Drive, Dropbox)

---

### 13. CASOS EXTREMOS Y VALIDACIONES

#### Validaciones de Datos
- Peso: 30-300 kg (rango razonable)
- Cintura: 40-200 cm (rango razonable)
- Estatura: 100-250 cm (rango razonable)
- Vasos: 0-15 (límite superior)

#### Manejo de Datos Faltantes
- Semanas sin peso/cintura: No mostrar en gráficos
- Comidas pendientes: No cuentan para índice
- Primera semana: Estadísticas mensuales mínimas

#### Compatibilidad
- Navegadores modernos (Chrome, Firefox, Safari, Edge)
- Responsive en móviles y tablets
- No requiere JavaScript externo (todo embebido)

---

### 14. ARQUITECTURA DE COMPONENTES

#### Componentes React Sugeridos
```
App (principal)
├── Header
│   ├── TitleSection
│   ├── ActionButtons
│   ├── MeasurementInputs
│   └── WeeklyMetrics
├── StatsPanel (colapsable)
│   ├── MonthlyStats
│   └── BodyProgressStats
├── ChartsPanel (colapsable)
│   ├── WeightWaistChart
│   ├── FoodQualityChart
│   ├── FoodDistributionChart
│   ├── HydrationChart
│   └── BMIChart
├── WeekGrid
│   └── DayCard (x7)
│       ├── DayHeader
│       ├── MovementSection
│       ├── FoodSection
│       │   ├── MealQualityButton (x3)
│       │   └── WaterCounter
│       └── WellbeingSection
└── Footer (instrucciones)
```

#### Hooks Personalizados
```javascript
useLocalStorage(key, initialValue)
useWeekData()
useHistoricalData()
useMonthlyStats()
```

---

### 15. PRIORIDADES DE IMPLEMENTACIÓN

#### Fase 1 - Core (MVP)
1. Estructura de datos semanal
2. Tarjetas de días con actividades básicas
3. Sistema de calidad alimenticia
4. Contador de agua
5. LocalStorage básico
6. Botón "Nueva Semana"

#### Fase 2 - Mediciones
7. Inputs de peso y cintura
8. Cálculo de IMC
9. Estadísticas semanales en header
10. Estadísticas mensuales

#### Fase 3 - Exportación
11. Exportar a CSV
12. Backup JSON
13. Importar JSON

#### Fase 4 - Visualización
14. Gráfico de peso/cintura
15. Gráfico de calidad alimenticia
16. Gráfico de IMC
17. Gráfico de hidratación
18. Gráfico de distribución de comidas

#### Fase 5 - Polish
19. Animaciones y transiciones
20. Validaciones completas
21. Mensajes de error/éxito
22. Instrucciones detalladas
23. Testing en diferentes navegadores

---

### 16. EJEMPLO DE USO COMPLETO

#### Semana 1
```
Lunes:
- Peso: 80.5 kg, Cintura: 95 cm → IMC: 26.8 (sobrepeso)
- Natación ✓
- Pausas ✓✓
- Desayuno: Saludable, Almuerzo: Saludable, Cena: Regular
- Agua: 9 vasos ✓
- Familia ✓, Sueño ✓

[... resto de la semana ...]

Domingo:
- Meal prep ✓
- Prep semana ✓

Resultado semana 1:
- Índice calidad: 85% (Excelente)
- Entrenamientos: 4/4
- Agua: 52 vasos
```

#### Semana 2-4
```
[Continuar registrando...]
```

#### Fin de Mes
```
Click en "Estadísticas":
- 16 entrenamientos totales
- 82% calidad promedio
- Peso: 80.5 kg → 78.2 kg (-2.3 kg) 🎉
- Cintura: 95 cm → 92 cm (-3 cm) 🎉
- IMC: 26.8 → 26.0 (mejora)
```

#### Exportar y Analizar
```
CSV descargado → Abrir en Excel:
- Crear tabla dinámica
- Gráfico de tendencia de peso
- Correlación calidad alimenticia vs peso
- Análisis día de la semana (¿fines de semana peor?)
```

---

## NOTAS FINALES

### Objetivos del Proyecto
✅ Herramienta personal de tracking sin costo
✅ Privacidad total (datos locales)
✅ Fácil de usar diariamente
✅ Análisis visual de progreso
✅ Exportable para análisis profundo
✅ Motivación mediante feedback visual

### Audiencia
- Usuario individual (familia de 3)
- Rutina establecida de ejercicio
- Busca balance vida-trabajo
- Valora datos y métricas
- Quiere mejorar composición corporal

### Valor Diferencial
- Personalizado a rutina específica (natación/fuerza)
- Sistema de calidad alimenticia (no solo SI/NO)
- Contador preciso de hidratación
- Mediciones corporales integradas
- Gráficos de progreso visual
- Todo en un solo archivo HTML

---

## ENTREGABLES ESPERADOS

1. **Archivo HTML único**: wellness-dashboard.html
2. **Funcional al abrir**: Sin instalación
3. **Responsive**: Desktop, tablet, móvil
4. **Datos persistentes**: LocalStorage
5. **Exportación**: CSV y JSON
6. **Gráficos**: 5 visualizaciones
7. **Documentación**: Comentarios en código

---

## EJEMPLO DE INICIO RÁPIDO PARA CLAUDE CODE

```bash
# Crea el archivo principal
touch wellness-dashboard.html

# Estructura básica:
# 1. HTML con meta tags y Tailwind CDN
# 2. React 18 desde unpkg CDN
# 3. Recharts desde unpkg CDN
# 4. Babel standalone
# 5. Componente principal en <script type="text/babel">
# 6. Implementar en orden de fases 1-5

# Test local:
# Abrir wellness-dashboard.html en navegador
# Verificar funcionalidades una por una
```

---

¡Buen trabajo con la implementación! 🚀