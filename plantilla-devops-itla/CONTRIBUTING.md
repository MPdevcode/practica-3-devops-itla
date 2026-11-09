# Guía de contribución

Gracias por contribuir al proyecto DevOps ITLA.

## Requisitos
- Node.js 20 o superior.
- npm.
- Git.

## Flujo de trabajo
1. Crea una rama descriptiva desde `main` (`feature/nombre` o `fix/nombre`).
2. Instala dependencias con `npm install`.
3. Implementa el cambio y agrega o actualiza pruebas en `tests/`.
4. Ejecuta `npm run lint` y `npm test` antes de confirmar los cambios.
5. Realiza commits claros y abre un Pull Request hacia `main`.
6. Espera que el workflow CI/CD termine correctamente antes de solicitar la integración.

## Criterios
- Mantener el código sencillo y legible.
- Validar las entradas recibidas por la API.
- No incluir contraseñas, tokens ni secretos en el repositorio.
- Documentar cualquier endpoint o cambio funcional.
