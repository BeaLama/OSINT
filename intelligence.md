# Manual de Intelligence X (IntelX) desde Cero

## Qué es Intelligence X

Intelligence X es un motor de búsqueda y archivo que indexa datos de la web pública, la deep web, redes de intercambio de archivos y fugas de datos (data leaks). Permite buscar información histórica y actual mediante selectores como:

* Direcciones de correo electrónico
* Nombres de dominio o subdominios
* Direcciones IP
* Direcciones de billeteras de criptomonedas
* Números de teléfono o identificadores específicos

---

## Obtener la API Key de IntelX

Para realizar consultas automatizadas o mediante scripts, necesitas una clave de acceso:

* Crea una cuenta de usuario en el sitio web oficial: [https://intelx.io](https://intelx.io)
* Dirígete a la sección de configuración de tu perfil de usuario.
* Localiza el apartado **API Key** (en developer )y copia tu identificador único.
* Nota: Algunas cuentas gratuitas o académicas pueden requerir una verificación o solicitud de activación de la API poniéndose en contacto con su soporte técnico.

---

## Configuración del Entorno en VS Code

Prepara una carpeta limpia en tu espacio de trabajo para aislar las librerías del proyecto.

### Crear la estructura de carpetas
* Abre la terminal de VS Code y crea un directorio exclusivo:
  ```bash
  mkdir intelx-proyecto && cd intelx-proyecto
  ```

### Crear entorno virtual

```bash
# Creación de entorno
python3 -m venv venv

#Para macOS / Linux
source venv/bin/activate

#Para Windows
venv\Scripts\Activate.ps1
```

### Instalar librería de API

```bash
pip install intelx
```

## Creación de script de búsqueda estándar

```bash
#Crear archivo llamado busqueda.py
import time
from intelxapi import intelx

# Configura tus credenciales y el objetivo
API_KEY = "TU_API_KEY_AQUI"
OBJETIVO = "ejemplo@dominio.com"

# Inicializar el cliente de Intelligence X
intel = intelx(API_KEY)

try:
    print(f"[*] Iniciando búsqueda en IntelX para: {OBJETIVO}")
    
    # Fase 1: Lanzar la búsqueda para obtener un ID de consulta
    busqueda = intel.search(OBJETIVO, maxresults=100)
    id_busqueda = busqueda["id"]
    print(f"[+] ID de búsqueda generado: {id_busqueda}")
    
    # Fase 2: Espera prudencial para que el sistema procese los datos
    print("[*] Esperando a que el servidor recopile los registros...")
    time.sleep(5)
    
    # Fase 3: Recuperar y mostrar los registros indexados
    resultados = intel.search_result(id_busqueda)
    
    print("\n[+] --- RESULTADOS ENCONTRADOS ---")
    for item in resultados.get("records", []):
        print(f"Tipo: {item.get('type')} | ID del Archivo: {item.get('systemid')} | Fecha: {item.get('date')}")
        
except Exception as e:
    print(f"[-] Ocurrió un error durante la consulta: {e}")
```

## Ejecución del script

```bash
python3 busqueda.py
```
