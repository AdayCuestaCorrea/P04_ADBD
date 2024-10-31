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

