# Future wishlist - pain points, rough edges, and problems

This page is intended as a public space to collect areas of the EE
platform and its included specifications that community members view as
needing improvement. Entries here should focus on issues of usability
by "Joe H. Developer" and the often-forgotten-about end users and
deployers. Things that make people say "Stuff this, I'm switching to
{blah}," points of frustration that slow down Java EE development and
hold up learning.

This article specifically excludes implementer-side issues that aren't
visible to application authors, simply to narrow its scope. A parallel
article for SPI-side and implementer-side issues would be an excellent
addition, but one this article's initial author lacks the experience to
begin at this point.

(This article is still being filled out with the basics, so please
forgive its current skeletal nature).

## Before editing

This is not a free-for-all feature request list. Please focus on areas
of problematic integration, "rough edges" and pain points in the
existing specifications, rather than things that would require whole
new specs and huge extensions to existing specs. Think about things
that would solve real problems experienced by real developers using the
Java EE platform today. Of particular interest are difficulties where
two different specs clash or are hard to use together, and apparent
oversights that make a spec much harder to use than it needs to be.

When you add something, explain what problem(s) it would solve, and
why. Mention the spec(s) the point relates to and always link to the
java.net project or JCP page for the project. Link to JIRA entries,
blog post(s), mailing list post(s) etc that discuss the issue -
preferably not just your own posts. If at all possible, reference
workarounds that are currently being employed for the problem, such as
a Seam 3 or DeltaSpike project, etc. For detailed spec-specific
discussion on an issue, please link to an appropriate spec's wiki page
unless that spec lacks a wiki, in which case create a sub-page here.
Keep entries here short.

If you don't have any references for the topic, please start discussion
about the topic in an appropriate place rather than just immediately
adding it here. Good places include app-server specific forums or
mailing lists, spec-specific -users mail aliases, your blog network,
etc. Once you've found some interest from others and confirmed you
aren't just missing something that already has a simple solution, feel
free to add it here. If you _do_ find a simple solution but think it
was too hard to find, counter-intuitive, or badly under-documented then
add it here along with a proposal for how to improve that situation.

When an issue has been addressed, don't delete it. Annotate it as such,
being specific about the spec version of the fix and if possible adding
links to specific statements about the fix. Then move it to the bottom
of the appropriate list. Same with anything where later discussion
concludes it's a bad idea.

## Core Java EE 6 / 7 spec

* Application configuration (preferences and settings) is an unsolved
and ongoing problem. There are many poor and limited ways to these
tasks, but the platform doesn't provide a clear answer for developers
about which approach(es) to take or a single clearly preferable path.
This is
[not a new issue](http://www.theserverside.com/discussions/thread.tss?thread_id=9360)
and
[has been raised with the EG in the past](http://antoniogoncalves.org/2011/06/10/debate-and-what-about-configuration-in-java-ee-7/)
before
[being raised in July '12 on the EE 7 EG list](http://java.net/projects/javaee-spec/lists/jsr342-experts/archive/2012-07/message/30)
with little apparent interest. It is possible that fairly small changes
may make a big improvement in this area possible.

* Deployment configuration is also an ongoing issue. Changing a bean
alternative in beans.xml currently requires the archive to be modified
and redeployed. There is no standard scheme for deploying "override" or
"companion" archives with alternative bean implementations,
additional/replacement descriptors, etc. This means users generally
cannot deploy a .war directly to their server, they often have to
modify and rebuild it. Application authors are avoiding the use of
standard descriptor-based facilities like JAAS because of the
difficulty of configuring them at run- or deploy-time. TODO: Refs.

* Incomplete SPIs render it difficult to swap out and replace some high
level components of application servers, like JPA providers and JSF
implementations. TODO: Ref ee7 spec discussion.

## [Java Transaction API (JTA)](http://java.net/projects/jta-spec/) [(JSR 907)](http://jcp.org/en/jsr/detail?id=907) and its EJB integration

* The exceptions thrown from some JTA methods are very confusing, and
it's hard for an application author to know what to do with them. How
do you respond to a
[HeuristicMixedException](http://download.oracle.com/javaee/6/api/javax/transaction/HeuristicMixedException.html)?
See:
[Ludovic on blog.bitronix.be](http://blog.bitronix.be/2011/02/why-we-need-jta-2-0/)

* Bean managed transactions using
[UserTransaction](http://docs.oracle.com/javaee/6/api/javax/transaction/UserTransaction.html)
offer no equivalent to the CMT
[TransactionAttributeType.REQUIRES_NEW](http://docs.oracle.com/javaee/6/api/javax/ejb/TransactionAttributeType.html#REQUIRES_NEW).
EJBs using bean managed transactions cannot suspend and resume
transactions.  See:
[Ludovic on blog.bitronix.be](http://blog.bitronix.be/2011/02/why-we-need-jta-2-0/).
Among other things this makes it very hard to handle and retry "normal"
failures of a transactional resource - like a serialization failure or
optimistic locking exception in a commit to a contested table - without
disrupting an unrelated outer transaction.

* EJBs using cannot
[cleanly](http://www.adam-bien.com/roller/abien/entry/how_to_self_invoke_ejb)
make business method calls to their own methods, ie
["self invoke"](http://stackoverflow.com/questions/633687/can-an-ejb3-bean-self-inject-and-call-its-own-methods-via-ejb-container).
This is an issue primarily because of the aforementioned inability for
BMT EJBs to suspend and resume transactions, as it means app authors
can't use a TransactionAttributeType.REQUIRES_NEW subroutine in a retry
loop inside the EJB, they have to factor it out into a separate helper
EJB or make the caller retry on failure.

## [Java Persistence API (JPA)](http://java.net/projects/jpa-spec) [(JSR 314)](http://jcp.org/en/jsr/summary?id=317)

* Control over how and when lazy properties and relationships are
fetched is lacking. It's on the
[JPA 2.1 spec proposal](http://jcp.org/en/jsr/detail?id=338)
but there has been
[little](http://java.net/projects/jpa-spec/lists/jsr338-experts/archive/2011-12/message/1)
discussion on the JPA 2.1 EG list as of July '12. Provider-specific
extensions for some providers address the issue. See
[this query that was sent to the JPA EG and JavaEE 7 EG](http://blog.ringerc.id.au/2012/06/mail-to-jpa-21-expert-group-re-fetch.html)
and its associated links. TODO: More references.

* There is no access to the EntityManager from within entity lifecycle
callbacks and interceptors. **Being addressed in JPA 2.1**. TODO: ref

* CDI injection into EntityListener and entities is not supported,
making integration into CDI apps very hard. **EntityListener injection
added to JPA 2.1**.  TODO: ref

## [Contexts and Dependency Injection (CDI)](https://github.com/jboss/cdi/wiki) ([JSR 299](http://jcp.org/en/jsr/summary?id=299), [JSR 330](http://jcp.org/en/jsr/detail?id=330) and [JSR 346](http://jcp.org/en/jsr/summary?id=346))

* Overriding or extending beans.xml at deploy time requires the archive
to be re-packaged, destroying signing information. This is a cumbersome
and confusing process for users who simply want to deploy an
application. TODO: refs.

* There's no facility for providing bean alternative implementations or
new interceptors at deploy time. TODO: refs.

* Applications have no control over the lifetime of beans obtained via
@New Instance&lt;T&gt;. They are always dependent scoped. TODO: JIRA
ref.

## JavaServer Faces (JSF) 2 ([JSR 344](http://jcp.org/en/jsr/summary?id=344))

* Injection into @FacesConverter is not supported. Fixed by JSF 2.1,
worked around by
[Seam 3 Faces](http://www.seamframework.org/Seam3/FacesModule).
TODO: jsf21 ref

## JDBC 4 ([JSR 221](http://jcp.org/en/jsr/summary?id=221))

* JDBC doesn't have a java.net project or a -users alias, so community
participation is almost totally excluded. This is a real pain for JDBC
driver implementers who aren't EG members; just ask the PostgreSQL JDBC
team.

## The Java Community Process (JCP)

* The -users and -experts list split discourages participation and
feedback.  Weighted toward spec implementers, fails to draw on
experience _using_ the specs. TODO: Refs

* Heavy use of conference calls and reliance on in-person scheduled
meetings excludes loosely coupled internet community contribution.
TODO: Refs

## The Java language as it affects Java EE

* The lack of native properties support in Java, combined with the
reliance on accessors for interceptor and proxying functionality in
EJB, CDI, etc, can produce produces excessively verbose Java EE code.
This is a particular problem with JPA entities, which are often 70% +
accessor methods, usually generated methods that serve no useful
purpose.
