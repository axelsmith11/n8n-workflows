# ⚙️ n8n Workflows — Templates de automatización con IA

Templates de n8n listos para importar, extraídos de patrones que uso en producción (sanitizados: sin tokens, sin credenciales, sin lógica de negocio propietaria).

## 📦 Workflows

### 1. WhatsApp Multimodal AI Router

[⬇️ Descargar JSON](./whatsapp-multimodal-ai-router.json)

Recibe cualquier mensaje de WhatsApp (Cloud API) y lo enruta según su tipo para procesarlo con IA:

```
Webhook → Detectar tipo → Router
                            ├─ Texto    ──────────────────┐
                            ├─ Voz      → Whisper ─────┤
                            ├─ Imagen   → GPT-4o Vision ┤→ Clasificar intención (LLM)
                            └─ PDF      → Extractor ───┘   → Parseo JSON seguro
                                                            → Respuesta por WhatsApp
```

**Qué demuestra:**

- **Routing multimodal**: un solo webhook maneja texto, notas de voz, imágenes y documentos.
- **Transcripción** de audio con Whisper vía API.
- **Visión** con GPT-4o para extraer datos estructurados de imágenes.
- **Parseo JSON defensivo**: limpia fences de markdown y usa fallback controlado — un JSON malformado del LLM nunca tumba el flujo.
- **Seguridad**: toda autenticación va por credenciales de n8n (Header Auth). Nada hardcodeado.

**Cómo usarlo:**

1. En n8n: *Workflows → Import from File* y selecciona el JSON.
2. Crea 2 credenciales Header Auth: token de Meta (WhatsApp Cloud API) y API key de OpenAI.
3. Reemplaza `<TU_PHONE_NUMBER_ID>` en el nodo de respuesta.
4. Apunta el webhook de tu app de Meta al endpoint del nodo Webhook.

> 💡 Este patrón es la base de asistentes conversacionales como **Pablo** ([usapablo.com](https://usapablo.com)), que proceso +500 usuarios activos en producción.

### 2. Telegram AI Data Agent

[⬇️ Descargar JSON](./telegram-ai-data-agent.json)

Agente de IA conversacional por Telegram que consulta tus datos en Google Sheets mediante **tools** y responde con analisis accionables.

- **Agente con herramientas**: 3 Google Sheets tools (registros, ranking, resumen) que el LLM consulta segun la intencion de la pregunta.
- **Memoria de conversacion** (buffer window) para dar seguimiento al hilo.
- **Guard de acceso**: valida el chat ID antes de procesar — nadie mas puede usar tu bot ni gastar tus tokens.
- **Prompt disciplinado**: respuestas de max 80 palabras, cada afirmacion con numero, sin inventar datos (consulta tools siempre).

**Setup**: credenciales de Telegram Bot + OpenAI + Google Sheets OAuth, reemplaza TU_SPREADSHEET_ID y tu chat ID en el guard.

### 3. Instagram Metrics → Google Sheets

[⬇️ Descargar JSON](./instagram-metrics-to-sheets.json)

Pipeline programado que trae las metricas de una cuenta de Instagram Business (Graph API) y las guarda en Google Sheets, listo para alimentar dashboards o agentes como el anterior.

- **Schedule diario** → Graph API (likes, comentarios, tipo, fecha) → transformacion → Sheets.
- **Idempotente**: appendOrUpdate con matching por id — corre mil veces sin duplicar filas.
- **Seguro**: token de Meta via credencial Header Auth, nunca en la URL.

**Setup**: reemplaza TU_IG_BUSINESS_ID y TU_SPREADSHEET_ID, crea la credencial Header Auth.

> 💡 Estos dos templates juntos forman el patron **data pipeline + agente**: el pipeline alimenta las hojas y el agente las consulta por chat.

## 🔒 Nota de seguridad

Al exportar workflows de n8n, las credenciales guardadas NO se exportan — pero **cualquier token hardcodeado en URLs o headers SÍ**. Antes de compartir un export, búscalo con: `grep -iE "bearer |access_token|api_key" workflow.json`

## Autor

**Axel Mestanza** — [LinkedIn](https://www.linkedin.com/in/axel-mestanza-920a071b9/) · [Perfil](https://github.com/axelsmith11)
