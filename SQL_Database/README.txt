To use the SQL statements contained in this folder, a compatible DBMS software must be found (we designed and implemented our SQL code on the Firefox extension SQLite Manager).
Afterwards, the valid SQL statements can be copied directly from the text files, and pasted into the DBMS software to have the software execute the commands.

To create the database, the Create statements (found in SQL_Create.txt) must be the first statements ran. The Create statements define the tables, attributes, and constraints of the database, and no other operation can be executed until the database is created.

The Insert statements can then be ran to populate the database tables with many entries. The Insert statements (found in SQL_Insert.txt) will have to adhere to the attribute definitions and constraints from the Create statements.

Once the database is populated, queries can finally be executed to observe the data inside. The Query statements (found in SQL_Queries.txt) will return the results of finding specific entries in all of the tables, whether the result is a single element, a list of elements, or an entirely new table.

Finally, there are additional Insert statements, as well as Delete statements, that are found in the SQL_Insert/Delete.txt, to have more examples of adding and removing members of the tables.