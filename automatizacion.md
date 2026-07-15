# Automatización de Sherlock e IntelX con Inteligencia Artificial (ChatGPT y Claude)

Este manual explica cómo conectar los resultados de tus búsquedas OSINT con las APIs de OpenAI (ChatGPT) y Anthropic (Claude) para generar reportes analíticos automáticos.

---

## Preparación de las API Keys

Para que el script pueda enviarle los datos a las IA, necesitas obtener sus llaves de acceso:

*   **OpenAI (ChatGPT):** Regístrate en [platform.openai.com](https://platform.openai.com), ve a la sección de API Keys, recarga saldo (mínimo 5$) y genera una clave secreta (`sk-...`).
*   **Anthropic (Claude):** Regístrate en [console.anthropic.com](https://console.anthropic.com), ve a API Keys, añade algo de saldo y genera tu clave (`sk-ant-...`).

---

## Configuración del Entorno en VS Code

Instalaremos las librerías oficiales de ambas inteligencias artificiales para poder invocarlas mediante código.

### Activar el entorno virtual
*   Asegúrate de estar en la carpeta de tu proyecto (por ejemplo, `intelx-project` o una carpeta nueva de automatización) y activa tu entorno:
    ```bash
    # En macOS / Linux:
    source venv/bin/activate

    # En Windows (PowerShell):
    venv\Scripts\Activate.ps1
    ```

### Instalar las librerías necesarias
*   Instala los paquetes de OpenAI, Anthropic y utilidades para manejar variables de entorno de forma segura:
    ```bash
    pip install openai anthropic python-dotenv
    ```

---

## Automatización 1: Analizar resultados de Sherlock con ChatGPT

Este script lee un archivo de texto generado por Sherlock (por ejemplo, `reporte_usuario.txt`), se lo envía a ChatGPT y le pide que identifique posibles patrones, correlaciones y cree un perfil del objetivo.

### Crear el archivo de configuración `.env`
Para no exponer tus claves en el código, crea un archivo llamado `.env` en tu carpeta y pega esto:
```text
OPENAI_API_KEY=tu_clave_de_openai_aqui
```

## Crear el script para Sherlock

```bash
import os
from openai import OpenAI
from dotenv import load_dotenv

# Cargar las API keys desde el archivo .env
load_dotenv()

# Inicializar cliente de OpenAI
client = OpenAI(api_key=os.getenv("OPENAI_API_KEY"))

# Archivo de entrada (el reporte que generó Sherlock previamente)
archivo_sherlock = "reporte_usuario.txt"

if not os.path.exists(archivo_sherlock):
    print(f"[-] Error: No se encuentra el archivo {archivo_sherlock}. ¡Ejecuta Sherlock primero!")
    exit()

# Leer el reporte de Sherlock
with open(archivo_sherlock, "r", encoding="utf-8") as f:
    datos_osint = f.read()

# Definir las instrucciones para la IA
prompt_sistema = (
    "Eres un analista de ciberseguridad y experto en OSINT. "
    "Tu tarea es analizar una lista de perfiles encontrados en la web por la herramienta Sherlock. "
    "Identifica qué tipo de plataformas usa el objetivo (foros, redes de desarrollo, ocio) "
    "y redacta un reporte estructurado de su huella digital con conclusiones de seguridad."
)

try:
    print("[*] Enviando datos de Sherlock a ChatGPT para su análisis...")
    
    # Llamada a la API de OpenAI (usando gpt-4o-mini por coste/velocidad)
    respuesta = client.chat.completions.create(
        model="gpt-4o-mini",
        messages=[
            {"role": "system", "content": prompt_sistema},
            {"role": "user", "content": f"Aquí tienes los datos recopilados por Sherlock:\n\n{datos_osint}"}
        ],
        temperature=0.3
    )
    
    # Extraer el reporte redactado
    reporte = respuesta.choices[0].message.content
    
    # Guardar el reporte analítico final
    with open("informe_analitico_sherlock.md", "w", encoding="utf-8") as f_out:
        f_out.write(reporte)
        
    print("[+] ¡Informe de IA generado con éxito en 'informe_analitico_sherlock.md'!")

except Exception as e:
    print(f"[-] Error al conectar con OpenAI: {e}")

```

## Automatización 2: Analizar resultados de IntelligenceX con Claude

### Crear el archivo de configuración `.env`
Para no exponer tus claves en el código, crea un archivo llamado `.env` en tu carpeta y pega esto:
```text
ANTHROPIC_API_KEY=tu_clave_de_claude_aqui
```

## Crear el script para Intelligence X

```bash

import os
import time
from anthropic import Anthropic
from intelxapi import intelx
from dotenv import load_dotenv

# Cargar las API keys
load_dotenv()

# Inicializar clientes
client_claude = Anthropic(api_key=os.getenv("ANTHROPIC_API_KEY"))
intel = intelx(api_key=os.getenv("INTELX_API_KEY_AQUI_O_EN_ENV")) 

OBJETIVO = "ejemplo@dominio.com"

try:
    # 1. Obtener datos crudos de IntelX
    print(f"[*] Buscando '{OBJETIVO}' en Intelligence X...")
    busqueda = intel.search(OBJETIVO, maxresults=15)
    search_id = busqueda["id"]
    time.sleep(3)
    resultados_raw = intel.search_result(search_id)
    
    # Convertir el JSON de resultados a una cadena de texto para enviarlo
    datos_sucios = str(resultados_raw)
    
    # 2. Enviar a Claude para que limpie y analice
    print("[*] Enviando volcados a Claude para análisis de riesgo...")
    
    # Usando claude-3-5-haiku (el modelo rápido y potente para tareas de datos)
    mensaje = client_claude.messages.create(
        model="claude-3-5-haiku-20241022",
        max_tokens=2000,
        temperature=0.2,
        system=(
            "Eres un experto analista de fugas de información. "
            "Se te proporcionará un volcado JSON crudo de Intelligence X con menciones a un correo. "
            "Analiza las menciones. Determina si el objetivo ha sido comprometido en filtraciones graves. "
            "Evalúa el nivel de riesgo global (Bajo, Medio, Alto) y propón medidas de mitigación inmediatas."
        ),
        messages=[
            {"role": "user", "content": f"Datos a analizar:\n\n{datos_sucios}"}
        ]
    )
    
    # 3. Guardar el informe de Claude
    reporte_claude = mensaje.content[0].text
    with open("analisis_riesgo_intelx.md", "w", encoding="utf-8") as f:
        f.write(reporte_claude)
        
    print("[+] ¡Análisis de riesgos completado y guardado en 'analisis_riesgo_intelx.md'!")
    
except Exception as e:
    print(f"[-] Ocurrió un error en la automatización: {e}")
```

## Ejecución de las Automatizaciones

```bash
# Para el análisis inteligente de Sherlock:
python3 analizar_sherlock.py

# Para el análisis inteligente de IntelX:
python3 analizar_intelx.py
