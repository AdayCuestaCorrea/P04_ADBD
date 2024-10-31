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

- **Obtenga las ventas totales por tienda, donde se refleje la ciudad, el país (concatenar la ciudad y el país empleando como separador la “,”), y el encargado. Pudiera emplear GROUP BY, ORDER BY**

- **Obtenga una lista de películas, donde se reflejen el identificador, el título, descripción, categoría, el precio, la duración de la película, clasificación, nombre y apellidos de los actores (puede realizar una concatenación de ambos). Pudiera emplear GROUP BY**

- **Obtenga la información de los actores, donde se incluya sus nombres y apellidos, las categorías y sus películas. Los actores deben de estar agrupados y, las categorías y las películas deben estar concatenados por “:”**
  
