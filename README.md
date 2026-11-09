# Práctica 3 — Implementación de pipeline CI/CD

**Institución:** Instituto Tecnológico de las Américas (ITLA)  
**Asignatura:** Electiva 1 — Introducción a DevOps  
**Estudiante:** Brayan Beltre  
**Actividad:** Práctica 3 — Pipeline de integración y despliegue continuo

## 1. Descripción del proyecto

Este repositorio contiene una aplicación web de gestión de tareas y un flujo automatizado de integración continua (CI) y despliegue continuo (CD), implementado con **GitHub Actions**.

El pipeline valida el código, ejecuta pruebas automatizadas, genera un reporte de cobertura, construye una imagen Docker y, cuando los controles terminan correctamente en un envío a la rama `main`, publica el frontend estático mediante **GitHub Pages**.

## 2. Tecnologías utilizadas

| Tecnología | Función |
|---|---|
| Node.js 20 | Entorno de ejecución de la aplicación y de las verificaciones |
| Express 4 | Desarrollo de la API REST |
| Jest | Ejecución de pruebas automatizadas |
| Supertest | Pruebas HTTP de la API |
| Docker | Construcción de una imagen de la aplicación |
| GitHub Actions | Automatización del pipeline CI/CD |
| GitHub Pages | Publicación del frontend estático |

## 3. Estructura del repositorio

```text
.
├── .github/
│   └── workflows/
│       └── ci.yml                 # Workflow CI/CD ejecutado por GitHub Actions
├── README.md                     # Documentación principal del repositorio
└── plantilla-devops-itla/
    ├── Dockerfile                 # Instrucciones para construir la imagen Docker
    ├── README.md                  # Documentación de la aplicación
    ├── docs/
    │   ├── PIPELINE.md            # Descripción detallada del flujo CI/CD
    │   └── INFORME_IMPLEMENTACION.md
    ├── package.json               # Dependencias y comandos del proyecto
    ├── public/
    │   └── index.html              # Interfaz web estática
    ├── src/
    │   ├── app.js                  # API y lógica de tareas
    │   └── server.js               # Inicio del servidor
    └── tests/
        └── app.test.js             # Pruebas automatizadas
```

## 4. Funcionamiento del pipeline

El workflow está definido en `.github/workflows/ci.yml` y se activa cuando se realiza un `push` o se abre/actualiza un Pull Request dirigido a la rama `main`.

### Integración continua (CI)

El job **Validación, pruebas y Docker** realiza las siguientes actividades:

1. Descarga el código del repositorio mediante `actions/checkout@v4`.
2. Configura Node.js 20 mediante `actions/setup-node@v4`.
3. Instala las dependencias con `npm install`.
4. Revisa la sintaxis del código con `npm run lint`.
5. Ejecuta las pruebas automatizadas y genera cobertura con `npm test` (Jest y Supertest).
6. Construye la imagen Docker con `docker build`.
7. Guarda el reporte de cobertura como artefacto de GitHub Actions durante siete días, cuando se genera.

### Despliegue continuo (CD)

El job **Despliegue a GitHub Pages** depende del éxito del job de CI. Se ejecuta únicamente cuando el evento es un `push` a `main). Prepara el contenido del directorio `plantilla-devops-itla/public` y lo publica mediante las acciones oficiales de GitHub Pages.

Un Pull Request ejecuta las verificaciones de CI, pero no publica el sitio.

## 5. Ejecutar el proyecto localmente

Se requiere Node.js 20 o superior y npm.

Desde la carpeta `plantilla-devops-itla`, ejecutar:

```bash
npm install
npm run lint
npm test
npm start
```

La API se inicia en `http://localhost:3000`.

Para construir la imagen Docker:

```bash
docker build -t devops-itla .
docker run --rm -p 3000:3000 devops-itla
```

La comprobación de salud está disponible en `http://localhost:3000/health`.

## 6. Pruebas y funcionalidades

La API permite consultar su estado y métricas, listar tareas, crear tareas, cambiar su estado y eliminarlas. Las pruebas automatizadas verifican, entre otros casos, la validación de títulos, las respuestas para recursos inexistentes y las operaciones de creación, actualización y eliminación.

Comandos disponibles:

- `npm run lint`: revisión de sintaxis de los archivos principales.
- `npm test`: ejecución de Jest con generación de cobertura.

## 7. Evidencia de ejecución

- **Repositorio:** [practica-3-devops-itla](https://github.com/MPdevcode/practica-3-devops-itla)
- **Ejecución exitosa de GitHub Actions:** [consultar ejecución CI/CD](https://github.com/MPdevcode/practica-3-devops-itla/actions/runs/42558658351)
- **Aplicación publicada:** [abrir GitHub Pages](https://mpdevcode.github.io/practica-3-devops-itla/)

La ejecución enlazada finalizó correctamente: los jobs de validación/pruebas/Docker y de despliegue a GitHub Pages reportaron estado `success`.

## 8. Documentación complementaria

- [Documentación detallada del pipeline](plantilla-devops-itla/docs/PIPELINE.md)
- [Informe de implementación](plantilla-devops-itla/docs/INFORME_IMPLEMENTACION.md)
- [README de la aplicación](plantilla-devops-itla/README.md)

## 9. Consideraciones y limitaciones

GitHub Pages publica contenido estático; no ejecuta el servidor Node.js/Express. Por eso, el sitio publicado permite demostrar la interfaz, mientras que la API debe ejecutarse por separado con Node.js o Docker.

La API utiliza almacenamiento en memoria, por lo que sus datos se pierden cuando el proceso se reinicia. El workflow construye la imagen Docker para validar que puede crearse, pero no la publica en un registro de contenedores.

## 10. Conclusión

La implementación demuestra un flujo básico de CI/CD con herramientas de GitHub. La automatización permite detectar errores mediante verificaciones y pruebas antes de realizar la publicación del frontend. La separación entre validación y despliegue ayuda a mantener un proceso controlado y repetible.
