# 📊 Sistema de Costos de Producción — Textiles Favio
> Informe técnico: 2026-05-09 | Estado: Implementado en Airtable

---

## 🎯 Objetivo

Calcular el costo real de producción por rollo para obtener el margen real por venta y la utilidad mensual real del negocio.

```
Costo por Rollo  = Costo Total Lote / Cantidad de Rollos
Margen por Venta = Precio de Venta - Costo por Rollo
Utilidad Mensual = Ingresos - Costos Variables - Costos Generales
```

---

## 🧵 Materias Primas

| Hilo | Uso | Variantes de precio |
|---|---|---|
| Acrílico 32/1 Normal | Todos los tipos de tela | Precio estándar |
| Acrílico 32/1 Marengo | Todos los tipos de tela | Más caro — precio separado |
| Poliéster Normal | Milano, Plush Llano, Plush Mil Rayas | Precio estándar |
| Poliéster Negro | Milano, Plush Llano, Plush Mil Rayas | Más caro — precio separado |
| Acrílico 32/2 | Solo venta directa | No entra en lotes |

---

## 🏭 Flujo de Producción

```
TODOS LOS TIPOS:
Hilo llega a fábrica
  → [Costo de Carga — trip al personal]
  → Tejido (cobran por kilo que entra — S/1.6 Plush/Milano, S/1.5 Rib)

PLUSH LLANO y PLUSH MIL RAYAS (procesos extra):
  → [taxi] → Tundido (cobran por kilo que sale)
  → [taxi] → Rameado (cobran por kilo que sale)

TODOS — Destino final:
  → [taxi] → Local (se guarda)
  → [taxi] → Agencia (+ costo agencia)
  → [taxi] → Cliente directo
  → Local → [taxi] → Agencia o Cliente
```

---

## 💰 Costos por Tipo de Tela

| Costo | Milano | Rib | Plush Llano | Plush Mil Rayas |
|---|---|---|---|---|
| Hilo Acrílico 32/1 | ✅ | ✅ | ✅ | ✅ |
| Hilo Poliéster | ✅ | ❌ | ✅ | ✅ |
| Carga de hilo | ✅ | ✅ | ✅ | ✅ |
| Tejido (kilos entrada) | S/1.6/kg | S/1.5/kg | S/1.6/kg | S/1.6/kg |
| Tundido (kilos salida) | ❌ | ❌ | ✅ | ✅ |
| Rameado (kilos salida) | ❌ | ❌ | ✅ | ✅ |
| Taxi entre procesos | ❌ | ❌ | ✅ | ✅ |
| Taxi a destino final | ✅ | ✅ | ✅ | ✅ |
| Costo Agencia | Según destino | Según destino | Según destino | Según destino |
| Costo Adicional | ✅ | ✅ | ✅ | ✅ |

---

## 🗄️ Tablas en Airtable

### Tabla 1: Lotes de Producción

| Campo | Tipo | Descripción |
|---|---|---|
| ID Lote | Primary | Identificador único |
| Fecha | Date | |
| Descripción | Text | Ej: "Plush mayo lote 1" |
| Kilos Acrílico 32/1 Normal | Number | |
| Kilos Acrílico 32/1 Marengo | Number | |
| Kilos Poliéster Normal | Number | |
| Kilos Poliéster Negro | Number | |
| Total Kilos Acrílico 32/1 | Fórmula | Normal + Marengo |
| Total Kilos Poliéster | Fórmula | Normal + Negro |
| Total Kilos Hilo | Fórmula | Acrílico + Poliéster |
| Precio kg Acrílico Normal | Currency | |
| Precio kg Acrílico Marengo | Currency | |
| Precio kg Poliéster Normal | Currency | |
| Precio kg Poliéster Negro | Currency | |
| Precio kg Tejido | Currency | Variable por lote |
| Precio kg Tundido | Currency | Solo Plush |
| Precio kg Rameado | Currency | Solo Plush |
| Costo de Carga | Currency | Trip al personal |
| Costo Agencia | Currency | Solo envíos a provincia |
| Costo Adicional | Currency | Imprevistos del lote |
| Gastos Taxi | Link → Gastos de Taxi | |
| Total Taxi | Rollup SUM (Monto) | |
| Total Hilo | Fórmula | Ver abajo |
| Total Procesos | Fórmula | Ver abajo |
| Costo Total Lote | Fórmula | Ver abajo |
| Kg Producidos | Number | Se ingresa al terminar producción |
| Cantidad de Rollos | Number | Se ingresa al terminar producción |
| Merma Kg | Fórmula | `{Total Kilos Hilo} - {Kg Producidos}` |
| Merma % | Fórmula | `ROUND(({Merma Kg} / {Total Kilos Hilo}) * 100, 1)` |
| Costo por Rollo | Fórmula | `{Costo Total Lote} / {Cantidad de Rollos}` |

**Fórmulas:**
```
Total Hilo =
  ({Kilos Acrílico 32/1 Normal} * {Precio kg Acrílico Normal}) +
  ({Kilos Acrílico 32/1 Marengo} * {Precio kg Acrílico Marengo}) +
  ({Kilos Poliéster Normal} * {Precio kg Poliéster Normal}) +
  ({Kilos Poliéster Negro} * {Precio kg Poliéster Negro})

Total Procesos =
  ({Total Kilos Hilo} * {Precio kg Tejido}) +
  ({Kg Producidos} * {Precio kg Tundido}) +
  ({Kg Producidos} * {Precio kg Rameado})

Costo Total Lote =
  {Total Hilo} + {Total Procesos} +
  {Costo de Carga} + {Total Taxi} +
  {Costo Agencia} + {Costo Adicional}
```

**Nota:** Tejido cobra por kilos que **entran** (`Total Kilos Hilo`). Tundido y Rameado cobran por kilos que **salen** (`Kg Producidos`).

---

### Tabla 2: Gastos de Taxi

| Campo | Tipo | Descripción |
|---|---|---|
| ID Gasto | Primary | |
| Lote | Link → Lotes de Producción | |
| Tipo | Single Select | Ver opciones |
| Monto | Currency | |
| Fecha | Date | |

**Opciones Tipo:**
- Tejido → Tundido
- Tundido → Rameado
- Tejido → Local
- Tejido → Agencia
- Tejido → Cliente
- Rameado → Local
- Rameado → Agencia
- Rameado → Cliente
- Local → Agencia
- Local → Cliente

---

### Tabla 3: Costos Generales

| Campo | Tipo | Descripción |
|---|---|---|
| ID Costo | Primary | |
| Fecha | Date | |
| Tipo | Single Select | Alquiler Fábrica / Mantenimiento / Otro |
| Monto | Currency | |
| Descripción | Text | |

**Nota sobre alquiler:** $100/mes se registra una vez al mes en soles al tipo de cambio del día.

---

## 🔗 Conexión con Sistema Existente

**En Entradas Rollos:**
- `Lote de Producción` → Link → Lotes de Producción
- `Costo por Rollo` → Rollup MAX desde Lotes de Producción

**En Salida Rollos:**
- `Costo por Rollo` → Rollup SUM desde Entradas Rollos
- `Margen` → Fórmula: `{Precio de Venta} - {Costo por Rollo}`

---

## 📈 Cálculo de Utilidad Mensual

Filtrar por mes en cada tabla:

```
Ingresos          = Suma {Precio de Venta} en Salida Rollos + Salidas Hilo
Costos Variables  = Suma {Costo Total Lote} en Lotes de Producción
Costos Generales  = Suma {Monto} en Costos Generales
─────────────────────────────────────────────────────
Utilidad Real     = Ingresos - Costos Variables - Costos Generales
```

---

## 🖥️ Softr — Formularios pendientes

| Formulario | Tabla | Lo registra |
|---|---|---|
| Nuevo Lote | Lotes de Producción | Mamá |
| Nuevo Gasto de Taxi | Gastos de Taxi | Mamá |
| Nuevo Costo General | Costos Generales | Mamá |

---

## 🚧 Pendiente

- [ ] Crear formularios en Softr para los 3 módulos nuevos
- [ ] Validar con datos reales el cálculo de Costo por Rollo y Margen
- [ ] Levantar histórico de merma por tipo de tela

---

*Última actualización: Sesión 2026-05-09*
