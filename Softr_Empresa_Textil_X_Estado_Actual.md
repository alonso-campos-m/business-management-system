# 📱 Softr — Sistema Frontend Empresa Textil X
> Informe generado: 2026-05-09 | Conectado a: Airtable (Untitled Base)

---

## 🗺️ Estructura de Páginas

```
Principal
├── Hilos y Rollos
│   ├── Hilos ingresar      → Formulario → Entradas de Hilo
│   ├── Hilos Salidas       → Formulario → Salidas de Hilo
│   ├── Rollos ingresar     → Formulario → Entradas Rollos
│   └── Rollos Salidas      → Formulario → Salida Rollos
│
├── Venta y Cobros
│   ├── cliente             → Formulario → Clientes
│   ├── Ventas              → Formulario → Ventas / Deudas
│   └── Cobranza            → Formulario → Pagos / Cobranzas
│
└── Inventario
    ├── Inventario Hilos    → Grid visual → sub-páginas por tipo
    │   ├── 32/1            → Cards → Inventario de Hilos (filtro: Acrílico 32/1)
    │   └── 32/2            → Cards → Inventario de Hilos (filtro: Acrílico 32/2)
    └── Inventario Telas    → Grid visual → sub-páginas por tipo
        ├── milano          → Cards → Inventario de Telas (filtro: Milano)
        ├── rib-dralon-acrilico → Cards → Inventario de Telas (filtro: Rib Dralon)
        ├── plush-llano     → Cards → Inventario de Telas (filtro: Plush Llano)
        └── plush-mill-rayas → Cards → Inventario de Telas (filtro: Plush Mill Rayas)
```

---

## 📋 Páginas y Estado

### Principal
**Estado:** ✅ Funcional
- Dos secciones: "Hilos y Rollos" y "Venta y Cobros"
- Botones con imagen que navegan a cada sección
- Navegación superior con link a Principal

---

### Hilos ingresar
**Estado:** ✅ Funcional
**Tabla destino:** Entradas de hilo
**Campos:**
| Campo | Tipo | Obligatorio |
|---|---|---|
| Fecha | Date | ✅ |
| Factura / Guía / Nota | Text | ✅ |
| Proveedor | Text | ✅ |
| Hilos Comprados | Linked record | ✅ |
| Kilos | Number | ✅ |

---

### Hilos Salidas
**Estado:** ✅ Funcional
**Tabla destino:** Salidas de Hilo
**Campos:**
| Campo | Tipo | Obligatorio |
|---|---|---|
| Cliente | Linked record | ✅ |
| Fecha | Date | ✅ |
| Hilo a usar | Linked record | ✅ |
| Kilos que salen | Number | ✅ |
| Motivo | Single select | ✅ |
| Precio de Venta | Currency | ✅ |

**Nota:** Motivo incluye: Mandado a Tejer (servicio) / Venta Directa / A Máquina Propia

---

### Rollos ingresar
**Estado:** ✅ Funcional
**Tabla destino:** Entradas Rollos
**Campos:**
| Campo | Tipo | Obligatorio |
|---|---|---|
| Fecha | Date | ✅ |
| Factura / Guía / Nota | Text | ✅ |
| Proveedor | Text | ✅ |
| Tipo de Tela / Color | Linked record | ✅ |
| Peso (Kg) | Number | ✅ |

---

### Rollos Salidas
**Estado:** ✅ Funcional
**Tabla destino:** Salida Rollos
**Campos:**
| Campo | Tipo | Obligatorio |
|---|---|---|
| Fecha | Date | ✅ |
| Cliente | Linked record | ✅ |
| Rollos Vendidos | Linked record | ✅ |
| Precio de Venta | Currency | ✅ |

---

### cliente
**Estado:** ⚠️ Parcial — solo tiene formulario de alta
**Tabla destino:** Clientes
**Campos del formulario:**
| Campo | Tipo | Obligatorio |
|---|---|---|
| Nombre | Text | ✅ |
| DNI o RUC | Text | ✅ |
| Ciudad | Text | ✅ |
| Dirección | Text | ✅ |
| Celular | Phone | ✅ |

**Pendiente crítico:**
- [ ] Agregar bloque List/Table con todos los clientes
- [ ] Mostrar: Nombre, Celular, Nivel Canasta, Saldo Pendiente, botón Enviar WA
- [ ] Esta vista es lo que tu mamá necesita para atender clientes

---

### Ventas
**Estado:** ⚠️ Bug — campos obligatorios mal configurados
**Tabla destino:** Ventas / Deudas
**Campos:**
| Campo | Tipo | Obligatorio | Correcto |
|---|---|---|---|
| Clientes | Linked record | ✅ | ✅ |
| Fecha | Date | ✅ | ✅ |
| Hilos del Pedido | Linked record | ✅ | ❌ debe ser opcional |
| Rollos del Pedido | Linked record | ✅ | ❌ debe ser opcional |

**Fix:** Desactivar "Required" en Hilos del Pedido y Rollos del Pedido — una venta puede ser solo rollos, solo hilos, o ambos.

**Pendiente:**
- [ ] Verificar que "Allow multiple selections" esté activo en ambos linked records
- [ ] Corregir campos obligatorios

---

### Cobranza
**Estado:** ✅ Funcional
**Tabla destino:** Pagos / Cobranzas
**Campos:**
| Campo | Tipo | Obligatorio |
|---|---|---|
| Fecha del Pago | Date | ✅ |
| Método de Pago | Single select | ✅ |
| Monto Pagado | Currency | ✅ |
| Venta a Pagar | Linked record | ✅ |

**Métodos de pago:** Efectivo / Plin / Transferencia / Yape ✅

---

### Inventario Hilos
**Estado:** ✅ Funcional — buen diseño visual
- Grid de 2 cards: Acrílico 32/1 y Acrílico 32/2
- Cada card navega a su sub-página con filtro por tipo
- Sub-páginas muestran: Nombre, Color, Stock Actual, Estado de Stock
- Faltan fotos en los registros (aparece placeholder de imagen)

**Pendiente:**
- [ ] Agregar fotos a los registros de Inventario de Hilos en Airtable

---

### Inventario Telas
**Estado:** ✅ Funcional — el mejor módulo visualmente
- Grid de 4 tipos con fotos reales: Milano, Rib Dralon, Plush Llano, Plush Mill Rayas
- Sub-páginas filtradas por Tipo de Tela
- Muestran: Color, Stock de Rollos, Estado de Stock
- Faltan fotos en registros individuales

**Pendiente:**
- [ ] Agregar fotos a los registros individuales de colores

---

## 🚧 Módulos Pendientes de Crear

| Módulo | Descripción | Prioridad |
|---|---|---|
| Lista de Clientes | Vista para tu mamá — ver todos los clientes con estado | 🔴 Alta |
| Detalle de Cliente | Historial de ventas y pagos por cliente | 🟡 Media |
| Vista de Deudas pendientes | Filtro de ventas con saldo > 0 | 🟡 Media |
| Costos de Producción | Nuevo módulo — pendiente de diseñar en Airtable primero | 🟢 Futura |

---

## ✅ Próximos Pasos Priorizados

1. **Fix urgente:** Quitar obligatoriedad de Hilos del Pedido y Rollos del Pedido en formulario Ventas
2. **Página cliente:** Agregar bloque List con todos los clientes y sus campos clave
3. **Fotos:** Subir fotos de hilos y rollos individuales a Airtable para que aparezcan en Softr
4. **Detalle cliente:** Página de detalle con historial de ventas y pagos
5. **Vista deudas:** Filtro de ventas pendientes de pago para facilitar cobranza

---

## ⚠️ Alerta — Trial activo

Softr muestra "Get 7 more trial days" en las capturas. Definir plan de pago antes de que venza para no perder acceso al frontend.

---

*Última actualización: Sesión 2026-05-09*
