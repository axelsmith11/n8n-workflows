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

## 🔒 Nota de seguridad

Al exportar workflows de n8n, las credenciales guardadas NO se exportan — pero **cualquier token hardcodeado en URLs o headers SÍ**. Antes de compartir un export, búscalo con: `grep -iE "bearer |access_token|api_key" workflow.json`

## Autor

**Axel Mestanza** — [LinkedIn](https://www.linkedin.com/in/axel-mestanza-920a071b9/) · [Perfil](https://github.com/axelsmith11)
