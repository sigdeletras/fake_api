Si estamos en los primeros momentos del desarrollo de una aplicación web, móvil o escritorio y necesitamos contar con el acceso a los datos suministrados por nuestra futura API, podemos montar de forma rápida una API REST falsa (fake REST API).

Podemos necesitar también tener acceso a una API para realizar una demo, un prototipo, o simplemente, afrontar un *side project* para probar o aprender alguna nueva funcionalidad.

Seguramente, el desarrollo una API para acceso, consulta y edición de datos la esté desarrollando a la vez el equipo de *backend* de nuestra empresa. Otra posibilidad, es que vaya a ser facilitada por el cliente en algún momento del desarrollo. Estos son un par de motivos por las puede que no contemos desde un primer con los *endpoints* sobre los que montar nuestra proyecto.


Si no queremos complicarnos mucho la vida, aunque como veremos no demasiado costoso montar tu propia API de pruebas, existen páginas que nos dan la posibibilidad de acceder a este tipo de recursos. He llegado a usar [mockapi](https://www.mockapi.io/) y me ha parecido realmente buena. 

![MockAPI](img/mockapi.png)

Otra opción es aprovechar alguna de las APIs existentes con cientos de temáticas. Se puede encontrar una buena recopilación  organizada por temáticas (animales, juegos, geocodificación, noticias...) en este repositorio de Github. https://github.com/public-apis/public-apis 

![public_api.png](img/public_api.png)

A pesar de los recursos existentes, que sin duda nos facilitarían la vida, vamos a montar nuestro propia API con el módulo JavaScript [JSON Server](https://github.com/typicode/json-server). 

Puede ocurrir que queramos que los datos sean los más parecidos a los que nos ofrecerá la API en producción o, simplemente, queremos aprender nuevas habilidades en el ámbito del desarrollo *frontend*.

## Montando una API de prueba JSON Server

JSON Server es una verdadera maravilla. Gracias a este módulo, y como podéis encontrar en muchas páginas web, en menos de 5 minutos podéis tener una Fake REST API funcionando. 

Nuestra API "de mentira", no solo nos permitirá realizar peticiones de tipo GET, sino también crear (POST), actualizar(PUT) o borrar (DELETE) datos.

Por si fuera poco, podremos acceder al listado de objetos, buscar por ID o por alguno de sus campos, filtrar, ordenar, añadir rutas personalizadas...

Por mucho que quiera, no voy a poder mejorar la [documentación el módulo](https://github.com/typicode/json-server#routes) que contiene gran cantidad de ejemplos. 

De todas formas, voy a explicar algunos puntos básicos.

## Instalación

El primer paso es la instalación del módulo con Node o Yarm en nuestro proyecto.

```
npm install json-server
```

Creamos ahora un archivo json (ej. *db.json*) que contendrá nuestros datos de ejempl.

```json
{
  "shop": [
    {
      "id": 1,
      "address": "Calle Juan Martín",
      "type": "frutería",
      "nombre": "Lola Castro Frutas",
      "latitude": 37.880273,
      "longitude": -4.792098
    },
    {
      "id": 2,
      "address": "Calle Pepe Cruz",
      "type": "supermercado",
      "nombre": "Ultramarinos Lolo Castro",
      "latitude": 37.862323,
      "longitude": -4.77812
    },
    {
      "id": 3,
      "address": "Avenida de la Cruz",
      "type": "pescadería",
      "nombre": "Pescados El Boquerón",
      "latitude": 37.856273,
      "longitude": -4.776992
    }
  ],
  "products": [
    {
      "id": 1,
      "name": "manzanas",
      "shopId": 1
    },
    {
      "id": 2,
      "name": "peras",
      "shopId": 1
    },
    {
      "id": 3,
      "name": "escoba",
      "shopId": 2
    },
    {
      "id": 4,
      "name": "detergente",
      "shopId": 2
    }
  ]
}

```

Ponemos en marcha nuestra API con el comando *json-server* apuntando al archivo con los datos.

```
json-server --watch db.json
```

O mejor, ya que la vamos a usar con frecuencia definimos un script a nuestro *package.json*. Hemos añadido en un parámetro la opción para poder indicar un puerto diferente al 3000.

```json
//package.json
"scripts": {
    ...
    "start": "json-server --watch db.json --port $PORT"
    ...
  },
```

Y ahora levantamos el servidor con npm start.

```
npm start
```

Accediendo a la URL local, en este caso http://localhost:3000, tenndremos una página estática con los *endpoits* de nuestra API.

![json-server.png](img/json-server.png)

Desde la misma página podemos acceder a los datos de tiendas y productos.

![get_products.png](img/get_products.png)

## Búsquedas, filtros y orden.

Como he comentado podemos, realizar búsquedas, filtros, obtener los datos ordenados o paginados. Todo está explicado en la documentación.

Solo algunos ejemplos.

- Búsqueda del texto 'lo' en cualquier parte del json.
```
http://localhost:3000/shop/?q=lo
```

Búsqueda de tiendas en las calles que contengan la palabra 'Cruz' usando el campo de busqueda seguido del operador _like. 

```
http://localhost:3000/shop?address_like=Cruz
```

Tenemos también las opciones *_sort* para ordenar por un valo y *_order* para indicar si queremos que se haga de forma ascendente o descendente. 

Por ejemplo, obtenemos el listado de tiendas ordenas de forma descendente por tipo.

```
http://localhost:3000/api/shop?_sort=type&_order=desc
```

## Obtener datos relacionados

Me interesa destacar que podemos obtener resultados con referencias entre objetos padre-hijo mediante su id usando *_embed*.

Obtenemos los datos de la tienda número 1 y los productos que venden.

```
http://localhost:3000/shop/1?_embed=products
```

Gracias a que hemos añadido un valor de referencia entre los objetos también podríamos realizar un petición con los resultados de la tabla hija a partir del fitro por ide del padre. 

Esta sería la url para extraer los productos de la tien cuyo id es 1.

```
http://localhost:3000/shop/1/products
```


![embebed.png](img/embebed.png)

## Rutas personalizadas

Para modificar y crear rutas personalizadas generamos un nuevo archivo json con la configuración que deseemos donde quedarán definidos los alias de las rutas. 

```json
//routes.json
{
  "/api/*": "/$1",
  "/shop/:type": "/shop?type=:type",
  "/shop/address/:address" :"/shop?address_like=:address"
}
```

Para usarlo lo llamaremos al lanzar json-server con la opción  *--routes*.

```
json-server db.json --routes routes.json
```

![rutas.png](img/rutas.png)

## Más allá del GET

Si solo pudíeramos hacer peticiones de tipo GET la librería estária muy limitada. Afortunadamente es posible crear, actualizar y borrar los datos.

Podemos usar los clientes REST Postman o Insomnia para realizar por ejemplo una prueba de POST.

![post_postman.png](img/post_postman.png)


## ...para la próxima

Como hemos visto, contar con una API REST personalizada es realmente cuestión de pocos minutos. El uso de JSON Server combinado con las opciones es muy sencillo.

Voy a dejar para otra entrada, el uso de otras **librerías como Faker y JSON Schema que nos van a permitir generar 'fake data' de forma masiva** y poder así, no tener que dedicarle tiempo a añadir dartos a mano.