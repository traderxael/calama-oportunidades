# Diseño: Mapa interactivo de oportunidades de Calama

## Objetivo
Crear un sitio público en GitHub Pages con un mapa interactivo de Calama para encontrar oportunidades laborales, programas y obras sociales, y otro tipo de información local, usando únicamente datos verificables desde fuentes oficiales o institucionales.

## Objetivo de éxito
Una persona puede abrir el sitio, filtrar por categoría, ver un punto en el mapa, leer la información esencial y abrir la fuente oficial original.

## Alcance inicial
### Incluye
- Mapa interactivo centrado en Calama, Región de Antofagasta.
- Marcadores por categorías iniciales:
  - Empleo y OMIL
  - Salud
  - Programas sociales
  - Educación
  - Obras públicas o licitaciones
  - Servicios públicos locales
- Filtros por categoría.
- Panel lateral o ficha con detalles del punto.
- Enlace directo a la fuente oficial.
- Fecha de última verificación por dato.
- Archivo `DATA.md` con la metodología y fuentes activas.
- README con instrucciones de despliegue.

### No incluye por ahora
- Registro o inicio de sesión.
- Publicación de ofertas por usuarios.
- Scraping automático masivo.
- Base de datos en línea.
- Apps nativas.
- Publicidad.

## Enfoque elegido
Sitio estático:

- `HTML` + `CSS` + `JavaScript` vanilla.
- `Leaflet` para el mapa.
- `OpenStreetMap` como mapa base.
- Datos en `data/places.json`.
- Despliegue inicial en `main` mediante GitHub Pages.
- Repositorio público `calama-oportunidades` en la cuenta `traderxael`.

## Alcance de datos del lanzamiento
- 10 a 20 registros verificados.
- Enfoque mixto:
  - Puntos permanentes: oficinas, centros de salud, instituciones y programas estables.
  - Oportunidades específicas: solo si están vigentes y con fuente directa.
  - Obras y licitaciones: preferir enlaces a portales oficiales y registros institucionales, evitando puntos que queden obsoletos sin actualización.
- Ningún registro se publica sin `source_url` y `last_verified_at`.

## Modelo de datos
Cada punto tendrá la estructura:

```json
{
  "id": "string",
  "title": "string",
  "category": "empleo|salud|social|educacion|obras|servicios",
  "description": "string",
  "address": "string | null",
  "lat": number,
  "lng": number,
  "status": "vigente|referencia",
  "source_name": "string",
  "source_url": "URL",
  "last_verified_at": "YYYY-MM-DD"
}
```

Reglas de datos:
1. No se publica ningún dato sin fuente.
2. La fuente debe ser oficial, institucional o una página del servicio mencionado.
3. Cada registro lleva `last_verified_at`.
4. Si una fuente deja de estar disponible, el punto debe revisarse antes de volver a publicarse.
5. No se usan coordenadas estimadas sin que estén marcadas como referencia.

## Fuentes preliminares
- Municipalidad de Calama: https://www.municipalidadcalama.cl/
- Portales públicos de empleo del Estado de Chile.
- Mercado Público para licitaciones y compras públicas.
- Instituciones locales de salud, educación y servicios.

La lista final de fuentes se documentará en `DATA.md` antes de publicar el sitio.

## Experiencia de uso
1. La persona abre el sitio.
2. El mapa aparece centrado en Calama.
3. Puede filtrar por categoría desde la parte superior o lateral.
4. Al hacer clic en un marcador, ve el título, la categoría, la descripción breve, la dirección y la fuente oficial.
5. Puede abrir la fuente en otra pestaña.
6. En móvil, la vista debe poder usarse sin rotar el dispositivo.

## Riesgos y mitigación
| Riesgo | Mitigación |
|---|---|
| Datos desactualizados | Fecha visible de última verificación y proceso de revisión documentado. |
| Datos no oficiales | Solo se aceptan fuentes oficiales o institucionales verificadas. |
| Coordenadas imprecisas | Estado `referencia` cuando la ubicación no sea exacta. |
| Dependencia de mapas de terceros | Leaflet + OpenStreetMap con atribución obligatoria. |
| Repositorio desordenado | Commits atómicos y estructura simple. |

## Próximos pasos tras aprobación
1. Crear repositorio público `calama-oportunidades`.
2. Crear estructura base del sitio.
3. Construir el mapa y los filtros.
4. Cargar solo datos verificados.
5. Documentar fuentes y metodología.
6. Publicar en GitHub Pages.
