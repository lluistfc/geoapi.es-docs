# geoapi.es-docs

### Documentación para la API de GeoAPI.es

Esta es la documentacion generica para la API de GeoAPI.es. Se recomienda la lectura en
conjunto con la documentacion especifica de la librería que se desee utilizar.

### Como funciona

El funcionamiento basico de GeoAPI consiste en lanzar peticiones `GET` al endpoint, el cual
es `http://apiv1.geoapi.es/` si no se indica algun otro endpoint de manera explicita, pasando
una serie de parámetros en cada petición. Dichos parámetros sirven para, por ejemplo, filtrar
o concretar una busqueda.

Ejemplo dde petición:

    http://apiv1.geoapi.es/poblaciones?CPRO=22&CMUM=907&type=JSON&key=&sandbox=1


### Parámetros de configuración

Hay varios parámetros de configuración que indican a la API como debe actuar así como los datos
de usuario que debe usar.

* `url` - Indica la URL del endpoint. `http://apiv1.geoapi.es/` por defecto.
* `type` - Indica el tipo de respuesta, a elegir entre `JSON` o `XML`. Si no se indica nada, se usara `JSON`.
* `key` - La API Key que se usara para hacer las peticiones. Puede tener un valor nulo (`''`) si se activa el modo sandbox. Si no se especifica, se enviara `''`.
* `sandbox` - Puede ser `0` (desactivado, por defecto) o `1` (activado). En caso de activar el modo sandbox, no se requerira una API Key. De ser enviada una API Key, no se descontaran peticiones de la misma. Ten en cuenta que en modo sandbox las letras de los resultados pueden estar mezcladas.

Dichos parámetros se establecen con el método `setConfig` de cada librería.

### Parámetros de consultas

Además de los parámetros de configuración, la API permite el uso de varios parámetros a mayores, que especifican que tipo de resultado se desea obtener.

| Parametro | Descripcion         |
|:---------:|:-------------------:|
| CCOM      | ID de comunidad     |
| CPRO      | ID de provincia     |
| CMUM`     | ID de municipio     |
| NENTSI50  | Nombre de poblacion |
| CUN       | ID de nucleo        |
| CPOS      | Codigo postal       |

Estos parámetros se usan con los métodos de consultas de cada librería.

### Orden de los datos

Cada método de cada una de las librerías ofrece acceso rápido a ciertas partes de la base de datos. Todos los datos están ordenados de tal manera para que sea posible acceder a cualquier información almacenada, sin conocer previamente ningún ID.

Asi por ejemplo, para obtener una lista de todas las comunidades y sus IDs, haremos una consulta a la API sin especificar parámetros.

Una vez obtenidos los IDs de las comunidades, podemos enviar otra petición a la API, pasando como parametro solo el ID de aquella comunidad cuyas provincias queremos obtener.

De la misma manera procederemos con los municipios, poblaciones, nucleos y codigos postales hasta llegar a las calles, que es el nivel de detalle mas alto.

### Métodos genericos

Todas las librerías tienen los mismos métodos, por lo tanto cubriremos sus nombres y sus parámetros aqui. Asi evitamos duplicar información.

Un método tiene el mismo nombre que la parte final (acción) de cualquier URL usada para hacer una petición a GeoAPI, entre el ultimo `/` y justo antes de los parámetros. Por ejemplo:

| URL                                               | Metodo        |
|:-------------------------------------------------:|:-------------:|
| apiv1.geoapi.es/<b>comunidades</b>                  | comunidades() |
| apiv1.geoapi.es/<b>poblaciones</b>?CPRO=22&CMUM=907 | poblaciones() |

### Listado completo de acciones y sus parámetros

Todos los parámetros son obligatorios, al menos que se especifique lo contrario.

| Accion       | Parámetros               | Descripcion                     |
|:------------:|:------------------------:|:--------------------------------|
| calles       | CPRO, CMUM, CUN, CPOS    | Devuelve un listado completo de todas las calles  |
| comunidades  |                          | Devuelve un listado de todas las comunidades      |
| cps          | CPRO, CMUM, CUN          | Devuelve un listado de todos los codigos postales |
| municipios   | CPRO                     | Devuelve un listado de todos los municipios       |
| nucleos      | CPRO, CMUM, NENTSI50     | Devuelve un listado de los nucleos de un municipio. Suele haber, aunque no se limita, 1 nucleo que representa el municipio en si y un nucleo <b>\*DISEMINADO\*</b> que representa areas, caminos, carreteras, fincas, etc mas alejadas del municipio, pero que están dentro de los limites territoriales del municipio e incluso llegan a compartir codigo postal con éste. |
| poblaciones  | CPRO, CMUM               | Devuelve un listado de todas las poblaciones      |
| provincias   | CCOM                     | Devuelve un listado de todas las provincias       |
| qcalles      | QUERY                    | Esta es una acción especial que acepta cadenas de texto y realiza busquedas basandose en los nombres de las calles. Es decir, es una busqueda libre para calles. |

### Tipos de respuesta

Las respuestas pueden ser del tipo `JSON` o `XML`, segun lo establecido en la configuración de la librería que se este usando o el parametro que se envie (si se prefiere usar la API sin usar las librerías proporcionadas).

Cubriremos las posibles respuesta en el formato `JSON`, aunque para `XML` son las mismas.

* <b>Datos</b>

    Una respuesta normal de la API tiene el siguiente formato:

    ```javascript
    //
    {
        "update_date": "2015.06",
        "size": 42,
        "data": [
            {}, {}, {}, {}...
        ]
    }
    ```

    Es decir, un objeto con las siguientes claves:
    * `update_date` - Especifica la fecha (año y mes) en la cual los datos devueltos fueron actualizados por ultima vez.
    * `size` - Contiene el número de elementos contenidos en `data`.
    * `data` - Es un array de objetos. Dependiendo de la petición enviada, cada objeto tendra unos datos u otros.

* <b>Avisos</b>

    Además de los datos anteriores, la API puede embeber otra clave en la respuesta. Esta clave se llama `warning` y contiene una cadena de texto con cualquier aviso que la API decida que debe ser devuelto, como por ejemplo que el modo sandbox esta activado.

* <b>Errores</b>

    Además de los datos anteriores, la API puede embeber otra clave en la respuesta. Esta clave se llama `error` y contiene una cadena de texto con cualquier error que la API decida que debe ser devuelto, como por ejemplo que no quedan peticiones en la API Key que se esta usando.

### Notas

* Todos los datos se envia en `UTF-8`.
* Existe una limitacion de 1 segundo entre petición y petición en el modo sandbox.
