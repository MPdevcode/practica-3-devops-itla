# Informe de implementación — Práctica 3

## 1. Introducción

Este informe documenta la implementación de un pipeline básico de integración continua y despliegue continuo (CI/CD) para una aplicación web de gestión de tareas. La solución utiliza GitHub Actions para automatizar controles de calidad y GitHub Pages para publicar el frontend estático, sin requerir servicios de pago ni Azure DevOps.

## 2. Objetivo

Configurar un proceso automatizado que valide los cambios del proyecto mediante revisión de sintaxis, pruebas automatizadas con cobertura y construcción de una imagen Docker. Además, se configura una etapa de publicación condicionada a que las verificaciones sean exitosas y el cambio llegue a la rama principal.

## 3. Descripción de la solución

La aplicación está construida con Node.js y Express. Expone endpoints para consultar el estado del servicio, revisar métricas y administrar tareas. El frontend se encuentra en `public/index.html`. Para la práctica se incorporó la operación de eliminación de tareas mediante `DELETE /api/tareas/:id`, validación de títulos de hasta 100 caracteres y pruebas de integración adicionales.

La API mantiene los datos en memoria. El frontend publicado en GitHub Pages funciona en modo demostración y almacena sus tareas en `localStorage`, ya que GitHub Pages no ejecuta aplicaciones backend.

## 4. Implementación de pruebas

Se utiliza Jest como framework de pruebas y Supertest para realizar solicitudes HTTP a la aplicación sin iniciar un puerto externo. Las pruebas verifican respuestas exitosas, validaciones de entrada, recursos inexistentes, actualización de estado, eliminación y métricas. El comando `npm test` genera el reporte de cobertura.

## 5. Implementación del pipeline

El workflow `.github/workflows/ci.yml` se activa ante `push` y `pull_request` dirigidos a `main`. El job de validación descarga el repositorio, configura Node.js 20, instala dependencias, verifica sintaxis, ejecuta pruebas y construye la imagen Docker. El reporte de cobertura se conserva como artefacto de GitHub Actions.

El job de despliegue depende del éxito del job anterior. Solo se ejecuta cuando el evento es un `push` a `main`; prepara el directorio `public/` y lo publica en GitHub Pages.

## 6. Prueba controlada de fallo

Como evidencia del comportamiento del pipeline, se debe introducir temporalmente una expectativa incorrecta en una prueba dentro de una rama de demostración. Al subirla, la etapa de pruebas debe fallar y el workflow debe reflejar el estado fallido. Después se revierte el cambio, se sube la corrección y se verifica una ejecución exitosa. Esta evidencia se completa con capturas reales de GitHub Actions.

## 7. Resultados y evidencias pendientes

La implementación incluye la nueva ruta DELETE, validación de longitud de títulos, pruebas adicionales, documentación técnica y workflow automatizado. Para cerrar la entrega se deben ejecutar las pruebas en GitHub Actions, confirmar la construcción Docker, habilitar GitHub Pages y adjuntar las capturas reales de las ejecuciones exitosa y fallida, además de la URL pública del repositorio y del sitio.

## 8. Conclusión

La práctica demuestra cómo automatizar controles de calidad y publicación mediante GitHub Actions. Separar validación y despliegue permite detectar errores antes de publicar. La solución utiliza herramientas gratuitas y deja una base extensible para incorporar persistencia de datos, análisis estático avanzado, publicación de imágenes y monitoreo más completo.
