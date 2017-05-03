# Versioning

This document describes the versioning rules used by the Java EE group
at Oracle.

We apply version numbers to all three of the JCP-defined artifacts -
specification document, reference implementation (RI), and technology
compatibility kit (TCK). At the completion of a JCP update to a
technology, all three artifacts will have the same version number, in
the single dot _major.minor_ format.

Between JCP updates, each of these artifacts may also be updated,
following these rules:

### Specification document

Updates to a specification document that do not change the meaning of
the specification are called "errata". (See [JCP Processes](JCP Processes)
for details.) An errata update to a specification document is indicated
by including a "Rev level" after the specification version number.  For
example, if an initial specification numbered "1.0" is updated by an
errata, the updated document would be given a number of "1.0 Rev a".  A
subsequent errata update would be "1.0 Rev b".  Note that since an
errata typically does not require a change to the RI or TCK, a Rev
level is **never** applied to those artifacts.

### Reference Implementation

A change to the RI that is not associated with a change to the API (for
example, a bug fix, performance improvement, new feature, etc.) is
indicated by using a dot-dot number of the form _major.minor.micro_
or a patch number of the form _major.minor.micro_patch_.  The
_major_ and _minor_ numbers usually correspond to the version of
the API that is implemented.

### Technology Compatibility Kit

Previously, the TCKs followed a scheme where minor updates, such as low
risk patches, bug fixes, or alternate tests, were indicated by
appending a single letter to the version number.  More significant
updates, such as the addition of new tests, followed the scheme
described above for reference implementation updates.  TCKs now use a
date-based version number. We believe this makes the bundles less
confusing to our licensees, given the number of standalone TCKs that we
post to the partner web site. There has been confusion in the past when
the standalone TCKs were on different letter based versions than the
platform TCK. This situation occurs since it is not always necessary to
patch all standalone TCKs when a platform TCK patch is posted. The date
based version numbers also work better when licensees are in the midst
of certifying and are working with interim weekly patches to validate a
test fix. The format is as follows:

> _technology major.minor.micro-date_ (date in the format _mmddyy_)

For example:

* Java EE CTS 6-093008
* Servlet TCK 3.1-093007
* JSP TCK 3.0-093007
* JBI TCK 2.0-093007

To summarize...

For a spec of version X.Y, the spec document will be version "X.Y" for
the initial release or "X.Y Rev L", where "L" is a letter nominally
starting with "a" and incrementing as more errata are approved via
the JCP Maintenance Review process.

For a spec of version X.Y, the RI will be "X.Y" or "X.Y.Z", where "Z"
is a number starting with 1 and incrementing as more RI bug fixes are
made. ("X.Y.0" should be considered equivalent to "X.Y".)
These changes are outside the JCP process.


## Schema and DTD versioning

Schema and DTD files (both standard-defined and product-specific) are
named using a base name appropriate for the descriptor and an extension
appropriate for the type (.dtd or .xsd). The base name should be all
lower case with words separated by dashes (e.g., "web-app").

The file name also **must** include a version number after the base name,
separated from the base name by an underscore. If the version of the
spec is "X.Y", the file name version number would be "X_Y". For the
Java EE platform version "X", the file name version would be simply "X".

Updates to the product-specific descriptor schema or DTD files that
happen between spec updates must include a revision number after the
version number, separated by a dash.

Examples:
* javaee_6.xsd - the main schema file for Java EE 6
* web-app_3_0.xsd - the web application descriptor defined by Servlet 3.0
* glassfish-web-app_3_0-1.dtd - an updated version of the
  GlassFish-specific web application descriptor corresponding to Servlet 3.0.

We haven't been consistent in whether the first version of a product-specific
descriptor should include a revision of "-0" or just omit the revision number.
