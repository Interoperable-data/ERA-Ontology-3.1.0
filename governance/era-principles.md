# ERA PRINCIPLES AND STANDARDS

## Principle 1. URIs for vocabulary components
**All vocabulary components (concepts and properties) MUST have a URI that follows a certain stucture and has certain restrictions on its identifier.**

Vocabulary component URIs MUST follow the following scheme: http://data.europa.eu/949/[vocabulary-component]

where [vocabulary-component] is the supplied name (identifier) for the class or property that is being defined. This identifier should follow:

* Camel Case Style
* For classes, a noun, and the initial character is uppercase
* For properties, a verbal form (or adjective) and the initial character is lowercase
* Unique within the vocabulary

The following are examples of these URIs:

    http://data.europa.eu/949/ContactLineSystem
    http://data.europa.eu/949/maximumBrakingDistance

## Principle 2. URIs for resources

**All the resources of the ERA Knowledge Graph (e.g., specific operational points, sections of line, tracks, etc.) MUST have a URI that follows a certain stucture and has certain restrictions on its identifier.**

Resource URIs MUST follow the following scheme: http://data.europa.eu/949/[vocabulary-module]/[class-name]/[data-id]

where

* [vocabulary-module] is one of the main modules of the vocabulary: 
    * functionalInfrastructure
    * topology
    * vehicleTypes
* [class-name] is the class (in plural) and in Camel Case style, starting with lowercase, that the resource belongs to (that is, its rdf:type). This does not apply to those in vehicleTypes.
* [data-id] is the resource's unique identifier within the class it belongs to. Standardized identifiers must be used following the data policies established in the [EC Data Governance and Data Policies](https://ec.europa.eu/info/sites/default/files/summary-data-governance-data-policies_en.pdf) document:

    * Alphanumeric
    * No separators such as spaces, "/", ".", "(", ")" or their URI encoding. In general identifiers may contain the "-" and "_" separators. 
    * For resources that are not valid yet (resources that will be valid in the future or where valid in the past), the validity dates may be added to the URI. For those that are valid currently, no validity dates will be applied. Note: this may change in the future if an RDF reification approach is considered.

The following are examples of valid URIs:

    http://data.europa.eu/949/functionalInfrastructure/sectionsOfLine/10601_ATAa_ATIz
    http://data.europa.eu/949/functionalInfrastructure/operationalPoints/ATAh-H1K
    http://data.europa.eu/949/authorizedVehicles/vehicleTypes/11-006-0001-2-001

The following will not be valid URIs:

    http://data.europa.eu/949/vehicleTypes#11-006-0001-2-001
    http://data.europa.eu/949/functionalInfrastructure/operationalPoints/ATAh%20H1K

## Principle 3. URIs for SKOS concept schemes and SKOS concepts

**All SKOS concept schemes MUST be compliant with the policies defined in the [Corporate Reference Data Management in the European Commission](https://op.europa.eu/documents/3938058/8065862/CorporateReferenceDataManagementPolicyInTheEC_v20220203_public.pdf.docx/78919508-788f-192f-77ef-efaebd20ecbf?t=1644831074905) document with respect to accesibility and reusability**.

SKOS concept scheme URIs MUST follow the following scheme:  http://data.europa.de/949/concepts/[concept-scheme]/[concept-scheme-label]

where:

* The [concept-scheme] is the name for the concept scheme in lowercase and using "-" as separator whenever the concept scheme name is a compound word, e.g. load-capabilities.

* The [concept-scheme-label] is the name for the concept scheme using CamelCase, e.g., LoadCapabilites

The following are examples of valid URIs for SKOS concept schemes:

    http://data.europa.eu/949/concepts/ten-classifications/TENClassifications
    http://data.europa.eu/949/concepts/load-capabilities/LoadCapabilities

SKOS concept URIs MUST follow the following scheme:  http://data.europa.de/949/concepts/[concept-scheme]/[area]/[concept-id]

where:

* The [concept-scheme] is the name for the concept scheme in lowercase and using "-" as separator whenever the concept scheme name is a compound word, e.g. load-capabilities.
* The [area] is either the term rinf or eratv, depending on whether it is applicable to railway infrastructure (RINF) or to vehicles (ERATV). This is optional
* The [concept-id] is either an id (commonly the case for RINF concepts) or the concept preferred label using "-" or "_" as separators. They should be unique within the concept scheme.

The following are examples of valid URIs for SKOS concepts:

    http://data.europa.eu/949/concepts/etcs-levels/3
    http://data.europa.eu/949/concepts/etcs-system-compatibilities/rinf/
    http://data.europa.eu/949/concepts/etcs-system-compatibilities/eratv/ESC-DK-02-West

The following are examples of non-valid URIs for SKOS concepts:

    http://data.europa.eu/949/kos/train-detection-specific-checks/125_Hz_track_circuit_and_ZP_43_E(manufactured_prior_to_2015). Because it has "(" and ")" as separators.
    http://data.europa.eu/949/kos/min-axle-loads-per-vehicle-category/04.0_50 because it has "." as separator.

## Principle 4. Management of changes in the ERA ontology

**Ontology maintenance (maintenance of classes, properties, reference data and restrictions) involves identifying and managing ontology changes. These changes can be classified from simple bug fixes to the inclusion of additional ontology requirements.**Additionally, ERA is responsible for managing and governing reference data, which are standardized and reusable datasets used across systems and applications to ensure consistency, accuracy, and interoperability. It is critical for maintaining uniformity across datasets and ensuring seamless integration. ERA organises the reference data catalogue using the Simple Knowledge Organization System (SKOS) standard, a W3C recommendation designed for representation of thesauri, classification schemes, taxonomies, or any other type of structured controlled vocabulary. 

Changes in the ontology involve considerations on the types of changes and compatibility of versions, the deprecation of classes, properties and SKOS concept schemes and the corresponding changes in mappings and re-generation of KGs, changes in SPARQL queries, and changes in the applications' UI.The following are several aspects that are considered in this principle. 

### Compatibility of versions ###
This aspect is based on the following assumptions:
* Knowledge graphs and applications MUST be aligned with the latest (deployed) release of the vocabulary. 
* Users SHOULD have access to previous releases of the vocabulary in the git system where the vocabulary is being maintained, by using the tag of the vocabulary version.
* In case that the git system is not available for non-authenticated users,the major ontology releases will be also archived in Zenodo, at PID https://zenodo.org/record/7775344

The following ontology changes represent some of the ontology modifications that have lowest impact on mappings, queries and applications: 
* add a class or a property
* add a subclass-superclass relationship
* add a subproperty-superproperty relationship
* add a property restriction (symmetric, inverse, transitive)
* add a minimum or maximum cardinality restriction
* deprecate a class or a property

These are the backbone of more complex changes that involve a set of these atomic changes, e.g. adding a class and its properties, changing the property’s domain or range, pulling up/down a class in the hierarchy. 

Compatibility is related to the interpretation of the concepts represented in the vocabulary. There are the following cases:
* Full compatible versions. The semantics of the vocabulary in general is not changed; this corresponds to minor changes, e.g., syntactic changes or updates of natural language descriptions. 
* Backward compatible versions. The vocabulary is changed in such a way that the interpretation of the concepts in the new vocabulary is the same as the interpretation of the concepts in the previous version of the vocabulary, e.g., the “additive” inclusion of a class and its properties. 
* Incompatible versions. The vocabulary is changed in such a way that the interpretation of the concepts in the new vocabulary is not the same as the interpretation of the concepts in the previous version of the vocabulary, e.g. pulling up/down a class in the hierarchy. 

Incompatible versions most probably lead to changes in the logic of the applications that consume the ontology and the knowledge graphs.

Other minor modifications can result from changes in the reference data, in the ERA ontology as SKOS concept schemes: 
* add a concept in a concept scheme
* deprecate a concept in a concept scheme

These changes might have no impact on mappings, queries or application UIs. Some of these changes happen under the direct control of specific stakeholders (e.g. as data providers and/or members of workgroups). 

### Changes in mappings and re-generation of KG ###
Each change or set of changes needs to be verified with respect to updating the mappings and executing them to re-generate the knowledge graph. 
Some examples of changes in the vocabulary, and corresponding mapping changes are shown in the following  table:

| Change in Vocabulary  | Change in Mappings |
| ----------------------|------------------|
| Add class `C`| Add mapping rule for instances of class `C`|
| Deprecate class `C` and replace it by class `D`|Add mapping rule for instances of class `D`. Mapping rule for `C` may not change, so there will be instances that belong to both, `C` and `D`|
| Add property `p` for class `C` | Add the generation of values for property `p` in mapping rule for class `C`|
| Change domain for Property `p` from `C1`  to  `C2`| Delete generation of mapping values for property `p` in mapping rule for Class `C1` and add the generation of values for property `p` in mapping rule for `C2`|
| Add or modify annotation properties|No changes|
| Add a superclass-subclass `C1-C2` relationship| Add the generation of inherited properties' values for `C2`|

### Deprecation of classes and properties ###
Classes or properties may become obsolete and  may be replaced by existing classes or properties in the vocabulary. In such cases it becomes necessary to deprecate (not delete) the obsolete terms. Existing applications and mappings may need to change as explained in the subsection on **Compatibility of versions**.

Standardized metadata must be added to the deprecated term:
* http://www.w3.org/2002/07/owl#deprecated/. 
Used to specify that a certain class or property is deprecated. The annotation value is equal to `"true"^^xsd:boolean`.
* http://purl.org/dc/terms/isReplacedBy. 
Used on deprecated terms to link to the terms that replace the deprecated term, in case that this is applicable. The annotation value is the URI of the replacement term.

## Principle 5. Gitlab branching and deployment of the vocabulary

**Version management using a git-based repository follows a certain workflow regarding branches until the vocabulary is deployed to the production environment. It also requires standardized metadata, and version numbering should be compliant with a numbering scheme that has been defined.** 

### Metadata ###
#### Ontology metadata ####
Standardized metadata (annotations) must be added to indicate information on the current release and on the prior version:

Prefixes
* owl:<http://www.w3.org/2002/07/owl#>
* dct:<http://purl.org/dc/terms/>

Metadata
* owl:versionInfo. 
Contains the version number (vX.X.X). Version numbering is defined in the next subsection.
* dct:modified. 
Date of deployment of the latest release.
* owl:versionURI. 
URI of the current release in gitlab. 
* owl:priorVersion. 
URI of the previous version in gitlab/

Releases must be labelled with the appropriate version tag in git. 

#### Term metadata ####
Standardized metadata (annotations) must be included for each new class or property:

Prefixes
* owl:<http://www.w3.org/2002/07/owl#>
* dct:<http://purl.org/dc/terms/>
* rdfs:<http://www.w3.org/2000/01/rdf-schema#>
* sws:<http://www.w3.org/2003/06/sw-vocab-status/ns>
* era:<http://data.europa.eu/949/>

Metadata
* rdfs:label
Preferred label for the term.
* dct:created
Date when the term was created.
* dct:modified. 
Date when the term was updated.
* rdfs:comment
A brief definition of the term.
* rdfs:isDefinedBy
The URI of the resource (vocabulary or ontology) where the term has been defined, for example if it an ontology term the URI will be http://data.europa.eu/949.
* sws:term_status 
It is "stable" or "unstable".
* era:isCoreParameter
The value of this parameter is "true" for those properties that have defined as core parameters in the RINF and ERATV reference documents.
* era:rinfIndex
The parameter number as defined in the [Guide on the application of the common specifications of the register of Infrastructure](https://www.era.europa.eu/sites/default/files/registers/docs/rinf_application_guide_for_register_en.pdf).
* era:eratvIndex
The parameter number as defined in the [Application Guide for the European register of authorised types of railway vehicles (ERATV)](https://www.era.europa.eu/sites/default/files/registers/docs/iu-eratv_application_guide_for_register_2016-797_en.pdf).
* era:routebookIndex
The parameter number as defined in the Appendix D2 - Elements the infrastructure manager has to provide to the railway undertaking for the Route Book from the document Commission Implementing Regulation (EU) 2019/773 of 16 May 2019 on the technical specification for interoperability relating to the operation and traffic management subsystem of the rail system within the European Union and repealing Decision 2012/757/EU
* era:ertmsTracksideIndex
The parameter number of the vocabulary term in Appendix D3 - ERTMS trackside engineering information relevant to operation that the infrastructure manager shall provide to the railway undertaking.


### Vocabulary version numbering according to type of change ###
Ontology version numbering is similar to the convention used in software development [https://semver.org/](https://semver.org/):
* Version numbers follow the pattern, MAJOR.MINOR.PATCH. A PATCH is also known as a FIX.
* Each pattern element MUST be a non-negative integer, and MUST NOT contain leading zeroes. 
* Each element increases numerically starting with 0 (zero) according to the following rules:
  * MAJOR: The field is updated when there is some significant change being introduced. Different versions of the same MAJOR reflect backward-compatible versions. 
  * MINOR: The field is updated when a backward-compatible change is introduced, for example a set of concepts corresponding to a new use case is rolled out. It must be reset to 0 when the MAJOR version is incremented.
  * PATCH: The field is updated when:
    * Typos or bugs are corrected in the vocabulary.
    
    * A single class, property, axiom, individual or annotation is added, deleted or modified.
    
    It MUST be reset to 0 when the MAJOR or the MINOR version are incremented.
  
* MAJOR version zero (0.y.z) is for initial development. Version 1.0.0 corresponds generally to the first productive release of the vocabulary.
* Other variations use build numbers as an additional identifier. MAJOR.MINOR.PATCH.BUILD if there are versions that are tested between releases.

### Vocabulary issues and gitlab branches ###
For each change in the vocabulary, the workflow to follow is:
* Create an issue with a summary of the change, e.g. ADD CLASS C, and its purpose. 
* Create a new *branch* from *master* that includes the issue number in its name.
* Encode the changes in the ontology.
* Add the metadata described in the **metadata** subsection.
* Generate the new version of the HTML documentation (see subsection on **Re-publishing documentation**).
* Commit and push the changes into the *branch* that you have created.
* Request the merge into the *master* branch.

### Re-publishing documentation ###
The vocabulary's HTML documentation must be generated with the [Widoco](https://github.com/dgarijo/Widoco) tool for each change that is being encoded into the ontology. Appropriate scripts and instructions are provided in the README.md document of the repository in order to regenerate and deploy the HTML documentation.

## Principle 6. Geometry representation using the GeoSPARQL pattern ##

**The geomerty representation of those features in the vocabulary that can have a geometry MUST be represented with the GeoSPARQL pattern.**

The GeoSPARQL pattern is presented in the following figure. ERA features are all those vocabulary classes that represent elements that have a geometry, e.g. point, surface, curve.  Examples in the vocabulary include operational points, platforms and tracks.

![GeoSPARQL pattern](./patterns/ERA-geosparql-pattern.png "GeoSPARQL pattern")

Each ERA feature must be a subclass of `geosparql:Feature`. It will then have one or several `geosparql:Geometry` through the property `geosparql:hasGeometry`. This could be an `sf:Point` with its coordinates `geo:lat` and `geo:long`, or a more complex geometry such as a Polyline or Polygon. For example, an operational point has a geometry which is a point with its latitude and longitude coordinates. 

## Principle 7. SKOS concept schemes management as separate files 

**Each reference dataset that is encoded as a SKOS concept scheme MUST be published and maintained independently**.

Each SKOS concept scheme that contains a set of reference data, must be maintained and published independently, i.e. in a separate file, and stored in a triple store to facilitate consumption. All of them may be also published together in a single file to facilitate its consumption by third parties.

## Principle 8. Object properties that relate to SKOS concepts must be modelled following a standard approach.

**All vocabulary object properties whose `rdfs:range` is a `skos:Concept` that belongs to an existing `skos:ConceptScheme` MUST be modelled following a common approach.**

The `rdfs:range` for these object properties MUST be `skos:Concept`, and the `era:inSkosConceptScheme` property is used to specify the concept scheme to which this concept belongs. An additional SHACL shape MUST be created for the verification of the concept scheme to which the SKOS concept that acts as a value belongs. The following pattern is followed:

```turtle
era:PROPERTY
  a owl:ObjectProperty;
  rdfs:range skos:Concept;
  era:inSkosConceptScheme scheme:CONCEPT_SCHEME.

shp:NODE_SHAPE
  a sh:NodeShape;
  sh:property shp:PROPERTY_SHAPE;
  sh:targetObjectsOf era:PROPERTY.
shp:PROPERTY_SHAPE
  a sh:PropertyShape;
  sh:hasValue scheme:CONCEPT_SCHEME;
  sh:path skos:inScheme.

con:CONCEPT_1
  a skos:Concept;
  skos:inScheme scheme:CONCEPT_SCHEME.
...

id:INSTANCE era:PROPERTY con:CONCEPT_1.
```

This enables validation and adds clarity, but does not enable deduction. This does not require significant maintenance, because the SHACL declaration is generic.

Further discussion on this decision is available at https://git.fpfis.tech.ec.europa.eu/datateam/ERA/era-vocabulary/-/issues/50

## Principle 9. Named graphs for resources in the triple store

**All the resources published in the triple store MUST be stored in their corresponding named graph, so as to faicilitate their management.**

The named graph for each type of resource and environment is as follows:
* ERA ontology
    * http://data.europa.eu/949/graph/dev/ontology
    * http://data.europa.eu/949/graph/uat/ontology
    * http://data.europa.eu/949/graph/prod/ontology
* SKOS concept schemes:
    * http://data.europa.eu/949/graph/dev/skos
    * http://data.europa.eu/949/graph/uat/skos
    * http://data.europa.eu/949/graph/prod/skos
* Data transformed from RINF XML files:
    * http://data.europa.eu/949/graph/dev/rinf
    * http://data.europa.eu/949/graph/uat/rinf
    * http://data.europa.eu/949/graph/prod/rinf
* SHACL shapes:
    * http://data.europa.eu/949/graph/dev/shacl
    * http://data.europa.eu/949/graph/uat/shacl
    * http://data.europa.eu/949/graph/prod/shacl
* Data transformed from the ERATV database:
    * http://data.europa.eu/949/graph/eratv
* Data about infrastructure managers:
    * http://data.europa.eu/949/graph/im
* Data about countries that are available in CELLAR:
    * http://data.europa.eu/949/graph/countries
