# Bitacora

## 2026-10-13 - Sesion 1: GitHub y Markdown

**Objetivo:** crear el repositorio individual y publicar la documentacion inicial en Markdown.

**Acciones realizadas:**
- Creada la estructura local del proyecto (`README.md`, `docs/`, `scripts/`, `ansible/`, `evidencias/`, `.gitignore`).
- Redactados README, ficha inicial, memoria tecnica inicial y esta bitacora.
- Configurados `user.name` y `user.email` (correo privado noreply de GitHub) solo para este repositorio.
- Inicializado el repositorio con `git init -b main`, revisado el contenido con `git diff --cached` y creado el primer commit.
- Publicado con `git remote add origin ...` y `git push -u origin main`, autenticando por HTTPS con un token personal.

**Pruebas y resultados:**
- `git --version` devuelve 2.43.0, Git esta disponible.
- Resultado esperado: el commit local aparece en GitHub. Resultado obtenido: `git status -sb` muestra `main...origin/main` sin diferencias y `git ls-remote origin main` devuelve `a40c0b3`, el mismo commit local.
- Busqueda de `token`, `password` y `ghp_` en los `.md`: sin coincidencias.
- Pendiente: comprobar en el navegador el README renderizado y los enlaces relativos.

**Decisiones y motivos:**
- Modalidad de Proxmox pendiente de decidir hasta inventariar el hardware.
- Autenticacion por HTTPS con token en lugar de SSH, por ser el metodo mas rapido de configurar en esta sesion.
- Correo noreply de GitHub para no publicar el correo personal en el historial.

**Incidencias:**
- No se dispuso de `crear_estructura_entrega.sh`; la estructura se creo manualmente con el mismo contenido. Estado: resuelta.
- El repositorio es publico y la guia recomienda privado por defecto. Estado: abierta, falta decidir la visibilidad y dar acceso a la cuenta docente si pasa a privado.

**Siguiente paso:** verificar el README en GitHub, fijar la visibilidad del repositorio, entregar la URL en Aules e inventariar el hardware disponible para decidir la modalidad de Proxmox.
