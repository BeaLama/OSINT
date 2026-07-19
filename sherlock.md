# Manual de Sherlock desde Cero

## Qué es Sherlock

Sherlock es una potente herramienta de línea de comandos escrita en Python. Su función principal es la enumeración de cuentas. Al introducir un nombre de usuario ("username"), el script realiza peticiones rápidas a cientos de plataformas (foros, redes sociales, repositorios de código, blogs) para comprobar en cuáles de ellas existe una cuenta registrada con ese identificador exacto.

Es ideal para:
* Investigaciones de huella digital propia o autorizada.
* Casos de suplantación de identidad.
* Recopilación de información (reconocimiento) en auditorías de seguridad.

---

## Configuración del Entorno en VS Code

Sigue estos pasos en la terminal integrada de VS Code para descargar el proyecto y preparar su espacio aislado de trabajo.

### Descargar el código original
* Clona el repositorio oficial de GitHub y accede a la carpeta que se creará de forma automática:
```bash
  git clone https://github.com/sherlock-project/sherlock.git
  cd sherlock
```

### 2. Crear y activar el entorno virtual (venv)

```bash
# Crear el entorno
python3 -m venv venv

# Activar - Windows (PowerShell)
venv\Scripts\Activate.ps1

# Activar - macOS / Linux
source venv/bin/activate
```

### 3. Instalar dependencias

```bash
python3 -m pip install .
```

## 3. Uso de comandos

```bash
# Búsqueda básica de un usuario (ej: 'usuario')
sherlock usuario

# Buscar múltiples usuarios a la vez
sherlock usuario1 usuario2 usuario3

# Buscar y guardar solo los resultados positivos en un archivo .txt
sherlock usuario --print-found --output resultados.txt

# Buscar limitando el tiempo de espera y solo en redes específicas
sherlock usuario --timeout 10 --site Instagram --site Twitter
```
