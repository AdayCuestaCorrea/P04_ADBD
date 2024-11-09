# P04_ADBD - Modelo relacional. Vistas y disparadores
## Aday Cuesta Correa y Manuel José Sebastián Noda

**1. Realice la restauración de la base de datos alquilerdvd.tar. Observe que la base de datos no tiene un formato SQL como el empleado en actividades anteriores.**

Para realizar la restauración de la base de datos he creado una base de datos y le he asignado el contenido del fichero .tar
```bash
$ createdb alquiler
$ pg_restore -d alquiler < AlquilerPractica.tar
```

**2. Identifique las tablas, vistas y secuencias.**

Para identificar las tablas, vistas y secuencias he utilizado comandos de psql:

```postgresql
alquiler=# \dt
             List of relations
 Schema |     Name      | Type  |  Owner   
--------+---------------+-------+----------
 public | actor         | table | postgres
 public | address       | table | postgres
 public | category      | table | postgres
 public | city          | table | postgres
 public | country       | table | postgres
 public | customer      | table | postgres
 public | film          | table | postgres
 public | film_actor    | table | postgres
 public | film_category | table | postgres
 public | inventory     | table | postgres
 public | language      | table | postgres
 public | payment       | table | postgres
 public | rental        | table | postgres
 public | staff         | table | postgres
 public | store         | table | postgres
(15 rows)

alquiler=# \dv
Did not find any relations.

alquiler=# \ds
                     List of relations
 Schema |            Name            |   Type   |  Owner   
--------+----------------------------+----------+----------
 public | actor_actor_id_seq         | sequence | postgres
 public | address_address_id_seq     | sequence | postgres
 public | category_category_id_seq   | sequence | postgres
 public | city_city_id_seq           | sequence | postgres
 public | country_country_id_seq     | sequence | postgres
 public | customer_customer_id_seq   | sequence | postgres
 public | film_film_id_seq           | sequence | postgres
 public | inventory_inventory_id_seq | sequence | postgres
 public | language_language_id_seq   | sequence | postgres
 public | payment_payment_id_seq     | sequence | postgres
 public | rental_rental_id_seq       | sequence | postgres
 public | staff_staff_id_seq         | sequence | postgres
 public | store_store_id_seq         | sequence | postgres
(13 rows)

```

**3. Identifique las tablas principales y sus principales elementos.**

Las tablas principales son las siguientes:
 - **Film** con sus atributos principales que son *film_id*, *title*, *rental_rate*, *rental_duration*, *replacement_cost*. Como empresa me interesa sobre todo como de bien se venden mis películas y por cuanto tiempo se alquilan, así como el coste de reemplazarlas si se pierden.
 - **Inventory** con sus atributos principales que son *inventory_id*, *store_id* y *film_id*. Es importante saber que peliculas tenemos disponibles para poder vender, pues puede ser que hayan películas que no poseamos y sean tendencia.
 - **Customer** con sus atributos principales que son *customer_id*, *store_id*, *active* y *active_bool*. Es de vital importancia conocer a nuestros clientes, si poseen películas y dónde las alquilan.
 - **Payment** con sus atributos principales que son *payment_id*, *customer_id*, *staff_id*, *rental_id* y *amount*. Como pagan nuestras películas también es importante pues sabemos que clientes están más activos, cuanto dinero gastan en nosotros, así como qué películas alquilan y que empleado les atiende (útil para ver la productividad del mismo).
 - **staff** con sus atributos principales que son *staff_id*, *store_id* y *active*. Es muy importante saber a quién tenemos contratado y si están en activo.
 - **store** con sus atributos principales *store_id*, *manager_staff_id* y *address_id*. Hay que saber cuantas tiendas poseemos, dónde se encuentran y quién es el manager encargado de dicha tienda.

**4. Realice las siguientes consultas.**

- **Obtenga las ventas totales por categoría de películas ordenadas descendentemente.**
```postgresql
SELECT C.NAME AS CATEGORY, SUM(P.AMOUNT) AS SALES
FROM CATEGORY C 
JOIN FILM_CATEGORY FC ON C.CATEGORY_ID = FC.CATEGORY_ID
JOIN FILM F ON FC.FILM_ID = F.FILM_ID
JOIN INVENTORY I ON F.FILM_ID = I.FILM_ID
JOIN RENTAL R ON I.INVENTORY_ID = R.INVENTORY_ID
JOIN PAYMENT P ON R.RENTAL_ID = P.RENTAL_ID
GROUP BY C.NAME
ORDER BY SALES DESC;

  category   |  sales  
-------------+---------
 Sports      | 4892.19
 Sci-Fi      | 4336.01
 Animation   | 4245.31
 Drama       | 4118.46
 Comedy      | 4002.48
 New         | 3966.38
 Action      | 3951.84
 Foreign     | 3934.47
 Games       | 3922.18
 Family      | 3830.15
 Documentary | 3749.65
 Horror      | 3401.27
 Classics    | 3353.38
 Children    | 3309.39
 Travel      | 3227.36
 Music       | 3071.52
(16 rows)
```

- **Obtenga las ventas totales por tienda, donde se refleje la ciudad, el país (concatenar la ciudad y el país empleando como separador la “,”), y el encargado. Pudiera emplear GROUP BY, ORDER BY**

```postgresql
SELECT CONCAT(city.city, ', ', country.country) AS location,
CONCAT(staff.first_name, ' ', staff.last_name) AS manager,
store.store_id, SUM(payment.amount) AS total_sales
FROM payment JOIN rental ON payment.rental_id = rental.rental_id
JOIN inventory ON rental.inventory_id = inventory.inventory_id
JOIN store ON inventory.store_id = store.store_id
JOIN address ON store.address_id = address.address_id
JOIN city ON address.city_id = city.city_id
JOIN country ON city.country_id = country.country_id
JOIN staff ON store.manager_staff_id = staff.staff_id
GROUP BY store.store_id, location, manager;

       location       |   manager    | store_id | total_sales 
----------------------+--------------+----------+-------------
 Lethbridge, Canada   | Mike Hillyer |        1 |    30628.91
 Woodridge, Australia | Jon Stephens |        2 |    30683.13
```

- **Obtenga una lista de películas, donde se reflejen el identificador, el título, descripción, categoría, el precio, la duración de la película, clasificación, nombre y apellidos de los actores (puede realizar una concatenación de ambos). Pudiera emplear GROUP BY**

```postgresql
SELECT film.film_id, film.title, film.description,
category.name AS category, film.rental_rate AS price,
film.length AS duration, film.rating,
STRING_AGG(CONCAT(actor.first_name, ' ', actor.last_name), ', ') AS actors
FROM film JOIN film_category ON film.film_id = film_category.film_id
JOIN category ON film_category.category_id = category.category_id
JOIN film_actor ON film.film_id = film_actor.film_id
JOIN actor ON film_actor.actor_id = actor.actor_id
GROUP BY film.film_id, film.title, film.description, category.name, film.rental_rate, film.length, film.rating
ORDER BY film.title;
```

El resultado de la consulta es difícil de leer, por tanto he obviado colocar el resultado en este informe.

- **Obtenga la información de los actores, donde se incluya sus nombres y apellidos, las categorías y sus películas. Los actores deben de estar agrupados y, las categorías y las películas deben estar concatenados por “:”**

```postgresql
SELECT CONCAT(actor.first_name, ' ', actor.last_name) AS actor_name,
STRING_AGG(CONCAT(category.name, ': ', film.title), ', ') AS categories_and_movies
FROM actor JOIN film_actor ON actor.actor_id = film_actor.actor_id
JOIN film ON film_actor.film_id = film.film_id
JOIN film_category ON film.film_id = film_category.film_id
JOIN category ON film_category.category_id = category.category_id
GROUP BY actor.actor_id, actor.first_name, actor.last_name
ORDER BY actor_name;
```

El resultado de la consulta es difícil de leer, por tanto he obviado colocar el resultado en este informe.

**5. Realice todas las vistas de las consultas anteriores. Colóqueles el prefijo view_ a su denominación.**

- **Vista sobre las ventas totales por categoría de películas ordenadas descendentemente**

```postgresql
CREATE VIEW view_sales_by_category AS
SELECT C.NAME AS CATEGORY, SUM(P.AMOUNT) AS SALES
FROM CATEGORY C 
JOIN FILM_CATEGORY FC ON C.CATEGORY_ID = FC.CATEGORY_ID
JOIN FILM F ON FC.FILM_ID = F.FILM_ID
JOIN INVENTORY I ON F.FILM_ID = I.FILM_ID
JOIN RENTAL R ON I.INVENTORY_ID = R.INVENTORY_ID
JOIN PAYMENT P ON R.RENTAL_ID = P.RENTAL_ID
GROUP BY C.NAME
ORDER BY SALES DESC;
```

- **Vista sobre las ventas totales por tienda, donde se refleja la ciudad, el país, y el encargado**

```postgresql
CREATE VIEW view_sales_by_store AS
SELECT CONCAT(city.city, ', ', country.country) AS location,
CONCAT(staff.first_name, ' ', staff.last_name) AS manager,
store.store_id, SUM(payment.amount) AS total_sales
FROM payment JOIN rental ON payment.rental_id = rental.rental_id
JOIN inventory ON rental.inventory_id = inventory.inventory_id
JOIN store ON inventory.store_id = store.store_id
JOIN address ON store.address_id = address.address_id
JOIN city ON address.city_id = city.city_id
JOIN country ON city.country_id = country.country_id
JOIN staff ON store.manager_staff_id = staff.staff_id
GROUP BY store.store_id, location, manager;
```

- **Vista sobre la lista de películas, donde se refleja el identificador, el título, descripción, categoría, el precio, la duración de la película, clasificación, nombre y apellidos de los actores.**

```postgresql
CREATE VIEW view_movie_list AS
SELECT film.film_id, film.title, film.description,
category.name AS category, film.rental_rate AS price,
film.length AS duration, film.rating,
STRING_AGG(CONCAT(actor.first_name, ' ', actor.last_name), ', ') AS actors
FROM film JOIN film_category ON film.film_id = film_category.film_id
JOIN category ON film_category.category_id = category.category_id
JOIN film_actor ON film.film_id = film_actor.film_id
JOIN actor ON film_actor.actor_id = actor.actor_id
GROUP BY film.film_id, film.title, film.description, category.name, film.rental_rate, film.length, film.rating
ORDER BY film.title;
```

- **Vista sobre la información de los actores, donde se incluyen sus nombres y apellidos, las categorías y sus películas.**

```postgresql
CREATE VIEW view_actor_info AS
SELECT CONCAT(actor.first_name, ' ', actor.last_name) AS actor_name,
STRING_AGG(CONCAT(category.name, ': ', film.title), ', ') AS categories_and_movies
FROM actor JOIN film_actor ON actor.actor_id = film_actor.actor_id
JOIN film ON film_actor.film_id = film.film_id
JOIN film_category ON film.film_id = film_category.film_id
JOIN category ON film_category.category_id = category.category_id
GROUP BY actor.actor_id, actor.first_name, actor.last_name
ORDER BY actor_name;
```
