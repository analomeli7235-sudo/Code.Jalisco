# CodeJalisco — Reportes urbanos ciudadanos

Plataforma para registrar y consultar reportes de baches, alumbrado, árboles caídos, basura y fugas de agua.
**No está afiliada a ninguna dependencia y NO envía los reportes a ninguna autoridad**: los guarda en su propia base de datos. No es un canal de emergencias (911).

## Arquitectura
- `server.py`: servidor **sin dependencias** (Python 3.9+): sirve `public/`, expone la API y guarda en SQLite (`data/reports.db`) y fotos en `data/uploads/` (se crean solos).
- `public/`: HTML/CSS/JS del sitio.
- API: `POST /api/reports`, `GET /api/reports/<folio>`, `GET /api/stats`, `PATCH /api/reports/<folio>` (administración).

## Ejecutar
```
python3 server.py            # http://localhost:8000
PORT=8080 CODEJALISCO_ADMIN_TOKEN="un-token-largo-y-secreto" python3 server.py
```
Sin `CODEJALISCO_ADMIN_TOKEN` la actualización de estados está deshabilitada. Con token, entra a `/admin.html` (no enlazada) para cambiar el estado de un folio. "Resuelto" y "No procedente" exigen una nota; el estado solo cambia con una actualización registrada.

## Funcionalidades
Formulario con validación (cliente y servidor), folio único `CJ-` + 10 hex aleatorios, foto opcional (JPG/PNG/WebP ≤ 2 MB, verificada por firma de archivo y renombrada), geolocalización opcional solo al pulsar el botón, seguimiento con línea de tiempo, estadísticas reales (vacías si no hay reportes), servicios públicos, FAQ.

## Seguridad y privacidad
Consultas SQL parametrizadas; escape de HTML; CSP, `nosniff`; límite de 10 reportes/hora por IP (en memoria); límite de tamaño de solicitud; datos de contacto **nunca** devueltos por la API pública; comparación de token en tiempo constante.

## Limitaciones
- Para producción: usar HTTPS (proxy inverso), respaldos de `data/`, moderación de fotos y un **aviso de privacidad real** (el del sitio está pendiente). El folio es el único control de acceso a la consulta.
- No hay cuentas de usuario, ni mapa interactivo (evita servicios externos), ni notificaciones.
- Información institucional: deliberadamente general; verificar con cada municipio antes de nombrar dependencias. **No hay canales de contacto verificados**; agregarlos en `faq.html`.
- No hay datos de ejemplo cargados.

## Pruebas (local)
API: validación, foto falsa rechazada, creación, consulta sin datos de contacto, PATCH con/sin token y nota, estadísticas, bloqueo de ruta fuera de `public/`. Interfaz (Chromium, 375 px): errores de formulario, alta, seguimiento, XSS escapado, sin errores JS. No se probó con carga ni en otros navegadores. Sin publicación en línea.
