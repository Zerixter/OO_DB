# Autors

- Cristian Moraru
- Dmytro Holota
- Hamza Saddouki


## Objectius

> Base de dades orientada a objectes amb taula Alumne i taula Notes.
> La taula Alumne ha de tenir un procediment per obtenir la mitjana de les Notes
> La taula Notes ha de tenir una referencia a la taula Alumnes.
> Cal crear un procediment per obtenir la mitjana de les notes d'un alumne

## Tecnologies que es treballen

- Taula d'objectes
- Taula amb columnes que referencien a objectes
- Taula amb funcions
- Taula amb procediments

### Codi:

```sql
-- Es crea un Tipus alumne_typ que actua com a objecte
CREATE TYPE alumne_typ AS OBJECT
(
	idno	NUMBER,
	nom		VARCHAR2(25),
	cognoms	VARCHAR2(25),
	correu	VARCHAR2(25),
	MAP MEMBER FUNCTION get_id RETURN NUMBER,
	MEMBER PROCEDURE get_mitjana(SELF IN OUT NOCOPY alumne_typ)
);
-- Crear el body del tipus alumne_typ
CREATE TYPE BODY alumne_typ AS
-- Funció que retorna la ID
MAP MEMBER FUNCTION get_id RETURN NUMBER IS
BEGIN
	RETURN idno;
END;
-- Procediment per obtenir la mitjana de les notes d'un alumne
MEMBER PROCEDURE get_mitjana(SELF IN OUT NOCOPY alumne_typ) IS
BEGIN
	DECLARE mitjana NUMBER(4,2);
	BEGIN
		SELECT
		AVG(n.nota) INTO mitjana FROM NOTES n
		WHERE n.alumne.idno = SELF.idno;
		DBMS_OUTPUT.PUT_LINE('La mitjana de ' || SELF.nom || ' és: ' || TO_CHAR(mitjana));
	END;
END;
END;
-- Crear una taula per emmagatzemar els objectes Alumne
CREATE TABLE ALUMNES_OBJ_TABLE OF alumne_typ;
-- Crear una taula per les Notes
CREATE TABLE NOTES
(
	id	NUMBER,
	alumne REF alumne_typ,
	asignatura	VARCHAR2(20),
	nota	NUMBER
);

-- Insertar Objectes Alumne a la taula ALUMNES_OBJ_TABLE
INSERT INTO ALUMNES_OBJ_TABLE VALUES(
	alumne_typ(1, 'Hamza', 'Saddouki', 'correu')
);
INSERT INTO ALUMNES_OBJ_TABLE VALUES(

	alumne_typ(2, 'Dmytro', 'Holota', 'correu')
);
INSERT INTO ALUMNES_OBJ_TABLE VALUES(
	alumne_typ(3, 'Cristian', 'Moraru', 'correu')
);

-- Insertar Notes de diferents Alumnes a la taula Notes
-- El INSERT selecciona la ID, la referencia a partir de la id del alumne, la assignatura i l'insereix a la taula
-- Insertar notes per l'alumne Hamza
INSERT INTO NOTES
	SELECT 1, REF(a), 'Disseny', 6
	FROM ALUMNES_OBJ_TABLE a
	WHERE a.idno = 1;
INSERT INTO NOTES
	SELECT 2, REF(a), 'Programacion', 7
	FROM ALUMNES_OBJ_TABLE a
	WHERE a.idno = 1;
INSERT INTO NOTES
	SELECT 3, REF(a), 'Desplegament', 8
	FROM ALUMNES_OBJ_TABLE a
	WHERE a.idno = 1;
-- Insertar notes per l'alumne Dmytro
INSERT INTO NOTES
	SELECT 1, REF(a), 'Disseny', 8
	FROM ALUMNES_OBJ_TABLE a
	WHERE a.idno = 2;
INSERT INTO NOTES
	SELECT 2, REF(a), 'Programacion', 9
	FROM ALUMNES_OBJ_TABLE a
	WHERE a.idno = 2;
INSERT INTO NOTES
	SELECT 3, REF(a), 'Desplegament', 8
	FROM ALUMNES_OBJ_TABLE a
	WHERE a.idno = 2;
-- Insertar notes per l'alumne Cristian
INSERT INTO NOTES
	SELECT 1, REF(a), 'Disseny', 6
	FROM ALUMNES_OBJ_TABLE a
	WHERE a.idno = 3;
INSERT INTO NOTES
	SELECT 2, REF(a), 'Programacion', 5
	FROM ALUMNES_OBJ_TABLE a
	WHERE a.idno = 3;
INSERT INTO NOTES
	SELECT 3, REF(a), 'Desplegament', 10
	FROM ALUMNES_OBJ_TABLE a
	WHERE a.idno = 3;
-- Comprobació

-- Primer un SELECT per comprobar que hi han Alumnes insertats a la Taula.
SELECT * FROM ALUMNES_OBJ_TABLE;
-- Després un SELECT per comprobar que les Notes s'han insertat correctament.
SELECT * FROM NOTES;
-- Declaro dos variables, una per guardar la referencia d'un alumne i un altre per guardar un objecte Alumne
DECLARE
  alumne_ref REF alumne_typ;
  alumne alumne_typ;
BEGIN
-- SELECT que agafa la referencia d'un alumne (Hamza) i l'insereix en la variable alumne_ref
	SELECT REF(a) INTO alumne_ref
    FROM ALUMNES_OBJ_TABLE a
    WHERE a.idno = 1;    
-- SELECT que agafa un objecte alumne a partir de la referencia i l'insereix en la variable alumne
	SELECT deref(alumne_ref) INTO alumne FROM dual;
-- A partir de la variable alumne crido el procediment per obtenir la mitjana.
   alumne.get_mitjana();

-- Realitzo els mateixos tres passos però amb els altres dos alumnes
	SELECT REF(a) INTO alumne_ref
  	FROM ALUMNES_OBJ_TABLE a
  	WHERE a.idno = 2;
	SELECT deref(alumne_ref) INTO alumne FROM dual;
	alumne.get_mitjana();

	SELECT REF(a) INTO alumne_ref
	FROM ALUMNES_OBJ_TABLE a
	WHERE a.idno = 3;
	SELECT deref(alumne_ref) INTO alumne FROM dual;
	alumne.get_mitjana();
END;
```
