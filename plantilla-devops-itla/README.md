# Práctica 3 — Pipeline CI/CD con GitHub Actions (ITLA)

Aplicación web de lista de tareas desarrollada con Node.js y Express para demostrar integración continua (CI) y despliegue continuo (CD) utilizando herramientas gratuitas de GitHub.

## 1. Objetivo

Automatizar la revisión estática, las pruebas automatizadas, la generación de cobertura y la construcción de una imagen Docker. Cuando los cambios llegan a `main` y superan las verificaciones, GitHub Actions publica el frontend estático en GitHub Pages.

## 2. Tecnologías

| Tecnología | Uso |
|---|---|
| Node.js 20+ | Entorno de ejecución |
| Express 4 | API REST |
| Jest | Pruebas automatizadas |
| Supertest | Pruebas de integración HTTP |
| Docker | Empaquetado de la API |
| GitHub Actions | Automatización CI/CD |
| GitHub Pages | Publicación del frontend estático |

## 3. Estructura del proyecto

```text
.
├── .github/workflows/ci.yml  # Pipeline CI/CD
├── docs/
│   └── PIPELINE.md           # Explicación del pipeline
├── public/
│   └── index.html            # Frontend estático
├── src/
│   ├── app.js                # API y lógica de negocio
│   └── server.js             # Inicio del servidor
├── tests/
│   └── app.test.js           # Pruebas Jest + Supertest
├── CONTRIBUTING.md           # Guía de contribución
├── Dockerfile                # Imagen de la API
└── package.json
```

## 4. Requisitos

- GitHub y un repositorio **público**.
- Node.js 20 o superior y npm para ejecución local; en GitHub Actions se configura automáticamente Node.js 20.
- Docker para construir y ejecutar el contenedor localmente (opcional si solo se usa el runner de GitHub Actions).

## 5. Instalación y ejecución

```bash
npm install
npm run lint
npm test
npm start
```

La API estará disponible en `http://localhost:3000`.

### Ejecutar con Docker

```bash
docker build -t devops-itla .
docker run --rm -p 3000:3000 devops-itla
```

Comprobar salud: `http://localhost:3000/health`.

## 6. Endpoints de la API

| Método | Ruta | Descripción | Respuesta esperada |
|---|---|---|---|
| GET | `/health` | Estado del servicio | 200 |
| GET | `/metrics` | Solicitudes, errores, tareas y tiempo activo | 200 |
| GET | `/api/tareas` | Listar tareas | 200 |
| POST | `/api/tareas` | Crear tarea | 201; 400 si el título no es válido |
| PATCH | `/api/tareas/:id` | Alternar estado completada | 200; 404 si no existe |
| DELETE | `/api/tareas/:id` | Eliminar tarea | 204; 404 si no existe |

Los títulos deben ser texto no vacío y no superar 100 caracteres. Los datos de la API se conservan en memoria mientras el proceso está activo.

## 7. Pruebas automatizadas

Ejecutar `npm test`. Jest ejecuta pruebas de integración HTTP con Supertest y genera un reporte de cobertura en `coverage/`.

La suite cubre:
- Estado del servicio.
- Creación de tareas y limpieza de espacios.
- Rechazo de títulos vacíos, no textuales y mayores de 100 caracteres.
- Cambio del estado de una tarea.
- Respuestas 404 para tareas inexistentes.
- Eliminación de tareas y respuesta HTTP 204.
- Unicidad de identificadores después de eliminar registros.
- Métricas básicas.

## 8. Pipeline CI/CD

El workflow se encuentra en `.github/workflows/ci.yml` y se activa con `push` o `pull_request` hacia `main`.

**Integración continua (CI):** checkout → configuración de Node.js → instalación → revisión de sintaxis → pruebas y cobertura → construcción Docker.

**Despliegue continuo (CD):** después de completar CI, un `push` a `main` publica `public/` en GitHub Pages. Los Pull Requests ejecutan CI, pero no publican el sitio.

Consulta [docs/PIPELINE.md](docs/PIPELINE.md) para el detalle de cada etapa.

## 9. Publicar en GitHub Pages

1. Crear un repositorio público vacío en GitHub.
2. Subir el contenido de este proyecto a la rama `main`.
3. Abrir **Settings → Pages** y seleccionar **GitHub Actions** como fuente.
4. Abrir **Actions** y esperar que el workflow finalice correctamente.
5. Consultar la URL publicada en el job **Despliegue a GitHub Pages**.

**Nota importante:** GitHub Pages solo aloja archivos estáticos; no ejecuta Express. Por esa razón, la página detecta que no existe una API disponible y utiliza `localStorage` para permitir la demostración del frontend. Para probar la API completa, iniciar el servidor con `npm start` o utilizar Docker.

## 10. Evidencia de prueba fallida y recuperación

Para demostrar el control de calidad del pipeline en la presentación:

1. En una rama de demostración, modificar temporalmente una expectativa de una prueba para que sea incorrecta (por ejemplo, esperar HTTP 201 en un caso que devuelve 400).
2. Confirmar y subir el cambio a GitHub.
3. Abrir **Actions** y guardar una captura donde el job de pruebas aparezca fallido.
4. Restaurar la expectativa correcta, confirmar y subir el cambio.
5. Guardar una captura de la ejecución exitosa.

No se debe dejar el error intencional en `main`. Las capturas deben corresponder a ejecuciones reales del repositorio.

## 11. Guía de contribución

Consultar [CONTRIBUTING.md](CONTRIBUTING.md). Todo cambio debe incluir pruebas cuando corresponda y superar `npm run lint` y `npm test` antes de integrarse a `main`.

## 12. Limitaciones y mejoras futuras

- La API utiliza almacenamiento en memoria; una mejora sería incorporar SQLite o PostgreSQL.
- El workflow construye la imagen Docker, pero no la publica en un registry.
- El análisis estático utiliza `node --check`; puede ampliarse con ESLint.
- La cobertura se genera y conserva como artefacto; un siguiente paso sería establecer umbrales mínimos.

## 13. Autoría

**Estudiante:** Brayan Beltre  
**Institución:** Instituto Tecnológico de las Américas (ITLA)  
**Asignatura:** Electiva 1 — Introducción a DevOps  
**Actividad:** Práctica 3 — Implementación de pipeline CI/CD
