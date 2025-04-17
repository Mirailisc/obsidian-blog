---
title: Sarup M1
description: This docs is the summary of M1 database lecture
pubDate: 30 Feb 2025
category: database
---

```sql
-- ex. CREATE DATABASE asdasdsadsadsad; 
CREATE TABLE <name>( 
	<attribute> <type> <condition ex. PRIMARY KEY, NOT NULL, UNIQUE, DEFAULT = "asdasdasdasdasd"> 
	-- <ex_foreign_key> ... 1-1 UNIQUE, 1-M : NOT UNIQUE, M-M Create new table 
	
	FOREIGN_KEY <ex_foreign_key> REFERENCES <ref_table>(<id_something>) 
); 

-- Many-to-Many relationship 
CREATE TABLE <m-m_table_something>( 
	<1st_table_id> 
	<2nd_table_id> 
	
	PRIMARY KEY (1st_table_id, 2nd_table_id) 
	FOREIGN_KEY <1st_table_id> REFERENCES <1st_table>(id) 
	FOREIGN_KEY <2nd_table_id> REFERENCES <2nd_table>(id) 
); 

ALTER TABLE <table_name> 
-- ADD <attribute_name> <type> <condition> 
-- DROP COMUMN <attribute_name> 
-- MODIFY <attribute_name> <type> <condition> 
-- RENAME <old_name> <new_name> ; 
-- ex. ALTER TABLE Students ADD COLUMN gpax FLOAT NOT NULL DEFAULT 0.00; # CRUD

INSERT INTO <table_name>(<attributes>) VALUES (<values>); 
-- ex. INSERT INTO Students(firstname, lastname) VALUES ('hairy', 'asd'); 
SELECT <attributes> FROM <tabele_name> WHERE <attribute> = <value>; 
-- ex. SELECT firstname, lastname FROM Students; 

UPDATE <table_name> SET <attribute> = <value> WHERE <attribute> = <value>; 
-- ex. UPDATE Students SET firstname = "Moya", lastname = "asd" WHERE id = 1; 

DELETE FROM <table_name> WHERE <attribute> = <value>; 
-- ex. DELETE FROM Students WHERE id = 1;
```