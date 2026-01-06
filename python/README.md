# Python 3.14 Development Environment 🐍

Este repositorio contiene un entorno de desarrollo contenerizado y optimizado para **Python 3.14** , utilizando herramientas modernas de gestión de paquetes y calidad de código.

## 🚀 Tecnologías

*   **Motor de Contenedores:** Podman (compatible con Docker)
*   **Gestor de Paquetes:** [uv](https://github.com/astral-sh/uv) (extremadamente rápido)
*   **Linter & Formatter:** [Ruff](https://github.com/astral-sh/ruff)
*   **Hooks de Git:** [Pre-commit](https://pre-commit.com/)

## 📋 Requisitos Previos

*   **Podman** y **podman-compose** instalados en tu sistema.
*   **Git**.

## 🛠️ Estructura del Proyecto

```text
.
├── src/                 # Código fuente de tu aplicación
├── Dockerfile           # Definición de la imagen (Python 3.14-slim)
├── compose.yaml         # Orquestación del contenedor
├── entrypoint.sh        # Script de inicio (instala pre-commit automáticamente)
├── pyproject.toml       # Definición de dependencias y configuración (uv/ruff)
├── ruff.toml            # Configuración específica de linter (opcional)
├── .pre-commit-config.yaml # Configuración de hooks de git
└── README.md            
```


## ⚡ Inicio Rápido

1.  **Clona el repositorio:** Copia esta carpeta en .zip

2. **Preparación de dependencias**
    Antes de construir la imagen, es necesario generar el archivo de bloqueo para asegurar versiones consistentes:

    ```bash
    uv lock
    ```

3. **Permisos de ejecución (Crítico)**
    Para que el contenedor pueda iniciar correctamente en Linux, los scripts de entrada deben tener permisos de ejecución en el host:

    ```bash
    chmod +x entrypoint.sh
    ```
4.  **Levanta el entorno:**
    Este comando construye la imagen e inicia el contenedor en segundo plano.
    ```bash
    podman-compose up -d --build
    ```
    > *Nota: Al iniciar, el contenedor configurará automáticamente los hooks de pre-commit en tu carpeta `.git` local.*

5.  **Accede al contenedor:**
    Para ejecutar comandos, entra en la terminal del contenedor:
    ```bash
    podman exec -it python_dev_env /bin/bash
    ```

## 📦 Gestión de Dependencias (uv)

Este proyecto usa `uv` en lugar de pip. Todos los comandos se ejecutan **dentro del contenedor**.

*   **Añadir una librería (ej. requests):**
    ```bash
    uv add requests
    ```
    *Esto actualiza `pyproject.toml` y `uv.lock` automáticamente.*

*   **Añadir una dependencia de desarrollo (ej. pytest):**
    ```bash
    uv add --dev pytest
    ```

*   **Sincronizar entorno (si cambiaste el toml manualmente):**
    ```bash
    uv sync
    ```

esta parte: "## 🛡️ Calidad de Código (Ruff & Pre-commit)

El proyecto está protegido para asegurar que no se suba código con errores o mal formato.

### Pre-commit (Automático)
Gracias al `entrypoint.sh`, los hooks se instalan solos. Cada vez que hagas un `git commit`, se ejecutarán las comprobaciones.

Si quieres ejecutarlos manualmente en todos los archivos:
```bash
# Dentro del contenedor
uv run pre-commit run --all-files
```
### Ruff (Manual)
Puedes ejecutar el linter y formatter directamente:

*   **Revisar errores:**
    ```bash
    ruff check .
    ```
*   **Corregir errores automáticamente:**
    ```bash
    ruff check . --fix
    ```
*   **Formatear código:**
    ```bash
    ruff format .
    ```

## 🐛 Troubleshooting / Notas de Podman

### Permisos de Archivos (Linux/SELinux)
El archivo `compose.yaml` utiliza la opción `:z` en los volúmenes para compatibilidad con SELinux. Además, se usa `userns_mode: keep-id` para que los archivos creados dentro del contenedor (como `uv.lock` o carpetas `__pycache__`) pertenezcan a tu usuario local y no a root.

### Reconstruir el entorno
Si modificas el `Dockerfile` o el `entrypoint.sh`, es necesario reconstruir:
```bash
podman-compose down
podman-compose up -d --build
```

*Happy Coding!* 🐍
```
