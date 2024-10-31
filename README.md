# P04_ADBD - Modelo relacional. Vistas y disparadores
## Aday Cuesta Correa y Manuel José Sebastián Noda

**1. Realice la restauración de la base de datos alquilerdvd.tar. Observe que la base de datos no tiene un formato SQL como el empleado en actividades anteriores.**
Para realizar la restauración de la base de datos he creado una base de datos y le he asignado el contenido del fichero .tar
```bash
$ createdb alquiler
$ pg_restore -d alquiler < AlquilerPractica.tar
```

**2. Identifique las tablas, vistas y secuencias.**
