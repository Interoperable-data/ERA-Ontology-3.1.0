# Changes to 3.1.0 in regards to ERATV and EVR

- spelling error in thw word `amendment`

## ERATV `era:VehicleType`

- Updated ERATV parameters as per Application Guide for 2011/665/EU, amended by (EU) 2019/776, (EU) 2021/701 and (EU) 2023/1696:
  - Deprecated according to the amendments:
    - 4.1.4 (operating conditions), 4.1.6 - 4.1.10
    - 4.3.2 (altitude)
    - 4.6.1-3
    - 4.7.3.1-2
    - 4.8.3 and 4.8.7-8
    - 4.9.3
    - 4.10.2-3, 9, 12-13
    - 4.11.x
    - 4.12.x, except 4.12.3.1 (Platform heights for which the vehicle is designed.)
    - 4.13.1.2-4, 6.
    - 4.13.2.2, 4
    - 4.14.2.X
  - corrected eratvIndex if this was not a string
  - added `era:unitOfMeasure qudt:KiloGM` for parameters under eratv 4.4 and 4.5
  - added `era:unitOfMeasure qudt:MilliM` for cant related parameters
  - added unit of speed for 4.6.4, acceleration for 4.7.1, gradient in 4.7.2, etc...
  - Split the parameter 4.7.7 as already 4.6.4 was split.
  - updated `era:safeConsistLengthInformationNecessary` to the VehicleType class, and SKOS Concept SCheme updated to provide correct meaning, as it responds to data for ERATV parameter 4.13.1.10.
  - updated `era:etcsMVersion` to the VehicleType class, as it responds to data for ERATV parameter 4.13.1.11.
  - updated `era:usesGroup555` to the VehicleType class, as it responds to data for ERATV parameter 4.13.2.12.
  - updated `era:atoSystemVersion` to the VehicleType class, as it responds to data for ERATV parameter 4.13.3.1.
- added `dcterms:relation` and `dcterms:requires` for dependent parameters, like 4.7.4.X.2 which depend on 4.7.4.X.1
  
## ERATV `era:VehicleAuthorization`

- Creation of the new Class
- Migration of existing properties to `era:VehicleAuthorization`:
  - (3.1.1) era:authorisedCountry
- Added to `era:VehicleAuthorization`:
  - (3.1.2.1) Status: { Valid, Suspended, Revoked, To be renewed }
  - (3.1.2.1 & 3.1.3.X.2) StatusChangeDate `xsd:date`, replaces the string encoding of the date in Status.
  - (3.1.3.1.1) issueDate `xsd:date`: Date on which the type authorisation being recorded was issued (not the date of entry in ERATV)
  - (3.1.2.2) authorisationValidity `xsd:date`
  - (3.1.3.1) isOriginalAuthorisation `xsd:boolean` replaces a separate entry for this Authorisation. Should be false for subsequent modifications
  - (3.1.3) previousAuthorisation `<IRI to previous Authorisation>`
  - (3.1.3.1.2 & 3.1.3.X.3) `<IRI to authorisation Holder>` (/ORGS)
  - (3.1.3.X.4) `xsd:string` - Authorisation modification document reference
  - (3.1.3.X.5) `<IRI to EC DEC/ EC TEC certificate>` - Reference(s) of type examination or design examination type
  - (3.1.3.X.6) `<SKOS Concept Scheme>` - Applicable national rules (if applicable) - Selection from a predefined list (multiple selection possible) based on Commission Decision 2015/2299/EU
  - (3.1.3.X.7) `xsd:string` Comments
  - (3.1.3.X.8) `xsd:string` Declaration by the proposer - Reference to the written declaration by the proposer referred to in Article 16 of Regulation (EU) No 402/2013 (CSM-REA)
