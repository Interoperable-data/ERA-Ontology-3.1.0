# Changes to 3.1.0 in regards to ERATV and EVR

- spelling error `amendment`

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

## EVR `era:Vehicle`

Remaining properties of a Vehicle should be those with a reference in [Table 1 "Parameters of the EVR"](https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=CELEX%3A32018D1614#d1e32-62-1), and not yet within the data for an Authorization or VehicleType.

- `era:operationalRestriction` currently only refers to a SKOS-CS containing TSI NOI-related verification results. This parameter however has its EVR basis [here](https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=CELEX%3A32018D1614#d1e32-74-1). The legal basis clearly states these restrictions to be originating from the APOM, and to be traced in [ERA/TD/2011-09/INT](https://www.era.europa.eu/system/files/2022-11/list_harmonised_national_restriction_codes_en.pdf). This means:
  - this property needs to belong to `era:VehicleAuthorization(Case)`, likely not `era:Vehicle`
  - it is linked to a Vehicle instance, via its link `era:vehicleType` to the type instance, and the link of that instance to the Authorization.
  - For the parameters:

    | Restriction Code |           ERA Vocabulary           | Ontology implementation                                                        | Action         |
    | :--------------- | :--------------------------------: | :----------------------------------------------------------------------------- | :------------- |
    | 1.1.[number]     |    era:minimumHorizontalRadius     | Use existing property                                                          | Link with code |
    | 1.2              |  era:minVehicleImpedanceVoltages,  | Use existing properties                                                        | Link with code |
    |                  |  era:minVehicleInputCapacitance,   |                                                                                | Link with code |
    |                  |    era:minVehicleInputImpedance    |                                                                                | Link with code |
    | 1.3.[number]     |                 -                  | era:vehicleTypeMaximumSpeed cannot be used. The value depends                  | New ?p needed  |
    |                  |                                    | on the Area Of Use, and must therefore be created as a                         | Link with code |
    |                  |                                    | property of era:VehicleAuthorization.                                          | Link with code |
    | 1.4.[number]     |   era:maximumLocomotivesCoupled    | Use existing property                                                          | Link with code |
    | -                |                 -                  | -                                                                              | -              |
    | 2.1              |         era:gaugingProfile         | Use existing SKOS CS `GaugingProfiles` (Kinematic gauge (coding WAG TSI))      | Link with code |
    | 2.2.1-8          |         era:wheelSetGauge          | Use existing SKOS CS `NominalTrackGauges`                                      | Link with code |
    | 2.3              |   era:etcsEquipmentOnBoardLevel    | Use value `era-eeobl-eratv:None` from SKOS CS `ETCSEquipmentLevels`            | Link with code |
    | 2.4              |   era:etcsEquipmentOnBoardLevel    | Use other values from SKOS CS `ETCSEquipmentLevels`                            | Link with code |
    | 2.4.20           |      era:voiceRadioCompatible      | Use existing property                                                          | Link with code |
    | 2.4.21           |      era:dataRadioCompatible       | Use existing property                                                          | Link with code |
    | 2.5.1xx          |     era:protectionLegacySystem     | Use existing property                                                          | Link with code |
    | 2.5.2xx          |       era:legacyRadioSystem        | Use existing property                                                          | Link with code |
    | 2.6.1xx          |                 -                  | Use value `era-tpls-eratv:SSC-BL3` from SKOS CS `TrainProtectionLegacySystems` | Link with code |
    | 2.6.2xx          |                 -                  | Use value `era-lrs-eratv:TETRA-URCA` from SKOS CS `LegacyRadioSystems`         | Link with code |
    | 2.7.1, 3, 5-7    |     era:operationalRestriction     | Use existing SKOS CS `era-skos-Restrictions.ttl`                               | OK             |
    |                  |  era:quieterRoutesExemptedCountry  | Use existing SKOS values 2.7.2 & 2.7.4 in `era-skos-Restrictions.ttl`          | Check with PAD |
    |                  | era:compositeBrakeBlockRetrofitted | Use existing SKOS value 2.7.1 in `era-skos-Restrictions.ttl`                   | Check with PAD |
    | -                |                 -                  | -                                                                              | -              |
    | 3.1.1-4          |        era:temperatureRange        | Use existing SKOS CS `era-tr:TemperatureRanges`                                | Link with code |
    | -                |                 -                  | -                                                                              | -              |
    | 4.1              |                 ?                  | Authorisation decision implies time-based restriction of use                   | New ?p needed  |
    | 4.2              |                 ?                  | Authorisation decision mentions condition-based restriction of use             | New ?p needed  |
    | 4.3              |                 ?                  | Authorisation decision only allows local, historical, touristic use            | New ?p needed  |
    | -                |                 -                  | -                                                                              | -              |
    | 5.1              |                 ?                  | Onboard recording device: no SKOS SC available                                 | New ?p needed  |

- Some parameters may better be at `era:VehicleType`: others to be examined as TSI NOI *Common Characteristics* properties of `era:VehicleAuthorization(Case)`, cannot remain at `era:Vehicle`.
- As `era:vehicleKeeper`, to be added: `era:vehicleECM.` and `era:vehicleOwner` (respecting era:Body model).
- `era:vehicleType` should be managed as a dynamic property
- `era:vehicleNumber`, when managed as a dynamic property, does not require `era:previousVehicleNumber`. Further, when modelled as an EVN, the meaning must be decoded in several other commonly grouped properties.
- `era:vehicleSeries`, could have a link with a vehicle set as considered in the authorization case preceding the registration.
- Missing properties of `era:Vehicle` are being documented separately.
  
## ERATV `era:VehicleAuthorization` (Sections 3-4)

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
