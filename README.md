# Accessing-DB2-on-Cloud-Using-Python

## Objectives

-   Create a table
-   Insert data into the table
-   Query data from the table
-   Retrieve the result set into a pandas dataframe
-   Close the database connection




## Import the `ibm_db` Python library

The `ibm_db` [API ](https://pypi.python.org/pypi/ibm_db?cm_mmc=Email_Newsletter-_-Developer_Ed%2BTech-_-WW_WW-_-SkillsNetwork-Courses-IBMDeveloperSkillsNetwork-DB0201EN-SkillsNetwork-20127838&cm_mmca1=000026UJ&cm_mmca2=10006555&cm_mmca3=M12345678&cvosrc=email.Newsletter.M12345678&cvo_campaign=000026UJ&cm_mmc=Email_Newsletter-_-Developer_Ed%2BTech-_-WW_WW-_-SkillsNetwork-Courses-IBMDeveloperSkillsNetwork-DB0201EN-SkillsNetwork-20127838&cm_mmca1=000026UJ&cm_mmca2=10006555&cm_mmca3=M12345678&cvosrc=email.Newsletter.M12345678&cvo_campaign=000026UJ&cm_mmc=Email_Newsletter-_-Developer_Ed%2BTech-_-WW_WW-_-SkillsNetwork-Courses-IBMDeveloperSkillsNetwork-DB0201EN-SkillsNetwork-20127838&cm_mmca1=000026UJ&cm_mmca2=10006555&cm_mmca3=M12345678&cvosrc=email.Newsletter.M12345678&cvo_campaign=000026UJ&cm_mmc=Email_Newsletter-_-Developer_Ed%2BTech-_-WW_WW-_-SkillsNetwork-Courses-IBMDeveloperSkillsNetwork-DB0201EN-SkillsNetwork-20127838&cm_mmca1=000026UJ&cm_mmca2=10006555&cm_mmca3=M12345678&cvosrc=email.Newsletter.M12345678&cvo_campaign=000026UJ) provides a variety of useful Python functions for accessing and manipulating data in an IBM data server database, including functions for connecting to a database, preparing and issuing SQL statements, fetching rows from result sets, calling stored procedures, committing and rolling back transactions, handling errors, and retrieving metadata.

I import the ibm_db library into the Python Application



```python
import ibm_db
```



## Identifing the database connection credentials

Connecting to dashDB or DB2 database requires the following information:

-   Driver Name
-   Database name 
-   Host DNS name or IP address 
-   Host port
-   Connection protocol
-   User ID
-   User Password




```python
dsn_database = "BLUDB"           
dsn_hostname = "dashdb-txn-sbox-yp-dal09-08.services.dal.bluemix.net"           
dsn_port = "50000"
dsn_driver = "{IBM DB2 ODBC DRIVER}" 
dsn_protocol = "TCPIP"            
dsn_uid = "fwh43441"                 
dsn_pwd = "nc7qjqmk-czlqdbv"               
```

##  Creating the database connection

Ibm_db API uses the IBM Data Server Driver for ODBC and CLI APIs to connect to IBM DB2 and Informix.

Create the database connection



```python
#Create database connection

dsn = (
    "DRIVER={0};"
    "DATABASE={1};"
    "HOSTNAME={2};"
    "PORT={3};"
    "PROTOCOL={4};"
    "UID={5};"
    "PWD={6};").format(dsn_driver, dsn_database, dsn_hostname, dsn_port, dsn_protocol, dsn_uid, dsn_pwd)

try:
    conn = ibm_db.connect(dsn, "", "")
    print ("Connected to database: ", dsn_database, "as user: ", dsn_uid, "on host: ", dsn_hostname)

except:
    print ("Unable to connect: ", ibm_db.conn_errormsg() )

```

    Connected to database:  BLUDB as user:  fwh43441 on host:  dashdb-txn-sbox-yp-dal09-08.services.dal.bluemix.net
    

## Creating a table in the database



```python
#drop the table INSTRUCTOR in case it exists from a previous attempt
dropQuery = "drop table INSTRUCTOR"

#execute the drop statment
dropStmt = ibm_db.exec_immediate(conn, dropQuery)
```


    ---------------------------------------------------------------------------

    Exception                                 Traceback (most recent call last)

    <ipython-input-9-83413676a2ca> in <module>
          3 
          4 #Now execute the drop statment
    ----> 5 dropStmt = ibm_db.exec_immediate(conn, dropQuery)
    

    Exception: [IBM][CLI Driver][DB2/LINUXX8664] SQL0204N  "FWH43441.INSTRUCTOR" is an undefined name.  SQLSTATE=42704 SQLCODE=-204


## Explanation of this error:

It just implies that the INSTRUCTOR table does not exist in the table - which would be the case if I had not created it previously.It is a good idea to drop the table first, in case it exists from a previous attempt.




```python
#Construct the Create Table DDL statement - replace the ... with rest of the statement
createQuery = "create table INSTRUCTOR(ID INTEGER PRIMARY KEY NOT NULL, FNAME VARCHAR(20), LNAME VARCHAR(20), CITY VARCHAR(20), CCODE CHAR(2))"


#Now fill in the name of the method and execute the statement

createStmt = ibm_db.exec_immediate(conn, createQuery)
```

##  Inserting data into the table



```python
#Construct the query - replace ... with the insert statement
insertQuery = "insert into INSTRUCTOR values (1, 'RAV','Ahuja', 'TORONTO','CA')"

#execute the insert statement
insertStmt = ibm_db.exec_immediate(conn, insertQuery)
```

Now I use a single query to insert the remaining two rows of data



```python
#replace ... with the insert statement that inerts the remaining two rows of data
insertQuery2 = "insert into INSTRUCTOR values(2, 'Raul','Chong','Markham','CA'), (3, 'Hima','Vasudevan','Chicago','US')"

#execute the statement
insertStmt2 = ibm_db.exec_immediate(conn, insertQuery2)
```

## Querying data in the table

In this step I will retrieve data we inserted into the INSTRUCTOR table. 



```python
#Construct the query that retrieves all rows from the INSTRUCTOR table
selectQuery = "select * from INSTRUCTOR"

#Execute the statement
selectStmt = ibm_db.exec_immediate(conn, selectQuery)

#Fetch the Dictionary (for the first row only) - replace ... with your code
... 
ibm_db.fetch_both(selectStmt)
```




    {'ID': 1,
     0: 1,
     'FNAME': 'RAV',
     1: 'RAV',
     'LNAME': 'Ahuja',
     2: 'Ahuja',
     'CITY': 'TORONTO',
     3: 'TORONTO',
     'CCODE': 'CA',
     4: 'CA'}




```python
#Fetch the rest of the rows and print the ID and FNAME for those rows
while ibm_db.fetch_row(selectStmt) != False:
   print (" ID:",  ibm_db.result(selectStmt, 0), " FNAME:",  ibm_db.result(selectStmt, "FNAME"))
```

     ID: 2  FNAME: Raul
     ID: 3  FNAME: Hima
    

Execute an update statement that changes the Rav's CITY to MOOSETOWN 



```python

updateQuery = "Update INSTRUCTOR SET CITY = 'MOOSETOWN' WHERE FNAME = 'RAV'" 
udateStmt = ibm_db.exec_immediate(conn, updateQuery)
```

##  Retrieving data into Pandas            



```python
import pandas
import ibm_db_dbi
```


```python
#connection for pandas
pconn = ibm_db_dbi.Connection(conn)
```


```python
#query statement to retrieve all rows in INSTRUCTOR table
selectQuery = "select * from INSTRUCTOR"

#retrieve the query results into a pandas dataframe
pdf = pandas.read_sql(selectQuery, pconn)

#print just the LNAME for first row in the pandas data frame
pdf.LNAME[0]

```




    'Ahuja'




```python
#print the entire data frame
pdf
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>ID</th>
      <th>FNAME</th>
      <th>LNAME</th>
      <th>CITY</th>
      <th>CCODE</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>RAV</td>
      <td>Ahuja</td>
      <td>MOOSETOWN</td>
      <td>CA</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>Raul</td>
      <td>Chong</td>
      <td>Markham</td>
      <td>CA</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>Hima</td>
      <td>Vasudevan</td>
      <td>Chicago</td>
      <td>US</td>
    </tr>
  </tbody>
</table>
</div>




```python
pdf.shape
```




    (3, 5)



## Close the Connection



```python
ibm_db.close(conn)
```




    True


