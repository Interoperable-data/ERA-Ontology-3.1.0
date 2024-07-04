# Changes to 3.1.0 in regards to ERATV and EVR

- [X] spelling error `amendment`
- [ ] alignment with the approach `org:Organization`. This correction is still needed.

## General observation

Data, which can be retrieved on vehicles, _evolves_ over time, due to changes:

- to the individual vehicle: these changes are not recorded in a registration, nor an authorisation;
- to the contents of its registration, not needing an authorisation
- to the vehicle type it belongs to, and the physical update of the specific vehicle according to that change in basic design characteristics, requiring a new authorisation in most cases.

## Data Model

### ERATV data: `era:VehicleType` (Section 1 and 4)

Current data in ERATV is to be migrated mainly to these two classes.

- Updated ERATV parameters as per Application Guide for 2011/665/EU, amended by (EU) 2019/776, (EU) 2021/701 and (EU) 2023/1696:
  - [X] Deprecated according to the amendments:
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
  - [X] corrected eratvIndex if this was not a string
  - [X] added `era:unitOfMeasure qudt:KiloGM` for parameters under eratv 4.4 and 4.5
  - [X] added `era:unitOfMeasure qudt:MilliM` for cant related parameters
  - [X] added unit of speed for 4.6.4, acceleration for 4.7.1, gradient in 4.7.2, etc...
  - [X] Split the parameter 4.7.7 as already 4.6.4 was split.
  - [X] updated `era:safeConsistLengthInformationNecessary` to the VehicleType class, and SKOS Concept SCheme updated to provide correct meaning, as it responds to data for ERATV parameter 4.13.1.10.
  - [X] updated `era:etcsMVersion` to the VehicleType class, as it responds to data for ERATV parameter 4.13.1.11.
  - [X] updated `era:usesGroup555` to the VehicleType class, as it responds to data for ERATV parameter 4.13.2.12.
  - [X] updated `era:atoSystemVersion` to the VehicleType class, as it responds to data for ERATV parameter 4.13.3.1.
- [X] added `dcterms:relation` and `dcterms:requires` for dependent parameters, like 4.7.4.X.2 which depend on 4.7.4.X.1
- [ ] the adminstrative data of a type (Section 1) needs some extra properties.

### EVR `era:Vehicle`

Only those properties existing in [Table 1 "Parameters of the EVR"](https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=CELEX%3A32018D1614#d1e32-62-1), not yet properties for a RegistrationCase, an AuthorisationCase or VehicleType, should exist for a Vehicle. 

> [!WARNING]
> The reason is the amount of triple duplication, and hence also the amount of triples to delete/update if a new Registration for many vehicles is created, or a new renewal/upgrade is executed (requiring a new AuthorisationCase/VehicleType).

Current EVR data is to be migrated into these Classes.

- `era:operationalRestriction` currently only refers to a SKOS-CS containing TSI NOI-related verification results. This parameter however has its EVR basis [here](https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=CELEX%3A32018D1614#d1e32-74-1). The legal basis clearly states these restrictions to be originating from the APOM, and to be traced in [ERA/TD/2011-09/INT](https://www.era.europa.eu/system/files/2022-11/list_harmonised_national_restriction_codes_en.pdf). This means:
  - [ ] this property needs to belong to `era:AuthorizationCase`, and where not possible (because it can change without re-authorisation): `era:Vehicle`.
  - [X] every instance of `era:Vehicle` has the `era:vehicleType`-PROPERTY linking to the type instance.
  - [ ] For the parameters:

    | Restriction Code |            ERA Vocabulary            | Ontology implementation                                                        | Action         |
    | :--------------- | :----------------------------------: | :----------------------------------------------------------------------------- | :------------- |
    | 1.1.[number]     |    `era:minimumHorizontalRadius`     | Use existing property from `era:VehicleType`                                   | Link with code |
    | 1.2              |  `era:minVehicleImpedanceVoltages`,  | `era:MinVehicleImpedance` _cannot_ be used (Trackside only).                   | New ?p needed  |
    |                  |  `era:minVehicleInputCapacitance`,   |                                                                                |                |
    |                  |    `era:minVehicleInputImpedance`    |                                                                                |                |
    | 1.3.[number]     |                  -                   | `era:vehicleTypeMaximumSpeed` _cannot_ be used. The value depends              | New ?p needed  |
    |                  |                                      | on the Area Of Use, and must therefore be created as a                         |                |
    |                  |                                      | property of `era:AuthorizationCase`.                                           |                |
    | 1.4.[number]     |   `era:maximumLocomotivesCoupled`    | Use existing property                                                          | Link with code |
    | -                |                  -                   | -                                                                              | -              |
    | 2.1              |         `era:gaugingProfile`         | Use existing SKOS CS `GaugingProfiles` (Kinematic gauge (coding WAG TSI))      | Link with code |
    | 2.2.1-8          |         `era:wheelSetGauge`          | Use existing SKOS CS `NominalTrackGauges`                                      | Link with code |
    | 2.3              |   `era:etcsEquipmentOnBoardLevel`    | Use value `era-eeobl-eratv:None` from SKOS CS `ETCSEquipmentLevels`            | Link with code |
    | 2.4              |   `era:etcsEquipmentOnBoardLevel`    | Use other values from SKOS CS `ETCSEquipmentLevels`                            | Link with code |
    | 2.4.20           |      `era:voiceRadioCompatible`      | Use existing property                                                          | Link with code |
    | 2.4.21           |      `era:dataRadioCompatible`       | Use existing property                                                          | Link with code |
    | 2.5.1xx          |     `era:protectionLegacySystem`     | Use existing property                                                          | Link with code |
    | 2.5.2xx          |       `era:legacyRadioSystem`        | Use existing property                                                          | Link with code |
    | 2.6.1xx          |                  -                   | Use value `era-tpls-eratv:SSC-BL3` from SKOS CS `TrainProtectionLegacySystems` | Link with code |
    | 2.6.2xx          |                  -                   | Use value `era-lrs-eratv:TETRA-URCA` from SKOS CS `LegacyRadioSystems`         | Link with code |
    | 2.7.1, 3, 5-7    |     `era:operationalRestriction`     | Use existing SKOS CS `era-skos-Restrictions.ttl`                               | OK             |
    |                  |  `era:quieterRoutesExemptedCountry`  | Use existing SKOS values 2.7.2 & 2.7.4 in `era-skos-Restrictions.ttl`          | Check with PAD |
    |                  | `era:compositeBrakeBlockRetrofitted` | Use existing SKOS value 2.7.1 in `era-skos-Restrictions.ttl`                   | Check with PAD |
    | -                |                  -                   | -                                                                              | -              |
    | 3.1.1-4          |        `era:temperatureRange`        | Use existing SKOS CS `era-tr:TemperatureRanges`                                | VehicleType    |
    | -                |                  -                   | -                                                                              | -              |
    | 4.1              |                  ?                   | Authorisation decision implies time-based restriction of use                   | New ?p needed  |
    | 4.2              |                  ?                   | Authorisation decision mentions condition-based restriction of use             | New ?p needed  |
    | 4.3              |                  ?                   | Authorisation decision only allows local, historical, touristic use            | New ?p needed  |
    | -                |                  -                   | -                                                                              | -              |
    | 5.1              |                  ?                   | Onboard recording device: no SKOS SC available                                 | New ?p needed  |

- [ ] Some parameters may better be at `era:VehicleType`: others to be examined as TSI NOI *Common Characteristics* properties of `era:VehicleAuthorization(Case)`, cannot remain at `era:Vehicle`.
- [X] Like `era:vehicleKeeper`, to be added: `era:vehicleECM.` and `era:vehicleOwner` (respecting era:Body model).

> [!WARNING]
> The model requires `era:role` and not properties like the ones above, this needs to be corrected.

- [ ] `era:vehicleType` should be removed, as it can be deduced from the last RegistrationCase, itself referring potentially to the AuthorisationCase which was its basis. It should _not_ be hard-linked to the Vehicle instance.
- [ ] `era:vehicleNumber`, when managed as a dynamic property, does not require `era:previousVehicleNumber`. Further, when modelled as an EVN, the meaning must be decoded in [several other commonly grouped properties](https://eur-lex.europa.eu/eli/dec_impl/2018/1614/oj#d1e32-84-1):
  - [ ] Rolling Stock Group: [SKOS CS](https://data-interop.era.europa.eu/era-vocabulary/skos/era-skos-Categories.ttl) to be **updated** for EVR Rolling Stock Groups:
    - Wagons, Hauled Passenger Vehicles, Traction Rolling Stock, Special vehicles.
    - These groups strongly resemble the Category as encoded in the ['ERATV type number'](https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=CELEX%3A02011D0665-20230908#tocId21).
  - [ ] Interoperability Capability
  - [ ] Country in which vehicle is registered
  - [ ] Technical Characteristics
  - [ ] Serial Number
  - [ ] (Check Digit): proposal to use `xsd:boolean`, false when the check digit is in fact wrong (these exist).
- [ ] `era:vehicleSeries`, could have a link with a vehicle set as considered in the authorization case preceding the registration.
- [ ] Technical properties of individual `era:Vehicle` are being documented separately. These can _only_ be changed per vehicle without re-registration/authorisation.
  
### ERATV `era:AuthorisationCase` and `era:Authorisation` (Sections 2-3)

This Case-class (subClassOf `vp:Case`) makes an abstraction of whether it exists in a VTA or VA (subClassOf `vp:Permission`), as the data is the same:

- [ ] per Case, exactly one vehicle type;
- [X] Authorised AreaOfUse, as a combination of several `era:areaOfUseMemberState` and border sections+stations under `era:limitedAreaOfUse`.
- [ ] ONE or MORE Vehicles, in the latter case preferably grouped in a `VehicleCollection`. The Vehicles instances are possibly ONLY having pre-reserved numbers, and are furtehr not detailed yet.

Actions in the ERA Vocabulary:

- [X] Creation of the new Class `era:AuthorisationCase`
- Migration of existing properties to `era:AuthorisationCase`:
  - [ ] (3.1.1) `era:authorisedCountry`, which should better become `era:memberstateOfAuthorisation`
- Added to `era:AuthorisationCase`:
  - [ ] (3.1.2.1) Status: { Valid, Suspended, Revoked, To be renewed }
  - [ ] (3.1.2.1 & 3.1.3.X.2) StatusChangeDate `xsd:date`, replaces the string encoding of the date in Status.
  - [ ] (3.1.3.1.1) issueDate `xsd:date`: Date on which the type authorisation being recorded was issued (not the date of entry in ERATV)
  - [ ] (3.1.2.2) authorisationValidity `xsd:date`
  - [ ] (3.1.3.1) isOriginalAuthorisation `xsd:boolean` replaces a separate entry for this Authorisation. Should be false for subsequent modifications
  - [ ] (3.1.3) previousAuthorisation `<IRI to previous Authorisation>`
  - [ ] (3.1.3.1.2 & 3.1.3.X.3) `<IRI to authorisation Holder>` (/ORGS)
  - [ ] (3.1.3.X.4) `xsd:string` - Authorisation modification document reference
  - [ ] (3.1.3.X.5) `<IRI to EC DEC/ EC TEC certificate>` - Reference(s) of type examination or design examination type
  - [ ] (3.1.3.X.6) `<SKOS Concept Scheme>` - Applicable national rules (if applicable) - Selection from a predefined list (multiple selection possible) based on Commission Decision 2015/2299/EU
  - [ ] (3.1.3.X.7) `xsd:string` Comments
  - [ ] (3.1.3.X.8) `xsd:string` Declaration by the proposer - Reference to the written declaration by the proposer referred to in Article 16 of Regulation (EU) No 402/2013 (CSM-REA)
- Conformity with TSI (of the vehicleType):
  - [ ] Combination of SKOS CS links to INF (gauge), ENE (Onboard power system), CCS (TP), in order to link them to a set of TSI references as in the /EURLEX dataset.
  - [ ] Provide these combinations for `era:conformityWithTSI` (Section 2.1)
  - [ ] Provide these combinations for `era:applicableSpecificCases`, together with the paragraphs 7 of the TSI (as Strings) (Section 2.3)
  - [ ] Allow for links to /ERADIS modules 1 and 2 certificates (Section 2.2).
  > [!TIP]
  > It is this link which allows to automatically trace the EC DoV (EC Declaration as in the EVR registration data) to which these certificates are linked.
