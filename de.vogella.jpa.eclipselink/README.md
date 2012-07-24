Running
================================

In order to run the examples you need maven installed, and be on the PATH

to run the test type:
```
mvn test
```
it will only run for the first time

clean test
-------------------------

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

the problem is that it JPA Persistence spec 2.0 says in 3.2.4:
> Bidirectional relationships between managed entities will be persisted based on references held by the owning side of the relationship.

and in 2.9:
> The inverse side of a bidirectional relationship must refer to its owning side by use of the mappedBy element of the OneToOne, OneToMany, or ManyToMany annotation.

so in case of the Person - Family relationship, in Family the member field is annotated as:
```
    @OneToMany(mappedBy = "family")
    private final List<Person> members = new ArrayList<Person>();
```
so Family is the inverse side, meaning Person is the owner.

so if want to be compliant to 3.2.4 the relationship must be persisted as:
```
    person.setFamily(family)
```

but as 3.2.4 states it later:
> It is the developer’s responsibility to keep the in-memory references held on the owning side and those held on the inverse side consistent with each other when they change.

so 2 lines are needed to keep the relationship correct both in memory and in the db:
```
    // needed to keep DB consistent
    person.setFamily(family)
    // needed to keep in-memory refs consistent
    family.getMembers().add(person);
```
