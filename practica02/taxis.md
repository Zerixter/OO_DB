#### Participants 
###### Eric, Victor, Alex 

-------------------------------------------------------------------------------
-------------------------------------------------------------------------------
## Introducció
## Base de dades OO de taxistas. Disposem d'un objecte taxista amb propietats : 
* Placa
* Nombre
* Telefono
* Mail
## I la col·lecció 'Taxistas', on recopilarem tota la informacio dels taxistes.
## Emmagatzem els taxistes en una taula de col·leccions (info_taxi).
```sql

-- Creación del objeto taxista  

CREATE TYPE taxista AS OBJECT (
	placa NUMBER,
	nombre VARCHAR2(30),
	telf NUMBER,
	mail VARCHAR2(30)
);

-- Creamos la coleccion de taxistas

CREATE TYPE taxistas AS TABLE OF taxista;

-- Creamos la tabla de colecciones donde se muestra
-- la información del taxista en base al numero del coche

CREATE TABLE info_taxi (
	num_coche NUMBER,
	info_taxista taxistas)
	NESTED TABLE info_taxista STORE AS info_taxista_nt;

-- Insertamos diferentes taxistas en la colección dependiendo 
-- del coche con el que trabajan

INSERT INTO info_taxi VALUES(
	1010,
	taxistas(taxista(10, 'Juan', 111111111, '@'),
		 taxista(20, 'Alex', 222222222, '@'))
);

INSERT INTO info_taxi VALUES(
	2020,
	taxistas(taxista(30, 'Pepe', 333333333, '@'),
		 taxista(40, 'Eric', 444444444, '@'))
);

SELECT * FROM info_taxi;

```
