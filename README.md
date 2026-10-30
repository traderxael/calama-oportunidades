# Calama Oportunidades

Mapa interactivo y público de Calama (Región de Antofagasta, Chile) que reúne oportunidades laborales, salud, programas sociales, educación, obras públicas y servicios locales, usando únicamente datos verificables desde fuentes oficiales o institucionales. La persona abre el sitio, filtra por categoría, encuentra un punto en el mapa, lee la información esencial y abre la fuente oficial original para confirmarla.

## Política de datos públicos

- Ningún registro se publica sin fuente: cada punto requiere una fuente oficial o institucional (`source_url`) y una fecha de última verificación (`last_verified_at`, formato `YYYY-MM-DD`).
- Se aceptan fuentes oficiales (gobierno, municipalidad), institucionales, o la propia página del servicio mencionado. No se usan rumores, redes sociales ni blogs no verificados como fuente única.
- Categorías permitidas: `empleo`, `salud`, `social`, `educacion`, `obras`, `servicios`. Estados permitidos: `vigente`, `referencia`.
- Los datos viven en `data/places.json`, la única fuente que consume la interfaz; la metodología y las fuentes activas se documentan en `DATA.md`.

## Vista previa local

El sitio es estático y carga los datos con `fetch`, por lo que debe servirse por HTTP (no funciona abriendo `index.html` directamente desde el sistema de archivos). Desde la raíz del repositorio:

```bash
npx serve .
# o bien
python -m http.server 8000
```

Luego abre `http://localhost:3000` (con `serve`) o `http://localhost:8000` (con Python).

## Actualización de datos

1. Edita `data/places.json` agregando o modificando registros según el esquema; cada registro necesita `source_url` y `last_verified_at`, o será rechazado.
2. Valida el dataset antes de publicar: `node scripts/validate-data.mjs` (sale con código `1` si algún registro es inválido).
3. Actualiza `DATA.md` con la fuente consultada, la fecha de verificación y cualquier cambio de metodología.
4. Haz un commit atómico que describa el cambio y empuja a `main`.

Si una fuente deja de estar disponible, el registro se revisa antes de volver a publicarse; nunca se elimina en silencio.

## Despliegue (GitHub Pages)

El sitio se publica con GitHub Pages desde la rama `main`, raíz del repositorio (`/`), y queda disponible en:

https://traderxael.github.io/calama-oportunidades/

Para activarlo: Settings → Pages → Source: Deploy from a branch, rama `main`, carpeta `/ (root)`.

## Licencia

[MIT](LICENSE)
