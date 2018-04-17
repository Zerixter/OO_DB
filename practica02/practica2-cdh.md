# Autors
- Cristian Moraru
- Dmytro Holota
- Hamza Saddouki

## Objectius
- Crear una colecció d'alumnes que s'insertara dins una taula de collecions

### Codi
```sql
    -- Creació de la taula daw2
    CREATE TABLE daw2 (
        group_no NUMBER,
        alumnes_column ALUMNES_TYP )
    -- S'indica que sera una colleció
        NESTED TABLE alumnes_column store AS alumnes_column_nt;
    -- Es crea un tipus com a taula de alumne_typ
    CREATE TYPE ALUMNES_TYP AS TABLE OF ALUMNE_TYP;

    -- Inserció de dades
    INSERT INTO daw2 VALUES (
        1,
        ALUMNES_TYP( ALUMNE_TYP(10,'Dmytro','Holota','MAIL'),
                     ALUMNE_TYP(20,'Hamza','Sudoku','Mail')));

    -- Update on el qual es canvia un valor de un objecte en la colecció
    BEGIN
        UPDATE TABLE (SELECT alumnes_column FROM daw2)
        SET cognoms = 'Supoku'
        WHERE nom = 'Hamza';
    END;

    -- Update on el qual es canvia tota la colecció
    BEGIN
        UPDATE TABLE daw2
        SET alumnes_column =
        ALUMNES_TYP(
            ALUMNE_TYP(10,'Cristian','Moraru','MAIL'),
            ALUMNE_TYP(20,'Dani','Herrera','Mail')
        )
        WHERE group_no = 1;
    END;

    -- Select de la colecció
    SELECT * FROM daw2 d, TABLE(d.alumnes_column) a;
```
