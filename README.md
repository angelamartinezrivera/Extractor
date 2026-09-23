# Módulo Extractor – El Vigía

## Descripción

El módulo Extractor se encarga de procesar la información limpia de las noticias de *El Vigía*, proporcionada por el módulo Requests. La función del módulo es identificar y extraer datos importantes de las noticias, como fechas, días de la semana, calles, avenidas y colonias, para después guardarlos y enviarlos al siguiente módulo **Validación geográfica** en un archivo JSON.

Además, el módulo cuenta con un código para extraer los enlaces de las noticias desde el RSS de *El Vigía* y entregarlos al módulo Requests para que posteriormente se obtenga la información limpia.

## Funciones principales

- `procesar_y_evaluar_dias()`: Detecta los días de la semana y sus números.
- `extraer_fecha()`: Detecta las fechas y las convierte al formato `YYYY-MM-DD`.
- `extraer_vias_publicas()`: Identifica calles, avenidas y colonias.
- `crear_contrato()`: Integra los datos encontrados y genera el resultado en formato JSON.

## Flujo del módulo

RSS de El Vigía → Extracción de enlaces → Requests → Texto limpio → Extractor → JSON → Siguiente módulo

## Contrato de salida

El módulo genera un JSON con los datos extraídos:

```json
{
    "date": "2026-09-15",
    "day": "martes",
    "street": "Primera",
    "avenue": "Reforma",
    "neighborhood": "Centro",
    "confidence": 1.0
}
