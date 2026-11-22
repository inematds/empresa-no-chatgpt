# Empresa en ChatGPT

[![Empresa en ChatGPT](guia/assets/banner-es.jpg)](https://inematds.github.io/empresa-no-chatgpt/guia/es/)

**🇧🇷 [Português](README.md) · 🇺🇸 [English](README.en.md) · 🇪🇸 [Español](README.es.md)**

## Qué es

**Empresa en ChatGPT** es un kit gratuito para reclamar el perfil de tu empresa en la **Central de Negocios de ChatGPT** (el “Business Hub”, lanzado por OpenAI en noviembre de 2026), similar al perfil de empresa de Google. Está pensado para quienes tienen una tienda, consultorio, oficina o venden por internet. Incluye una prueba para ver qué dice ChatGPT sobre ti hoy, una ficha para completar, consejos sobre lo que nos salió mal y una solicitud lista para que un agente de IA haga el registro en tu navegador. Solo necesitas una cuenta de ChatGPT, incluso la gratuita.

**Puedes reclamar tu empresa en ChatGPT.** Hoy, lo que aparece sobre ella depende de lo que ChatGPT encuentre por ahí. Cuando buscamos “INEMA”, mostró el instituto ambiental de Bahía, con 6 direcciones. Ninguna era la nuestra.

## 📖 Guía de uso

Guía completa (página de presentación + paso a paso, con capturas reales): **https://inematds.github.io/empresa-no-chatgpt/guia/es/**

## Los 3 pasos

1. **Encuentra tu empresa:** `chatgpt.com/business-hub` → busca el nombre corto y el completo → elige la que corresponda a tu dirección o selecciona **Crear**.
2. **Demuestra que es tuya:** establecimiento físico = código al teléfono comercial; tienda online = un registro TXT en el DNS del sitio (también hay opciones por correo electrónico de la empresa y Shopify).
3. **Indica quién eres:** categoría, descripción de hasta 300 caracteres, entrega, devoluciones → identidad (documento + selfie) → invita a tu equipo.

## Pídele al agente que lo haga (la solicitud)

Pégala en Claude Code con la extensión Claude in Chrome (usa tu navegador, donde ya tienes la sesión iniciada). La solicitud completa y lo que hizo el agente en nuestro caso están en [`automatico/PEDIDO-AGENTE.md`](automatico/PEDIDO-AGENTE.md) (en portugués).

```text
Quiero registrar mi empresa en ChatGPT (Central de Negocios, chatgpt.com/business-hub).
Lee el kit en https://github.com/inematds/empresa-no-chatgpt (los consejos y la ficha del perfil).
Datos: nombre <NOMBRE>, <establecimiento físico o tienda online>, sitio web <SITIO>, dirección <DIRECCIÓN>, a qué nos dedicamos <2 o 3 frases>.
Usa mi navegador con la sesión iniciada en la cuenta de la EMPRESA. Primero, haz la prueba del kit. La descripción debe tener un máximo de 300 caracteres.
Detente y pregúntame antes de aceptar términos, modificar el DNS, publicar el teléfono o el correo electrónico y enviar.
Yo me encargo del código telefónico, el documento y la selfie. Al final, capturas de pantalla y lo que falte.
```

## El kit

| Archivo | Para qué sirve |
|---|---|
| [`kit/1-teste-o-que-o-chatgpt-diz.md`](kit/1-teste-o-que-o-chatgpt-diz.md) (en portugués) | 8 preguntas para ver qué dice ChatGPT sobre ti + ficha antes/después |
| [`kit/2-ficha-do-perfil.md`](kit/2-ficha-do-perfil.md) (en portugués) | Todos los campos que solicita el registro + modelo de descripción de ≤ 300 caracteres |
| [`kit/3-dicas.md`](kit/3-dicas.md) (en portugués) | 19 consejos de nuestro registro real (qué salió mal y cómo evitarlo) |
| [`automatico/PEDIDO-AGENTE.md`](automatico/PEDIDO-AGENTE.md) (en portugués) | Cómo el agente hizo el registro por su cuenta y la solicitud para copiar |
| [`kit/conferir_perfil.py`](kit/conferir_perfil.py) | Revisa la ficha: descripción de ≤ 300 caracteres y que el nombre, sitio web y teléfono coincidan en todas partes |
| [`automatico/txt_cloudflare.py`](automatico/txt_cloudflare.py) | Crea el registro TXT en Cloudflare y espera a que aparezca en el DNS público |
| [`automatico/conferir_txt.sh`](automatico/conferir_txt.sh) | Comprueba si el TXT ya aparece (cualquier proveedor de DNS) |

## La prueba (22/11/2026)

- **Antes:** “INEMATDS” → ninguna empresa; “INEMA” → instituto ambiental de Bahía, 6 direcciones.
- **Después:** el agente creó INEMATDS LTDA en ~10 minutos, como tienda online; **inema.club verificado** por DNS, categoría “Empresa de IA”. Estado: borrador hasta que el propietario verifique su identidad.
- Fuente de la función: [OpenAI, “Empresas en ChatGPT”](https://openai.com/pt-BR/index/introducing-businesses-chatgpt/) (beta; las reglas pueden cambiar).

---

## Instalación técnica

Requisitos: Python 3.10+, `dig` (paquete `dnsutils`/`bind-utils`) para comprobar el DNS. Para crear el TXT con el script: dominio en Cloudflare y un token con permiso *Zone → DNS → Edit*. Sin dependencias externas.

```bash
git clone https://github.com/inematds/empresa-no-chatgpt && cd empresa-no-chatgpt

# revisar la ficha antes del registro
python3 kit/conferir_perfil.py kit/exemplo-ficha.json          # copia y edita el ejemplo

# crear el TXT de verificación en Cloudflare (el token solo va en el entorno, nunca en el código)
export CLOUDFLARE_API_TOKEN=...
python3 automatico/txt_cloudflare.py --dominio seusite.com.br \
  --valor "openai-business-profile=ungi_..." --dry-run          # muestra lo que hará
python3 automatico/txt_cloudflare.py --dominio seusite.com.br \
  --valor "openai-business-profile=ungi_..."                    # crea el registro y espera a que aparezca

# comprobar el TXT con cualquier proveedor
bash automatico/conferir_txt.sh seusite.com.br

# pruebas (10 casos: límite de 300, discrepancias en sitio/teléfono, tipo local sin dirección, nombre del TXT, dry-run)
python3 -m unittest discover -s tests
```

`txt_cloudflare.py` termina con código 0 cuando el TXT aparece en el DNS público, 1 si hay un error y 2 si lo creó, pero aún no se propagó.

## El proceso, de principio a fin

| # | Paso | Comando / dónde | Quién |
|---|---|---|---|
| 1 | Prueba del “antes” | 8 preguntas de `kit/1-teste-o-que-o-chatgpt-diz.md` en ChatGPT | 🧠 propietario (o el agente) |
| 2 | Ficha del perfil | `python3 kit/conferir_perfil.py minha-ficha.json` | 🧠 propietario |
| 3 | Abrir la Central y buscar | `chatgpt.com/business-hub` en el navegador con la sesión iniciada en la cuenta de la empresa | 🤖 agente (Claude in Chrome) |
| 4 | Crear o elegir la empresa | “Crear '<nombre>'” → Comenzar → establecimiento físico / tienda online | 🤖 agente |
| 5 | Demostrar que es tuya | DNS: `python3 automatico/txt_cloudflare.py …` → “Verificar dominio” · teléfono: código | ⚙️ script (con el visto bueno del propietario) / 🧠 propietario |
| 6 | Categoría, descripción, entrega, devoluciones → Enviar | formulario | 🤖 agente (muestra antes de enviar) |
| 7 | Identidad (Persona: documento + selfie) | botón “Verificar” en la vista general | 🧠 propietario |
| 8 | Invitar al equipo | “Invitar” en la Central | 🧠 propietario |
| 9 | Prueba del “después” (7 días) | las mismas 8 preguntas | 🧠 propietario |

## Uso responsable

Reclama solo una empresa que sea tuya o a la que representes. No publiques un teléfono o correo electrónico que no pueda recibir consultas de clientes. El kit no garantiza una posición en las respuestas de ChatGPT.

Proyectos y cursos gratuitos: https://inema.club · Licencia MIT.