# Oracle PL/SQL(Procedural Language / Standard Query Language) CheatSheet 

## Contents
- [Oracle PL/SQL(Procedural Language / Standard Query Language) CheatSheet](#oracle-plsqlprocedural-language--standard-query-language-cheatsheet)
- [Contents](#contents)
- [Blocks](#blocks)
- [Variables](#variables)
- [Constant](#constant)
- [Select Into](#select-into)
- [%Type](#type)
- [Conditions](#conditions)
- [Case](#case)
- [Loops](#loops)
- [Triggers](#triggers)
- [Bulk Processing](#bulk-processing)
- [Cursors](#cursors)
  - [Cursors Attributes](#cursors-attributes)
  - [FORALL Cursors Attributes](#forall-cursors-attributes)
  - [Cursor Examples](#cursor-examples)
- [Records](#records)
- [Functions](#functions)
- [Stored Procedure](#stored-procedure)
- [Package](#package)
- [Exceptions](#exceptions)
- [Collections](#collections)
- [Object Oriented](#object-oriented)
- [Pseudo Column](#pseudo-column)
- [Date Format](#date-format)
- [Number Format](#number-format)
- [Data Conversion Functions](#data-conversion-functions)
- [Date Functions](#date-functions)
- [Group Functions](#group-functions)
- [Other Functions](#other-functions)
- [Single Character Functions](#single-character-functions)
- [Single Number Functions](#single-number-functions)
- [Regular Expression Functions](#regular-expression-functions)
- [Unit Testing (utPLSQL)](#unit-testing-utplsql)
- [Hints](#hints)
- [SQL Plus Export Hints](#sql-plus-export-hints)

## Blocks
```sql
SET SERVEROUTPUT ON;
DECLARE
 --Declaration statements

BEGIN
 --Executable statements

EXCEPTION
 --Exception handling statements

END;
```

## Variables
###### Data Types
- Scalar
> Number, Date, Boolean, Character
- Large Object
> Large Text, Picture - BFILE, BLOB, CLOB, NCLOB
- Composite
> Collections, Records
- Reference

```sql
--NUMBER(precision,scale)
v_number NUMBER(5,2) := 5.01;
v_character VARCHAR2(20) := 'test';
newyear DATE:='01-JAN-2020';
current_date DATE:=SYSDATE;
```

## Constant
```sql
DECLARE
	v_pi CONSTANT NUMBER(7,6) := 3.141592;
BEGIN
	DBMS_OUTPUT.PUT_LINE(v_pi);
END;
```

## Select Into
```sql
DECLARE
	v_last_name VARCHAR2(20);
BEGIN
	SELECT last_name INTO v_last_name FROM persons WHERE person_id = 1;
	DBMS_OUTPUT.PUT_LINE('Last name: ' || v_last_name);
END;
```

## %Type
```sql
DECLARE
	v_last_name persons.last_name%TYPE;
BEGIN
	SELECT last_name INTO v_last_name FROM persons WHERE person_id = 1;
	DBMS_OUTPUT.PUT_LINE('Last Name: ' || v_last_name);
END;
```

## Conditions
```sql
DECLARE
	v_num NUMBER := &enter_a_number;
BEGIN
	IF mod(v_num,2) = 0 THEN
	    dbms_output.put_line(v_num || ' is even');
	ELSIF mod(v_num,2) = 1 THEN
	    dbms_output.put_line(v_num || ' is odd');
	ELSE
	    dbms_output.put_line('None');
	END IF;
END;
```
## Case
```sql
set serveroutput on;
DECLARE
    a NUMBER :=65;
    b NUMBER :=2;
    arth_operation VARCHAR2(20) :='MULTIPLY';
BEGIN
    dbms_output.put_line('Program started.' );
    CASE (arth_operation)
        WHEN 'ADD' THEN 
	    dbms_output.put_line('Addition of the numbers are: '|| a+b );
        WHEN 'SUBTRACT' THEN 
	    dbms_output.put_line('Subtraction of the numbers are: '||a-b ); 
        WHEN 'MULTIPLY' THEN 
	    dbms_output.put_line('Multiplication of the numbers are: '|| a*b);
        WHEN 'DIVIDE' THEN 
	    dbms_output.put_line('Division of the numbers are:'|| a/b);
        ELSE 
	    dbms_output.put_line('No operation action defined. Invalid operation');
    END CASE;
    dbms_output.put_line('Program completed.' );
END;

--Searched case
DECLARE 
    a NUMBER :=70;
    b NUMBER :=2;
    arth_operation VARCHAR2(20) :='DIVIDE';
BEGIN
    dbms_output.put_line('Program started.' );
    CASE
        WHEN arth_operation = 'ADD' THEN 
            dbms_output.put_line('Addition of the numbers are: '||a+b );
        WHEN arth_operation = 'SUBTRACT' THEN 
            dbms_output.put_line('Subtraction of the numbers are: '|| a-b);
        WHEN arth_operation = 'MULTIPLY' THEN 
            dbms_output.put_line('Multiplication of the numbers are: '|| a*b ); 
        WHEN arth_operation = 'DIVIDE' THEN 
            dbms_output.put_line('Division of the numbers are: '|| a/b );
        ELSE 
            dbms_output.put_line('No operation action defined. Invalid operation'); 
    END CASE;
    dbms_output.put_line('Program completed.' );
END;
```

## Loops
```sql
--Simple Loop
DECLARE
	v_num number(5) := 0;
BEGIN
	loop
	    v_num := v_num + 1;
	    dbms_output.put_line('Number: ' || v_num);
	    
	    exit when v_num = 3;
	    /*
	    if v_num = 3 then
	        exit;
	    end if;
	    */
	end loop;
END;

--While Loop
DECLARE
	v_num number := 0;
BEGIN
	while v_num <= 100 loop

	    exit when v_num > 40;
	    
	    if v_num = 20 then
	        v_num := v_num + 1;
	        continue;
	    end if;
	    
	    if mod(v_num,10) = 0 then
	        dbms_output.put_line(v_num || ' can be divided by 10.');
	    end if;
	    
	    v_num := v_num + 1;
	    
	end loop;
END;

--For Loop
DECLARE
	v_num number := 0;
BEGIN
	for x in 10 .. 13 loop
	    dbms_output.put_line(x);
	end loop;

	for x in reverse 13 .. 15 loop
	    if mod(x,2) = 0 then
	        dbms_output.put_line('even: ' || x);
	    else
	        dbms_output.put_line('odd: ' || x);
	    end if;
	end loop;
END;
```

## Triggers
```sql
-- DML Triggers
CREATE OR REPLACE TRIGGER tr_persons
BEFORE INSERT OR DELETE OR UPDATE ON persons
FOR EACH ROW
ENABLE
DECLARE
	v_user varchar2(20);
BEGIN
	SELECT user INTO v_user FROM dual;
	IF INSERTING THEN
		DBMS_OUTPUT.PUT_LINE('One line inserted by ' || v_user);
	ELSIF DELETING THEN
		DBMS_OUTPUT.PUT_LINE('One line Deleted by ' || v_user);
	ELSIF UPDATING THEN
		DBMS_OUTPUT.PUT_LINE('One line Updated by ' || v_user);
	END IF;
END;

--

CREATE OR REPLACE TRIGGER persons_audit
BEFORE INSERT OR DELETE OR UPDATE ON persons
FOR EACH ROW
ENABLE
DECLARE
  v_user varchar2 (30);
  v_date  varchar2(30);
BEGIN
  SELECT user, TO_CHAR(sysdate, 'DD/MON/YYYY HH24:MI:SS') INTO v_user, v_date  FROM dual;
  IF INSERTING THEN
    INSERT INTO sh_audit (new_name,old_name, user_name, entry_date, operation) 
    VALUES(:NEW.LAST_NAME, Null , v_user, v_date, 'Insert');  
  ELSIF DELETING THEN
    INSERT INTO sh_audit (new_name,old_name, user_name, entry_date, operation)
    VALUES(NULL,:OLD.LAST_NAME, v_user, v_date, 'Delete');
  ELSIF UPDATING THEN
    INSERT INTO sh_audit (new_name,old_name, user_name, entry_date, operation) 
    VALUES(:NEW.LAST_NAME, :OLD.LAST_NAME, v_user, v_date,'Update');
  END IF;
END;

-- DDL Triggers
CREATE OR REPLACE TRIGGER db_audit_tr 
AFTER DDL ON DATABASE
BEGIN
    INSERT INTO schema_audit VALUES (
		sysdate, 
		sys_context('USERENV','CURRENT_USER'), 
		ora_dict_obj_type, 
		ora_dict_obj_name, 
		ora_sysevent);
END;

-- Instead of Triggers
CREATE VIEW vw_twotable AS
SELECT full_name, subject_name FROM persons, subjects;

CREATE OR REPLACE TRIGGER tr_Insert
INSTEAD OF INSERT ON vw_twotable
FOR EACH ROW
BEGIN
  INSERT INTO persons (full_name) VALUES (:new.full_name);
  INSERT INTO subjects (subject_name) VALUES (:new.subject_name);
END;

insert into vw_twotable values ('Caner','subject');
```

## Bulk Processing
```sql
-- bulk collect, FETCH LIMIT, FORALL, and BULK EXCEPTION HANDLEING
DECLARE
   -- Parameter for department ID
   p_dept_id employees.department_id%TYPE := 50;

   -- Cursor with parameter
   CURSOR emp_cur (dept_id IN employees.department_id%TYPE) IS
      SELECT employee_id, salary * 1.10 AS new_sal
      FROM employees
      WHERE department_id = dept_id;

   -- Record and collection types
   TYPE emp_rec IS RECORD (
      emp_id   employees.employee_id%TYPE,
      new_sal  employees.salary%TYPE
   );
   TYPE emp_tab IS TABLE OF emp_rec;

   l_emps emp_tab;
   l_limit CONSTANT PLS_INTEGER := 100;

BEGIN
   OPEN emp_cur(p_dept_id);

   LOOP
      FETCH emp_cur BULK COLLECT INTO l_emps LIMIT l_limit;
      EXIT WHEN l_emps.COUNT = 0;

      BEGIN
         FORALL i IN 1 .. l_emps.COUNT SAVE EXCEPTIONS
            UPDATE employees
            SET salary = l_emps(i).new_sal
            WHERE employee_id = l_emps(i).emp_id;

         COMMIT;
         DBMS_OUTPUT.PUT_LINE(l_emps.COUNT || ' rows updated and committed.');

      EXCEPTION
         WHEN OTHERS THEN
            IF SQLCODE = -24381 THEN  -- ORA-24381: error(s) in array DML
               DBMS_OUTPUT.PUT_LINE('Bulk DML errors occurred:');
               FOR i IN 1 .. SQL%BULK_EXCEPTIONS.COUNT LOOP
                  DBMS_OUTPUT.PUT_LINE('Error #' || i || 
                                       ' at index ' || SQL%BULK_EXCEPTIONS(i).ERROR_INDEX ||
                                       ': ORA-' || SQL%BULK_EXCEPTIONS(i).ERROR_CODE || 
                                       ' - ' || SQLERRM(-SQL%BULK_EXCEPTIONS(i).ERROR_CODE));
               END LOOP;
            ELSE
               DBMS_OUTPUT.PUT_LINE('Unexpected error during DML: ' || SQLERRM);
            END IF;

            ROLLBACK;
            DBMS_OUTPUT.PUT_LINE('Transaction rolled back due to errors in this chunk.');
      END;
   END LOOP;

   CLOSE emp_cur;

EXCEPTION
   WHEN OTHERS THEN
      DBMS_OUTPUT.PUT_LINE('Fatal error in main block: ' || SQLERRM);
      ROLLBACK;
END;
```

## Cursors
### Cursors Attributes
| Text | Description |
|---|---|
| %FOUND | Returns TRUE if the last fetch from the cursor returned a row, or FALSE if the last fetch failed to return a row. |
| %ISOPEN | If a cursor is open, cursor_name%ISOPEN returns TRUE; otherwise, it returns FALSE. |
| %NOTFOUND | Returns FALSE if the last fetch from the cursor returned a row, or TRUE if the last fetch failed to return a row. |
| %ROWCOUNT | Returns the number of rows returned so far from the cursor. Reset to zero when the cursor is opened. |
| %ROWTYPE | Allows the definition of a variable to receive a complete row from the cursor. |

### FORALL Cursors Attributes
| Text | Description |
|---|---|
| %BULK_EXCEPTIONS | Returns the number of rows processed by a FORALL operation with the SAVE_EXCEPTIONS clause. This attribute applies to the implicit cursor named SQL only. |
| %BULK_ROWCOUNT | Returns the number of rows processed by a FORALL operation. This attribute applies to the implicit cursor named SQL only. |

### Cursor Examples
```sql
declare
    v_first_name varchar2(20);
    v_last_name varchar2(20);
    Cursor test_cursor is select first_name,last_name from persons;
begin
    open test_cursor;
    loop
        fetch test_cursor into v_first_name,v_last_name;
        exit when test_cursor%NOTFOUND;
        dbms_output.put_line('Name: ' || v_first_name || ', Lastname: ' || v_last_name);
    end loop;
    close test_cursor;
end;

----

declare
    v_first_name varchar2(20);
    v_last_name varchar2(20);
    Cursor test_cursor (first_name_parameter varchar2) is 
    	select first_name,last_name from persons where first_name = first_name_parameter;
begin
    open test_cursor('caner');
    loop
        fetch test_cursor into v_first_name,v_last_name;
        exit when test_cursor%NOTFOUND;
        dbms_output.put_line('Name: ' || v_first_name || ', Lastname: ' || v_last_name);
    end loop;
    close test_cursor;
end;

----

declare
    v_first_name varchar2(20);
    v_last_name varchar2(20);
    Cursor test_cursor (first_name_parameter varchar2 := 'caner') is 
    	select first_name,last_name from persons where first_name = first_name_parameter;
begin
    open test_cursor;
    loop
        fetch test_cursor into v_first_name,v_last_name;
        exit when test_cursor%NOTFOUND;
        dbms_output.put_line('Name: ' || v_first_name || ', Lastname: ' || v_last_name);
    end loop;
    close test_cursor;
end;

--for
declare
    Cursor test_cursor is select first_name,last_name from persons;
begin
    for obj in test_cursor
    loop 
        dbms_output.put_line('Name: ' || obj.first_name || ', Lastname: ' || obj.last_name);
    end loop;
end;

--for parameter
declare
    Cursor test_cursor (first_name_parameter varchar2 := 'can') is 
    	select first_name,last_name from persons where first_name = first_name_parameter;
begin
    for obj in test_cursor('caner')
    loop 
        dbms_output.put_line('Name: ' || obj.first_name || ', Lastname: ' || obj.last_name);
    end loop;
end;

```

## Records
```sql
--table based
declare 
    v_person persons%ROWTYPE;
begin
    select * into v_person from persons where PERSON_ID = 2;
    dbms_output.put_line('Name: ' || v_person.first_name || ', Lastname: ' || v_person.last_name);
end;

--

declare 
    v_person persons%ROWTYPE;
begin
    select first_name,last_name into v_person.first_name,v_person.last_name 
    	from persons where PERSON_ID = 2;
    dbms_output.put_line('Name: ' || v_person.first_name || ', Lastname: ' || v_person.last_name);
end;

--cursor based record
declare
    Cursor test_cursor is select first_name,last_name from persons where person_id = 2;
    v_person test_cursor%rowtype;
begin
    open test_cursor;
    fetch test_cursor into v_person;
    dbms_output.put_line('Name: ' || v_person.first_name || ', Lastname: ' || v_person.last_name);
    close test_cursor;
end;

--

declare
    Cursor test_cursor is select first_name,last_name from persons;
    v_person test_cursor%rowtype;
begin
    open test_cursor;
    loop
        fetch test_cursor into v_person;
        exit when test_cursor%NOTFOUND;
        dbms_output.put_line('Name: ' || v_person.first_name || ', Lastname: ' || v_person.last_name);
    end loop;
    close test_cursor;
end;

--user based

declare
    type rv_person is record(
        f_name varchar2(20),
        l_name persons.last_name%type
    );
    v_person rv_person;
begin
    select first_name,last_name into v_person.f_name,v_person.l_name from persons where person_id = 2;
    dbms_output.put_line('Name: ' || v_person.f_name || ', Lastname: ' || v_person.l_name);
end;
```

## Functions
```sql
CREATE OR REPLACE FUNCTION circle_area (radius NUMBER) 
RETURN NUMBER IS
--Declare a constant and a variable
pi      CONSTANT NUMBER(7,3) := 3.141;
area    NUMBER(7,3);
BEGIN
  --Area of Circle pi*r*r;
  area := pi * (radius * radius);
  RETURN area; 
END;

BEGIN
	dbms_output.put_line('Alan: ' || circle_area(10));
END;
```

## Stored Procedure
```sql
create or replace procedure pr_test is
    v_name varchar(20) := 'Caner';
    v_city varchar(20) := 'Istanbul';
begin
    dbms_output.put_line(v_name || ',' || v_city);
end pr_test;
--
execute pr_test;
--
begin
    pr_test;
end;

----

create or replace procedure pr_test_param(v_name varchar2 default 'caz') 
is
    v_city varchar(20) := 'Istanbul';
begin
    dbms_output.put_line(v_name || ',' || v_city);
end pr_test_param;
--
execute pr_test_param(v_name => 'cam');

----

create or replace procedure pr_test_param(v_name varchar2) 
is
    v_city varchar(20) := 'Istanbul';
begin
    dbms_output.put_line(v_name || ',' || v_city);
end pr_test_param;
--
execute pr_test_param('Caner');
--
begin
    pr_test_param('Caner');
end;
```

## Package
```sql
CREATE OR REPLACE PACKAGE pkg_person IS
  FUNCTION get_name (v_name VARCHAR2) RETURN VARCHAR2;
  PROCEDURE proc_update_lastname(p_id NUMBER, l_name VARCHAR2);
END pkg_person;

--Package Body
CREATE OR REPLACE PACKAGE BODY pkg_person IS
  --Function Implimentation
  FUNCTION get_name (v_name VARCHAR2) RETURN VARCHAR2 IS
    BEGIN
      RETURN v_name;
    END get_name;
   
  --Procedure Implimentation
   PROCEDURE proc_update_lastname(p_id NUMBER, l_name VARCHAR2) IS
     BEGIN
      UPDATE persons SET last_name = l_name where person_id = p_id;
     END;
   
END pkg_person;

--
begin
    dbms_output.put_line(pkg_person.get_name('Caner'));
end;
execute pkg_person.proc_update_lastname(2,'new lastname');
```

## Exceptions
| Text | Description |
|---|---|
| ACCESS_INTO_NULL | ORA-06530 Your program attempts to assign values to the attributes of an uninitialized (atomically null) object. |
| COLLECTION_IS_NULL | ORA-06531 Your program attempts to apply collection methods other than EXISTS to an uninitialized (atomically null) nested table or varray, or the program attempts to assign values to the elements of an uninitialized nested table or varray. |
| CURSOR_ALREADY_OPEN | ORA-06511 Your program attempts to open an already open cursor. A cursor must be closed before it can be reopened.  A cursor FOR loop automatically opens the cursor to which it refers. So, your program cannot open that cursor inside the loop. |
| DUP_VAL_ON_INDEX | ORA-00001 Your program attempts to store duplicate values in a database column that is constrained by a unique index. |
| INVALID_CURSOR | ORA-01001 Your program attempts an illegal cursor operation such as closing an unopened cursor. |
| INVALID_NUMBER | ORA-01722 In a SQL statement, the conversion of a character string into a number fails because the string does not represent a valid number. (In procedural statements, VALUE_ERROR is raised.) |
| LOGIN_DENIED | ORA-01017 Your program attempts to log on to Oracle with an invalid username and/or password. |
| NO_DATA_FOUND | ORA-01403 A SELECT INTO statement returns no rows, or your program references a deleted element in a nested table or an uninitialized element in an index-by table.  SQL aggregate functions such as AVG and SUM always return a value or a null. So, a SELECT INTO statement that calls a aggregate function will never raise NO_DATA_FOUND. The FETCH statement is expected to return no rows eventually, so when that happens, no exception is raised. |
| NOT_LOGGED_ON | ORA-01012 Your program issues a database call without being connected to Oracle. |
| PROGRAM_ERROR | ORA-06501 PL/SQL has an internal problem. |
| ROWTYPE_MISMATCH | ORA-06504 The host cursor variable and PL/SQL cursor variable involved in an assignment have incompatible return types. For example, when an open host cursor variable is passed to a stored subprogram, the return types of the actual and formal parameters must be compatible. |
| SELF_IS_NULL | ORA-30625 Your program attempts to call a MEMBER method on a null instance. That is, the built-in parameter SELF (which is always the first parameter passed to a MEMBER method) is null. |
| STORAGE_ERROR | ORA-06500 PL/SQL runs out of memory or memory has been corrupted. |
| SUBSCRIPT_BEYOND_COUNT | ORA-06533 Your program references a nested table or varray element using an index number larger than the number of elements in the collection. |
| SUBSCRIPT_OUTSIDE_LIMIT | ORA-06532 Your program references a nested table or varray element using an index number (-1 for example) that is outside the legal range. |
| SYS_INVALID_ROWID | ORA-01410 The conversion of a character string into a universal rowid fails because the character string does not represent a valid rowid. |
| TIMEOUT_ON_RESOURCE | ORA-00051 A time-out occurs while Oracle is waiting for a resource. |
| TOO_MANY_ROWS | ORA-01422 A SELECT INTO statement returns more than one row. |
| VALUE_ERROR | ORA-06502 An arithmetic, conversion, truncation, or size-constraint error occurs. For example, when your program selects a column value into a character variable, if the value is longer than the declared length of the variable, PL/SQL aborts the assignment and raises VALUE_ERROR. In procedural statements, VALUE_ERROR is raised if the conversion of a character string into a number fails. (In SQL statements, INVALID_NUMBER is raised.) |
| ZERO_DIVIDE | ORA-01476 Your program attempts to divide a number by zero. |

```sql
accept p_divisor number prompt 'Enter divisor';
declare
    v_divided number := 24;
    v_divisor number := &p_divisor;
    v_result number;
    ex_four exception;
    pragma exception_init(ex_four,-20001); --20000 , 20999
begin
    if v_divisor = 4 then
        raise ex_four;
    end if;
    
    if v_divisor = 5 then
        raise_application_error(-20001,'div five');
    end if;
    
    if v_divisor = 6 then
        raise_application_error(-20002,'div six');
    end if;
    
    v_result := v_divided/v_divisor;
    
    exception 
        when ex_four then  --user defined
            dbms_output.put_line('Div four');
            dbms_output.put_line(SQLERRM);
        when ZERO_DIVIDE then --system defined
            dbms_output.put_line('Div zero');
        when OTHERS then
            dbms_output.put_line('Other exception');
            dbms_output.put_line(SQLERRM);
end;
```

## Collections
```sql
--Nested table
DECLARE
  TYPE my_nested_table IS TABLE OF number;
  var_nt  my_nested_table :=  my_nested_table (5,12,17,66,44,88,25,45,65);
BEGIN
  FOR i IN 1..var_nt.COUNT
  LOOP
    DBMS_OUTPUT.PUT_LINE ('Value stored at index '||i||' is '||var_nt(i));
  END LOOP;
END;

--VARRAY
DECLARE
    TYPE inBlock_vry IS VARRAY (5) OF NUMBER;
    vry_obj inBlock_vry  :=  inBlock_vry(); --inBlock_vry(null,null,null,null,null);
BEGIN
    --vry_obj.EXTEND(5);
    FOR i IN 1 .. vry_obj.LIMIT
    LOOP
        vry_obj.EXTEND;
        vry_obj (i):= 10*i;    
        DBMS_OUTPUT.PUT_LINE (vry_obj (i));    
    END LOOP;
END;

--Associative Array(dictionary)
DECLARE
    TYPE books IS TABLE OF NUMBER INDEX BY VARCHAR2 (20);
    Isbn Books;
BEGIN
        -- How to insert data into the associative array 
        isbn('Oracle Database') := 1122;
        isbn('MySQL') := 6543;
        DBMS_OUTPUT.PUT_LINE('Value Before Updation '||isbn('MySQL'));
 
        -- How to update data of associative array.
        isbn('MySQL') := 2222;
     
        -- how to retrieve data using key from associative array.  
        DBMS_OUTPUT.PUT_LINE('Value After Updation '||isbn('MySQL'));
END;

--

DECLARE
    TYPE books IS TABLE OF NUMBER INDEX BY VARCHAR2 (20);
    Isbn Books;
    flag varchar2(20);
BEGIN
    isbn('Oracle Database') := 1122;
    isbn('MySQL') := 6543;
    isbn('MySQL') := 2222;
    flag := isbn.FIRST;
    while flag is not null
    loop
        DBMS_OUTPUT.PUT_LINE('Key -> '||flag||'Value -> '||isbn(flag));
        flag := isbn.NEXT(flag);
    end loop;
END;

-----Collection Methods
--Count
DECLARE
    TYPE my_nested_table IS TABLE OF number;
    var_nt my_nested_table := my_nested_table (5,12,17,66,44,88,25,45,65);
BEGIN
    DBMS_OUTPUT.PUT_LINE ('The Size of the Nested Table is ' ||var_nt.count);
END;


--exists
DECLARE
    TYPE my_nested_table IS TABLE OF VARCHAR2 (20); 
	col_var_1   my_nested_table := my_nested_table('Super Man','Iron Man','Bat Man');
BEGIN
    IF col_var_1.EXISTS (4) THEN
        DBMS_OUTPUT.PUT_LINE ('Hey we found '||col_var_1 (1));
    ELSE
        DBMS_OUTPUT.PUT_LINE ('Sorry, no data at this INDEX');
        col_var_1.EXTEND;
        col_var_1(4) := 'Spiderman';
    END IF;
    IF col_var_1.EXISTS (4) THEN
        DBMS_OUTPUT.PUT_LINE ('New data at index 4 '||col_var_1 (4));
    end if;
END;

--first and last
SET SERVEROUTPUT ON;
DECLARE
    TYPE nt_tab IS TABLE OF NUMBER;
    col_var nt_tab := nt_tab(10, 20, 30, 40, 50);
BEGIN
    col_var.DELETE(1);
    col_var.TRIM;
    DBMS_OUTPUT.PUT_LINE ('First Index of the Nested table is ' || col_var.FIRST);
    DBMS_OUTPUT.PUT_LINE ('Last Index of the Nested table is ' || col_var.LAST);

    DBMS_OUTPUT.PUT_LINE ('Value stored at First Index is ' || col_var(col_var.FIRST));
    DBMS_OUTPUT.PUT_LINE ('Value stored at First Index is ' || col_var(col_var.LAST));
END;

--limit
DECLARE
    TYPE inBlock_vry IS VARRAY (5) OF NUMBER;
    vry_obj inBlock_vry := inBlock_vry();
BEGIN
    DBMS_OUTPUT.PUT_LINE ('Total Indexes '||vry_obj.LIMIT);
END;

--
DECLARE
    --Create VARRAY of 5 element
    TYPE inblock_vry IS
        VARRAY ( 5 ) OF NUMBER;
    vry_obj   inblock_vry := inblock_vry ();
BEGIN
    vry_obj.extend;
    vry_obj(1) := 10 * 2; 
    dbms_output.put_line('Total Number of Index ' || vry_obj.limit);
    dbms_output.put_line('Total Number of Index which are occupied ' || vry_obj.count);
END;

-- Prior and Next
DECLARE
    TYPE my_nested_table IS
        TABLE OF NUMBER;
    var_nt   my_nested_table := my_nested_table(5,12,17,66,44,88,25,45,65);
BEGIN
        var_nt.DELETE(2);
        dbms_output.put_line('Index prior to index 3 is '||var_nt.PRIOR(3)); 
        dbms_output.put_line('Value before 3rd Index is '||var_nt(var_nt.PRIOR(3))); 
END;
--
DECLARE
    TYPE my_nested_table IS
        TABLE OF NUMBER;
    var_nt   my_nested_table := my_nested_table(5,12,17,66,44,88,25,45,65);
BEGIN
        dbms_output.put_line('Next Higher Index to index 3 is '||var_nt.NEXT(3)); 
        dbms_output.put_line('Value after 3rd Index is '||var_nt(var_nt.NEXT(3))); 
END;

--Delete
DECLARE
    TYPE my_nested_table IS
        TABLE OF NUMBER;
    var_nt my_nested_table := my_nested_table(2,4,6,8,10,12,14,16,18,20);
BEGIN
 
    --Delete Range
    var_nt.DELETE(2,6);
    FOR i IN 1..var_nt.LAST LOOP
        IF var_nt.EXISTS(i) THEN
            DBMS_OUTPUT.PUT_LINE('Value at Index ['||i||'] is '|| var_nt(i));
        END IF;
    END LOOP;
END;

--extend
DECLARE
    TYPE my_nestedTable IS TABLE OF number;
    nt_obj  my_nestedTable := my_nestedTable();
BEGIN
    nt_obj.EXTEND;
    nt_obj(1) := 28;
    nt_obj.EXTEND(3);
    nt_obj(2) := 10;
    nt_obj(3) := 20;
    nt_obj(4) := 30;
    DBMS_OUTPUT.PUT_LINE ('Data at index 1 is '||nt_obj(1));
    DBMS_OUTPUT.PUT_LINE ('Data at index 2 is '||nt_obj(2)); 
    DBMS_OUTPUT.PUT_LINE ('Data at index 3 is '||nt_obj(3));
    DBMS_OUTPUT.PUT_LINE ('Data at index 4 is '||nt_obj(4));
    nt_obj.EXTEND(2,4);
    DBMS_OUTPUT.PUT_LINE ('Data at index 5 is '||nt_obj(5));
    DBMS_OUTPUT.PUT_LINE ('Data at index 6 is '||nt_obj(6));
END;

--TRIM
DECLARE
 TYPE inBlock_vry IS VARRAY (5) OF NUMBER;
 vry_obj inBlock_vry := inBlock_vry(1, 2, 3, 4, 5);
BEGIN
    --TRIM without parameter
    vry_obj.TRIM;
    DBMS_OUTPUT.PUT_LINE ('After TRIM procedure');
    FOR i IN 1..vry_obj.COUNT
    LOOP
        DBMS_OUTPUT.PUT_LINE (vry_obj(i));
    END LOOP;
    --TRIM with Parameter
    vry_obj.TRIM (2);
    DBMS_OUTPUT.PUT_LINE ('After TRIM procedure');
    FOR i IN 1..vry_obj.COUNT
    LOOP
        DBMS_OUTPUT.PUT_LINE (vry_obj(i));
    END LOOP;
END;
--
DECLARE
    TYPE my_nestedTable IS TABLE OF number;
    nt_obj  my_nestedTable := my_nestedTable(1,2,3,4,5);
BEGIN
    nt_obj.TRIM (3);
    DBMS_OUTPUT.PUT_LINE ('After TRIM procedure');
    FOR i IN 1..nt_obj.COUNT
    LOOP
        DBMS_OUTPUT.PUT_LINE (nt_obj(i));
    END LOOP;
END;
```

## Object Oriented
```sql
CREATE OR REPLACE TYPE Worker AS OBJECT (
    v_id number(3),
    v_name varchar2(10),
    v_last_name varchar(10),
    v_email varchar(20),
    member procedure display,
    member function getName return varchar2,
    static procedure displaySquare(v_num number)
); 

CREATE OR REPLACE TYPE BODY Worker AS
    MEMBER PROCEDURE display IS
    BEGIN
        DBMS_OUTPUT.put_line('id: '||SELF.v_id);
        DBMS_OUTPUT.put_line('name: '||SELF.v_name);
        DBMS_OUTPUT.put_line('lastName : '||SELF.v_last_name);
        DBMS_OUTPUT.put_line('mail: '||SELF.v_email);
    END;
    MEMBER FUNCTION getName RETURN VARCHAR2 IS
    BEGIN
        RETURN SELF.v_name || ' ' || SELF.v_last_name;
    END;
    STATIC PROCEDURE displaySquare(v_num number) IS
    BEGIN
        DBMS_OUTPUT.put_line('Square : '||v_num);
    END;
END;

DECLARE
    v_person Worker := new Worker(1, 'Caner', 'lastName', 'mail@.com');	--constructor
BEGIN
    DBMS_OUTPUT.put_line('Name: '||v_person.getName());
    v_person.display;
    Worker.displaySquare(2);
END;
```

## Pseudo Column
| Pseudo Column                         | Description |
|---------------------------------------|-------------|
| ROWNUM                                | Returns a number indicating the order in which a row was selected from a table. Useful for limiting rows.
| ROWID                                 | Returns the unique address of a row in the database.
| LEVEL                                 | Used with hierarchical queries to indicate the level of a node in a tree structure.
| SYSDATE                               | Returns the current date and time from the server.
| SYSTIMESTAMP                          | Returns the current timestamp with time zone.
| USER                                  | Returns the name of the current user.
| UID                                   | Returns the numeric ID of the current user.
| CURRVAL                               | Returns the current value of a sequence for the session.
| NEXTVAL                               | Returns the next value of a sequence.
| ROWDEPENDENCIES                       | Used in flashback queries to track row-level changes.
| ORA_ROWSCN                            | Returns the system change number (SCN) of the last change to a row.
| VERSIONS_STARTSCN, VERSIONS_ENDSCN    | Used in flashback queries to show the SCN range during which a row version was valid.
| CONNECT_BY_ISCYCLE | For each row returned by a hierarchial query, CONNECT_BY_ISCYCLE returns 1 if the current row has a child which is also its ancestor. Otherwise it returns 0.  CONNECT_BY_ISCYCLE will only be useful if you have specified NOCYCLE in the CONNECT BY clause. |
| CONNECT_BY_ISLEAF | For each row returned by a hierarchial query, CONNECT_BY_ISLEAF returns 1 if the row is a leaf of the tree defined by the CONNECT BY condition (i.e. has no children). Otherwise it returns 0. |


## Date Format
| Element&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;| Description |
|---|---|
| AD or A.D. | AD indicator with or without periods. |
| AM or A.M. | Meridian indicator with or without periods. |
| BC or B.C. | BC indicator with or without periods. |
| CC or SCC | One greater than the first two digits of a four-digit year; "S" prefixes BC dates with "-". For example, '20' from '1900'. |
| D | Day of week (1-7). |
| DAY | Name of day, padded with blanks to length of 9 characters. |
| DD | Day of month (1-31). |
| DDD | Day of year (1-366). |
| DY | Abbreviated name of day. |
| E | Abbreviated era name (Japanese Imperial, ROC Official, and Thai Buddha calendars). |
| EE | Full era name (Japanese Imperial, ROC Official, and Thai Buddha calendars). |
| FF | Fractional seconds; no radix printed (see X format element below). Example: 'HH:MI:SS.FF'. |
| HH | Hour of day (1-12). |
| HH12 | Hour of day (1-12). |
| HH24 | Hour of day (0-23). |
| IW | Week of year (1-52 or 1-53) based on the ISO standard. |
| IYY or IY or I | Last 3, 2, or 1 digit(s) of ISO year. |
| IYYY | 4-digit year based on the ISO standard. |
| J | Julian day; the number of days since January 1, 4712 BC. Number specified with 'J' must be integers. |
| MI | Minute (0-59). |
| MM | Month (01-12; JAN = 01). |
| MON | Abbreviated name of month. |
| MONTH | Name of month, padded with blanks to length of 9 characters. |
| PM or P.M. | Meridian indicator with or without periods. |
| Q | Quarter of year (1, 2, 3, 4; JAN-MAR = 1). |
| RM | Roman numeral month (I-XII; JAN = I). |
| RR | Given a year with 2 digits: If the year is <50 and the last 2 digits of the current year are >=50, the first 2 digits of the returned year are 1 greater than the first 2 digits of the current year. If the year is >=50 and the last 2 digits of the current year are <50, the first 2 digits of the returned year are 1 less than the first 2 digits of the current year. |
| RRRR | Round year. Accepts either 4-digit or 2-digit input. If 2-digit, provides the same return as RR. If you don't want this functionality, simply enter the 4-digit year. |
| SS | Second (0-59). |
| SSSSS | Seconds past midnight (0-86399). |
| TZD | Daylight savings information. The TZD value is an abbreviated time zone string with daylight savings information. It must correspond with the region specified in TZR. Example: PST (for US/Pacific standard time); PDT (for US/Pacific daylight time). |
| TZH | Time zone hour. (See TZM format element below.) Example: 'HH:MI:SS.FFTZH:TZM'. |
| TZM | Time zone minute. (See TZH format element above.) Example: 'HH:MI:SS.FFTZH:TZM'. |
| TZR | Time zone region information. The value must be one of the time zone regions supported in the database. Example: US/Pacific |
| WW | Week of year (1-53) where week 1 starts on the first day of the year and continues to the seventh day of the year. |
| W | Week of month (1-5) where week 1 starts on the first day of the month and ends on the seventh. |
| X | Local radix character. Example: 'HH:MI:SSXFF'. |
| Y,YYY | Year with comma in this position. |
| YEAR or SYEAR | Year, spelled out; "S" prefixes BC dates with "-". |
| YYYY or SYYYY | 4-digit year; "S" prefixes BC dates with "-". |
| YYY or YY or Y | Last 3, 2, or 1 digit(s) of year. |

## Number Format
| Text&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;| Description |
|---|---|
| 9,999 | Returns a comma in the specified position. You can specify multiple commas in a number format model. Restrictions: A comma element cannot begin a number format model. A comma cannot appear to the right of a decimal character or period in a number format model. |
| 99.99 | Returns a decimal point, which is a period (.) in the specified position. Restriction: You can specify only one period in a number format model. |
| $9999 | Returns value with a leading dollar sign. |
| 0999 | Returns leading zeros. |
| 9990 | Returns trailing zeros. |
| 9999 | Returns value with the specified number of digits with a leading space if positive or with a leading minus if negative. Leading zeros are blank, except for a zero value, which returns a zero for the integer part of the fixed-point number. |
| B9999 | Returns blanks for the integer part of a fixed-point number when the integer part is zero (regardless of "0"s in the format model). |
| C999 | Returns in the specified position the ISO currency symbol (the current value of the NLS_ISO_CURRENCY parameter). |
| 99D99 | Returns in the specified position the decimal character, which is the current value of the NLS_NUMERIC_CHARACTER parameter. The default is a period (.). Restriction: You can specify only one decimal character in a number format model. |
| 9.9EEEE | Returns a value using in scientific notation. |
| FM90.9 | Returns a value with no leading or trailing blanks. |
| 9G999 | Returns in the specified position the group separator (the current value of the NLS_NUMERIC_CHARACTER parameter). You can specify multiple group separators in a number format model. Restriction: A group separator cannot appear to the right of a decimal character or period in a number format model. |
| L999 | Returns in the specified position the local currency symbol (the current value of the NLS_CURRENCY parameter). |
| 9999MI | Returns negative value with a trailing minus sign (-). Returns positive value with a trailing blank. Restriction: The MI format element can appear only in the last position of a number format model. |
| 9999PR | Returns negative value in <angle brackets>. Returns positive value with a leading and trailing blank. Restriction: The PR format element can appear only in the last position of a number format model. |
| RN | Returns a value as Roman numerals in uppercase. Value can be an integer between 1 and 3999. |
| rn | Returns a value as Roman numerals in lowercase. Value can be an integer between 1 and 3999. |
| S9999 | Returns negative value with a leading minus sign (-). Returns negative value with a trailing minus sign (-). Returns positive value with a trailing plus sign (+). Restriction: The S format element can appear only in the first or last position of a number format model. |
| 9999S | Returns positive value with a leading plus sign (+). Returns negative value with a trailing minus sign (-). Returns positive value with a trailing plus sign (+). Restriction: The S format element can appear only in the first or last position of a number format model. |
| TM | "Text minimum". Returns (in decimal output) the smallest number of characters possible. This element is case-insensitive. The default is TM9, which returns the number in fixed notation unless the output exceeds 64 characters. If output exceeds 64 characters, Oracle automatically returns the number in scientific notation. Restrictions: You cannot precede this element with any other element. You can follow this element only with 9 or E (only one) or e (only one). |
| U9999 | Returns in the specified position the "Euro" (or other) dual currency symbol (the current value of the NLS_DUAL_CURRENCY parameter). |
| 999V99 | Returns a value multiplied by 10n (and if necessary, round it up), where n is the number of 9's after the "V". |
| XXXX | Returns the hexadecimal value of the specified number of digits. If the specified number is not an integer, Oracle rounds it to an integer. Restrictions: This element accepts only positive values or 0. Negative values return an error. You can precede this element only with 0 (which returns leading zeroes) or FM. Any other elements return an error. If you specify neither 0 nor FM with X, the return always has 1 leading blank. |
| TO_CHAR(number, 'DATE') | Converts a numeric Julian date to a date in the format "MM/DD/YY" |

## Data Conversion Functions
| Text | Description |
|---|---|
| ASCIISTR('string') | ASCIISTR takes as its argument a string in any character set and returns an ASCII string in the database character set. The value returned contains only characters that appear in SQL, plus the forward slash (/). |
| BIN_TO_NUM(expr) | BIN_TO_NUM converts a bit vector to its equivalent number. Each argument to this function represents a bit in the bit vector. Each expr must evaluate to 0 or 1. This function returns Oracle NUMBER. BIN_TO_NUM is useful in data warehousing applications for selecting groups of interest from a materialized view using grouping sets. |
| CAST(expr AS typename) | Converts expression into the specified datatype.  Example: convert data from a NUMBER to a CHAR, or from a nested table to a varray. |
| CAST(MULTISET(subquery)AS typename) | Converts the results of the subquery into the collection type specified by typename |
| CHARTOROWID(char) | Converts a CHAR value to ROWID datatype. Used to reverse a rowid that was converted to CHAR via ROWIDTOCHAR function. |
| COMPOSE('string') | COMPOSE takes as its argument a string in any datatype, and returns a unicode string in its fully normalized form in the same character set as the input. For example, an \o\" codepoint qualified by an umlaut codepoint will be returned as the o-umlaut codepoint." |
| CONVERT(char1, dest_char_set [,source_char_set) | Returns Char1 converted to dest_char_set.  Source_char_set defaults to the current char set. E.g.:  Convert('sample', 'US7ASCII', 'WE8DEC') |
| DECOMPOSE('string') | DECOMPOSE takes as its argument a string in any datatype, and returns a unicode string after canonical decomposition in the same character set as the input. For example, an o-umlaut codepoint will be returned as the \o\" codepoint followed by an umlaut codepoint." |
| FROM_TZ(timestamp, timezone) | Converts a TIMESTAMP to a TIMESTAMP WITH TIME ZONE |
| HEXTORAW(char) | HEXTORAW converts char containing hexadecimal digits in the CHAR, VARCHAR2, NCHAR, or NVARCHAR2 character set to a raw value. |
| NUMTODSINTERVAL(n, 'char_expr') | NUMTODSINTERVAL converts n to an INTERVAL DAY TO SECOND literal. n can be a number or an expression resolving to a number. char_expr can be of CHAR, VARCHAR2, NCHAR, or NVARCHAR2 datatype. The value for char_expr specifies the unit of n and must resolve to one of the following string values: 'DAY', 'HOUR', 'MINUTE', 'SECOND' - char_expr is case insensitive. Leading and trailing values within the parentheses are ignored. By default, precision of the return is 9. |
| NUMTOYMINTERVAL(n, 'char_expr') | NUMTOYMINTERVAL converts number n to an INTERVAL YEAR TO MONTH literal. n can be a number or an expression resolving to a number. char_expr can be of CHAR, VARCHAR2, NCHAR, or NVARCHAR2 datatype. The value for char_expr specifies the unit of n, and must resolve to one of the following string values: 'YEAR', 'MONTH' - char_expr is case insensitive. Leading and trailing values within the parentheses are ignored. By default, precision of the return is 9. |
| RAWTOHEX(raw) | RAWTOHEX converts raw to a character value containing its hexadecimal equivalent. The raw argument can be either RAW or BLOB datatype. |
| RAWTONHEX(raw) | The RAWTONHEX function converts raw to an NVARCHAR2 character value containing its hexadecimal equivalent. |
| ROWIDTOCHAR(rowid) | ROWIDTOCHAR converts a rowid value to VARCHAR2 datatype. The result of this conversion is always 18 characters long. |
| ROWIDTONCHAR(rowid) | The ROWIDTONCHAR function converts a rowid value to NVARCHAR2 datatype. The result of this conversion is always 18 characters long. |
| SCN_TO_TIMESTAMP(scn) | Returns the approximate timestamp associated with the specified SCN (system change number). The returned value is of TIMESTAMP datatype. It can be used to associate a timestamp with the most recent change to a row, via the ORA_SCN pseudocolumn.  Oracle 10g only. |
| TO_CHAR(date1 [,fmt]) | Date1 is converted to char type in the format specified by fmt. |
| TO_CHAR(sysdate) | Returns '27-FEB-97' |
| TO_CHAR(sysdate, 'MM/DD/YY') | Returns '02/27/97' |
| TO_CHAR(sysdate, 'J') | Returns the Julian date '2450507' |
| TO_CHAR(sysdate,'Mon') | Returns the short Month name 'Feb' |
| TO_CHAR(sysdate, 'Month') | Returns the long Month name 'February' |
| TO_CHAR(sysdate, 'DDD') | Returns the 3 digit day of the year 058 |
| TO_CHAR(number1 [,fmt]) | Number1 is converted by the format specified by fmt. |
| TO_CHAR(123.456, '09999') | Number1 is returned as '00123' |
| TO_CHAR(123.456, '09999.9') | Number1 is returned as '00123.5' |
| TO_CHAR(123456, 'FM999,999,999') | Number1 is returned as '123,456' |
| TO_CLOB(lob_column) | The TO_CLOB function converts NCLOB values in a LOB column to CLOB values. Oracle executes this function by converting the underlying LOB data from the NCHAR or NVARCHAR2 character set to the database character set. |
| TO_DATE(char [,fmt]) | Converts a Char date to an Oracle Date data type. See TO_CHAR for samples of fmt. |
| TO_DSINTERVAL(char [,nlsparam]) | Converts a CHAR, VARCHAR2, NCHAR, or NVARCHAR2 string into an INTERVAL DAY TO SECOND.  The nlsparam is a value of NLS_NUMERIC_CHARACTERS. |
| TO_LOB(long_column) | TO_LOB converts LONG or LONG RAW values in the column long_column to LOB values. You can apply this function only to a LONG or LONG RAW column, and only in the SELECT list of a subquery in an INSERT statement. Before using this function, you must create a LOB column to receive the converted LONG values. To convert LONGs, create a CLOB column. To convert LONG RAWs, create a BLOB column. |
| TO_MULTI_BYTE(char) | TO_MULTI_BYTE returns char with all of its single-byte characters converted to their corresponding multibyte characters. char can be of datatype CHAR, VARCHAR2, NCHAR, or NVARCHAR2. The value returned is in the same datatype as char. |
| TO_NCHAR(char, fmt, 'nlsparam') | The TO_NCHAR (character) function converts a character string from the database character set to the NVARCHAR2 character set. This function is equivalent to the TRANSLATE ... USING function with a USING clause in the NCHAR character set. |
| TO_NCHAR(datetime, fmt, 'nlsparam') | The TO_NCHAR (datetime) function converts a character string of DATE, TIMESTAMP, TIMESTAMP WITH TIME ZONE, TIMESTAMP WITH LOCAL TIME ZONE, INTERVAL MONTH TO YEAR, or INTERVAL DAY TO SECOND datatype from the database character set to the NVARCHAR2 character set. |
| TO_NCHAR(n, fmt, 'nlsparam') | The TO_NCHAR (number) function converts a number to a string in the NVARCHAR2 character set. The optional fmt and nlsparam corresponding to n can be of DATE, TIMESTAMP, TIMESTAMP WITH TIME ZONE, TIMESTAMP WITH LOCAL TIME ZONE, INTERVAL MONTH TO YEAR, or INTERVAL DAY TO SECOND datatype. |
| TO_NCLOB(lob_column) | The TO_NCLOB function converts CLOB values in a LOB column to NCLOB values. Oracle implements this function by converting the character set of the LOB column from the database character set to the NCHAR character set. |
| TO_NUMBER(char [,fmt [, 'nlsparams']]) | Converts Char, a value of CHAR or VARCHAR2 datatype containing a number in the format specified by the optional format model fmt, to a value of NUMBER datatype. |
| TO_SINGLE_BYTE(char) | TO_SINGLE_BYTE returns char with all of its multibyte characters converted to their corresponding single-byte characters. char can be of datatype CHAR, VARCHAR2, NCHAR, or NVARCHAR2. The value returned is in the same datatype as char. |
| TO_TIMESTAMP(char [,fmt [, 'nlsparam']]) | Converts a CHAR, VARCHAR2, NCHAR, or NVARCHAR2 value to a TIMESTAMP, using the given format.  For example, consider TO_TIMESTAMP ('10-Sep-02 14:10:10.123000','DD-Mon-RR HH24:MI:SS.FF). |
| TO_TIMESTAMP_TZ(char [,fmt [, 'nlsparam']]) | Converts a CHAR, VARCHAR2, NCHAR, or NVARCHAR2 value to a TIMESTAMP WITH TIME ZONE, using the given format. |
| TO_YMINTERVAL(char) | Converts a CHAR, VARCHAR2, NCHAR, or NVARCHAR2 value to an INTERVAL YEAR TO MONTH.  For example, '02-04' becomes 2 years and 4 months. |
| TRANSLATE(text USING CHAR_CS\|NCHAR_CS) | TRANSLATE ... USING converts text into the character set specified for conversions between the database character set and the national character set. \nThe text argument is the expression to be converted. \nSpecifying the USING CHAR_CS argument converts text into the database character set. The output datatype is VARCHAR2. \nSpecifying the USING NCHAR_CS argument converts text into the national character set. The output datatype is NVARCHAR2. \nThis function is similar to the Oracle CONVERT function, but must be used instead of CONVERT if either the input or the output datatype is being used as NCHAR or NVARCHAR2. If the input contains UCS2 codepoints or backslash characters (\), use the UNISTR function. |
| UNISTR('string') | UNISTR takes as its argument a string in any character set and returns it in unicode in the database unicode character set. To include UCS2 codepoint characters in the string, use the escape backslash (\) followed by the next number. To include the backslash itself, precede it with another backslash (\\). |

## Date Functions
| Text | Description |
|---|---|
| ADD_MONTHS(d,n) | Returns the date D plus N months.  N can be any integer. |
| CURRENT_DATE | CURRENT_DATE returns the current date in the session time zone, in a value in the Gregorian calendar of datatype DATE. |
| CURRENT_TIMESTAMP(precision) | CURRENT_TIMESTAMP returns the current date and time in the session time zone, in a value of datatype TIMESTAMP WITH TIME ZONE. The time zone displacement reflects the current local time of the SQL session. If you omit precision, the default is 6. The difference between this function and LOCALTIMESTAMP is that CURRENT_TIMESTAMP returns a TIMESTAMP WITH TIME ZONE value while LOCALTIMESTAMP returns a TIMESTAMP value. |
| DBTIMEZONE | The DBTIMEZONE function returns the value of the database time zone. The return type is a time zone offset (a character type in the format '[+\|-]TZH:TZM') or a time zone region name, depending on how the user specified the database time zone value in the most recent CREATE DATABASE or ALTER DATABASE statement. |
| FROM_TZ(timestamp_value,time_zone_value) | The FROM_TZ function converts a timestamp value at a time zone to a TIMESTAMP WITH TIME ZONE value. time_zone_value is a character string in the format 'TZH:TZM' or a character expression that returns a string in TZR with optional TZD format. |
| LAST_DAY(d) | Returns the date of the last day of the month that contains D. |
| LOCALTIMESTAMP(timestamp_precision) | The LOCALTIMESTAMP function returns the current date and time in the session time zone in a value of datatype TIMESTAMP. The difference between this function and CURRENT_TIMESTAMP is that LOCALTIMESTAMP returns a TIMESTAMP value while CURRENT_TIMESTAMP returns a TIMESTAMP WITH TIME ZONE value. |
| MONTHS_BETWEEN(d1, d2) | Returns the number of months between dates d1 and d2.\nIf d1 is later than d2, result is positive; if earlier, than negative. |
| NEW_TIME(d, z1, z2) | Returns the date and time in time zone z2 when date and time in time zone z1 are d. |
| NEXT_DAY(d, char) | Returns the date of the first weekday named by Char that is later than the date d. E.g., next_day ('15-MAR-92', 'Tuesday') returns 17-MAR-92 |
| NUMTODSINTERVAL(n,'char_expr') | NUMTODSINTERVAL converts n to an INTERVAL DAY TO SECOND literal. n can be a number or an expression resolving to a number. char_expr can be of CHAR, VARCHAR2, NCHAR, or NVARCHAR2 datatype. The value for char_expr specifies the unit of n and must resolve to one of the following string values: 'DAY', 'HOUR', 'MINUTE', 'SECOND' - char_expr is case insensitive. Leading and trailing values within the parentheses are ignored. By default, precision of the return is 9. |
| NUMTOYMINTERVAL(n,'char_expr') | NUMTOYMINTERVAL converts number n to an INTERVAL YEAR TO MONTH literal. n can be a number or an expression resolving to a number. char_expr can be of CHAR, VARCHAR2, NCHAR, or NVARCHAR2 datatype. The value for char_expr specifies the unit of n, and must resolve to one of the following string values: 'YEAR', 'MONTH' - char_expr is case insensitive. Leading and trailing values within the parentheses are ignored. By default, precision of the return is 9. |
| ROUND(d [,fmt]) | Returns d rounded to the unit specified by the format model fmt.\nIf you omit fmt, d is rounded to the nearest day.\nIf fmt is 'YYYY' the year of the date is rounded Up/Down as determined by the day of the year.\nIf fmt is 'MM' the month is rounded Up/Down determined by the day of the month. |
| SESSIONTIMEZONE | The SESSIONTIMEZONE function returns the value of the current session's time zone. The return type is a time zone offset (a character type in the format '[+\|]TZH:TZM') or a time zone region name, depending on how the user specified the session time zone value in the most recent ALTER SESSION statement. |
| SYS_EXTRACT_UTC(datetime_with_timezone) | The SYS_EXTRACT_UTC function extracts the UTC (Coordinated Universal Time--formerly Greenwich Mean Time) from a datetime with time zone displacement. |
| SYSTIMESTAMP | The SYSTIMESTAMP function returns the system date, including fractional seconds and time zone of the database. The return type is TIMESTAMP WITH TIME ZONE. |
| SYSDATE | Returns the current system date and time of the database server. |
| TO_DSINTERVAL(char 'nlsparam') | TO_DSINTERVAL converts a character string of CHAR, VARCHAR2, NCHAR, or NVARCHAR2 datatype to an INTERVAL DAY TO SECOND type. char is the character string to be converted. The only valid NLS parameter you can specify in this function is NLS_NUMERIC_CHARACTERS. This argument can have the form: NLS_NUMERIC_CHARACTERS = \dg\" where d and g represent the decimal character and group separator respectively." |
| TO_TIMESTAMP(char,fmt 'nlsparam') | TO_TIMESTAMP converts char of CHAR, VARCHAR2, NCHAR, or NVARCHAR2 datatype to a value of TIMESTAMP datatype. The optional fmt specifies the format of char. If you omit fmt, char must be in the default format of the TIMESTAMP datatype. The optional nlsparam has the same purpose in this function as in the TO_CHAR function for date conversion. |
| TO_TIMESTAMP_TZ(char,fmt 'nlsparams') | TO_TIMESTAMP_TZ converts char of CHAR, VARCHAR2, NCHAR, or NVARCHAR2 datatype to a value of TIMESTAMP WITH TIME ZONE datatype. |
| TO_YMINTERVAL(char) | The TO_YMINTERVAL function converts a character string of CHAR, VARCHAR2, NCHAR, or NVARCHAR2 datatype to an INTERVAL YEAR TO MONTH type, where char is the character string to be converted. |
| TRUNC(d [,fmt]) | Returns D with the time portion of the day truncated to the unit specified by the format model fmt.\nIf you omit fmt, d is truncated to the nearest day. |
| TZ_OFFSET(time_zone_name\|SESSIONTIMEZONE\|DBTIMEZONE) | TZ_OFFSET returns the time zone offset corresponding to the value entered based on the date the statement is executed. You can enter a valid time zone name, a time zone offset from UTC (which simply returns itself), or the keyword SESSIONTIMEZONE or DBTIMEZONE. For a listing of valid values, query the V$TIMEZONE_NAMES dynamic performance view. |
| TO_CHAR(date1 [,fmt]) | Date1 is converted to CHAR data type in the format specified by fmt. |
| TO_CHAR(sysdate) | Given a current date of 2/25/97, returns '27-FEB-97' |
| TO_CHAR(sysdate, 'MM/DD/YY') | Given a current date of 2/25/97, returns '02/27/97' |
| TO_CHAR(sysdate, 'J') | Given a current date of 2/25/97, returns the Julian date '2450507' |
| TO_CHAR(sysdate,'Mon') | Given a current date of 2/25/97, returns the short Month name 'Feb' |
| TO_CHAR(sysdate, 'Month') | Given a current date of 2/25/97, returns the long Month name 'February' |
| TO_CHAR(sysdate, 'DDD') | Given a current date of 2/25/97, returns the 3 digit day of the year 058 |

## Group Functions
| Text | Description |
|---|---|
| AVG([DISTINCT \| ALL] n) | Calculates the arithmetic average of the values in a column. |
| CORR(expr1, expr2) OVER(analytic_clause) | CORR returns the coefficient of correlation of a set of number pairs. You can use it as an aggregate or analytic function. |
| COUNT(*) | Counts the number of values in a column or the number of rows in a table.\nCOUNT(*) returns the number of rows in a table.\nCOUNT(DISTINCT ColName) returns the number of unique values for column ColName.\nCOUNT(ColName) returns the number of rows that have a non null value for ColName. |
| COVAR_POP(expr1, expr2) OVER(analytic_clause) | COVAR_POP returns the population covariance of a set of number pairs. You can use it as an aggregate or analytic function. |
| COVAR_SMP(expr1, expr2) OVER(analytic_clause) | COVAR_SAMP returns the sample covariance of a set of number pairs. You can use it as an aggregate or analytic function. |
| CUME_DIST(expr) WITHIN GROUP(ORDER BY expr ASC\|DESC NULLS FIRST\|LAST) | CUME_DIST calculates the cumulative distribution of a value in a group of values. The range of values returned by CUME_DIST is >0 to <=1. Tie values always evaluate to the same cumulative distribution value. |
| DENSE_RANK(expr) WITHIN GROUP(ORDER BY expr ASC\|DESC NULLS FIRST\|LAST) | The DENSE_RANK function computes the rank of a row in an ordered group of rows. The ranks are consecutive integers beginning with 1. The largest rank value is the number of unique values returned by the query. Rank values are not skipped in the event of ties. Rows with equal values for the ranking criteria receive the same rank. |
| KEEP(DENSE_RANK FIRST ORDER BY expr ASC\|DESC NULLS FIRST\|LAST) | The FIRST and LAST functions are very similar. Both are aggregate and analytic functions that operate on a set of values from a set of rows that rank as the FIRST or LAST with respect to a given sorting specification. If only one row ranks as FIRST or LAST, the aggregate operates on the set with only one element. |
| GROUP_ID() | The GROUP_ID function distinguishes duplicate groups resulting from a GROUP BY specification. It is therefore useful in filtering out duplicate groupings from the query result. It returns an Oracle NUMBER to uniquely identify duplicate groups. This function is applicable only in a SELECT statement that contains a GROUP BY clause. |
| GROUPING(expr) | The GROUPING function distinguishes superaggregate rows from regular grouped rows. GROUP BY extensions such as ROLLUP and CUBE produce superaggregate rows where the set of all values is represented by null. Using the GROUPING function, you can distinguish a null representing the set of all values in a superaggregate row from a null in a regular row. |
| GROUPING_ID(expr) | The GROUPING_ID function returns a number corresponding to the GROUPING bit vector associated with a row. GROUPING_ID is applicable only in a SELECT statement that contains a GROUP BY extension, such as ROLLUP or CUBE, and a GROUPING function. In queries with many GROUP BY expressions, it is difficult to determine the GROUP BY level of a particular row. GROUPING_ID is useful in these cases. |
| MAX([DISTINCT \| ALL]  n) | Determines the largest value in a column. See COUNT for usage of DISTINCT |
| MIN([DISTINCT \| ALL] n) | Determines the smallest value in a column. See COUNT for usage of DISTINCT |
| PERCENTILE_CONT(expr) WITHIN GROUP(ORDER BY expr ASC\|DESC) | The PERCENTILE_CONT function is an inverse distribution function that assumes a continuous distribution model. It takes a percentile value and a sort specification, and returns an interpolated value that would fall into that percentile value with respect to the sort specification. Nulls are ignored in the calculation. |
| PERCENTILE_DISC(expr) WITHIN GROUP(ORDER BY expr ASC\|DESC) | The PERCENTILE_DISC function is an inverse distribution function that assumes a discrete distribution model. It takes a percentile value and a sort specification and returns an element from the set. Nulls are ignored in the calculation. |
| PERCENT_RANK(expr) WITHIN GROUP(ORDER BY expr ASC\|DESC NULLS FIRST\|LAST) | The PERCENT_RANK function is similar to the CUME_DIST (cumulative distribution) function. The range of values returned by PERCENT_RANK is 0 to 1, inclusive. The first row in any set has a PERCENT_RANK of 0. |
| RANK(expr) WITHIN GROUP(ORDER BY expr ASC\|DESC NULLS FIRST\|LAST) | RANK calculates the rank of a value in a group of values. Rows with equal values for the ranking criteria receive the same rank. Oracle then adds the number of tied rows to the tied rank to calculate the next rank. Therefore, the ranks may not be consecutive numbers. |
| REGR_SLOPE(expr1, expr2) OVER(analytic_clause) | REGR_SLOPE returns the slope of the line. The return value is a number and can be null. After the elimination of null (expr1, expr2) pairs, it makes the following computation: COVAR_POP(expr1, expr2) / VAR_POP(expr2) |
| REGR_INTERCEPT(expr1, expr2) OVER(analytic_clause) | REGR_INTERCEPT returns the y-intercept of the regression line. The return value is a number and can be null. After the elimination of null (expr1, expr2) pairs, it makes the following computation: AVG(expr1) - REGR_SLOPE(expr1, expr2) * AVG(expr2) |
| REGR_COUNT(expr1, expr2) OVER(analytic_clause) | REGR_COUNT returns an integer that is the number of non-null number pairs used to fit the regression line. |
| REGR_R2(expr1, expr2) OVER(analytic_clause) | REGR_R2 returns the coefficient of determination (also called \R-squared\" or \"goodness of fit\") for the regression. The return value is a number and can be null. VAR_POP(expr1) and VAR_POP(expr2) are evaluated after the elimination of null pairs." |
| REGR_AVGX(expr1, expr2) OVER(analytic_clause) | REGR_AVGX evaluates the average of the independent variable (expr2) of the regression line. It makes the following computation after the elimination of null (expr1, expr2) pairs: AVG(expr2) |
| REGR_AVGY(expr1, expr2) OVER(analytic_clause) | REGR_AVGY evaluates the average of the dependent variable (expr1) of the regression line. It makes the following computation after the elimination of null (expr1, expr2) pairs: AVG(expr1) |
| REGR_SXX(expr1, expr2) OVER(analytic_clause) | REGR_SXX makes the following computation after the elimination of null (expr1, expr2) pairs: REGR_COUNT(expr1, expr2) * VAR_POP(expr2) |
| REGR_SYY(expr1, expr2) OVER(analytic_clause) | REGR_SYY makes the following computation after the elimination of null (expr1, expr2) pairs: REGR_COUNT(expr1, expr2) * VAR_POP(expr1) |
| REGR_SXY(expr1, expr2) OVER(analytic_clause) | REGR_SXY makes the following computation after the elimination of null (expr1, expr2) pairs: REGR_COUNT(expr1, expr2) * COVAR_POP(expr1, expr2) |
| STDDEV([DISTINCT \| ALL] n) | Determines the standard deviation, the square root of the variance, of the values in a column. See COUNT for usage of DISTINCT |
| STDDEV_POP(expr) OVER(analytic_clause) | STDDEV_POP computes the population standard deviation and returns the square root of the population variance. You can use it as both an aggregate and analytic function. |
| STDDEV_SAMP(expr) OVER(analytic_clause) | STDDEV_SAMP computes the cumulative sample standard deviation and returns the square root of the sample variance. You can use it as both an aggregate and analytic function. |
| SUM([DISTINCT \| ALL] n) | Calculates the sum of all values in a column. See COUNT for usage of DISTINCT |
| SYS_XMLAGG(expr[,fmt]) | Aggregates all the XML documents or fragments represented by expr into one XML document, enclosed by an element with default name ROWSET. The fmt parameter is of type SYS.XMLGenFormatType. |
| VAR_POP(expr) OVER(analytic_clause) | VAR_POP returns the population variance of a set of numbers after discarding the nulls in this set. You can use it as both an aggregate and analytic function. |
| VAR_SAMP(expr) OVER(analytic_clause) | VAR_SAMP returns the sample variance of a set of numbers after discarding the nulls in this set. You can use it as both an aggregate and analytic function. |
| VARIANCE( [DISTINCT \| ALL] n) | Determines the variance of the values in a column. See COUNT for usage of DISTINCT |

## Other Functions
| Text | Description |
|---|---|
| BFILENAME(directory,filename) | BFILENAME returns a BFILE locator that is associated with a physical LOB binary file on the server's file system. A directory is an alias for a full pathname on the server's file system where the files are actually located, and 'filename' is the name of the file in the server's file system. |
| CASE expr WHEN expr1 THEN ret_expr1 ELSE ret_expr2 END | Simple CASE statements evaluate an expression and compare it to different values to output another value.  \nFor example:  \nSELECT DEPTNO, CASE DEPTNO WHEN 10 THEN 'ACCTG' WHEN 20 THEN 'R AND D' ELSE 'OTHER' END \Result\" FROM SCOTT.DEPT;  \n\nIf no ELSE clause is specified,  then a NULL is returned.  Oracle 9i and up. |
| CASE WHEN cond1 THEN ret_expr1 ELSE ret_expr2 END | A searched CASE statement evaluates expressions one by one and returns a value when an expression evaluates to true.  \nFor example: \nSELECT DEPTNO, CASE WHEN DEPTNO=10 THEN 'ACCT' WHEN DEPTNO=20 THEN 'R AND D' ELSE 'OTHER' END \Result\" FROM SCOTT.DEPT;\n\nIf no ELSE clause is specified, then a NULL is returned.  Oracle 9i and up. |
| COALESCE(expr) | The COALESCE function returns the first non-null expr in the expression list. At least one expr must not be the literal NULL. If all occurrences of expr evaluate to null, the function returns null. This function is a generalization of the NVL function. |
| DECODE(expr, value1 [, return1, value2, return2....,] default ) | Translates expr to a return value. If expr equals value1, function returns Return1. If expr equals value2, function returns Return2. Etc. If expr does not equal any Value in the list, the default value is returned. |
| DUMP(expr,return_fmt,start_position,length) | DUMP returns a VARCHAR2 value containing the datatype code, length in bytes, and internal representation of expr. The returned result is always in the database character set. |
| EMPTY_BLOB() | EMPTY_BLOB returns an empty LOB locator that can be used to initialize a LOB variable or in an INSERT or UPDATE statement to initialize a LOB column or attribute to EMPTY. EMPTY means that the LOB is initialized, but not populated with data. |
| EMPTY_CLOB() | EMPTY_CLOB returns an empty LOB locator that can be used to initialize a LOB variable or in an INSERT or UPDATE statement to initialize a LOB column or attribute to EMPTY. EMPTY means that the LOB is initialized, but not populated with data. |
| EXISTSNODE(XMLType_instance, Xpath_string) | Returns 0 if the path does not exist in the XMLType.  Returns a number > 0 otherwise. |
| GREATEST(expr [,expr2, expr3...]) | Returns the greatest of the list of exprs. |
| LEAST(expr [,expr2, expr3...]) | Returns the least of the list of exprs. |
| NLS_CHARSET_DECL_LEN(byte_count,char_set_id) | NLS_CHARSET_DECL_LEN returns the declaration width (in number of characters) of an NCHAR column. The byte_count argument is the width of the column. The char_set_id argument is the character set ID of the column. |
| NLS_CHARSET_ID(text) | NLS_CHARSET_ID returns the NLS character set ID number corresponding to NLS character set name text. The text argument is a run-time VARCHAR2 value. The text value 'CHAR_CS' returns the database character set ID number of the server. The text value 'NCHAR_CS' returns the national character set ID number of the server. |
| NLS_CHARSET_NAME(number) | NLS_CHARSET_NAME returns the name of the NLS character set corresponding to ID number number. The character set name is returned as a VARCHAR2 value in the database character set. If number is not recognized as a valid character set ID, this function returns null. |
| NULLIF(expr1, expr2) | The NULLIF function compares expr1 and expr2. If they are equal, the function returns null. If they are not equal, the function returns expr1. You cannot specify the literal NULL for expr1. |
| NVL(expr1, expr2) | If expr1 is null, returns expr2.  If expr1 is not null, returns expr1. |
| NVL2(expr1, expr2, expr3) | If expr1 is not null, NVL2 returns expr2. If expr1 is null, NVL2 returns expr3. The argument expr1 can have any datatype. The arguments expr2 and expr3 can have any datatypes except LONG. |
| SYS_CONTEXT(namespace, attribute, length) | SYS_CONTEXT returns the value of attribute associated with the context namespace. You can use this function in both SQL and PL/SQL statements. |
| SYS_GUID() | SYS_GUID generates and returns a globally unique identifier (RAW value) made up of 16 bytes. On most platforms, the generated identifier consists of a host identifier and a process or thread identifier of the process or thread invoking the function, and a nonrepeating value (sequence of bytes) for that process or thread. |
| UID | Returns an integer that uniquely identifies the current user. |
| USER | Returns the current Oracle user with the datatype VARCHAR2. |
| USERENV(option) | Returns information about the current session.\nValid options are 'ENTRYID', 'SESSIONID', 'TERMINAL', 'LANGUAGE', 'LANG', 'CLIENT_INFO' and 'LABEL'. |
| VSIZE(expr) | Returns the number of bytes in the internal representation of expr. |
| SYSDATE | Returns the current system date and time of the database server. |
| SQLERRM | If SQLERRM is passed a number value, it will return the error associated with that value. If SQLERRM is called with no arguments, inside of the exception section, it will return the error message associated with the most recent SQLCODE value. |
| SQLCODE | Returns the number associated with the most recently raised PL/SQL exception. This function can only be used from within a PL/SQL exception handler. |
| SYS_CONNECT_BY_PATH(column,char) | SYS_CONNECT_BY_PATH is valid only in hierarchical queries. It returns the path of a column value from root to node, with column values separated by char for each row returned by CONNECT BY condition. |
| SYS_XMLGEN(expr, [fmt]) | Generates an XMLType object based on the value of the expression.  The expr parameter can be a scalar, a value of a user-defined type, or an XMLType object.  The fmt parameter is a value of type SYS.XMLGenFormatType.  Oracle 9i and up. |

## Single Character Functions
| Text | Description |
|---|---|
| CHR(n) | Returns the character having the binary equivalent to N in the database character set. |
| CONCAT(char1, char2) | Returns Char1 concatenated with Char2. |
| INITCAP(char) | Returns Char, with the first letter of each word in uppercase, all other letters in lowercase. Words are delimited by white space or characters that are not alphanumeric. |
| LOWER(char) | Returns Char, with all letters lowercase. |
| LPAD(char1, n [,char2]) | Returns Char1, left-padded to length N with the sequence of characters in Char2. Char2 defaults to a single blank. |
| LTRIM(char [,set]) | Removes characters from the left of Char, with all the leftmost characters that appear in Set removed. Set defaults to a single blank. Oracle begins scanning Char from its first character and removes all characters that appear in Set until reaching a character not in set and then returns the result. |
| NLS_INITCAP(char [, 'nlsparams']) | Returns Char, with the first letter of each word in uppercase, and all other letters in lowercase. Words are delimited by white space or characters that are not alphanumeric. The value of NLSPARAMS can have this form: 'NLS_SORT = sort' where sort is either a linguistic sort sequence or BINARY. |
| NLS_LOWER(char [, 'nlsparams']) | Returns Char, with all letters lowercase. The 'nlsparams' can have the same form and serve the same purpose as in the NLS_INITCAP function. |
| NLSSORT(char [, 'nlsparams']) | Returns the string of bytes used to sort Char. |
| NLS_UPPER(char [, 'nlsparams']) | Returns Char, with all letters uppercase. The 'nlsparams' can have the same form and serve the same purpose as in the NLS_INITCAP function. |
| REPLACE(char, search_string [,replacement_string]) | Returns Char with every occurrence of search_string replaced with replacement_string. |
| RPAD(char1, n [,char2]) | Returns Char1, right-padded to length N with Char2, replicated as many times as necessary.  Char2 defaults to a single blank. |
| RTRIM(char [,set]) | Returns char, with all the rightmost characters that appear in Set removed. Set defaults to a single blank. |
| SOUNDEX(char) | Returns a character string containing the phonetic representation of Char. This function allows you to compare words that are spelled differently, but sound alike in English. |
| SUBSTR(char, m [, n]) | Returns a portion of Char, beginning at character M, N characters long.\nIf M is positive, Oracle counts from the beginning of Char to find the first character.\nif M is negative, Oracle counts backwards from the end of Char.\nIf N is omitted, Oracle returns all characters to the end of Char. |
| SUBSTRB(char, m [, n]) | The same as SUBSTR, except that the arguments M and N are expressed in bytes, rather than in characters. |
| TRANSLATE(char, from, to) | Returns Char with all occurrences of each character in From replaced by its corresponding character in To. Characters in Char that are not in From are not replaced. E.g., Translate (colName, 'i', 'a') replaces all 'i's with 'a's |
| TREAT(expr AS REF schema.type) | Use the TREAT function to change the declared type of an expression. |
| TRIM(LEADING\|TRAILING\|BOTH trim_char FROM trim_source) | TRIM enables you to trim leading or trailing characters (or both) from a character string. If trim_character or trim_source is a character literal, you must enclose it in single quotes.\nIf you specify LEADING, Oracle removes any leading characters equal to trim_character. \nIf you specify TRAILING, Oracle removes any trailing characters equal to trim_character. \nIf you specify BOTH or none of the three, Oracle removes leading and trailing characters equal to trim_character. \nIf you do not specify trim_character, the default value is a blank space. \nIf you specify only trim_source, Oracle removes leading and trailing blank spaces. \nThe function returns a value with datatype VARCHAR2. The maximum length of the value is the length of trim_source. \nIf either trim_source or trim_character is a null value, then the TRIM function returns a null value. |
| UPPER(char) | Returns Char, with all letters uppercase. |
| ASCII(char) | Returns the decimal representation in the database character set of the first byte of Char. |
| INSTR(char1, char2 [,n [,m]]) | Searches Char1 beginning with its Nth character for the Mth occurrence of Char2 and returns the position of the character in Char1 that is the first character of this occurrence.\nIf N is negative, Oracle counts and searches backward from the end of Char1.  N and M default to 1. |
| INSTRB(char1, char2 [,n [,m]]) | The same as INSTR, except that N and the return value are expressed in bytes, rather than in characters. |
| LENGTH(char) | Returns the length of Char in characters.\nIf Char has datatype CHAR, then length includes all trailing blanks. |
| LENGTHB(char) | Returns the length of Char in bytes. |

## Single Number Functions
| Text | Description |
|---|---|
| ABS(n) | Returns the absolute value of N. |
| ACOS(n) | Returns the arc cosine of N. Inputs are in the range of -1 to 1, and outputs are in the range of 0 to pi and are expressed in radians. |
| ADD_MONTHS(d,n) | ADD_MONTHS returns the date d plus n months. The argument n can be any integer. If d is the last day of the month or if the resulting month has fewer days than the day component of d, then the result is the last day of the resulting month. Otherwise, the result has the same day component as d. |
| ASIN(n) | Returns the arc sine of N. Inputs are in the range of -1 to 1, and outputs are in the range of -pi/2 to pi/2 and are expressed in radians. |
| ATAN(n) | Returns the arc tangent of N. Inputs are in an unbounded range, and outputs are in the range of -pi/2 to pi/2 and are expressed in radians. |
| ATAN2(n,m) | Returns the arc tangent of N and M. Inputs are in an unbounded range, and outputs are in the range of -pi to pi, depending on the signs of x and y, and are expressed in radians.  Atan2(x,y) is the same as Atan2(x/y). |
| BITAND(argument1, argument2) | BITAND computes an AND operation on the bits of argument1 and argument2, both of which must resolve to nonnegative integers, and returns an integer. This function is commonly used with the DECODE expression, as illustrated in the example that follows. |
| CEIL(n) | Returns the smallest integer greater than or equal to N.  (see FLOOR) |
| COS(n) | Returns the cosine of N (an angle expressed in radians). |
| COSH(n) | Returns the hyperbolic cosine of N. |
| EXP(n) | Returns e raised to the Nth power, where e = 2.71828183... |
| FLOOR(n) | Returns largest integer equal to or less than N.  (see CEIL) |
| LN(n) | Returns the natural logarithm of N where N > 0 |
| LOG(m,n) | Returns the logarithm, base M, of N. The base M can be any positive number other than 0 or 1 and N can be any positive number. |
| MOD(m,n) | Returns remainder of M divided by N.  Returns M if N is 0. |
| POWER(m,n) | Returns M raised to the Nth power. The base M and the exponent N can by any numbers, but if M is negative, N must be an integer. |
| ROUND(n [,m]) | Returns N rounded to M places right of the decimal point.  M defaults to 0. M can be negative to round off digits left of the decimal point.  M must be an integer. |
| SIGN(n) | if N < 0 then -1;  if N = 0 then 0;  if N > 0 then 1 |
| SIN(n) | Returns the sine of N (an angle expressed in radians). |
| SINH(n) | Returns the hyperbolic sine of N. |
| SQRT(n) | Returns square root of N;  if N < 0 then NULL |
| TAN(n) | Returns the tangent of N (an angle expressed in radians). |
| TANH(n) | Returns the hyperbolic tangent of N. |
| TRUNC(n [,m]) | Returns N truncated to M decimal places;  M defaults to 0. M can be negative to truncate (make zero) M digits left of the decimal point. |
| WIDTH_BUCKET(expr,min_value,max_value,num_buckets) | The WIDTH_BUCKET function lets you construct equiwidth histograms. For a given expression, it returns the bucket number into which the value of this expression would fall after being evaluated. |

## Regular Expression Functions
| Text | Description |
|---|---|
| REGEXP_COUNT(source_char, pattern [, position [, match_param]]) | Returns the number of times the POSIX compatible regular expression pattern is matched. |
| REGEXP_INSTR(source_char, pattern [, position [, occurrence [, return_option [, match_parameter]]]]) | Extends the functionality of INSTR by allowing you to search a string for a POSIX compatible regular expression pattern. It returns the beginning or ending position of the match depending on the value of the return_option parameter. If the expression cannot be matched 0 is returned. |
| REGEXP_LIKE(source_string, pattern [, match_parameter]) | Similar to LIKE, this condition evaluates strings using a POSIX compatible regular expression pattern. |
| REGEXP_REPLACE(source_char, pattern [, replace_string [, position [, occurrence [, match_pattern]]]]) | Extends the functionality of REPLACE by allowing you to search a string for a POSIX compatible regular expression pattern. |
| REGEXP_SUBSTR(source_string, pattern [, position [, occurrence [, match_parameter]]]) | Extends the functionality of SUBSTR by allowing you to search a string for a POSIX compatible regular expression pattern. It is similar to REGEXP_INSTR, but returns the actual substring instead of the position of the substring. |

## Unit Testing (utPLSQL)
| Categories.Snippets.Text | Categories.Snippets.Description |
|---|---|
| --%afterall | Annotation denoting a procedure that should be executed once after all tests in the suite. |
| --%aftereach | Annotation denoting a procedure that should be executed after each test in the suite. |
| --%aftertest(procedure_name) | Annotation set at the test level that specifies a procedure to be executed after the test. |
| --%beforeall | Annotation denoting a procedure that should be executed once before all tests in the suite. |
| --%beforeeach | Annotation denoting a procedure that should be executed before each test in the suite. |
| --%beforetest(procedure_name) | Annotation set at the test level that specifies a procedure to be executed before the test. |
| --%context(description) | Annotation denoting the start of a nested context (sub-suite) in a suite. |
| --%disabled | Annotation denoting a suite or test that is disabled. Disabled suites/tests are not executed; however, they are marked and reported as disabled in the run results. |
| --%displayname(description) | Annotation assigning a display name to a suite or test. It has the same meaning as the optional description parameter of the --%suite and --%test annotations. |
| --%endcontext(description) | Annotation denoting the end of a nested context (sub-suite) in a suite. |
| --%rollback(type) | Annotation defining transaction control that can be set at either the suite or test level. Type is one of: \auto\" (default) - a savepoint is created before invocation of each “before block” is and a rollback to specific savepoint is issued after each “after” block; \"manual\" - rollback is never issued automatically. Property can be overridden for tests when it is set at the suite level." |
| --%suite(description) | Annotation denoting a test suite. Optional description can be provided. |
| --%suitepath(path) | Annotation allowing for logical grouping of suites. |
| --%test(description) | Annotation denoting a test. Optional description can be provided. |
| ut.expect(value [,message]) | Returns ut_expectation to use for applying a matcher to a value. |
| ut.expect(value [,message]).not_to_be_between(lower_bound, upper_bound) | Validates value is not between a lower and upper bound. |
| ut.expect(value [,message]).not_to_be_false | Validates value is true. |
| ut.expect(value [,message]).not_to_be_greater_or_equal(expected) | Validates value is not greater than or equal to an expected value. |
| ut.expect(value [,message]).not_to_be_greater_than(expected) | Validates value is not greater than an expected value. |
| ut.expect(value [,message]).not_to_be_less_or_equal(expected) | Validates value is not less than or equal to an expected value. |
| ut.expect(value [,message]).not_to_be_less_than(expected) | Validates value is not less than an expected value. |
| ut.expect(value [,message]).not_to_be_like(mask [, escape_char]) | Validates value is not like a mask. |
| ut.expect(value [,message]).not_to_be_not_null | Validates value is null. |
| ut.expect(value [,message]).not_to_be_null | Validates value is not null. |
| ut.expect(value [,message]).not_to_be_true | Validates value is false. |
| ut.expect(value [,message]).not_to_equal(expected [, exclude, nulls_are_equal]) | Validates value is not equal to an expected value. |
| ut.expect(value [,message]).not_to_equal(expected [, nulls_are_equal]) | Validates value is not equal to an expected value. |
| ut.expect(value [,message]).not_to_match(pattern [, modifiers]) | Validates value does not match a pattern. |
| ut.expect(value [,message]).to_be_between(lower_bound, upper_bound) | Validates value is between a lower and upper bound. |
| ut.expect(value [,message]).to_be_false | Validates value is false. |
| ut.expect(value [,message]).to_be_greater_or_equal(expected) | Validates value is greater than or equal to an expected value. |
| ut.expect(value [,message]).to_be_greater_than(expected) | Validates value is greater than an expected value. |
| ut.expect(value [,message]).to_be_less_or_equal(expected) | Validates value is less than or equal to an expected value. |
| ut.expect(value [,message]).to_be_less_than(expected) | Validates value is less than an expected value. |
| ut.expect(value [,message]).to_be_like(mask [, escape_char]) | Validates value is like a mask. |
| ut.expect(value [,message]).to_be_not_null | Validates value is not null. |
| ut.expect(value [,message]).to_be_null | Validates value is null. |
| ut.expect(value [,message]).to_be_true | Validates value is true. |
| ut.expect(value [,message]).to_equal(expected [, exclude, nulls_are_equal]) | Validates value is equal to an expected value. |
| ut.expect(value [,message]).to_equal(expected [, nulls_are_equal]) | Validates value is equal to an expected value. |
| ut.expect(value [,message]).to_match(pattern [, modifiers]) | Validates value matches a pattern. |
| --%throws(exception_number [, exception_number [, ...]]) | Annotation denoting a test that must throw one of the exceptions provided. |

## Hints
| Text | Description |
|---|---|
| /*+ ALL_ROWS */ | The ALL_ROWS hint explicitly chooses the cost-based approach to optimize a statement block with a goal of best throughput (that is, minimum total resource consumption). |
| /*+ FIRST_ROWS(integer) */ | The FIRST_ROWS hint explicitly chooses the cost-based approach to optimize a statement block with a goal of best response time (minimum resource usage to return first row). |
| /*+ CHOOSE */ | The CHOOSE hint causes the optimizer to choose between the rule-based and cost-based approaches for a SQL statement. The optimizer bases its selection on the presence of statistics for the tables accessed by the statement. If the data dictionary has statistics for at least one of these tables, then the optimizer uses the cost-based approach and optimizes with the goal of best throughput. If the data dictionary does not have statistics for these tables, then it uses the rule-based approach. |
| /*+ FULL(table_name) */ | The FULL hint explicitly chooses a full table scan for the specified table. |
| /*+ ROWID(table_name) */ | The ROWID hint explicitly chooses a table scan by rowid for the specified table. |
| /*+ CLUSTER(table_name) */ | The CLUSTER hint explicitly chooses a cluster scan to access the specified table. It applies only to clustered objects. |
| /*+ HASH(table_name) */ | The HASH hint explicitly chooses a hash scan to access the specified table. It applies only to tables stored in a cluster. |
| /*+ INDEX(table_name index_name) */ | The INDEX hint explicitly chooses an index scan for the specified table. You can use the INDEX hint for domain, B-tree, bitmap, and bitmap join indexes. However, Oracle recommends using INDEX_COMBINE rather than INDEX for bitmap indexes, because it is a more versatile hint. |
| /*+ INDEX_ASC(table_name index_name) */ | The INDEX_ASC hint explicitly chooses an index scan for the specified table. If the statement uses an index range scan, then Oracle scans the index entries in ascending order of their indexed values. |
| /*+ INDEX_COMBINE(table_name index_name) */ | The INDEX_COMBINE hint explicitly chooses a bitmap access path for the table. If no indexes are given as arguments for the INDEX_COMBINE hint, then the optimizer uses whatever Boolean combination of bitmap indexes has the best cost estimate for the table. If certain indexes are given as arguments, then the optimizer tries to use some Boolean combination of those particular bitmap indexes. |
| /*+ INDEX_JOIN(table_name index_name) */ | The INDEX_JOIN hint explicitly instructs the optimizer to use an index join as an access path. For the hint to have a positive effect, a sufficiently small number of indexes must exist that contain all the columns required to resolve the query. |
| /*+ INDEX_DESC(table_name index_name) */ | The INDEX_DESC hint explicitly chooses an index scan for the specified table. If the statement uses an index range scan, then Oracle scans the index entries in descending order of their indexed values. In a partitioned index, the results are in descending order within each partition. |
| /*+ INDEX_FFS(table_name index_name) */ | The INDEX_FFS hint causes a fast full index scan to be performed rather than a full table scan. |
| /*+ INDEX_NOINDEX(table_name index_name) */ | The NO_INDEX hint explicitly disallows a set of indexes for the specified table. |
| /*+ INDEX_NOINDEX(table_name index_name[,index_name]) */ | The AND_EQUAL hint explicitly chooses an execution plan that uses an access path that merges the scans on several single-column indexes. |
| /*+ USE_CONCAT */ | The USE_CONCAT hint forces combined OR conditions in the WHERE clause of a query to be transformed into a compound query using the UNION ALL set operator. Generally, this transformation occurs only if the cost of the query using the concatenations is cheaper than the cost without them. |
| /*+ NO_EXPAND */ | The NO_EXPAND hint prevents the cost-based optimizer from considering OR-expansion for queries having OR conditions or IN-lists in the WHERE clause. Usually, the optimizer considers using OR expansion and uses this method if it decides that the cost is lower than not using it. |
| /*+ REWRITE(view_name) */ | The REWRITE hint forces the cost-based optimizer to rewrite a query in terms of materialized views, when possible, without cost consideration. Use the REWRITE hint with or without a view list. If you use REWRITE with a view list and the list contains an eligible materialized view, then Oracle uses that view regardless of its cost. |
| /*+ NOREWRITE */ | The NOREWRITE hint disables query rewrite for the query block, overriding the setting of the parameter QUERY_REWRITE_ENABLED. Use the NOREWRITE hint on any query block of a request. |
| /*+ MERGE(table_name) */ | The MERGE hint lets you merge a view on a per-query basis. |
| /*+ NO_MERGE(table_name) */ | The NO_MERGE hint causes Oracle not to merge mergeable views. |
| /*+ STAR_TRANSFORMATION */ | The STAR_TRANSFORMATION hint makes the optimizer use the best plan in which the transformation has been used. Without the hint, the optimizer could make a cost-based decision to use the best plan generated without the transformation, instead of the best plan for the transformed query. |
| /*+ FACT(table_name) */ | The FACT hint is used in the context of the star transformation to indicate to the transformation that the hinted table should be considered as a fact table. |
| /*+ NO_FACT(table_name) */ | The NO_FACT hint is used in the context of the star transformation to indicate to the transformation that the hinted table should not be considered as a fact table. |
| /*+ ORDERED */ | The ORDERED hint causes Oracle to join tables in the order in which they appear in the FROM clause. |
| /*+ STAR */ | The STAR hint forces a star query plan to be used, if possible. A star plan has the largest table in the query last in the join order and joins it with a nested loops join on a concatenated index. The STAR hint applies when there are at least three tables, the large table's concatenated index has at least three columns, and there are no conflicting access or join method hints. The optimizer also considers different permutations of the small tables. |
| /*+ USE_NL(table_name) */ | The USE_NL hint causes Oracle to join each specified table to another row source with a nested loops join using the specified table as the inner table. |
| /*+ USE_MERGE(table_name) */ | The USE_MERGE hint causes Oracle to join each specified table with another row source with a sort-merge join. |
| /*+ USE_HASH(table_name) */ | The USE_HASH hint causes Oracle to join each specified table with another row source with a hash join. |
| /*+ DRIVING_SITE(table_name) */ | The DRIVING_SITE hint forces query execution to be done at a different site than that selected by Oracle. This hint can be used with either rule-based or cost-based optimization. |
| /*+ LEADING(table_name) */ | The LEADING hint causes Oracle to use the specified table as the first table in the join order. |
| /*+ MERGE_AJ */ | For a specific query, place the MERGE_AJ hint into the NOT IN subquery. MERGE_AJ uses a sort-merge anti-join. |
| /*+ HASH_AJ */ | For a specific query, place the HASH_AJ hint into the NOT IN subquery. HASH_AJ uses a hash anti-join. |
| /*+ NL_AJ */ | For a specific query, place the NL_AJ hint into the NOT IN subquery. NL_AJ uses a nested loop anti-join. |
| /*+ PARALLEL(table_name degree) */ | The PARALLEL hint lets you specify the desired number of concurrent servers that can be used for a parallel operation. The hint applies to the INSERT, UPDATE, and DELETE portions of a statement as well as to the table scan portion. |
| /*+ NOPARALLEL(table_name) */ | The NOPARALLEL hint overrides a PARALLEL specification in the table clause. In general, hints take precedence over table clauses. |
| /*+ PQ_DISTRIBUTE(table_name table_distribution,table_distribution) */ | The PQ_DISTRIBUTE hint improves parallel join operation performance. Do this by specifying how rows of joined tables should be distributed among producer and consumer query servers. Using this hint overrides decisions the optimizer would normally make. |
| /*+ APPEND parallel_hint */ | When you use the APPEND hint for INSERT, data is simply appended to a table. Existing free space in the blocks currently allocated to the table is not used. If INSERT is parallelized using the PARALLEL hint or clause, then append mode is used by default. You can use NOAPPEND to override append mode. The APPEND hint applies to both serial and parallel insert. The append operation is performed in LOGGING or NOLOGGING mode, depending on whether the [NO] option is set for the table in question. Use the ALTER TABLE... [NO]LOGGING statement to set the appropriate value. |
| /*+ NOAPPEND */ | The NOAPPEND hint overrides append mode. |
| /*+ PARALLEL_INDEX(table_name, index_name, degree, RAC degree) */ | The PARALLEL_INDEX hint specifies the desired number of concurrent servers that can be used to parallelize index range scans for partitioned indexes. |
| /*+ NOPARALLEL_INDEX(table_name index_name) */ | The NOPARALLEL_INDEX hint overrides a PARALLEL attribute setting on an index to avoid a parallel index scan operation. |
| /*+ CACHE(table_name) */ | The CACHE hint specifies that the blocks retrieved for the table are placed at the most recently used end of the LRU list in the buffer cache when a full table scan is performed. This option is useful for small lookup tables. |
| /*+ NOCACHE(table_name) */ | The NOCACHE hint specifies that the blocks retrieved for the table are placed at the least recently used end of the LRU list in the buffer cache when a full table scan is performed. This is the normal behavior of blocks in the buffer cache. |
| /*+ UNNEST */ | Setting the UNNEST_SUBQUERY session parameter to TRUE enables subquery unnesting. Subquery unnesting unnests and merges the body of the subquery into the body of the statement that contains it, allowing the optimizer to consider them together when evaluating access paths and joins. |
| /*+ NO_UNNEST */ | If you enabled subquery unnesting with the UNNEST_SUBQUERY parameter, then the NO_UNNEST hint turns it off for specific subquery blocks. |
| /*+ PUSH_PRED(table_name) */ | The PUSH_PRED hint forces pushing of a join predicate into the view. |
| /*+ NO_PUSH_PRED(table_name) */ | The NO_PUSH_PRED hint prevents pushing of a join predicate into the view. |
| /*+ PUSH_SUBQ */ | The PUSH_SUBQ hint causes non-merged subqueries to be evaluated at the earliest possible place in the execution plan. Generally, subqueries that are not merged are executed as the last step in the execution plan. If the subquery is relatively inexpensive and reduces the number of rows significantly, then it improves performance to evaluate the subquery earlier. |
| /*+ ORDERED_PREDICATES */ | The ORDERED_PREDICATES hint forces the optimizer to preserve the order of predicate evaluation, except for predicates used as index keys. Use this hint in the WHERE clause of SELECT statements. |
| /*+ CURSOR_SHARING_EXACT */ | Oracle can replace literals in SQL statements with bind variables if it is safe to do so. This is controlled with the CURSOR_SHARING startup parameter. The CURSOR_SHARING_EXACT hint causes this behavior to be switched off. In other words, Oracle executes the SQL statement without any attempt to replace literals by bind variables. |

## SQL Plus Export Hints
```sql
SET SQLFORMAT ANSICONSOLE;
SELECT /*ansiconsole*/ * FROM scott.emp;

SET SQLFORMAT CSV;
SELECT /*csv*/ * FROM scott.emp;

SET SQLFORMAT DELIMITED | ' '
SELECT /*delimited*/ * FROM scott.emp;

SET SQLFORMAT FIXED;
SELECT /*fixed*/ * FROM scott.emp;

SET SQLFORMAT HTML;
SELECT /*html*/ * FROM scott.emp;

SET SQLFORMAT INSERT;
SELECT /*insert*/ * FROM scott.emp;

SET SQLFORMAT JSON-FORMATTED
SELECT /*json*/ * FROM scott.emp;

SET SQLFORMAT LOADER;
SELECT /*loader*/ * FROM scott.emp;

SET SQLFORMAT DEFAULT;
SELECT /*text*/ * FROM scott.emp;

SET SQLFORMAT XML;
SELECT /*xml*/ * FROM scott.emp;
```