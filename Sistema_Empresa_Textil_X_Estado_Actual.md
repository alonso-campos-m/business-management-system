# 🏭 Sistema de Gestión — Textiles Favio
> Informe generado: 2026-05-09 | Herramienta: Airtable + Softr

---

## 📦 Arquitectura General

```
Entradas Rollos ──→ Inventario Telas (stock rollup)
Entradas Hilo   ──→ Inventario Hilos (stock rollup)

Salida Rollos ──→ Ventas/Deudas ──→ Pagos/Cobranzas
Salidas Hilo  ──→ Ventas/Deudas

Clientes ←── Salida Rollos  (Packs Rollos: rollup de Aporte Canasta)
Clientes ←── Salidas Hilo   (Packs Hilos: rollup de Aporte Canasta)
         ──→ Enviar WA      (link WhatsApp automático por fidelización)
```

---

## 📋 Módulos y Estado

### 1. Inventario de Telas
**Estado:** ✅ Funcional con automatización de stock

| Campo | Tipo | Descripción |
|---|---|---|
| Código | Primary | Identificador único (Tipo + Color) |
| Entradas Rollos | Link | Rollos físicos comprados |
| Stock de Rollos | Rollup | `SUM(Es Disponible)` — se resta automático al vender |
| Estado de Stock | Fórmula | Agotado / Disponible |

**Flujo de stock:**
```
Se linkea rollo en Salida Rollos
  → Estado en Entradas Rollos = "❌ Vendido" (fórmula automática)
    → Es Disponible = 0
      → Stock de Rollos se resta solo ✅
```

**Pendientes:**
- [ ] Validar con datos reales que el rollup cuenta correctamente

---

### 2. Entradas Rollos
**Estado:** ✅ Funcional

| Campo | Tipo | Descripción |
|---|---|---|
| Código | Primary | ID de la entrada |
| Fecha | Date | Fecha de compra |
| Proveedor | Text | Nombre del proveedor |
| Tipo de Tela / Color | Link | Linkea a Inventario Telas |
| Peso (Kg) | Number | Peso del rollo físico |
| Estado | Fórmula | `IF({Salida Rollos}, "❌ Vendido", "✅ Disponible")` |
| Es Disponible | Fórmula | `IF({Estado} = "✅ Disponible", 1, 0)` — alimenta el rollup de stock |
| Salida Rollos | Link | Backlink — se llena cuando se vende |

**Nota:** Cada registro = 1 rollo físico individual.

---

### 3. Inventario de Hilos
**Estado:** ✅ Funcional

| Campo | Tipo | Descripción |
|---|---|---|
| Nombre del Hilo | Primary | Tipo + Color |
| Stock Actual | Rollup | Entradas - Salidas en kg |
| Estado de Stock | Fórmula | Agotado / Bajo / Ok |

---

### 4. Entradas de Hilo
**Estado:** ⚠️ Funcional pero sin automatización de stock completa

| Campo | Tipo | Descripción |
|---|---|---|
| Código | Primary | ID entrada |
| Proveedor | Text | Texto libre — sin tabla de proveedores |
| Hilos Comprados | Link | Linkea a Inventario Hilos |
| Kilos | Number | Cantidad en kg |

**Pendientes:**
- [ ] Verificar que el rollup de stock en Inventario Hilos suma correctamente los kilos de entradas menos salidas
- [ ] Evaluar crear tabla Proveedores si se manejan más de 4 proveedores

---

### 5. Salida Rollos
**Estado:** ✅ Funcional

| Campo | Tipo | Descripción |
|---|---|---|
| Cliente | Link | Linkea a Clientes |
| Rollos Vendidos | Link | Linkea a Entradas Rollos — filtrado por vista "Salidas Disponibles" |
| Total Kilos Vendidos | Rollup | Suma Peso(Kg) de rollos linkeados |
| Ciclo | Fórmula | Junio/Diciembre según fecha |
| Aporte Canasta | Fórmula | `ROUND(Kilos/15, 0)` si ciclo coincide con hoy |
| Precio de Venta | Currency | Ingreso manual (precio varía por cliente) |
| Venta Asignada | Link | Linkea a Ventas/Deudas |

**Vista "Salidas Disponibles":** Filtra solo salidas sin venta asignada, para que no se puedan asignar dos veces.

---

### 6. Salidas de Hilo
**Estado:** ✅ Funcional

| Campo | Tipo | Descripción |
|---|---|---|
| Cliente | Link | Linkea a Clientes |
| Hilo a usar | Link | Linkea a Inventario Hilos |
| Kilos que salen | Number | Cantidad vendida/usada |
| Motivo | Single Select | `Venta Directa` / `Mandado a Tejer (servicio)` / `A Máquina Propia` |
| Ciclo | Fórmula | Junio/Diciembre según fecha |
| Aporte Canasta | Fórmula | Solo suma si Motivo = "Venta Directa" Y ciclo actual |
| Venta Asignada | Link | Linkea a Ventas/Deudas |

**Fórmula Aporte Canasta (corregida):**
```
IF(
  AND(
    {Ciclo} = IF(
      OR(MONTH(TODAY()) = 12, MONTH(TODAY()) <= 5),
      "🏆 Junio " & IF(MONTH(TODAY()) = 12, YEAR(TODAY()) + 1, YEAR(TODAY())),
      "🎄 Diciembre " & YEAR(TODAY())
    ),
    {Motivo} = "Venta Directa"
  ),
  ROUND({Kilos que salen} / 15, 0),
  0
)
```

---

### 7. Ventas / Deudas
**Estado:** ✅ Funcional

| Campo | Tipo | Descripción |
|---|---|---|
| ID Venta | Primary | Identificador único |
| Clientes | Link | Cliente de la venta |
| Rollos del Pedido | Link | Salida Rollos — filtrado por "Salidas Disponibles" |
| Hilos del Pedido | Link | Salidas de Hilo — filtrado por "Salidas Disponibles" |
| Subtotal Rollos | Rollup | Suma Precio de Venta de rollos |
| Subtotal Hilos | Rollup | Suma Precio de Venta de hilos |
| Monto Total Venta | Fórmula | Subtotal Rollos + Subtotal Hilos |
| Total Amortizado | Rollup | Suma pagos recibidos |
| Saldo Pendiente | Fórmula | Monto Total - Total Amortizado |
| Estado de Deuda | Fórmula | Pagado / Pendiente / Parcial |
| WA Cobro | Fórmula | Link WhatsApp con mensaje de cobro personalizado |

**Nota:** Campo antes llamado `Calculation` — renombrado a `WA Cobro`.

---

### 8. Pagos / Cobranzas
**Estado:** ✅ Funcional

| Campo | Tipo | Descripción |
|---|---|---|
| ID Pago | Primary | Identificador único |
| Venta a Pagar | Link | Linkea a Ventas/Deudas |
| Fecha del Pago | Date | |
| Monto Pagado | Currency | |
| Método de Pago | Text | |

**Nota:** Permite múltiples pagos por venta (amortizaciones parciales).

---

### 9. Clientes
**Estado:** ✅ Funcional — módulo de fidelización completo

| Campo | Tipo | Descripción |
|---|---|---|
| Nombre | Primary | |
| DNI o RUC | Text | |
| Celular | Phone | |
| Packs Rollos | Rollup | `SUM(Aporte Canasta)` desde Salida Rollos |
| Packs Hilos | Rollup | `SUM(Aporte Canasta)` desde Salidas Hilo |
| Total Packs Ciclo | Fórmula | Packs Rollos + Packs Hilos |
| Nivel Canasta | Fórmula | Bronce(30) / Plata(80) / Oro(160) |
| Packs Faltantes | Fórmula | Packs para siguiente nivel |
| Enviar WA | Fórmula | Link WhatsApp con mensaje de fidelización |
| Presupuesto Canasta | Number | Monto estimado de la canasta/regalo |

**Lógica de ciclos:**
- Ciclo Junio: meses Diciembre → Mayo (ventas de esa temporada)
- Ciclo Diciembre: meses Junio → Noviembre
- Al cambiar de ciclo, Aporte Canasta de ventas anteriores pasa a 0 automáticamente (reseteo natural por fórmula)

---

## 🔗 Softr (Frontend para tu mamá)
**Estado:** 🔄 Por configurar

- Tu mamá accede solo desde Softr — no ve las tablas de Airtable
- **Pendiente:** Crear página de lista de clientes con: nombre, celular, nivel canasta, saldo pendiente
- **Pendiente:** Crear página de detalle por cliente con historial de ventas y pagos

---

## 🚧 Módulos en Pausa

### Chatbot
- **Estado:** Desarrollado pero no implementado
- **Razón:** Presupuesto y baja prioridad
- **Acción:** Evaluar implementación en siguiente etapa

### Página Web (Framer)
- **Estado:** En desarrollo
- **Bloqueante:** Conseguir fotos de productos
- **Pendiente:** Completar galería y publicar

---

## 📊 Sistema de Costos de Producción (PRÓXIMO MÓDULO)
**Estado:** 🔲 Por diseñar

**Objetivo:** Calcular ganancia real mensual descontando costos reales de producción.

**Costos a registrar:**
- Costo real del hilo por lote (precio pagado al proveedor)
- Costo real del tejido por rollo (lo que se paga realmente, no lo de la factura)
- Procesos adicionales por tipo de tela:
  - Plush llano → 2 procesos extra además del tejido
  - Plush mil rayas → 2 procesos extra además del tejido
- Gastos de transporte por despacho o compra

**Output esperado:**
```
Costo real por rollo = hilo + tejido + procesos adicionales + transporte prorrateado
Margen por venta = Precio de Venta - Costo real por rollo
Ganancia mensual = Suma de márgenes del mes
```

**Pendiente:** Diseñar estructura de tablas en Airtable para este módulo.

---

## ✅ Próximos Pasos Priorizados

1. **Validar flujo de stock con datos reales** — registrar una entrada, linkearla a una salida y verificar que `Stock de Rollos` en Inventario Telas se descuenta
2. **Configurar Softr** — página lista de clientes para tu mamá
3. **Diseñar módulo de costos** — tablas para registrar costos de hilo, tejido, procesos y transporte
4. **Conseguir fotos de productos** — desbloquea la web en Framer
5. **Evaluar chatbot** — cuando haya presupuesto y la operación esté estabilizada

---

## 🐛 Bugs Conocidos / Alertas

| Problema | Estado |
|---|---|
| `Aporte Canasta` en Salidas Hilo no filtraba por Motivo | ✅ Corregido |
| `Calculation` nombre genérico en Ventas/Deudas | ✅ Renombrado a WA Cobro |
| Stock de rollos era número manual | ✅ Ahora es rollup automático |
| `A Máquina Propia` acumulaba packs indebidamente | ✅ Corregido por filtro de Motivo |

---

*Última actualización: Sesión 2026-05-09*
