CREATE TYPE taxista AS OBJECT (
	placa NUMBER,
	nombre VARCHAR2(30),
	telf NUMBER,
	mail VARCHAR2(30)
);

CREATE TYPE taxistas AS TABLE OF taxista;

CREATE TABLE info_taxi (
	num_coche NUMBER,
	info_taxista taxistas)
	NESTED TABLE info_taxista STORE AS info_taxista_nt;

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