 # Version Control for Database Schema Migration with Liquibase + Oracle SQL Developer

Este documento demuestra cómo gestionar cambios en el esquema de base de datos entre entornos de desarrollo y producción utilizando **Liquibase** con **Oracle SQL Developer**.

---

## Objetivo

Implementar control de versiones sobre cambios en el esquema de una base de datos Oracle mediante **Liquibase**, facilitando la migración de estructuras desde desarrollo hacia producción de forma segura, trazable y automatizada.

---

## Requisitos Previos

Antes de comenzar, asegúrate de tener:

- Oracle SQL Developer instalado y acceso a tu base de datos **dev** y **prod**
- Java instalado y configurado en tu sistema
- Liquibase instalado (versión recomendada: 4.x)
- Archivo `ojdbc8.jar` (driver JDBC para Oracle)
- Credenciales válidas para ambas bases de datos
- Estructura de carpetas de proyecto como:

## Configuración de Bases de Datos y Migraciones

### 1. Crear las Bases de Datos Oracle

Usa **Oracle SQL Developer** o tu entorno favorito para crear dos bases de datos:

- Desarrollo → `devdb`
- Producción → `proddb`

También debes crear usuarios con permisos adecuados:

- Usuario de desarrollo: `dev_user`
- Usuario de producción: `prod_user`

Asegúrate de otorgar los permisos necesarios para crear tablas, secuencias, etc.

---

### 2. Crear Archivos de Configuración de Liquibase

Crea dos archivos de configuración en el directorio raíz del proyecto.

#### `liquibase-dev.properties`

```
changeLogFile=db-migrations/changelog/changelog.xml
url=jdbc:oracle:thin:@//dev-oracle-host:1521/devdb
username=dev_user
password=your_dev_password
driver=oracle.jdbc.OracleDriver
classpath=ojdbc8.jar
```
#### `liquibase-prod.properties`
```
changeLogFile=db-migrations/changelog/changelog.xml
url=jdbc:oracle:thin:@//prod-oracle-host:1521/proddb
username=prod_user
password=your_prod_password
driver=oracle.jdbc.OracleDriver
classpath=ojdbc8.jar
```

#### `Changelog`
```
<?xml version="1.0" encoding="UTF-8"?>
<databaseChangeLog
    xmlns="http://www.liquibase.org/xml/ns/dbchangelog"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.liquibase.org/xml/ns/dbchangelog
    http://www.liquibase.org/xml/ns/dbchangelog/dbchangelog-3.8.xsd">

    <changeSet id="001-initial-schema" author="yourname">
        <sqlFile path="db-migrations/changelog/changesets/001-initial-schema.sql"/>
    </changeSet>

</databaseChangeLog>
```

#### `Changeset`
```
--liquibase formatted sql

--changeset yourname:001-initial-schema
CREATE TABLE students (
    id INTEGER GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    first_name VARCHAR2(100) NOT NULL,
    last_name VARCHAR2(100) NOT NULL,
    email VARCHAR2(150) UNIQUE NOT NULL,
    enrollment_date DATE
);

--rollback DROP TABLE students;
```

#### Ejecutar la Migración

### Verificar el estado de cambios pendientes
liquibase --defaultsFile=liquibase-dev.properties status

### Aplicar la migración en la base de datos de desarrollo
liquibase --defaultsFile=liquibase-dev.properties update

### Aplicar en Producción
liquibase --defaultsFile=liquibase-prod.properties update




