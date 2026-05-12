🏭 Empresa Textil X — Sistema de Gestión ERP
Sistema de gestión integral diseñado e implementado para una empresa textil en operación. Cubre inventario en tiempo real, control de deudas, fidelización de clientes y automatización de cobranzas vía WhatsApp.

🎯 Problema que resuelve
Empresa Textil X operaba con registros manuales en cuadernos y Excel sin conexión entre módulos. No había forma de saber el stock real de rollos, qué clientes debían, ni cuánto acumulaban para el programa de fidelización. Este sistema centraliza toda la operación en una plataforma accesible desde cualquier dispositivo.

🛠️ Stack tecnológico
CapaTecnologíaBase de datosAirtable (9 tablas relacionales)FrontendSoftr (web + móvil)AutomatizaciónFórmulas avanzadas, Rollups, WhatsApp APIDocumentaciónMarkdown / Obsidian

📦 Módulos del sistema
Inventario

Inventario de Hilos — stock en kg con rollup automático de entradas y salidas
Inventario de Telas — stock por rollo individual, actualización automática al vender
Cada rollo tiene estado (✅ Disponible / ❌ Vendido) que cambia solo al registrar una salida

Ventas y Cobranzas

Registro de ventas con soporte para pedidos mixtos (hilos + rollos)
Control de deudas con amortizaciones parciales
Generación automática de link WhatsApp con mensaje de cobro personalizado (nombre, total, saldo pendiente)

Fidelización de Clientes

Programa por ciclos semestrales (Junio / Diciembre)
Acumulación de packs según volumen de compra (1 pack = 15 kg)
Niveles automáticos: 🥉 Bronce (30) / 🥈 Plata (80) / 🥇 Oro (160)
Reset automático al cambiar de ciclo — sin intervención manual
Link WhatsApp personalizado por cliente con packs acumulados y nivel actual

Frontend (Softr)

Interfaz operativa para uso no técnico por el equipo en planta
Formularios de entrada/salida para hilos y rollos
Vistas de inventario por tipo de tela e hilo con fotos
Módulo de ventas, cobranzas y registro de clientes


🗄️ Arquitectura de datos
Entradas Rollos ──→ Inventario Telas (stock rollup automático)
Entradas Hilo   ──→ Inventario Hilos (stock rollup automático)

Salida Rollos ──→ Ventas/Deudas ──→ Pagos/Cobranzas
Salidas Hilo  ──→ Ventas/Deudas

Clientes ←── Salida Rollos  (rollup Aporte Canasta → Nivel Fidelización)
Clientes ←── Salidas Hilo   (rollup Aporte Canasta → Nivel Fidelización)
         ──→ WhatsApp API   (link automático de fidelización y cobranza)

✅ Estado actual
MóduloEstadoInventario de Telas✅ Funcional — stock automáticoInventario de Hilos✅ FuncionalEntradas de Rollos e Hilos✅ FuncionalSalidas de Rollos e Hilos✅ FuncionalVentas / Deudas✅ FuncionalPagos / Cobranzas✅ FuncionalFidelización de Clientes✅ Funcional — ciclos automáticosFrontend Softr🔄 Sistema de Costos de Producción✅ Funcional — implementado en Airtable

🚀 Próximos módulos

Costos de producción — costo real por rollo (hilo + tejido + procesos + transporte) para calcular margen real mensual
Vista de clientes en Softr para gestión operativa en planta
Detalle de cliente con historial de ventas y pagos


👤 Autor
Alonso Campos Mercado
Estudiante de Ingeniería de Sistemas — Universidad de Lima
Analista de Sistemas y Datos — Textiles Favio
LinkedIn
