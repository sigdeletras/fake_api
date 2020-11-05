Si estamos en los primeros momentos del desarrollo de una aplicación web, móvil o escritorio y necesitamos contar con el acceso a los datos suministrados por nuestra futura API podemos montar de forma rápida una API REST falsa (fake REST API).

Puede que esta labor la esté desarrollando a la vez el equipo de backend, que vaya a ser facilitada por el cliente en algún momento del desarrollo, que la necesitemos para una demo, o simplemente afrontemos un proyecto personal de desarrollo y la necesitemos.

Si no queremos complicarnos mucho la vida, aunque por lo que he aprendido no es demasiado costoso montar tu propia API de pruebas existen páginas que nos dan la posibibilidad de acceder a este tipo de recursos. He llegado a usar [mockapi](https://www.mockapi.io/) y me ha parecido realmente buena. 

![MockAPI](img/mockapi.png)

Otra opción es usar alguna de las múltiples API con cientos de temáticas que existen. Una buena recopilación se puede encontrar en este repositorio de Github. https://github.com/public-apis/public-apis organizada por temáticas tipo animales, juegos, geocodificación, noticias...

![public_api.png](img/public_api.png)

A pesar de los recursos existentes, que sin duda nos facilitarían la vida, vamos a montar nuestro propia API con divesos paquetes JavaScript muy conocidos como JSON server o Faker. Puede ocurrir que queramos que los datos sean los más parecidos a los que nos ofrecerá la API en producción o, simplemente, queremos aprender nuevas habilidades en el ámbito del desarrollo frontend ¿porqué no?.

## Montando una API de prueba JSON Server

JSON Server es una verdadera maravilla. Gracias a este módulo, y como podéis encontrar en muchas entradas, en menos de 5 minutos podéis tener una Fake API funcionando y que permite no solo realizar peticiones de tipo GET, sino también crear (POST), actualizar(PUT) o borrar (DELETE) datos.

Por si fuera poco, podremos acceder al listado de objetos, buscar por ID o por alguno de sus campos, filtrar, ordenar, añadir rutas personalizadas...

Por mucho que quiera, no voy a poder mejorar la [documentación el módulo](https://github.com/typicode/json-server#routes) que además contiene gran cantidad de ejemplos.

El primer paso es la instalación del módulo con Node o Yarm en nuestro proyecto,

```
npm install json-server
```

Creamos ahora el archivo json (ej. db.json) que contendrá nuestros datos

```json
{
  "shop": [
    { "id": 1, "address": "Calle Juan Martín", "type": "frutería", "nombre": "Frutería Lola", "latitude": 37.892306, "longitude": -4.7795159 },
    { "id": 2, "address": "Calle Pepe Cruz", "type": "Supermercado", "nombre": "Ultramarinos Chacho", "latitude": 37.862323, "longitude": -4.77812 },
  ],
  "products": [
    { "id": 1, "name": "manzanas", "shopId": 1 }
    { "id": 2, "name": "peras", "shopId": 1 }
    { "id": 3, "name": "escoba", "shopId": 2 }
    { "id": 4, "name": "detergente", "shopId": 2 }
  ],
}

```

Ponemos en marcha nuestra API

```
json-server --watch db.json
```

O mejor, ya que la vamos a usar con frecuencia añadimos un script a nuestro package.json

```json
//package.json
"scripts": {
    ...
    "start": "json-server --watch db.json --port $PORT"
    ...
  },
```

Y ahora lanzamos el servidor con npm

```
npm start
```

Accediendo a la URL local, en este caso http://localhost:3000, tenndremos una página estática con los endpoits de nuestra API.

![json-server.png](img/json-server.png)

Desde la misma página podemos acceder a los datos de shop y products.

![get_products.png](img/get_products.png)

## Busquedas, filtros y orden

http://localhost:3000/shop/?q=lo


## Obtener datos referenciados

Como he comentado podemos realizar búsquedas, filtros, obtener los datos ordenados o paginados. Todo explicado en la documentación. A pesar de ello, me interesa destacar que podemos obtener resultados con referencias entre objetos padre-hijo mediante su id usando '_embed'

Optenemos los datos de la tienda número 1 y los productos que venden.

```
http://localhost:3000/shop/1?_embed=products
```


![embebed.png](img/embebed.png)

## Rutas personalizadas


Create a routes.json file. Pay attention to start every route with /.

{
  "/api/*": "/$1",
  "/shop/:type": "/shop?type=:type",
  "/shop\\?id=:id": "/shop/:id"
}

Start JSON Server with --routes option.

json-server db.json --routes routes.json
Now you can access resources using additional routes.

/api/posts # → /posts
/api/posts/1  # → /posts/1
/posts/1/show # → /posts/1
/posts/javascript # → /posts?category=javascript
/articles?id=1 # → /posts/1


http://localhost:3000/api/shop/Supermercado


## Más allá del GET

Si solo pudíeramos hacer peticiones de tipo GET la librería estária muy limitada. Afortunadamente es posible crear, actualizar y borrar los datos.

Podemos usar los clientes REST Postman o Insomnia para realizar por ejemplo una prueba de POST.

![post_postman.png](img/post_postman.png)


JSON Schema

npm install json-schema-faker
npm install faker