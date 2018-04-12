# Authors : Jordi Besalú Colomer i Oriol Julià Brunet

## Introducció
  ### La nostra bbdd calcula l'any en que un cotxe ha de passar la ITV a partir del seu any de matriculació i la data actual.
  ##### Primer mirem l'antiguitat del cotxe. A partir d'aixó el separem en tres grups:
  ###### -Entre 0 i 4 anys
  ###### -Entre 4 i 10 anys
  ###### -Mes de 10 anys
  #### Un cop separat, retornem un data a partir de un sencill càlcul, on li sumem els anys depenent del grup.            

```sql
--Creem l'objecte Cotxe amb els seus atributs i funcions
CREATE OR REPLACE TYPE Cotxe AS OBJECT (
    Any_Matriculacio DATE,
    Ultim_Any_ITV DATE,
    Model VARCHAR(20),
    Matricula VARCHAR(10),
    MAP MEMBER FUNCTION getMatricula RETURN VARCHAR,
    MEMBER FUNCTION calcula_any RETURN DATE
);
/

--Creem les funcions getMatricula i calcula_any
CREATE OR REPLACE TYPE BODY Cotxe AS
    --A getMatricula simplement retornem la matricula del cotxe desitjat
    MAP MEMBER FUNCTION getMatricula RETURN VARCHAR IS
    BEGIN
        RETURN Matricula;
    END;
    --Funcio calcula_any, on mirem l'any de la ITV
    MEMBER FUNCTION calcula_any RETURN DATE IS
    BEGIN
        DECLARE 
          --Primer declarem les variables que ens faran falta
          dataActual DATE := SYSDATE;
          aux_darrera_itv DATE := COALESCE(Ultim_Any_ITV, Any_Matriculacio);
          
          --A ADD_MONTHS li pasem l'any que li volem afegir mesos i els mesos que volem afegir
          matriculacioMes2 DATE := ADD_MONTHS(Any_Matriculacio, 24);
          matriculacioMes4 DATE := ADD_MONTHS(Any_Matriculacio, 48);
          matriculacioMes10 DATE := ADD_MONTHS(Any_Matriculacio, 120);
          
          BEGIN
            --Antiguitat entre 0 i 4 anys:
            IF dataActual < matriculacioMes4 THEN 
              RETURN matriculacioMes4;
            END IF;
            
           --Antiguitat entre 4 i 10 anys:
           IF dataActual < matriculacioMes10 THEN 
            RETURN ADD_MONTHS( aux_darrera_itv, 24);
           END IF;
           
           --Més de 10 anys:
           RETURN ADD_MONTHS( aux_darrera_itv, 12);
        END;
    END;
END;
/


--Creem la taula cotxe a partir de l'objecte cotxe
CREATE TABLE Cotxes OF Cotxe;

--Insertem dades
INSERT INTO Cotxes VALUES(
'12/1/14', NULL, 'Volkswagen','1111AAA');

INSERT INTO Cotxes VALUES(
'12/1/16', NULL, 'Renault Clio','2222BBB');

INSERT INTO Cotxes VALUES(
'12/1/13', NULL, 'Nissan GTR','3333CCC');

--Fem el select per veure els anys quan han de pasar la ITV
SELECT c.ANY_MATRICULACIO, COALESCE(Ultim_Any_ITV, SYSDATE), c.calcula_any() FROM COTXES c; 
```
