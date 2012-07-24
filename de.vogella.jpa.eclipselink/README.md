== running

In order to run the examples you need maven installed, and be on the PATH

to run the test type:
```
mvn test
```
it will only run for the first time

=== clean test

If you want to run the tests from scratch you can use the 'clean' goal, which deletes the whole derbyDB directory

```
mvn clean test
```

The problems described here:

https://groups.google.com/d/msg/vogella/cp2L_L-OwOA/VvAcSzZ9l0cJ
https://groups.google.com/d/msg/vogella/6sD6Xq67ioI/A1uYqrJjOJAJ
https://groups.google.com/d/msg/vogella/6sD6Xq67ioI/A1uYqrJjOJAJ

the invalid DB state can be checked by:

```
mvn exec:java -Dexec.mainClass=org.apache.derby.tools.ij -Dexec.args="-p ij.properties show_last_5_person.sql"
...
ij> select * from test.person OFFSET 35 rows;
ID                  |LASTNAME            |FIRSTNAME           |FAMILY_ID  
--------------------------------------------------------------------------
31                  |Knopf_29            |Jim_29              |NULL       
29                  |Knopf_27            |Jim_27              |NULL       
35                  |Knopf_33            |Jim_33              |NULL       
25                  |Knopf_23            |Jim_23              |NULL       
8                   |Knopf_6             |Jim_6               |NULL       
...
```

