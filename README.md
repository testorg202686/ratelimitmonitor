# GitHub API Rate Limit Monitor

## Descripción

Workflow de GitHub Actions que monitorea el consumo del rate limit de la API de GitHub Enterprise Cloud asociado a un token de autenticación.

Consulta periódicamente los límites disponibles y genera un resumen de los recursos monitoreados. Si algún recurso tiene menos del 20 % de solicitudes disponibles, el workflow genera una alerta y finaliza la ejecución con estado fallido.

## ¿Qué revisa?

El workflow consulta el endpoint `GET /rate_limit` y reporta los recursos disponibles en la respuesta de GitHub, incluyendo:

- **Core:** solicitudes generales de la API REST.
- **Search:** solicitudes de búsqueda.
- **Code Search:** solicitudes de búsqueda de código, cuando se reporta como recurso independiente.
- **GraphQL:** solicitudes de la API GraphQL.
- **Otros recursos:** cualquier otra categoría devuelta por el endpoint.

Para cada recurso muestra:

- Límite total de solicitudes.
- Solicitudes utilizadas.
- Solicitudes restantes.
- Porcentaje disponible.
- Fecha y hora de restablecimiento del límite (UTC).

## Ejecución

El workflow se ejecuta:

- **Automáticamente:** todos los días a las 09:00, hora de Ciudad de México.
- **Manualmente:** desde la pestaña **Actions** del repositorio.

## Umbral de alerta

El umbral predeterminado es del **20 % de solicitudes restantes**.

- **Normal:** todos los recursos reportados tienen al menos un 20 % disponible.
- **Alerta:** algún recurso tiene menos del 20 % disponible; se genera una advertencia y la ejecución termina como fallida.

El resultado se publica en el resumen de la ejecución de GitHub Actions.

## Configuración requerida

En **Settings → Secrets and variables → Actions**, configura:

| Tipo | Nombre | Descripción |
|---|---|---|
| Secret | `ORG_RATE_LIMIT_TOKEN` | Token utilizado para consultar la API. |
| Variable (opcional) | `GH_ORG` | Nombre de la organización que se muestra en el reporte. |

El endpoint configurado es `https://api.github.com`, correspondiente a GitHub Enterprise Cloud.

El umbral se configura mediante la variable `RATE_LIMIT_THRESHOLD` definida en el workflow.

## Limitaciones

- El reporte corresponde al rate limit de la credencial autenticada, no al consumo total consolidado de toda la organización.
- No identifica qué repositorio o workflow consumió las solicitudes.
- No detecta de forma exhaustiva los límites secundarios de GitHub.
- El monitoreo diario no garantiza detectar agotamientos temporales entre ejecuciones.
- Las alertas se muestran en GitHub Actions; no se envían notificaciones externas.

## Referencia

[GitHub REST API — Rate limit](https://docs.github.com/en/rest/rate-limit/rate-limit)
