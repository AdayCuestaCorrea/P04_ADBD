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

**Tabla Customer**
![customer](https://github.com/AdayCuestaCorrea/P04_ADBD/blob/main/Tablas/customer.png)

La tabla "customer" almacena la información de los clientes en el sistema, incluyendo datos personales, de contacto y de estado. Cada cliente está identificado de manera única por el campo customer_id, que actúa como la clave primaria de la tabla. Además, el campo store_id indica la tienda a la que pertenece el cliente, lo que permite relacionarlo con la sucursal correspondiente.

Los campos first_name y last_name guardan el nombre y apellido del cliente, respectivamente, mientras que el campo email almacena su dirección de correo electrónico, facilitando la comunicación directa con el cliente. Para especificar la ubicación del cliente, se utiliza el campo address_id, que conecta con la dirección asociada en otra tabla.

La tabla también incluye campos para gestionar el estado del cliente en el sistema. El campo activebool, de tipo booleano, indica si el cliente está activo (t para verdadero o f para falso). De manera similar, el campo active, de tipo numérico, también representa el estado activo del cliente con valores de 1 para activo y 0 para inactivo.
El campo create_date registra la fecha en que se creó el registro del cliente, proporcionando un historial del tiempo que el cliente ha estado en el sistema. Por último, el campo last_update muestra la última vez que se actualizó la información del cliente, lo cual es útil para mantener los datos actualizados.

**Tabla Country**
![country](https://github.com/AdayCuestaCorrea/P04_ADBD/blob/main/Tablas/country.png)

La tabla "country" almacena información sobre los países asociados a los clientes, empleados y tiendas en el sistema. Cada país está identificado de manera única por el campo country_id, que actúa como la clave primaria de la tabla. Este identificador permite hacer referencia a un país de manera única dentro del sistema.
El campo country contiene el nombre del país, lo que permite identificar fácilmente de qué país se trata. Este campo está diseñado para almacenar el nombre del país en formato de texto, y es clave para la organización y clasificación de los datos relacionados con los países en el sistema.
El campo last_update registra la última vez que se actualizó la información del país. Este campo es útil para mantener un historial de cuándo se realizaron cambios en los datos relacionados con los países, lo que ayuda a gestionar las actualizaciones y mantener la integridad de la base de datos.

**Tabla City**
![city](https://github.com/AdayCuestaCorrea/P04_ADBD/blob/main/Tablas/city.png)

La tabla "city" almacena la información de las ciudades asociadas a las direcciones de los clientes, empleados y tiendas en el sistema. Cada ciudad está identificada de manera única por el campo city_id, que actúa como la clave primaria de la tabla. Este identificador único permite hacer referencia a cada ciudad dentro del sistema de manera precisa.

El campo city almacena el nombre de la ciudad, facilitando su identificación y clasificación. Este campo es fundamental para asociar direcciones a ciudades específicas dentro del sistema.

El campo country_id es una clave foránea que hace referencia al country_id de la tabla "country". Este campo indica el país al que pertenece la ciudad, permitiendo establecer una relación entre las ciudades y los países correspondientes.

El campo last_update registra la fecha y hora en que se realizó la última actualización de los datos relacionados con la ciudad. Este campo es esencial para el mantenimiento de la base de datos, ya que permite llevar un registro de cuándo se modificaron los datos de las ciudades.

**Tabla Address**
![address](https://github.com/AdayCuestaCorrea/P04_ADBD/blob/main/Tablas/address.png)

La tabla "address" almacena la información detallada de las direcciones asociadas a distintas entidades del sistema, como clientes, empleados y tiendas. Cada dirección está identificada de manera única por el campo address_id, que actúa como la clave primaria de la tabla, permitiendo una referencia única a cada dirección dentro del sistema.

El campo address contiene la dirección principal, como el nombre de la calle y el número, proporcionando la ubicación básica de la dirección. En caso de que se necesite información adicional sobre la dirección, se utiliza el campo address2, que es opcional y puede almacenar detalles como el número de apartamento o unidad.

El campo district registra el distrito o la región administrativa en la que se encuentra cada dirección, lo que ayuda a clasificar la ubicación dentro de un contexto más específico de la ciudad o área geográfica.

Para asociar cada dirección a una ciudad, la tabla incluye el campo city_id, que es una clave foránea que enlaza cada dirección con la tabla CITY. Este campo permite identificar la ciudad a la que pertenece la dirección mediante su identificador único.

El campo postal_code almacena el código postal correspondiente a cada dirección, lo que facilita la identificación precisa de la ubicación geográfica en sistemas de envío o distribución.

El campo phone almacena el número de teléfono asociado a la dirección, lo que puede ser útil para contactar directamente con los clientes o entidades relacionadas con esa dirección.

Finalmente, el campo last_update registra la fecha y hora en que se realizó la última actualización de los datos en la tabla, permitiendo un historial de cambios y asegurando que los registros se mantengan actualizados.

**Tabla Store**
![store](https://github.com/AdayCuestaCorrea/P04_ADBD/blob/main/Tablas/store.png)

La tabla "store" almacena información básica sobre las tiendas en el sistema, asignando un identificador único a cada tienda a través del campo store_id, que actúa como la clave primaria de la tabla. Este identificador único permite referenciar y gestionar cada tienda de forma individual dentro de la base de datos.

Cada tienda está asociada a un gerente, identificado por el campo manager_staff_id, que almacena el número de empleado del gerente encargado de la tienda. Este campo es clave para establecer quién está a cargo de la gestión y operación de cada tienda en el sistema.

El campo address_id es una clave foránea que enlaza la tabla STORE con la tabla ADDRESS, asociando cada tienda a una dirección específica previamente registrada. Esta relación permite determinar la ubicación física de cada tienda en el sistema.

Además, la tabla incluye el campo last_update, que registra la fecha y hora de la última modificación realizada en los datos de cada tienda. Este campo es importante para mantener un historial de las actualizaciones y garantizar que la información de la tienda esté siempre actualizada.

**Tabla Payment**
![payment](https://github.com/AdayCuestaCorrea/P04_ADBD/blob/main/Tablas/payment.png)

La tabla "payment" representa y almacena la información de los pagos realizados por los clientes en el sistema. Cada pago está identificado de manera única por el campo payment_id, que actúa como la clave primaria de la tabla, permitiendo hacer referencia de forma precisa a cada transacción realizada.

El campo customer_id almacena el identificador del cliente que realizó el pago, siendo este un campo de tipo clave foránea que se refiere al customer_id de la tabla customer, permitiendo asociar cada pago con un cliente específico.

El campo staff_id almacena el identificador del empleado que gestionó o procesó el pago. Este campo es una clave foránea que se relaciona con el staff_id de la tabla staff, permitiendo hacer seguimiento de qué empleado gestionó la transacción.

El campo rental_id es una clave foránea que hace referencia al rental_id de la tabla rental, indicando el alquiler o servicio relacionado con el pago. Este campo establece una relación entre los pagos y los alquileres asociados a los clientes.

El campo amount registra el monto pagado por el cliente. Este campo es de tipo numérico y contiene el valor total de la transacción.

Por último, el campo payment_date almacena la fecha y hora en que se realizó el pago. Este campo es fundamental para registrar el momento exacto de la transacción y facilitar la trazabilidad de los pagos en el sistema.

**Tabla Staff**
![staff](https://github.com/AdayCuestaCorrea/P04_ADBD/blob/main/Tablas/staff.png)

La tabla "staff" almacena la información del personal de las tiendas, incluyendo detalles personales, de contacto y laborales. Cada miembro del personal está identificado de manera única por el campo staff_id, que actúa como la clave primaria de la tabla, permitiendo una referencia única y precisa para cada empleado dentro del sistema.

El campo first_name almacena el nombre del empleado, mientras que last_name guarda su apellido, ambos campos esenciales para identificar al miembro del personal de manera individual.

El campo address_id es una clave foránea que enlaza la tabla STAFF con la tabla ADDRESS, asociando cada empleado con una dirección específica previamente registrada. Este campo permite conocer la ubicación física del empleado.

El campo email almacena la dirección de correo electrónico del empleado, lo que facilita la comunicación con él para cuestiones laborales o administrativas.

El campo store_id es una clave foránea que se refiere al store_id de la tabla STORE, indicando la tienda en la que trabaja cada empleado. Este campo establece la relación entre el personal y la tienda correspondiente.

El campo active es de tipo numérico y se utiliza para indicar si el empleado está activo en el sistema. Un valor de 1 significa que el empleado está activo, mientras que un valor de 0 indica que está inactivo.

El campo username almacena el nombre de usuario del empleado, utilizado para acceder al sistema, mientras que el campo password guarda la contraseña cifrada del empleado, asegurando la protección de las credenciales de acceso.

El campo last_update registra la fecha y hora de la última actualización realizada en los datos del empleado, lo que permite llevar un control sobre los cambios y actualizaciones de la información laboral.

Por último, el campo picture almacena una referencia a la imagen del empleado, permitiendo asociar una foto al perfil del miembro del personal para fines de identificación visual en el sistema.

**Tabla Rental**
![rental](https://github.com/AdayCuestaCorrea/P04_ADBD/blob/main/Tablas/rental.png)

La tabla "rental" representa y almacena información detallada sobre los alquileres de inventario realizados por los clientes. Cada alquiler está identificado de manera única por el campo rental_id, que actúa como la clave primaria de la tabla, permitiendo una referencia precisa a cada transacción de alquiler en el sistema.

El campo rental_date registra la fecha y hora en que se realizó el alquiler, lo que ayuda a identificar el momento exacto en que el cliente obtuvo el artículo en alquiler.

El campo inventory_id es una clave foránea que hace referencia al inventory_id de la tabla INVENTORY, indicando el artículo específico que fue alquilado. Este campo es crucial para rastrear qué artículo de inventario fue entregado al cliente.

Para asociar cada alquiler con un cliente, la tabla incluye el campo customer_id, que es una clave foránea que se refiere al customer_id de la tabla CUSTOMER, permitiendo identificar al cliente que realizó el alquiler.

El campo return_date registra la fecha y hora en que el artículo alquilado fue devuelto. Este campo es importante para rastrear la duración del alquiler y controlar los plazos de devolución de los artículos.

El campo staff_id almacena el identificador del miembro del personal responsable de gestionar el alquiler, siendo una clave foránea que hace referencia al staff_id de la tabla STAFF. Este campo facilita el seguimiento del personal que ha intervenido en cada transacción de alquiler.

Por último, el campo last_update registra la fecha y hora de la última actualización de los datos en la tabla. Este campo es útil para el control de auditoría y el seguimiento de cambios en los registros de alquiler, garantizando que los datos se mantengan actualizados.

**Tabla Inventory**
![inventory](https://github.com/AdayCuestaCorrea/P04_ADBD/blob/main/Tablas/inventory.png)

La tabla "inventory" representa y almacena información detallada sobre el inventario de películas disponibles en cada tienda del sistema. Cada artículo de inventario está identificado de manera única por el campo inventory_id, que actúa como la clave primaria de la tabla, permitiendo una referencia precisa y única para cada copia física de una película en el inventario.

El campo film_id es una clave foránea que enlaza cada artículo de inventario con un film_id en la tabla FILM, indicando el título de la película a la que pertenece cada artículo. Este campo es esencial para identificar a qué película corresponde cada unidad del inventario, lo que facilita la gestión y el seguimiento de las películas disponibles.

Para asociar cada artículo de inventario con una tienda específica, la tabla incluye el campo store_id, que es una clave foránea que hace referencia al store_id de la tabla STORE, indicando en qué tienda se encuentra cada copia de película disponible para alquiler.

El campo last_update registra la fecha y hora de la última actualización realizada en los datos del artículo de inventario. Este campo es crucial para el control de auditoría y para rastrear las actualizaciones y cambios en los registros del inventario, garantizando que la información esté siempre actualizada.

**Tabla Actor**
![actor](https://github.com/AdayCuestaCorrea/P04_ADBD/blob/main/Tablas/actor.png)

La tabla "actor" representa y almacena información detallada sobre los actores asociados a las películas en el sistema. Cada actor está identificado de manera única por el campo actor_id, que actúa como la clave primaria de la tabla, permitiendo una referencia precisa y única para cada actor registrado en el sistema.

El campo first_name almacena el primer nombre del actor, mientras que last_name guarda su apellido. Estos campos son esenciales para almacenar el nombre completo del actor y permitir su identificación de manera adecuada en el sistema.

El campo last_update registra la fecha y hora de la última actualización realizada en los datos del actor. Este campo es útil para el control de auditoría y para rastrear cualquier cambio o modificación en la información del actor, asegurando que los registros se mantengan actualizados.

**Tabla Film_actor**
![film_actor](https://github.com/AdayCuestaCorrea/P04_ADBD/blob/main/Tablas/film_actor.png)

La tabla "film_actor" representa y almacena información detallada sobre la participación de actores en películas dentro del sistema de gestión de datos cinematográficos. Cada fila de esta tabla establece una relación entre un actor y una película en la que el actor ha participado.

El campo actor_id es una clave foránea que se refiere al actor_id de la tabla ACTOR, permitiendo identificar de manera única al actor que ha participado en la película. Este campo vincula al actor con su participación en el sistema.

El campo film_id es una clave foránea que hace referencia al film_id de la tabla FILM, indicando a qué película específica ha sido asignado el actor. Esto permite asociar un actor a una o más películas en las que haya participado.

El campo last_update registra la fecha y hora de la última actualización realizada en los datos de la participación del actor en la película. Este campo es esencial para el control de auditoría y para rastrear cualquier cambio o actualización en los registros relacionados con la participación de los actores, asegurando que los datos se mantengan actualizados.

**Tabla Film**
![film](https://github.com/AdayCuestaCorrea/P04_ADBD/blob/main/Tablas/film.png)

La tabla "film" representa y almacena información detallada sobre cada película disponible en el sistema de la tienda de alquiler. Cada película está identificada de manera única por el campo film_id, que actúa como la clave primaria de la tabla, permitiendo diferenciar cada título en el inventario de películas.

El campo title almacena el título de la película, proporcionando una manera clara de identificarla dentro del sistema, mientras que el campo description ofrece una breve descripción de la trama o contenido de la película, brindando información adicional sobre la misma.

Para registrar la disponibilidad y el contexto de la película, la tabla incluye varios campos clave:

- **release_year** indica el año en que se lanzó la película, lo que ayuda a organizar y clasificar las películas por fecha de estreno.
- **language_id** es una clave foránea que se refiere al language_id de la tabla LANGUAGE, identificando el idioma en el que está disponible la película, lo cual es útil en un sistema multilingüe.

Los detalles operativos del alquiler de la película son gestionados a través de los siguientes campos:

- **rental_duration** especifica la duración en días por la cual la película puede ser alquilada.
- **rental_rate** almacena la tarifa de alquiler de la película, permitiendo calcular el costo de cada alquiler.
- **length registra** la duración de la película en minutos, lo que permite a los clientes conocer cuánto tiempo dura la película.

El campo replacement_cost es utilizado para calcular el valor de reposición de una copia de la película en caso de pérdida o daño, lo que ayuda a la tienda a gestionar el inventario de manera eficiente.

El campo rating clasifica la película según su audiencia adecuada, permitiendo determinar si la película es apta para ciertos grupos de edad o públicos específicos.

El campo special_features enumera características especiales adicionales de la película, como escenas detrás de cámaras, versiones extendidas o comentarios del director, lo que puede resultar atractivo para los clientes interesados en contenido adicional.

Por último, el campo last_update registra la fecha y hora de la última actualización realizada en los datos de la película, lo cual es útil para el control de auditoría y para rastrear las modificaciones en el inventario de películas.

**Tabla Language**
![language](https://github.com/AdayCuestaCorrea/P04_ADBD/blob/main/Tablas/language.png)

La tabla "language" representa y almacena información sobre los idiomas disponibles en el sistema de gestión de películas. Cada idioma está identificado de manera única por el campo language_id, que actúa como la clave primaria de la tabla, permitiendo diferenciar cada idioma registrado en el sistema de forma única.

El campo name almacena el nombre del idioma, especificando claramente de qué idioma se trata, como "English", "Italian", "Japanese", entre otros. Este campo es esencial para clasificar las películas y otros registros del sistema según el idioma en el que están disponibles.

Por último, el campo last_update registra la fecha y hora de la última actualización realizada en los datos del idioma. Este campo es crucial para el control de auditoría y para rastrear las modificaciones en los registros de idiomas, asegurando que los datos se mantengan actualizados.

**Tabla Film_category**
![film_category](https://github.com/AdayCuestaCorrea/P04_ADBD/blob/main/Tablas/film_category.png)

La tabla "film_category" representa y almacena información sobre la relación entre cada película y su categoría correspondiente en el sistema de la tienda de alquiler. Esta tabla permite clasificar las películas en diferentes géneros o tipos, como acción, comedia, drama, entre otros, facilitando la organización y búsqueda de títulos en el inventario.

El campo film_id es una clave foránea que hace referencia al film_id de la tabla FILM, identificando de manera única cada película en el sistema. Este campo establece la conexión entre la película y su categoría asociada.

El campo category_id es una clave foránea que se refiere al category_id de la tabla CATEGORY, indicando a qué género o tipo de película pertenece cada título. Este campo permite agrupar las películas bajo categorías específicas, como acción, comedia, etc.

Por último, el campo last_update registra la fecha y hora de la última actualización realizada en los registros de la relación entre películas y categorías. Este campo es importante para el control de auditoría y para asegurar que la información relacionada con las categorías de las películas esté siempre actualizada.

**Tabla Category**
![category](https://github.com/AdayCuestaCorrea/P04_ADBD/blob/main/Tablas/category.png)

La tabla "category" representa y almacena información detallada sobre cada categoría de películas disponible en el sistema de la tienda de alquiler. Esta tabla permite organizar y clasificar las películas en diferentes géneros o tipos, facilitando la búsqueda y gestión de títulos en el inventario.

El campo category_id es el identificador único de cada categoría, actuando como la clave primaria de la tabla. Este campo permite diferenciar y organizar los distintos géneros o tipos de películas, asegurando que cada categoría tenga una referencia única en el sistema.

El campo name almacena el nombre de la categoría, que describe el tipo de contenido de las películas agrupadas bajo esta categoría, como "Action", "Comedy", "Horror", entre otros. Este campo es esencial para clasificar las películas y permite a los usuarios buscar y filtrar títulos según el género o tipo de contenido que desean ver.

El campo last_update registra la fecha y hora de la última actualización realizada en los datos de la categoría. Este campo es útil para el control de auditoría y para rastrear los cambios realizados en los registros de categorías, asegurando que la información esté siempre actualizada.

**Revisando las tablas, observé como la tabla inventory no posee los store_id como clave foránea de store, esto está mal teniendo en cuenta como está hecho el esquema de la base de datos así que decidí arreglarlo**

```postgresql
\d inventory

                                              Table "public.inventory"
    Column    |            Type             | Collation | Nullable |                     Default                     
--------------+-----------------------------+-----------+----------+-------------------------------------------------
 inventory_id | integer                     |           | not null | nextval('inventory_inventory_id_seq'::regclass)
 film_id      | smallint                    |           | not null | 
 store_id     | smallint                    |           | not null | 
 last_update  | timestamp without time zone |           | not null | now()
Indexes:
    "inventory_pkey" PRIMARY KEY, btree (inventory_id)
    "idx_store_id_film_id" btree (store_id, film_id)
Foreign-key constraints:
    "inventory_film_id_fkey" FOREIGN KEY (film_id) REFERENCES film(film_id) ON UPDATE CASCADE ON DELETE RESTRICT
Referenced by:
    TABLE "rental" CONSTRAINT "rental_inventory_id_fkey" FOREIGN KEY (inventory_id) REFERENCES inventory(inventory_id) ON UPDATE CASCADE ON DELETE RESTRICT
Triggers:
    last_updated BEFORE UPDATE ON inventory FOR EACH ROW EXECUTE FUNCTION last_updated()
```

**Como observamos, store_id no es foreign key**

```postgresql
ALTER TABLE inventory
ADD CONSTRAINT fk_store_id FOREIGN KEY (store_id) REFERENCES store(store_id) ON UPDATE CASCADE ON DELETE RESTRICT;
```

**Ocurre lo mismo con la tabla customer, que store_id no es foreign key así que hacemos lo mismo**

```postgresql
                                             Table "public.customer"
   Column    |            Type             | Collation | Nullable |                    Default                    
-------------+-----------------------------+-----------+----------+-----------------------------------------------
 customer_id | integer                     |           | not null | nextval('customer_customer_id_seq'::regclass)
 store_id    | smallint                    |           | not null | 
 first_name  | character varying(45)       |           | not null | 
 last_name   | character varying(45)       |           | not null | 
 email       | character varying(50)       |           |          | 
 address_id  | smallint                    |           | not null | 
 activebool  | boolean                     |           | not null | true
 create_date | date                        |           | not null | 'now'::text::date
 last_update | timestamp without time zone |           |          | now()
 active      | integer                     |           |          | 
Indexes:
    "customer_pkey" PRIMARY KEY, btree (customer_id)
    "idx_fk_address_id" btree (address_id)
    "idx_fk_store_id" btree (store_id)
    "idx_last_name" btree (last_name)
Foreign-key constraints:
    "customer_address_id_fkey" FOREIGN KEY (address_id) REFERENCES address(address_id) ON UPDATE CASCADE ON DELETE RESTRICT
Referenced by:
    TABLE "payment" CONSTRAINT "payment_customer_id_fkey" FOREIGN KEY (customer_id) REFERENCES customer(customer_id) ON UPDATE CASCADE ON DELETE RESTRICT
    TABLE "rental" CONSTRAINT "rental_customer_id_fkey" FOREIGN KEY (customer_id) REFERENCES customer(customer_id) ON UPDATE CASCADE ON DELETE RESTRICT
Triggers:
    last_updated BEFORE UPDATE ON customer FOR EACH ROW EXECUTE FUNCTION last_updated()

ALTER TABLE customer
ADD CONSTRAINT fk_store_id FOREIGN KEY (store_id) REFERENCES store(store_id) ON UPDATE CASCADE ON DELETE RESTRICT;
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

**6. Haga un análisis del modelo e incluya las restricciones CHECK que considere necesarias.**
Vamos a realizar varios checks para las siguientes tablas:

- **category**: category_id debe ser mayor que 0.
```postgresql
ALTER TABLE category
ADD CONSTRAINT category_id_check CHECK (category_id > 0);
```


- **language**: language_id debe ser mayor que 0.
```postgresql
ALTER TABLE language
ADD CONSTRAINT language_id_check CHECK (language_id > 0);
```

- **actor**: actor_id debe ser mayor que 0
```postgresql
ALTER TABLE actor
ADD CONSTRAINT actor_id_check CHECK (actor_id > 0);
```

- **film**:
  - film_id debe ser mayor que 0.
    ```postgresql
    ALTER TABLE film
    ADD CONSTRAINT film_id_check CHECK (film_id > 0);
    ```
  - release_year debe ser mayor o igual que 1895 (año en el que se estrenó la primera pelicula) y el año actual.
    ```postgresql
    ALTER TABLE film
    ADD CONSTRAINT release_year_check CHECK (release_year >= 1895 AND release_year <= DATE_PART('year', CURRENT_DATE));
    ```
  - rental_duration debe ser mayor que 0 y menor que 8.
    ```postgresql
    ALTER TABLE film
    ADD CONSTRAINT rental_duration_check CHECK (rental_duration >= 1 AND rental_duration <= 7);
    ```
  - Tanto rental_rate como replacement_cost deben ser mayor o igual a 0.
    ```postgresql
    ALTER TABLE film
    ADD CONSTRAINT rental_rate_check CHECK (rental_rate > 0),
    ADD CONSTRAINT replacement_cost_check CHECK (replacement_cost > 0);
    ```
  - length debe ser mayor que 0.
    ```postgresql
    ALTER TABLE film
    ADD CONSTRAINT length_check CHECK (length > 0);
    ```
    
- **inventory**: inventory_id debe ser mayor o igual a 0.
  ```postgresql
  ALTER TABLE inventory
  ADD CONSTRAINT inventory_id_check CHECK (inventory_id > 0);
  ```
  
- **rental**:
  - rental_id debe ser mayor que 0.
  ```postgresql
  ALTER TABLE rental
  ADD CONSTRAINT rental_id_check CHECK (rental_id > 0);
  ```
  
  - rental_date no puede ser mayor que el día actual.
  ```postgresql
  ALTER TABLE rental
  ADD CONSTRAINT rental_date_check CHECK (rental_date <= CURRENT_TIMESTAMP);
  ```
  
  - return_date tiene que ser mayor o igual que rental_date.
  ```postgresql
  ALTER TABLE rental
  ADD CONSTRAINT return_date_check CHECK (return_date >= rental_date);
  ```

- **staff**: staff_id debe ser mayor que 0.
  ```postgresql
  ALTER TABLE staff
  ADD CONSTRAINT staff_id_check CHECK (staff_id > 0);
  ```
- **payment**:
  - payment_id debe ser mayor que 0.
  ```postgresql
  ALTER TABLE payment
  ADD CONSTRAINT payment_id_check CHECK (payment_id > 0);
  ```

  - amount debe ser mayor o igual que 0.
  ```postgresql
  ALTER TABLE payment
  ADD CONSTRAINT amount_check CHECK (amount >= 0);
  ```
  
  - payment_date no puede ser mayor que el timestamp actual.
  ```postgresql
  ALTER TABLE payment
  ADD CONSTRAINT payment_date_check CHECK (payment_date <= CURRENT_TIMESTAMP);
  ```
  
- **customer**:
  - customer_id debe ser mayor que 0.
  ```postgresql
  ALTER TABLE customer
  ADD CONSTRAINT customer_id_check CHECK (customer_id > 0);
  ```

  - create_date debe ser menor o igual al timestamp actual.
  ```postgresql
  ALTER TABLE customer
  ADD CONSTRAINT create_date_check CHECK (create_date <= CURRENT_DATE);
  ```

  - active debería de ser tipo booleano pues solo tiene valores de 0 y 1.
  ```postgresql
  ALTER TABLE customer
  ALTER COLUMN active TYPE boolean USING active::boolean;
  ```

- **store**: store_id debe ser mayor que 0.
  ```postgresql
  ALTER TABLE store
  ADD CONSTRAINT store_id_check CHECK (store_id > 0);
  ```

- **address**: address_id debe ser mayor que 0.
  ```postgresql
  ALTER TABLE address
  ADD CONSTRAINT address_id_check CHECK (address_id > 0);
  ```

- **country**: country_id debe ser mayor que 0.
  ```postgresql
  ALTER TABLE country
  ADD CONSTRAINT country_id_check CHECK (country_id > 0);
  ```

- **city**: city_id debe ser mayor que 0.
  ```postgresql
  ALTER TABLE city
  ADD CONSTRAINT city_id_check CHECK (city_id > 0);
  ```

**7. Explique la sentencia que aparece en la tabla customer**
```postgresql
Triggers:
    last_updated BEFORE UPDATE ON customer FOR EACH ROW EXECUTE FUNCTION last_updated()
```

**Identifique alguna tabla donde se utilice una solución similar.**

El trigger en cuestión es el siguiente:
```postgresql
SELECT prosrc
FROM pg_proc
WHERE proname = 'last_updated';
                  prosrc                  
------------------------------------------
                                         +
 BEGIN                                   +
     NEW.last_update = CURRENT_TIMESTAMP;+
     RETURN NEW;                         +
 END 
(1 row)
```

Esa sentencia nos indica que la tabla customer posee un trigger llamado last_updated que se ejecuta antes de actualizar la tabla en cuestión y se activa para cada fila llamando a la función last_updated que es la encargada de que campo last_update de la tabla customer se actualice automáticamente con el timestamp actual (que será el timestamp del momento de la actualización del registro)

**Tablas que utilizan soluciones similares:** 
- La tabla **film** posee 2 triggers, siendo uno de ellos el trigger last_updated
  ```postgresql
  Triggers:
    film_fulltext_trigger BEFORE INSERT OR UPDATE ON film FOR EACH ROW EXECUTE FUNCTION tsvector_update_trigger('fulltext', 'pg_catalog.english', 'title', 'description')
    last_updated BEFORE UPDATE ON film FOR EACH ROW EXECUTE FUNCTION last_updated()
  ```
En cuanto al otro trigger (*film_fulltext_trigger*), cuando se inserta o actualiza una fila en la tabla film, se ejecuta la función *tsvector_update_trigger* y actualiza la columna fulltext con un valor de tipo tsvector generado a partir de los valores de las columnas title y description, utilizando las reglas de tokenización del idioma especificado.
- La tabla **actor**, **address**, **category**, **city**, **country**, **film_actor**, **film_category**, **inventory**, **language**, **rental**, **staff** y **store** también poseen el trigger last_updated

**8. Construya un disparador que guarde en una nueva tabla creada por usted la fecha de cuando se insertó un nuevo registro en la tabla film**

Lo primero que tenemos que hacer es crear la tabla en la que almacenaremos los registros de inserción:

```postgresql
CREATE TABLE film_insertion_log (
    log_id SERIAL PRIMARY KEY,
    film_id INT NOT NULL,
    insertion_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```
- **log_id:** Identificador único para cada registro en esta tabla.
- **film_id:**  ID de la película que se insertó en la tabla film.
- **insertion_date:** Fecha y hora de inserción, que se establece de forma predeterminada en la fecha y hora actuales.

Ahora tenemos que crear la función para el trigger, tal que así:

```postgresql
CREATE OR REPLACE FUNCTION log_film_insertion()
RETURNS TRIGGER AS $$
BEGIN
    INSERT INTO film_insertion_log (film_id, insertion_date)
    VALUES (NEW.film_id, CURRENT_TIMESTAMP);
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;
```
- **NEW.film_id:** NEW es una referencia a la nueva fila que se está insertando en film. NEW.film_id hace referencia al ID de esa nueva película.
- **CURRENT_TIMESTAMP:** Almacena la fecha y hora actuales en la columna insertion_date.

Por último tenemos que crear el trigger:

```postgresql
CREATE TRIGGER log_film_insertion_trigger
AFTER INSERT ON film FOR EACH ROW EXECUTE FUNCTION log_film_insertion();
```

**Veamos como funciona:**

```postgresql
INSERT INTO film (film_id, language_id, title, description, release_year, rental_duration, rental_rate, length, replacement_cost, rating, special_features)
VALUES (10000, 1, 'Dummy Movie', 'This movie is really good, underrated even.', 2024, 5, 0.99, 120, 20.99, 'PG', '{"It has dinosaurs in it"}');
INSERT 0 1

SELECT * FROM film_insertion_log;
 log_id | film_id |       insertion_date       
--------+---------+----------------------------
      1 |   10000 | 2024-11-10 21:35:56.325695
(1 row)
```

**9. Construya un disparador que guarde en una nueva tabla creada por usted la fecha de cuando se eliminó un registro en la tabla film y el identificador del film.**

Empecemos entonces otra vez creando la nueva tabla que contrendrá los registros:

```postgresql
CREATE TABLE film_deletion_log (
    log_id SERIAL PRIMARY KEY,
    film_id INT NOT NULL,
    deletion_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```
- **log_id:** Identificador único para cada registro en esta tabla.
- **film_id:** ID de la película eliminada de la tabla film.
- **deletion_date:** Fecha y hora en que se eliminó el registro, con un valor predeterminado de la fecha y hora actuales.

Creemos la función del trigger:
```postgresql
CREATE OR REPLACE FUNCTION log_film_deletion()
RETURNS TRIGGER AS $$
BEGIN
    INSERT INTO film_deletion_log (film_id, deletion_date)
    VALUES (OLD.film_id, CURRENT_TIMESTAMP);
    RETURN OLD;
END;
$$ LANGUAGE plpgsql;
```
- **OLD.film_id:** OLD hace referencia a la fila que está a punto de eliminarse. OLD.film_id captura el ID de la película antes de que se elimine.
- **CURRENT_TIMESTAMP:** Almacena la fecha y hora actuales en la columna deletion_date.

Creemos el trigger:

```postgresql
CREATE TRIGGER trigger_log_film_deletion
AFTER DELETE ON film FOR EACH ROW EXECUTE FUNCTION log_film_deletion();
```

**Veamos como funciona, probemos eliminando la entrada que hicimos en el ejercicio anterior:**

```postgresql
DELETE FROM film
WHERE film_id = 10000;
DELETE 1

SELECT * FROM film_deletion_log;
 log_id | film_id |       deletion_date       
--------+---------+---------------------------
      1 |   10000 | 2024-11-10 21:43:12.14864
(1 row)
```

**10. Comente el significado y la relevancia de las secuencias.**
