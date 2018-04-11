# Autores

- Cristian Moraru
- Dmytro Holota
- Hamza Saddouki

### Codi:

```sql
-- Crear tipo para el alumno
CREATE TYPE alumne_typ AS OBJECT
(
	idno	NUMBER,
	nom		VARCHAR2(25),
	cognoms	VARCHAR2(25),
	correu	VARCHAR2(25),
	MAP MEMBER FUNCTION get_id RETURN NUMBER,
	MEMBER PROCEDURE get_notes(SELF IN OUT NOCOPY alumne_typ)
);
-- Crear el body del tipo alumno
CREATE TYPE BODY alumne_typ AS
MAP MEMBER FUNCTION get_id RETURN NUMBER IS
BEGIN
	RETURN idno;
END;
MEMBER PROCEDURE get_notes(SELF IN OUT NOCOPY alumne_typ) IS
BEGIN
	DECLARE mitjana NUMBER(4,2);
	BEGIN
		SELECT
		AVG(c.nota) INTO mitjana FROM CLASSE c
		WHERE c.alumne.idno = SELF.idno;
		DBMS_OUTPUT.PUT_LINE('La mitjana de ' || SELF.nom || ' és: ' || TO_CHAR(mitjana));
	END;
END;
END;
/
-- Crear una tabla de objetos del tipo alumno
CREATE TABLE ALUMNES_OBJ_TABLE OF alumne_typ;
-- creat una tabla para la classe
CREATE TABLE CLASSE
(
	id	NUMBER,
	alumne REF alumne_typ,
	asignatura	VARCHAR2(20),
	nota	NUMBER
);
-- insertar datos en la tabla de objectos de tipo alumno
INSERT INTO ALUMNES_OBJ_TABLE VALUES(
	alumne_typ(1, 'Hamza', 'Saddouki', 'correu')
);
INSERT INTO ALUMNES_OBJ_TABLE VALUES(

	alumne_typ(2, 'Dmytro', 'Holota', 'correu')
);
INSERT INTO ALUMNES_OBJ_TABLE VALUES(
	alumne_typ(3, 'Cristian', 'Moraru', 'correu')
);
-- insertar datos de classe
INSERT INTO CLASSE
	SELECT 1, REF(a), 'Disseny', 6
	FROM ALUMNES_OBJ_TABLE a
	WHERE a.idno = 1;
INSERT INTO CLASSE
	SELECT 2, REF(a), 'Programacion', 7
	FROM ALUMNES_OBJ_TABLE a
	WHERE a.idno = 1;
INSERT INTO CLASSE
	SELECT 3, REF(a), 'Desplegament', 8
	FROM ALUMNES_OBJ_TABLE a
	WHERE a.idno = 1;
-- comprobacion
SELECT * FROM ALUMNES_OBJ_TABLE;
SELECT * FROM CLASSE;

DECLARE 
  alumne_ref REF alumne_typ;
  alumne alumne_typ;
BEGIN 
 
  SELECT REF(a) INTO alumne_ref
    FROM ALUMNES_OBJ_TABLE a
    WHERE a.idno = 1;    
 
   select deref(alumne_ref) into alumne from dual;
   alumne.get_notes();
END;
```
