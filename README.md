# geoapi.es-docs

### Documentación para la API de GeoAPI.es

Esta es la documentacion generica para la API de GeoAPI.es. Se recomienda la lectura en
conjunto con la documentacion especifica de la libreria que se desee utilizar.

### Como funciona

El funcionamiento basico de GeoAPI consiste en lanzar peticiones `GET` al endpoint, el cual
es `http://geoapi.es/API/` si no se indica algun otro endpoint de manera explicita, pasando
una serie de parametros en cada peticion. Dichos parametros sirven para, por ejemplo, filtrar
o concretar una busqueda.

Ejemplo dde peticion:

    http://geoapi.es/API/poblaciones?CPRO=22&CMUM=907&type=JSON&key=&sandbox=1


### Parametros de configuracion

Hay varios parametros de configuracion que indican a la API como debe actuar asi como los datos
de usuario que debe usar.

* `url` - Indica la URL del endpoint. `http://geoapi.es/API/` por defecto.
* `type` - Indica el tipo de respuesta, a elegir entre `JSON` o `XML`. Si no se indica nada, se usara `JSON`.
* `key` - La API Key que se usara para hacer las peticiones. Puede tener un valor nulo (`''`) si se activa el modo sandbox. Si no se especifica, se enviara `''`.
* `sandbox` - Puede ser `0` (desactivado, por defecto) o `1` (activado). En caso de activar el modo sandbox, no se requerira una API Key. De ser enviada una API Key, no se descontaran peticiones de la misma. Ten en cuenta que en modo sandbox las letras de los resultados pueden estar mezcladas.

Dichos parametros se establecen con el metodo `setConfig` de cada libreria.

### Parametros de consultas

Ademas de los parametros de configuracion, la API permite el uso de varios parametros a mayores, que especifican que tipo de resultado se desea obtener.

| Parametro | Descripcion         |
|:---------:|:-------------------:|
| CCOM      | ID de comunidad     |
| CPRO      | ID de provincia     |
| CMUM`     | ID de municipio     |
| NENTSI50  | Nombre de poblacion |
| CUN       | ID de nucleo        |
| CPOS      | Codigo postal       |

Estos parametros se usan con los metodos de consultas de cada libreria.

### Orden de los datos

Cada metodo de cada una de las librerias ofrece acceso rapido a ciertas partes de la base de datos. Todos los datos estan ordenados de tal manera para que sea posible acceder a cualquier informacion almacenada, sin conocer previamente ningun ID.

Asi por ejemplo, para obtener una lista de todas las comunidades y sus IDs, haremos una consulta a la API sin especificar parametros.

Una vez obtenidos los IDs de las comunidades, podemos enviar otra peticion a la API, pasando como parametro solo el ID de aquella comunidad cuyas provincias queremos obtener.

De la misma manera procederemos con los municipios, poblaciones, nucleos y codigos postales hasta llegar a las calles, que es el nivel de detalle mas alto.

### Metodos genericos

Todas las librerias tienen los mismos metodos, por lo tanto cubriremos sus nombres y sus parametros aqui. Asi evitamos duplicar informacion.

Un metodo tiene el mismo nombre que la parte final (accion) de cualquier URL usada para hacer una peticion a GeoAPI, entre el ultimo `/` y justo antes de los parametros. Por ejemplo:

| URL                                               | Metodo        |
|:-------------------------------------------------:|:-------------:|
| geoapi.es/API/<b>comunidades</b>                  | comunidades() |
| geoapi.es/API/<b>poblaciones</b>?CPRO=22&CMUM=907 | poblaciones() |

### Listado completo de acciones y sus parametros

Todos los parametros son obligatorios, al menos que se especifique lo contrario.

| Accion       | Parametros               | Descripcion                     |
|:------------:|:------------------------:|:--------------------------------|
| calles       | CPRO, CMUM, CUN, CPOS    | Devuelve un listado completo de todas las calles  |
| comunidades  |                          | Devuelve un listado de todas las comunidades      |
| cps          | CPRO, CMUM, CUN          | Devuelve un listado de todos los codigos postales |
| municipios   | CPRO                     | Devuelve un listado de todos los municipios       |
| nucleos      | CPRO, CMUM, NENTSI50     | Devuelve un listado de los nucleos de un municipio. Suele haber, aunque no se limita, 1 nucleo que representa el municipio en si y un nucleo <b>\*DISEMINADO\*</b> que representa areas, caminos, carreteras, fincas, etc mas alejadas del municipio, pero que estan dentro de los limites territoriales del municipio e incluso llegan a compartir codigo postal con éste. |
| poblaciones  | CPRO, CMUM               | Devuelve un listado de todas las poblaciones      |
| provincias   | CCOM                     | Devuelve un listado de todas las provincias       |
| qcalles      | QUERY                    | Esta es una accion especial que acepta cadenas de texto y realiza busquedas basandose en los nombres de las calles. Es decir, es una busqueda libre para calles. |

### Tipos de respuesta

Las respuestas pueden ser del tipo `JSON` o `XML`, segun lo establecido en la configuracion de la libreria que se este usando o el parametro que se envie (si se prefiere usar la API sin usar las librerias proporcionadas).

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
    * `size` - Contiene el numero de elementos contenidos en `data`.
    * `data` - Es un array de objetos. Dependiendo de la peticion enviada, cada objeto tendra unos datos u otros.

* <b>Avisos</b>

    Ademas de los datos anteriores, la API puede embeber otra clave en la respuesta. Esta clave se llama `warning` y contiene una cadena de texto con cualquier aviso que la API decida que debe ser devuelto, como por ejemplo que el modo sandbox esta activado.

* <b>Errores</b>

    Ademas de los datos anteriores, la API puede embeber otra clave en la respuesta. Esta clave se llama `error` y contiene una cadena de texto con cualquier error que la API decida que debe ser devuelto, como por ejemplo que no quedan peticiones en la API Key que se esta usando.

### Notas

* Todos los datos se envia en `UTF-8`.
* Existe una limitacion de 1 segundo entre peticion y peticion en el modo sandbox.
