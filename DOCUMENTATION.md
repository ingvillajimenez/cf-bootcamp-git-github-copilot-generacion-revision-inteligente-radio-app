# Documentación del Proyecto - Radio App

## 📋 Resumen del Repositorio

Este repositorio contiene una aplicación web desarrollada con Flask que permite explorar y reproducir estaciones de radio en línea de todo el mundo. La aplicación proporciona una interfaz intuitiva para buscar estaciones de radio por país, género musical o etiquetas específicas.

## 🎯 Propósito del Proyecto

El proyecto fue diseñado como parte del bootcamp de Código Facilito para enseñar:
- Desarrollo web con Flask
- Integración con APIs REST externas
- Manipulación de datos JSON
- Renderizado dinámico de plantillas HTML
- Estilización con CSS

## 🏗️ Arquitectura del Proyecto

### Estructura de Archivos

```
radio_app/
│
├── LogicaBackend.py          # Lógica del backend para interactuar con la API
├── app.py                     # Aplicación Flask principal
├── aplicacion_radio.py        # Aplicación completa con documentación extendida
├── requirements.txt           # Dependencias del proyecto
├── respuesta.json            # Ejemplo de respuesta de API (Bolivia)
├── tags.json                 # Ejemplo de respuesta de API (por tags - rock)
│
├── static/
│   └── styles.css            # Estilos CSS de la aplicación
│
└── templates/
    └── index.html            # Plantilla HTML principal
```

## 🌐 API Utilizada: Radio Browser API

### Descripción de la API

La aplicación utiliza la **Radio Browser API** (https://www.radio-browser.info), una API pública y gratuita que proporciona acceso a un extenso catálogo de estaciones de radio en línea de todo el mundo.

### Características de la API

- **Gratuita**: No requiere autenticación ni claves API
- **Distribuida**: Múltiples servidores mirror para alta disponibilidad
- **Datos Ricos**: Información detallada sobre estaciones incluyendo:
  - Nombre de la estación
  - País y ubicación
  - URL del stream de audio
  - Logo/favicon
  - Género musical (tags)
  - Codec y bitrate
  - Idioma
  - Votos y popularidad

### Servidores Mirror Disponibles

La aplicación implementa redundancia utilizando múltiples servidores:

```python
MIRRORS = ["de1", "nl1", "us1", "gb1", "at1"]
```

- `de1.api.radio-browser.info` - Alemania
- `nl1.api.radio-browser.info` - Países Bajos
- `us1.api.radio-browser.info` - Estados Unidos
- `gb1.api.radio-browser.info` - Reino Unido
- `at1.api.radio-browser.info` - Austria

### Endpoints Principales Utilizados

#### 1. Búsqueda por País

```
GET https://de1.api.radio-browser.info/json/stations/bycountry/{country}
```

**Parámetros:**
- `limit`: Número máximo de resultados
- `hidebroken`: Ocultar estaciones rotas (true/false)

**Ejemplo:**
```
https://de1.api.radio-browser.info/json/stations/bycountry/Bolivia?limit=10&hidebroken=true
```

#### 2. Búsqueda por Etiqueta/Género

```
GET https://de1.api.radio-browser.info/json/stations/search
```

**Parámetros:**
- `tag`: Género musical o etiqueta
- `limit`: Número máximo de resultados
- `hidebroken`: Ocultar estaciones rotas (true/false)

**Ejemplo:**
```
https://de1.api.radio-browser.info/json/stations/search?tag=rock&limit=10&hidebroken=true
```

### Formato de Respuesta

La API devuelve un array de objetos JSON con la siguiente estructura:

```json
{
  "stationuuid": "unique-id",
  "name": "Nombre de la Estación",
  "url": "http://stream-url",
  "url_resolved": "http://resolved-stream-url",
  "homepage": "http://homepage-url",
  "favicon": "http://logo-url",
  "tags": "rock,pop,music",
  "country": "País",
  "countrycode": "XX",
  "state": "Estado/Región",
  "language": "idioma",
  "codec": "MP3",
  "bitrate": 128,
  "votes": 100
}
```

## 🛠️ Componentes Técnicos

### 1. LogicaBackend.py

Este módulo contiene la lógica pura de backend para interactuar con la Radio Browser API:

- `rb_get(path, params)`: Función principal para hacer peticiones a la API con fallback automático entre mirrors
- `search_by_country(country, limit, hidebroken)`: Busca estaciones por país
- `search_by_tag(tag, limit, hidebroken)`: Busca estaciones por género/etiqueta
- `pick_stream(station)`: Selecciona la URL correcta del stream

### 2. app.py

Aplicación Flask que proporciona la interfaz web:

- **Ruta `/`**: Página principal con estaciones agrupadas por género (rock, pop, jazz, classical)
- **Ruta `/dev/<nombre_dev>`**: Página principal personalizada con nombre del desarrollador
- `get_stations_by_tags(tags)`: Obtiene múltiples grupos de estaciones por género

### 3. aplicacion_radio.py

Versión extendida y completamente documentada que combina backend y Flask con funcionalidades adicionales:

- Todas las funciones de `app.py` y `LogicaBackend.py`
- **Ruta `/country/<country_name>`**: Búsqueda dinámica por país
- **Ruta `/tag/<tag_name>`**: Búsqueda dinámica por etiqueta
- Documentación completa en español

### 4. Frontend (templates/index.html)

Plantilla HTML que muestra:
- Tarjetas visuales para cada estación
- Reproductor de audio HTML5 integrado
- Información del desarrollador
- Imágenes de logos de estaciones
- Diseño responsive

## 📦 Dependencias

El proyecto requiere las siguientes bibliotecas Python:

```
Flask - Framework web para Python
requests - Librería para hacer peticiones HTTP
```

Instalación:
```bash
pip install -r requirements.txt
```

## 🚀 Cómo Ejecutar la Aplicación

### Opción 1: Usando app.py

```bash
python app.py
```

### Opción 2: Usando aplicacion_radio.py

```bash
python aplicacion_radio.py
```

La aplicación estará disponible en `http://localhost:5000`

## 🎨 Rutas Disponibles

1. **`/`** - Página principal con géneros predefinidos
2. **`/dev/<nombre>`** - Página principal con nombre de desarrollador personalizado
3. **`/country/<pais>`** - Estaciones de un país específico (solo en aplicacion_radio.py)
4. **`/tag/<genero>`** - Estaciones de un género específico (solo en aplicacion_radio.py)

## 📝 Ejemplos de Uso

### Búsqueda de estaciones bolivianas

```bash
# En el navegador
http://localhost:5000/country/Bolivia
```

### Búsqueda de estaciones de rock

```bash
# En el navegador
http://localhost:5000/tag/rock
```

### Página con nombre de desarrollador

```bash
# En el navegador
http://localhost:5000/dev/Juan
```

## 🔧 Manejo de Errores

La aplicación implementa estrategias robustas de manejo de errores:

1. **Fallback entre Mirrors**: Si un servidor falla, intenta automáticamente con el siguiente
2. **Timeout**: Límite de 10 segundos por petición
3. **Valores por Defecto**: Muestra placeholders cuando faltan datos (logos, país, etc.)

## 📊 Archivos de Ejemplo

El repositorio incluye dos archivos JSON de ejemplo:

- **`respuesta.json`**: Respuesta de ejemplo para búsqueda por país (Bolivia)
- **`tags.json`**: Respuesta de ejemplo para búsqueda por etiqueta (rock)

Estos archivos sirven como referencia de la estructura de datos retornada por la API.

## 🎓 Propósito Educativo

Este proyecto fue creado para el bootcamp de Código Facilito con los siguientes objetivos de aprendizaje:

### PARTE 1 - FLASK
1. Probar endpoints de API con Postman
2. Configurar un entorno virtual de Python
3. Crear y gestionar dependencias con requirements.txt
4. Implementar lógica de backend en Flask
5. Crear plantillas HTML dinámicas
6. Personalizar estilos CSS

### PARTE 2 - PYODBC (Extensión futura)
- Conexión a bases de datos
- Operaciones CRUD con PyODBC

## 🌟 Características Destacadas

- ✅ Interfaz web intuitiva y responsive
- ✅ Múltiples opciones de búsqueda (país, género, etiqueta)
- ✅ Reproductor de audio integrado
- ✅ Alta disponibilidad mediante múltiples servidores mirror
- ✅ Código bien documentado y modular
- ✅ Manejo robusto de errores
- ✅ Sin necesidad de autenticación o claves API

## 📄 Licencia

Este proyecto está disponible bajo la licencia especificada en el archivo LICENSE del repositorio.

## 🤝 Contribuciones

Este es un proyecto educativo. Para contribuir o reportar problemas, contacta al propietario del repositorio.

---

**Desarrollado como parte del Bootcamp de Código Facilito**
