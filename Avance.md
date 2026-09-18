# Reporte Avance Semana 2 - Módulo Extractor "El Vigía"

## Resumen general

Para este avance se utilizaron las siguientes herramientas:

- **Versión:** Python 3.14.3
- **Terminal (CMD):** Para trabajar con Git (control de versiones)
- **Editor de código:** Visual Studio Code

En este avance, se realizó la configuración de GitHub para la rama del repositorio. Además, se desarrolló un código para extraer datos como fecha, día, calles, avenidas y colonias de las noticias de la página de **El Vigía**, así como otro código para extraer los links del RSS de la página.

## Guía y comandos de Git (paso a paso)

### Paso 1: Configuración inicial

Es necesario instalar Git en el CMD y configurar los datos personales para que cada commit quede registrado.

```bash
git config --global user.name "Tu Nombre"
git config --global user.email "tu_correo@institucional.com"

# Verificación de datos configurados:
git config --list
```

<img width="277" height="271" alt="Captura de pantalla 2026-09-10 182301" src="https://github.com/user-attachments/assets/a0dda104-e73c-4888-925c-116a5107a453" />

### Paso 2: Clonar repositorio

Se descargó el proyecto desde GitHub a la computadora y se crearon los archivos iniciales.

```bash
git clone https://github.com/JaazielArellano/proyectoTraficoEsenada.git
cd proyectoTraficoEsenada

# Crear los archivos iniciales del repositorio
echo # proyectoTraficoEnsenada > README.MD
echo. > .gitignore
echo > LICENSE
```

<img width="475" height="272" alt="Captura de pantalla 2026-09-10 182457" src="https://github.com/user-attachments/assets/e227b2d8-ac2c-4863-bd70-b7849783000b" />

### Paso 3: Creación de rama

Se creó y se cambió a la rama de trabajo `extractor`.

```bash
# Verificación de archivos dentro de la carpeta:
dir

# Crear y cambiarse a la rama extractor
git checkout -b extractor

# Verificación de la rama activa:
git branch

# Agregar los archivos creados
git add .
```

<img width="616" height="484" alt="Captura de pantalla 2026-09-10 182819" src="https://github.com/user-attachments/assets/5d768ca3-6e0f-461a-88de-0092a55d6848" />

### Paso 4: Control de calidad del código

Antes de subir los archivos al repositorio, se instaló Pylint en el CMD para revisar la calidad del código.

```bash
# Instalación de Pylint
python -m pip install pylint

# Verificación de Pylint
python -m pylint extractor_vigia.py
```

Para subir el archivo, se usaron los siguientes comandos:

```bash
# Preparar el archivos modificado
git add extractor_vigia.py

# Guardar con un comentario
git commit -m "Avance de la función procesar_y_evaluar_dias en extractor_vigia"

# Subir a la rama en GitHub
git push origin extractor
```

## Avance del código

El código para extraer dichos datos está compuesto por las siguientes funciones:

1. **`procesar_y_evaluar_dias()`**: Detecta los días de la semana y los números dentro del texto de la noticia.
2. **`extraer_fecha()`**: Crea las fechas en formato `YYYY-MM-DD`.
3. **`extraer_vias_publicas()`**: Identifica nombres de calles, avenidas y colonias.
4. **`crear_contrato()`**: Une los datos procesados y calcula el porcentaje de confianza.
5. **`main()`**: Se creó para hacer pruebas con noticias para ver si funcionaba el código completo al igual que la función de `crear_contrato()` para saber si arrojaba el porcentaje de confianza correcto en base a los datos encontrados. 

El código completo es el siguiente:

```bash
"""Módulo para extraer datos del portal El Vigía."""

# Importa el módulo json
import json


# Define la función que identifica los días de la semana y sus números
def procesar_y_evaluar_dias(texto):
    """Analizar el texto de una noticia y detectar los días de la semana."""
    # Lista de días a buscar, incluyendo con y sin acento
    dias_semana = [
        "domingo", "lunes", "martes", "miercoles", "miércoles",
        "jueves", "viernes", "sabado", "sábado"
    ]
    # Separa el texto en una lista de palabras individuales
    palabras = texto.split()
    # Diccionario donde se guardará día:número
    diccionario_dias = {}
    # Obtiene la cantidad total de palabras
    texto_size = len(palabras)

    # Recorre cada palabra obteniendo su índice (i) y su valor (palabra)
    for i, palabra in enumerate(palabras):
        # Convierte la palabra a minúsculas y elimina signos de puntuación pegados
        palabra_actual = palabra.lower().strip(",.:;")

        # Comprueba si la palabra es un día válido y si hay una palabra después
        if palabra_actual in dias_semana and i + 1 < texto_size:
            # Obtiene la siguiente palabra y elimina la puntuación
            siguiente_palabra = palabras[i + 1].strip(",.:;")

            # Verifica si la siguiente palabra es un número (ejemplo: "martes 15")
            if siguiente_palabra.isdigit():
                # Convierte el texto del número a un valor entero (int)
                numero_dia = int(siguiente_palabra)

                # Asegura que el número esté en un rango válido de días del mes
                if 1 <= numero_dia <= 31:
                    # Ajusta la ortografía del día si es miércoles
                    if palabra_actual in ["miercoles", "miércoles"]:
                        dia_limpio = "miércoles"
                    # Ajusta la ortografía del día si es sábado
                    elif palabra_actual in ["sabado", "sábado"]:
                        dia_limpio = "sábado"
                    # Mantiene el nombre del día tal cual si no requirió acento
                    else:
                        dia_limpio = palabra_actual

                    # Guarda el resultado en el diccionario
                    diccionario_dias[dia_limpio] = numero_dia

    # Devuelve el diccionario con los días y números
    return diccionario_dias


# Función para extraer una fecha estructurada en formato YYYY-MM-DD
def extraer_fecha(texto):
    """Buscar una fecha utilizando las palabras del texto.

    Ejemplo: martes 15 de septiembre de 2026
    """
    # Lista con los meses del año
    meses = [
        "enero", "febrero", "marzo", "abril", "mayo", "junio",
        "julio", "agosto", "septiembre", "octubre", "noviembre", "diciembre"
    ]
    # Convierte el texto completo a minúsculas y lo divide en palabras
    palabras = texto.lower().split()
    # Guarda la cantidad total de palabras procesadas
    total_palabras = len(palabras)

    # Recorre la lista de palabras buscando su posición exacta
    for i, palabra in enumerate(palabras):
        # Limpia signos de puntuación
        palabra_actual = palabra.strip(",.:;")

        # Evalúa si la palabra es un número y representa un día del 1 al 31
        if palabra_actual.isdigit() and 1 <= int(palabra_actual) <= 31:
            # Convierte la palabra detectada a número entero
            numero_dia = int(palabra_actual)

            # Verifica que existan al menos 4 palabras más adelante para el patrón completo
            if i + 4 < total_palabras:
                # Extrae la primera palabra unión (debería ser "de")
                p_de1 = palabras[i + 1].strip(",.:;")
                # Extrae el posible nombre del mes
                mes = palabras[i + 2].strip(",.:;")
                # Extrae la segunda palabra uníon (debería ser "de")
                p_de2 = palabras[i + 3].strip(",.:;")
                # Extrae el año
                año = palabras[i + 4].strip(",.:;")

                # Confirma que la estructura tenga "de (mes) de"
                if p_de1 == "de" and mes in meses and p_de2 == "de":
                    # Valida que el año sea numérico y tenga 4 dígitos
                    if año.isdigit() and len(año) == 4:
                        # Obtiene el número del mes (1 al 12) basado en su índice
                        numero_mes = meses.index(mes) + 1
                        # Retorna la fecha en formato YYYY-MM-DD
                        return f"{año}-{numero_mes:02d}-{numero_dia:02d}"

    # Retorna None si no se encontró ninguna fecha válida en el texto
    return None


# Función para extraer nombres de calles, avenidas y colonias
def extraer_vias_publicas(texto):
    """Busca palabras clave de vías públicas en el texto y guarda las calles."""
    # Diccionario con listas vacías para cada uno
    diccionario_vias = {
        "calle": [],
        "avenida": [],
        "colonia": []
    }
    # Divide el texto en palabras individuales
    palabras = texto.split()
    # Obtiene la cantidad total de palabras
    total_palabras = len(palabras)

    # Recorre las palabras evaluando cada posición
    for i, palabra in enumerate(palabras):
        # Pasa la palabra a minúsculas y quita la puntuación
        palabra_actual = palabra.lower().strip(",.:;")

        # Comprueba que exista una palabra siguiente para tomar el nombre de la vía
        if i + 1 < total_palabras:
            # Obtiene el nombre de la vía/colonia eliminando puntuación
            siguiente_palabra = palabras[i + 1].strip(",.:;")

            # Si encuentra alguna palabra similar de avenida o bulevar, guarda la siguiente palabra
            if palabra_actual in ["avenida", "av", "bulevar", "blvd"]:
                diccionario_vias["avenida"].append(siguiente_palabra)
            # Si encuentra la palabra calle, guarda el nombre asignado
            elif palabra_actual == "calle":
                diccionario_vias["calle"].append(siguiente_palabra)
            # Si encuentra la palabra colonia o col, la clasifica como tal
            elif palabra_actual in ["colonia", "col"]:
                diccionario_vias["colonia"].append(siguiente_palabra)

    # Devuelve el diccionario con las vías detectadas
    return diccionario_vias


# Función que une la extracción y calcula la confianza
def crear_contrato(texto):
    """Crear el contrato de salida del Extractor."""
    # Ejecuta la extracción de días
    dias = procesar_y_evaluar_dias(texto)
    # Ejecuta la extracción de fecha
    fecha = extraer_fecha(texto)
    # Ejecuta la extracción de vías públicas
    vias = extraer_vias_publicas(texto)

    # Selecciona el primer día encontrado o asigna None si no hubo resultados
    dia = list(dias.keys())[0] if dias else None
    # Selecciona la primera calle encontrada o asigna None
    calle = vias["calle"][0] if vias["calle"] else None
    # Selecciona la primera avenida encontrada o asigna None
    avenida = vias["avenida"][0] if vias["avenida"] else None
    # Selecciona la primera colonia encontrada o asigna None
    colonia = vias["colonia"][0] if vias["colonia"] else None

# Asignación ponderada de confianza
    confidence = 0.50
    if fecha:
        confidence += 0.15
    if dia:
        confidence += 0.10
    if calle or avenida:
        confidence += 0.15
    if colonia:
        confidence += 0.10

    # Retorna el diccionario final estructurado como contrato JSON
    return {
        "date": fecha,
        "day": dia,
        "street": calle,
        "avenue": avenida,
        "neighborhood": colonia,
        "confidence": round(confidence, 2)
    }


# Define la función para probar la ejecución local del script
def main():
    """Función principal de prueba."""
    # Texto de prueba que simula una noticia
    texto_noticia = """
    El martes 15 de septiembre de 2026 se registró un accidente
    sobre Avenida Reforma, en la calle Primera, colonia Centro.
    """
    # Procesa el texto de prueba con la función del contrato
    resultado = crear_contrato(texto_noticia)
    # Imprime el resultado transformado a JSON
    print(json.dumps(resultado, ensure_ascii=False, indent=4))


# Ejecutar main()
if __name__ == "__main__":
    main()
```

### Extracción de links del RSS

Posteriormente, se hizo el código para extraer los links desde el RSS de **El Vigía**, el cual es: `https://www.elvigia.net/rss/feed.html?r=77`

El código guarda la lista de links de noticias recientes en un archivo  `urls.json`.

## Errores y soluciones

Durante el desarrollo de la extracción de enlaces del RSS surgieron los siguientes problemas/errores y sus respectivas soluciones:

- **Error:** La extracción devolvía una lista vacía `[]` al intentar leer la dirección del RSS.
  - **Causa:** El servidor web detectaba las peticiones del programa y bloqueaba la conexión.
  - **Solución:** Se incluyeron encabezados HTTP (`headers`) con un `User-Agent` que simula la lectura desde un navegador web real.
 
## Pendientes

En la clase pasada quedamos en esperar un API por parte del módulo Requests. Los extractores tendremos que subir los links extraídos de nuestras respectivas páginas para que posteriormente nos envíen la información limpia de las noticias de cada página. De ser necesario, una vez que cada quien tenga su información limpia, en caso de que se presenten problemas/errores, tendríamos que modificar el código de extracción.
