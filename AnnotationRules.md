# DRAFT Annotation Rules

This is a collection of rules for how annotations should work.
Some of these duplicate what's in the specs (in particular,
the platform spec and the Common Annotations spec).
You can consider these to be rules about how annotations
currently work, as well as rules about how new annotations
should be designed to work to be consistent with current
annotations.  Of course, there are exceptions, called out below.



### Annotations and inheritance

This section describes how annotations interact with inheritance.
Note that, in general, we do not use the @Inherited annotation
on our annotations.  @Inherited only applies to annotations at
the class level.  If multiple classes in a hierarchy are annotated
with an @Inherited annotation, only the annotation furthest from
the base class is returned.  For resource annotations, we want
all such annotations to apply, so @Inherited would give the wrong
results.  For component-defining annotations, we don't want the
fact that a superclass is a component to cause all subclasses to
be components, so we don't use @Inherited for component-defining
annotations.

Some frameworks have defined additional annotations that specify
how **their** annotations behave with regard to inheritance.
For example, see the JPA @Inheritance and @DiscriminatorColumn
annotations.

Note that CDI is an exception to this, and recommends that scopes and interceptor bindings are declared @Inherited. It also recommends that stereotypes may be @Inherited depending on their intended usage. It recommends that qualifiers are not declared @Inherited.
##### General rules

A resource annotation (@Resource, @EJB, etc.) on a superclass
is **not** overridden by a similar annotation on a subclass.

In general, annotations on interfaces are ignored.

A method-level annotation on a superclass method is ignored if the
subclass overrides the method; only annotations on the subclass
method apply.

Since a class always "overrides" methods in an interface, annotations on
the interface method are ignored.

A method-level annotation on a superclass method or field that is **not**
overridden "shows through" and still applies.  (Note that it is not possible
to override a field.)

A method-level annotation on a private superclass method or field remains
in effect.

##### Exceptions

JAX-RS allows per-method annotations on the methods of an interface.
If the implementing class doesn't declare **any** JAX-RS annotations
on a method (other annotations are allowed), the JAX-RS annotations
on the interface's method apply.  Any JAX-RS annotation on a method
overrides **all** JAX-RS annotations on the corresponding interface method.

JAX-RS defines a similar rule for annotations on methods of a superclass,
again violating the general rule above.  If a subclass overrides a method
but does not include any JAX-RS annotations on the method declaration, the
JAX-RS annotations from the superclass apply.

>XXX - what happens if the class implements multiple interfaces with the same method but different annotations?
>Santiago - The spec is mute about this case. It does say that annotations from superclasses take precedence over annotations in implemented interfaces.

In some cases, an annotation on a method will "show through" to subclasses
that override the method.  Since this prevents the normal approach of
"disabling" the annotation by omitting it, the annotation will need an
attribute that undoes its normal effect.  For example, JAX-WS has
@WebMethod(exclude=true) and Connectors has @ConfigProprty(ignore=true).
Generally this style is discouraged.

EJB allows the @Local and @Remote annotations to appear on interfaces.

The JPA annotations @Table, @SecondaryTable, and @SecondaryTables
interact with the @Inheritance annotation to control how they behave
with respect to inheritance.

CDI producer fields or methods are not inherited by subclasses, even if they
are not overridden.  The superclass would not know how to produce an
instance of the subclass.

CDI introduces specialization, which affects inheritance. If a subclass is defined @Specializes, then

* all qualifiers are inherited, regardless of whether they are declared @Inherited
* the EL name of the bean is inherited (@Named is not declared @Inherited)


The following component-defining annotations do have @Inherited on them due to historical accident.  In common usage, these annotations are not involved in inheritance hierarchies so it was decided to leave them in a state of non-compliance with these rules rather than risk introducing unforeseen problems.

* javax.faces.validator.FacesValidator
* javax.faces.view.facelets.FaceletsResourceResolver
* javax.faces.render.FacesBehaviorRenderer
* javax.faces.render.FacesRenderer
* javax.faces.convert.FacesConverter
* javax.faces.component.behavior.FacesBehavior
* javax.faces.component.FacesComponent



### @Documented

Many annotations are used to effect the implementation of a class.
For example, the Resource annotations define resources that are
used by the implementation of the class.  The resources used by a
class are rarely part of the API specification of the class.

In some cases, annotations add to the API specification of a class,
and contain important information that a user of the class will need
to know.  Annotations of this sort should be marked with @Documented;
these annotations will then appear in the javadoc for the class.

> XXX - We haven't done a good job of applying @Documented to our annotations.  We need to reconsider this issue.

All CDI annotations are marked @Documented.

### Component-defining annotations

A component-defining annotation (e.g., @Stateless, @WebService) on a
superclass does not apply to a subclass.

A component-defining annotation (e.g., @Stateless, @WebService) on an
interface does not apply to a class that implements the interface.
(This is an instance of the general rule that annotations on interfaces
are ignored.)



### Class vs. method annotations

##### General rules

Class level annotations that effect the semantics of the class
are local to the defining class.
These class level annotations are a shorthand for annotating
methods defined in that class.  Class level annotations
play no direct role in inheritance unless the annotation
is marked @Inherited.  See above for why we don't use
@Inherited for annotations that need to combine, although
it is useful for annotations that simply need to override
the same annotation in a superclass (e.g., @ServletSecurity).

After the effect of class level annotations is considered
for each class, the methods defined on that class end up
with attributes that are a result of a direct annotation
or a result of applying the class level annotation.

When a subclass overrides a method, none of the attributes
of the superclass method apply.  When a subclass does not
override a method, the attributes of the method are the
effective attributes for the method from the parent class.

A method default rule such as "all public methods are web
service methods" acts as an implicit class level annotation.

Some class level annotations don't effect the semantics of
the class, but are just attached to some class "for convenience".
For example, @Resource doesn't effect the semantics of the class.
Many JPA annotations don't effect the specific class they're
attached to, but instead effect the persistence unit the class
is a part of.

##### Exceptions

In some cases, annotations on subclass and superclass methods
"combine".  For example, the interceptor annotations on superclass
methods apply even if the subclass overrides the method and provides
its own interceptor annotations.  The interceptors from both the
superclass and the subclass are invoked, from superclass down to
subclass.

##### Package level annotations

We've made very little use of package level annotations in Java EE.
Where they are used, they effectively act as another level of defaulting
above classes.  That is, package level annotations are overridden by
class level annotations, which in turn are overridden by method level
annotations.

For annotations that can be used at the class level and at the package
level, the implementation should look for the annotation on the class
first [XXX - possibly following the superclass hierarchy if the annotation is not
marked @Inherited].  If the annotation isn't found, the implementation
should look for the annotation on the package for the class [XXX - possibly
following the superclass hierarchy and considering the package for each
superclass if the annotation isn't marked @Inherited].

JAXB uses package level annotations in this way.

> XXX - It's unclear what the inheritance rules for package level annotations should be.



### Annotations vs. deployment descriptors

In general, information in deployment descriptors should override
information in annotations.  In some cases, the information combines,
such as for injection target specifications or for interceptors.
In rare cases, an annotation can override a deployment descriptor entry;
see the web fragments section below.

From (the proposed update to) the Java EE platform spec:

        The following list describes the rules for how a deployment
        descriptor entry may override a Resource annotation.
 
        - The relevant deployment descriptor entry is located based on
          the JNDI name used with the annotation (either defaulted or
          provided explicitly).
 
        - The type specified in the deployment descriptor must be
          assignable to the type of the field or property.
 
        - The description, if specified, overrides the description
          element of the annotation.
 
        - The injection target, if specified, defines additional
          injection points for the resource.
 
        - The mapped-name element, if specified, overrides the
          mappedName element of the annotation.
 
        - The res-sharing-scope element, if specified, overrides the
          shareable element of the annotation. In general, the
          Application Assembler or Deployer should not change this
          value as doing so is likely to break the application.
 
        - The res-auth element, if specified, overrides the
          authenticationType element of the annotation. In general, the
          Application Assembler or Deployer should not change this
          value as doing so is likely to break the application.
 
        - The lookup-name element, if specified, overrides the lookup
          element of the annotation
 
        It is an error to request injection of two resources into the
        same target.  The behavior of an application that does so is
        undefined.



### Web fragment descriptors vs web.xml and annotations

An ejb-jar.xml in a war file is treated the same as a web-fragment.xml
in terms of the interaction with web.xml for resources.

A Resource entry in a web fragment descriptor overrides annotations
**only in the corresponding library**, using the rules above.

A Resource entry in a web.xml file overrides **all** attributes of any
corresponding Resource entry in a web fragment descriptor, **except**
for the injection targets, which are combined.

A Resource entry in a web.xml file overrides a Resource annotation on a
class in WEB-INF/classes using the rules above.

A Resource annotation on a class in WEB-INF/classes overrides **all**
attributes of any corresponding Resource entry in a web fragment descriptor.
**This may be the only place where an annotation overrides a deployment
descriptor entry.**



### Repeated annotations

In some cases it's desirable to allow a particular annotation to
occur multiple times on a single element, usually with different
parameters.  The convention for allowing @SomeAnnotation to be
repeated is to define a pluralized version of the annotation:

```java
 public @interface SomeAnnotations {
     SomeAnnotation[] value();
 }
```

Used as:

```java
 @SomeAnnotations({
     @SomeAnnotation("value1"),
     @SomeAnnotation("value2")
 })
 public class MyClass { ... }
```

We're hoping that Java SE 8 will support this style more directly
in the compiler, allowing:

```java
 @SomeAnnotation("value1")
 @SomeAnnotation("value2")
 public class MyClass { ... }
```



### Annotations vs. interfaces

In some cases we use annotations to mark which classes and which methods
should be used by the container for which purposes, and in other cases we
specify this interface with a Java language interface.  The following rules are
used to choose which to use when:

1. When defining an API that an application is going to implement and
   the container is going to call, use an interface, unless one of the
   following rules applies.
2. If an application class is providing an API that's exposed to other
   applications, and that class also needs to provide methods that the
   container will call for lifecycle functions (or other container support
   or management functions), use an annotation to mark those methods so
   that the application has flexibility in the choice of names for those
   methods.
3. If an application is going to expose an interface that another
   application (or user) is going to use **without** Java, use annotations
   to mark the methods that correspond to this interface.  This avoids the
   need to define a Java interface that's never going to be used by anyone
   other than the one class implementing the interface.
4. If there can be more than one occurrence of a method for a given
   purpose, use an annotation.
5. If an interface is chosen and the interface has many methods,
   consider providing an "adapter" class that implements all the methods
   with nop implementations.  Applications can then subclass this adapter
   class to avoid the need to implement all methods.  It's often better to
   decompose the interface into multiple smaller interfaces.


### Annotation Style

Use correct types instead of strings for annotation attributes when possible.

> XXX - Need guidance on use of Class vs. String for class names

For optional attributes, prefer these default values:
* String - ""
* int - -1
* Class - void.class
* [] - \{ }

Use enumerated types rather than magic String values when the set of 
possible options is finite and fairly small.

Avoid nesting annotations more than one level.

Rather than adding more and more different aspects to an annotation, 
try to separate the concerns by creating different annotations that
may be applied together or independently.

Use "value()" as the element name in an annotation that only has one 
element or if, when using the annotation, that one element is the
one most commonly specified.



### Open Issues


Need to check JAXB annotations.

Need to write up something that describes when it's appropriate to only
add an annotation, or only add a deployment descriptor entry, vs. having
both.


