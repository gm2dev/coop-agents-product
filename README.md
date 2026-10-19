# coop-agents-product

Repositorio de documentación de producto: visión, goals, PRDs, casos de uso, requerimientos y registros de decisión. Lo mantiene el PO; la visión, los goals y las prioridades globales los decide el CPO.

## Convención: la documentación está en HTML

Toda la documentación de producto se escribe en archivos `.html` autocontenidos (sin dependencias externas). Las únicas excepciones en Markdown son este `README.md` y los archivos de `.agents/`.

**Punto de entrada: [index.html](index.html)**, índice navegable de todos los documentos.

## Cómo visualizar los documentos

GitHub muestra el código fuente de los `.html`, no los renderiza. Para leerlos:

- **Preview por PR (Vercel):** el repo está conectado a Vercel como sitio estático (framework "Other", sin build, raíz del repo como salida). Cada push a una rama o PR genera un preview deployment; el link aparece en el check de Vercel del PR (`gh pr checks <n> --repo gm2dev/coop-agents-product`). `main` es producción.
- **Localmente:** cloná el repo y abrí `index.html` en el navegador. Los links entre documentos son relativos y funcionan sin servidor.
