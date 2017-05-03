# JCP Processes

This page provides some additional detail on how the Java EE group at
Oracle uses or interprets the standard JCP processes described in the
[JCP process document](http://jcp.org/en/procedures/jcp2).

The JCP process defines two ways to update a JCP spec:

* **Full JSR** - This is the common case where a JSR is submitted, an
  expert group is formed, etc.

* **Maintenance Release** - This is a lightweight process suitable for
  small or obvious changes. No expert group is formed. The Executive
  Committee gets a "line item veto" for each change proposed in the
  Maintenance Release.

We typically make two types of changes to specs:

* **Errata** - These are small changes to a spec that don't change the
  meaning of the spec. They might include fixing typos, correcting
  obvious inconsistencies in the spec, clarifying the wording of
  requirements, fixing bugs where the spec doesn't correspond to the
  Reference Implementation, etc. In most cases, an errata should not
  require a change to the reference implementation or the TCK.

* **API Changes** - These are changes that change the meaning of the
  spec. We can break these changes down into three cases:
  - **API signature changes** - Adding a class, adding a method, etc.
  - **Change in behavior** - Change the way a method interprets existing
     parameters, e.g., a null parameter now returns a default value
     instead of throwing a NullPointerException.
  - **Change in requirements** - Requiring the implementation to do
     something specific in a case that was previously undefined.

Unfortunately, the two types of changes don't map directly to the two
ways of updating a spec.



## Which process do I use for which change?

We always use the Maintenance Release process for spec errata.

We also use the Maintenance Release process for API Changes.

Of course, we only use the Full JSR process for significant API Changes.

Even though both errata and API changes can be done in a Maintenance
Release, we prefer to not mix them in a single Maintenance Release.



## Can I do this in a Maintenance Release?

Which API changes are "too big" for the Maintenance Release
process is a judgment call. In general, the changes should
be straightforward. A person who understands the spec should
be able to look at the proposed changes and say "yes, I understand
that". If you feel like you need to consult other experts
in order to make sure your proposed changes are correct,
you may be outside the scope of a Maintenance Release.
But note that just because you **have** consulted other experts
doesn't mean you're automatically disqualified from the
Maintenance Release process.

The appropriate measure is less the **size** of the change and more
the **complexity** of the change. (But of course size and complexity
are related.)

You can add new methods, new classes, and new packages to an existing spec.

You can **not** create an entirely new spec.

In the end, the practical answer to "what can I do in an MR?" is
"whatever the JCP EC approves". If you think there's significant risk that
they won't approve it if they see it in an MR, don't do it in an MR.



## What happens to the version number of my spec?

A Maintenance Release that includes API changes defines a new
version of the spec.

A Maintenance Release that includes **only** errata does **not**
define a new version of the spec, but only updates the spec document.
The spec clarifications included in such a Maintenance Release apply
to the current version of the API.

If a single Maintenance Release contains both errata and API changes,
the clarifications in the errata apply to only the newly defined
version of the API. For this reason it's usually best to separate
errata and API changes into separate Maintenance Releases.

See [Versioning](Versioning) for more on versioning of APIs and specification
documents.
