# Common Annotations for the Java Platform 1.2 Maintenance Release

### 1. Add new annotation – **javax.annotation.Priority**  in section 2.7 defined as follows:

The Priority annotation can be applied to classes to indicate in what order the classes should be used.  The effect of using the Priority annotation in any particular instance is defined by other specifications that define the use of a specific class.

For example, the Interceptors specification defines the use of priorities on interceptors to control the order in which interceptors are called.

Priority values should generally be non-negative, with negative values reserved for special meanings such as "undefined" or "not specified". A specification that defines use of the Priority annotation may define the range of allowed priorities and any priority values with special meaning.

```java
package javax.annotation;
import java.lang.annotation.*;
import static java.lang.annotation.ElementType.*;
import static java.lang.annotation.RetentionPolicy.*;
@Target({TYPE}) 
@Retention(RUNTIME)
@Documented
public @interface Priority {
    /**
     * The priority value.
     */
    int value();
} 
```

### 2. Update Resource annotation as follows:

Return Class<?> from Resource.type() instead of the raw Class type.

### 3. Update Section 2.14 javax.annotation.sql.DataSourceDefinition with the following:

Replace the following paragraph (2nd paragraph in the section)

"The url property should not be specified in conjunction with other standard  properties for defining the connectivity to the database. If the url property is  specified along with other standard DataSource properties such as serverName and portNumber, the more specific properties will take precedence and url will be ignored."

with 

"DataSource properties should not be specified more than once. If the url annotation element contains a DataSource property that was also specified using the corresponding annotation element or was specified in the properties annotation element, the precedence order is undefined and implementation specific"

### 4. Update Table 2-9 "url" element description as follows:

"A JDBC URL. If the url annotation element contains a DataSource property that was also specified using the corresponding annotation element, the precedence order is undefined and implementation specific."

### 5. Update Table 2-9 "properties" element description as follows:
Used to specify vendor specific properties and less commonly used DataSource properties. If a DataSource property is specified in the properties element and the annotation element for the property is also specified, the annotation element value takes precedence

### 6. Update Javadocs for PostConstruct annotation 

The method MUST NOT have any parameters except in the case of EJB interceptors in which case it takes an InvocationContext object as defined by the EJB specification. The return type of the method MUST be void. The method MUST NOT throw a checked exception.


with the following

The method MUST NOT have any parameters except in the case of defined by the Interceptors specification.
The method defined on an interceptor class MUST HAVE one of the 
following signatures:

The method MUST NOT have any parameters except in the case of interceptors in which case it takes an InvocationContext object as defined by the Interceptors specification.
The method defined on an interceptor class MUST HAVE one of the following signatures:

`void <METHOD>(InvocationContext)`

`Object <METHOD>(InvocationContext) throws Exception`

Note: A PostConstruct interceptor method must not throw application exceptions, but it may be declared to throw checked exceptions including the java.lang.Exception if the same interceptor method interposes on business or timeout  methods in addition to lifecycle events. If a PostConstruct interceptor method returns a value, it is ignored by the container.

The method defined on a non-interceptor class MUST HAVE the following signature:

`void <METHOD>()`


### 7. Update Javadocs for PostDestroy annotation 

The method MUST NOT have any parameters except in the case of EJB 
interceptors in which case it takes an InvocationContext object as defined 
by the EJB specification.
The return type of the method MUST be void.
The method MUST NOT throw a checked exception.

with the following

The method MUST NOT have any parameters except in the case of
interceptors in which case it takes an InvocationContext object as
defined by the Interceptors specification.
The method defined on an interceptor class MUST HAVE one of the
following signatures:

`void <METHOD>(InvocationContext)`

`Object <METHOD>(InvocationContext) throws Exception`

Note: A PreDestroy interceptor method must not throw application
exceptions, but it may be declared to throw checked exceptions including
the java.lang.Exception if the same interceptor method interposes on
business or timeout methods in addition to lifecycle events. If a
PreDestroy interceptor method returns a value, it is ignored by
the container.

The method defined on a non-interceptor class MUST HAVE the
following signature:

`void <METHOD>()`
