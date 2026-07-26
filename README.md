# Checklist de Auditoría de Agentes de IA en Producción

Una lista práctica y abierta para revisar la seguridad, trazabilidad y cumplimiento de sistemas RAG y agentes LLM antes de llevarlos a producción.

Mantenido por **[DatotecAudit](https://datotecaudit.com)** — plataforma de observabilidad, auditoría y gobernanza para agentes de IA (trazabilidad forense, control Human-in-the-Loop, cumplimiento AI Act).

---

## 1. Seguridad del código que orquesta al agente

- [ ] Sin secretos hardcodeados (API keys, tokens, credenciales de BD) en el repositorio
- [ ] Dependencias sin vulnerabilidades conocidas (CVE) — revisar contra [OSV.dev](https://osv.dev)
- [ ] Sin patrones de inyección evidentes: `eval()`, concatenación directa en SQL, `child_process.exec` con variables sin sanear
- [ ] CORS restringido a una whitelist explícita, nunca `Access-Control-Allow-Origin: *` en producción
- [ ] Variables de entorno correctamente separadas del código fuente (`.env` en `.gitignore`)

## 2. Prompts y system messages

- [ ] El system prompt no puede ser sobrescrito por input del usuario sin control
- [ ] Existe un límite claro de qué herramientas/acciones puede invocar el agente
- [ ] Los prompts están versionados (no se editan "a mano" en producción sin registro)

## 3. Trazabilidad (RAG y agentes en general)

- [ ] Cada acción del agente con efecto real queda registrada: qué se propuso, con qué datos, cuándo
- [ ] El registro incluye el resultado final (ejecutado / fallido / pendiente), no solo la intención
- [ ] Existe un identificador único por traza, para poder reconstruir una secuencia completa de decisiones
- [ ] El historial de trazas no se puede alterar retroactivamente (idealmente con hash de integridad)

## 4. Control humano (Human-in-the-Loop)

- [ ] Las acciones de alto impacto (pagos, envíos masivos, modificación de registros) requieren aprobación humana antes de ejecutarse
- [ ] Existe un punto de control real (panel, no un canal de Slack sin auditar) donde aprobar o rechazar
- [ ] Queda registrado quién aprobó o rechazó cada acción, y cuándo

## 5. Control de costes

- [ ] Se registra el coste (tokens, llamadas a APIs de terceros) por acción del agente, no solo el agregado mensual
- [ ] Existen límites o alertas que avisan antes de un sobrecoste, no después de la factura
- [ ] Se puede identificar qué agente o qué flujo concreto genera el gasto, no solo un total global

## 6. Cumplimiento AI Act (si operas en la UE)

- [ ] Identificado si el sistema entra en la categoría de "alto riesgo" según el Reglamento (UE) 2024/1689
- [ ] Existe documentación de supervisión humana real, no solo una declaración de intenciones
- [ ] Se puede generar un informe de cumplimiento con evidencia verificable en cualquier momento, no solo bajo petición urgente
- [ ] La evaluación de riesgo se revisa cuando cambian los agentes o sus permisos, no solo una vez al año

## 7. Latencia y fiabilidad (agentes de voz / tiempo real)

- [ ] Existe un timeout definido por acción, para que un fallo no deje el flujo colgado indefinidamente
- [ ] Los fallos de un componente no bloquean el resto del sistema (fallo aislado, no en cascada)
- [ ] Hay reintento automático limitado (no infinito) para fallos transitorios de red o de proveedor LLM

---

## Sobre este checklist

Este documento es de uso libre. Si tu equipo opera agentes de IA en producción y quiere automatizar buena parte de estas comprobaciones (auditoría de seguridad del repo, trazabilidad forense, control Human-in-the-Loop e informes de cumplimiento AI Act), puedes ver cómo lo hacemos en [DatotecAudit](https://datotecaudit.com).

Contribuciones y sugerencias vía issues o pull requests son bienvenidas.
