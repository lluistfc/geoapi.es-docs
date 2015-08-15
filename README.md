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

* `CCOM` - ID de comunidad.
* `CPRO` - ID de provincia.
* `CMUM` - ID de municipio.
* `NENTSI50` - Nombre de poblacion.
* `CUN` - ID de nucleo.
* `CPOS` - Codigo postal.

Estos parametros se usan con los metodos de consultas de cada libreria.

### Orden de los datos

Cada metodo de cada una de las librerias ofrece acceso rapido a ciertas partes de la base de datos. Todos los datos estan ordenados de tal manera para que sea posible acceder a cualquier informacion almacenada, sin conocer previamente ningun ID.

Asi por ejemplo, para obtener una lista de todas las comunidades y sus IDs, haremos una consulta a la API sin especificar parametros.

Una vez obtenidos los IDs de las comunidades, podemos enviar otra peticion a la API, pasando como parametro solo el ID de aquella comunidad cuyas provincias queremos obtener.

De la misma manera procederemos con los municipios, poblaciones, nucleos y codigos postales hasta llegar a las calles, que es el nivel de detalle mas alto.

### Metodos genericos (y sus parametros)

Todas las librerias tienen los mismos metodos, por lo tanto cubriremos sus nombres y sus parametros aqui. Asi evitamos duplicar informacion.

//TODO

### Tipos de respuesta

Las respuestas pueden ser del tipo `JSON` o `XML`, segun lo establecido en la configuracion de la libreria que se este usando o el parametro que se envie (si se prefiere usar la API sin usar las librerias proporcionadas).

Cubriremos las posibles respuesta en el formato `JSON`, aunque para `XML` son las mismas.

* Datos

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

* Avisos

    Ademas de los datos anteriores, la API puede embeber otra clave en la respuesta. Esta clave se llama `warning` y contiene una cadena de texto con cualquier aviso que la API decida que debe ser devuelto, como por ejemplo que el modo sandbox esta activado.

* Errores

    Ademas de los datos anteriores, la API puede embeber otra clave en la respuesta. Esta clave se llama `error` y contiene una cadena de texto con cualquier error que la API decida que debe ser devuelto, como por ejemplo que no quedan peticiones en la API Key que se esta usando.

### Notas

* Todos los datos se envia en `UTF-8`.
* Existe una limitacion de 1 segundo entre peticion y peticion en el modo sandbox.
