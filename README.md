# Kids First Data Dictionary

The Kids First data dictionary provides the first
level of validation for genomic data and minimal clinical elements managed by the Kids First Data Resource Center (KFDRC).
The general structure and concepts derive from the [GDC Data Dictionary](https://github.com/nci-gdc/gdcdictionary).

Written in YAML, the JSON schemas define all the individual entities (nodes) in the
data model. Moreover, these schemas define all of the
relationships (links) between the nodes. Finally, the schemas define
the valid key-value pairs that can be used to describe the nodes.

Read more information about the Data Dictionary structure in Gen3 here:
https://github.com/uc-cdis/gen3-userservices-info/blob/master/data_dictionary/data_dictionary_readme.md

## Data Dictionary Structure

The data dictionary covers nodes, and the relationships between then,
primarily for tracking of data files and running genomic harmonization pipelines.
All of the nodes in the data model are strongly typed and individually
defined for a specific data type. For example, submitted files can come in two different forms,
aligned or unaligned; within the model we have two separately defined nodes for
`Submitted Unaligned Reads` and `Submitted Aligned Reads`. Doing such allows for faster querying of
the data model as well as providing a clear and concise representation of the data in the KFDRC.

Beyond node type, there are also a number of GDC-based extensions used to further define the nodes within
the data model. Nodes are grouped up into categories that represent broad roles for the node such
as `analysis` or `biospecimen`. Additionally, nodes are defined within their `Program` or `Project`
and have descriptions of their use. All nodes also have a series of `systemProperties`; these
properties are those that will be automatically filled by the system unless otherwise defined by
the user.  These basic properties define the node itself but it still needs to be placed into the model.

The model itself is represented as a graph. Within the schema are defined `links`; these links
point from child to parent with Program being the root of the graph. The links also contain a
`backref` that allows for a parent to point to a child. Other features of the link include a
semantic `label` that describes the relationship between the two nodes, a `multiplicity` property
that describes the numeric relationship from the child to the parent, and a requirement property
to define whether a node must have that link. Taken all together the nodes and links create the
directed graph of the KFDRC Data Model.

## Node Properties and Examples

Each node contains a series of potential key-value pairs (`properties`) that can be used to
characterize the data they represent. Some properties are categorized as `required` or `preferred`.
If a submission lacks a required property, it cannot be accepted. Preferred properties can denote
two things: the property is being highlighted as it has become more desired by the community or
the property is being promoted to required. All properties not designated either `required` or
`preferred` are still sought, but submissions without them are allowed.

The properties have further validation through their entries. Legal values are defined in each
property. For the most part these are represented in the `enum` categories although some keys,
such as `submitter_id`, will allow any string value as a valid entry. Other numeric properties
can have maximum and minimum values to limit valid entries.  For examples of what a valid entry
would look like, each node has a mock submission located in the `examples/valid/` directory.

## Dictionary Changes

The following is an attempt to layout guidelines for the level of
impact of changes to the dictionary by categorizing them into
**Breaking Changes**, **Entity Relation Additions**, **Schema Additions**,
**Cosmetic Corrections**.

### Breaking Changes

Breaking changes are changes to the dictionary such that previously
allowable data is invalid against the new schema, e.g. a **removal** of
part of the dictionary.

N.B. That not all changes classified here as Breaking Changes are
promised to require a data migration.  It is possible that no data
exists that is invalidated by the change, e.g. making a
field required that has never been left blank.  This should be
confirmed against the corpus of data and the userbase should be
notified of a break in backwards-compatibility.

**Breaking Changes include**:
- Renaming/removing anything that is not a description or comment
  - Removing an entity schema
  - Removing a property's allowed `type`
  - Removing a property's allowed `enum` value
  - Changing an entity's `category`
  - Changing an entity's `unique_keys`
  - Changing an entity's `links`, including `label`, `backref`
  - Removing a property from an entity schema
- Changing existence requirements
  - Adding a property to the `required` list
  - Changing link `required` from `false` to `true`
  - Changing link `multiplicity` from `one_to_many` or `many_to_one` to `one_to_one`
  - Changing link subgroup exclusivity from `false` to `true`


**Handling breaking changes**:

Sometimes it may be best to introduce necessary breaking changes
incrementally.  Given you have State A and State B, which are
incomatible, if you can create a State AB that is compatible with
both, you can upgrade to State AB without breaking changes, update
data to be compliant with State B, then upgrade to State B.

1. State A is deployed
2. Upgrade to State AB
3. Update data while State AB is deployed to be valid under State B
4. Upgrade to State B

An example could be: _Introduce required property `color`_:

1. Property `color` does not exist
2. Deploy schema that allows but does not require `color`
3. Add color to all records
4. Deploy schema that requires `color`


### Entity Relation Additions

Additions to the dictionary that create entities or add links between
entities should not be considered breaking changes, however, they
should be carefully considered in context of downstream effects.

**Entity Relation Additions include**:
- Adding a new entity schema
- Adding a new link between entities


**Entity Relation downstream effects**:
- The database schema will have to be updated
- Users should be notified of additions

### Schema Additions

The schema is setup to allow strict additions to properties have minimal
impact on existing data.


**Schema Additions include**:
- New properties
- New allowed types for properties
- New allowed `enum` members for properties

**Schema Addition downstream effects**:
- Users should be notified of additions


### Cosmetic Corrections

Cosmetic corrections are changes that have little to no behavioral
effects.

**Cosmetic Corrections include**:
- Changes to terms
- Changes to documentation
- Schema formatting changes

**Schema Addition downstream effects**:
- No large impacts


### Versioning

The Dictionary should
follow [Semantic Versioning](http://semver.org/) by updating the
`VERSION` file to `MAJOR.MINOR.PATCH` accordingly:

1. MAJOR: version when you make incompatible API changes: **Breaking Changes**
   - e.g. 1.2.4 -> 2.0.0
2. MINOR: version when you add functionality in a backwards-compatible manner: **Relationship Additions**, **Schema Additions**
   - e.g. 1.2.4 -> 1.3.0
3. PATCH: version when you make backwards-compatible bug fixes: **Cosmetic Corrections**
   - e.g. 1.2.4 -> 1.2.5

## Contributing

Read how to contribute [here](https://github.com/kids-first/kf-dictionary/blob/develop/CONTRIBUTING.md).
