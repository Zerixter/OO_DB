#### Participantes 
###### Eric, Victor, Alex 

-------------------------------------------------------------------------------
-------------------------------------------------------------------------------
## Introducció
#### Base de dades OO de productes. Disposem d'un objecte producte amb propietats : 
* Nombre
* Cantidad
* Precio unitario
* Descuento
#### I el mètode 'precio_total', on calculem el preu total en base a la quantitat, el preu unitari i el descompte. 
#### Emmagatzem els productes en una taula d'objectes (ticket).


```sql

-- Creacion del objeto Producto con una funcion que te calcula el precio total en base 
-- al precio unitario,la cantidad y el descuento.

CREATE OR replace TYPE producto AS OBJECT(
	  nombre VARCHAR2(30),
	  cantidad NUMBER(6),
	  precio_unitario NUMBER(8,2),
	  descuento NUMBER(4,2) ,
	  MEMBER FUNCTION precio_total RETURN NUMBER
);
/

-- Añadimos la funcion para que haga el calculo y te devuelva el total 

CREATE OR REPLACE TYPE body producto AS 
   MEMBER function precio_total RETURN NUMBER IS 
	BEGIN 
		RETURN ((cantidad * precio_unitario)-((descuento/100)*(cantidad * precio_unitario)));
	END;
   END;

-- Creamos la tabla de objetos (Productos) 

CREATE TABLE ticket OF producto;

-- Añadimos valores al objeto (producto) que esta dentro de la tabla ticket 

INSERT INTO ticket values(
	producto('Ñerds',50,2,99)
);

-- Hacemos la select para mostrar todos los objetos (productos) con el calculo total 

SELECT c.nombre, c.precio_total()  FROM TICKET c ;
```
