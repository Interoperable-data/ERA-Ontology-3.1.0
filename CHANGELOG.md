# Changes to 3.1.0 in regards to ERATV and EVR

- [X] correct spelling error `amendment`
- [ ] align with the approach `org:Organization`. 
- [ ] merge properties `era:cantDefficiency` and `era:cantDeficiency`

## General observation

Data, which can be retrieved on vehicles, _evolves_ over time, due to changes:

- to the individual vehicle: these changes are not recorded in a registration, nor an authorisation;
- to the contents of its registration, not needing an authorisation. A new Registration instance is updated under `era:currentRegistration` (a `era:Vehicle` property).
- to the vehicle type it belongs to, and the physical update of the specific vehicle according to that change in basic design characteristics, requiring a new authorisation in most cases. A new Authorisation causes:
  - setting `era:currentAuthorisation` (a `era:VehicleType` property) to this new Authorisation instance;
  - updating `era:previousAuthorisation` of the new Authorisation instance to the one superseded.

## Data Model

The complexity of modelling 'ERATV' and 'EVR' data lies in the fact that:

- Almost all Vehicles have most of their technical characteristics documented via their VehicleType, and only a few are able to be changed per Vehicle. The list is [here](https://github.com/Certiman/automate-va/blob/dev/EVR/VEHICLES.md)
- A VehicleType originates and is updated through a Sequence of Authorisations, themselves a set of AuthorisationCase's (only OSS data can provide for the latter).
- Parameters should be linked to the Class in which they are created and instances should link through.
- Parameters sometimes depend on the combination of `TechnicalCharacteristics` (INF, ENE, CCS) a Vehicle is operating in, which we call `operating mode` in this document.

### Parameters independent of the operating mode

Some parameters REMAIN linked directly to `era:VehicleType`, as they do not depend on an operating mode [INF,ENE,CCS].

> [!WARNING]
> If the value for the property is already available during Authorization/Registration, it should be moved to that Class.

### Parameters dependent of [ENE, INF, CCS] or [ENE]

Any parameter, be it of an Authorisation or a VehicleType, which depends on the operating mode of the Vehicle (on what gauge, using what power system, and operating what TP-system), must have the domain `era:TechnicalCharacteristic`. Three subClasses allow the parameter to belong to ENE, INF or CCS.

A detailed explanation is given in [this document](Modes_of_Operation_[INF-ENE-CCS].md).

### Coded Restrictions (ERATV parameter 3.1.2.3)

As can be seen from the table below, Coded restrictions MUST be deduced from other properties and are not encoded separately. Exceptions are the conditions under 4.x and 5.x.

Parameters which express a coded restriction shall all be modelled as depending on [INF, ENE, CCS]|ENE. They must therefore be treated as above (domain: `era:TechnicalCharacteristic` (below: TC), as linked to the Authorisation in which they were ascertained). In some cases, the parameters cannot depend on this combination as it must be identical for all operating modes. An example is parameter 4.8.4, which is repeated in all coded restrictions 1.1. These parameters remain under the domain `era:VehicleType`.

The property `era:operationalRestriction` currently only refers to a SKOS-CS containing TSI NOI-related verification results. This parameter however has its EVR basis [here](https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=CELEX%3A32018D1614#d1e32-74-1). The legal basis clearly states these restrictions to be originating from the APOM, and to be traced in [ERA/TD/2011-09/INT](https://www.era.europa.eu/system/files/2022-11/list_harmonised_national_restriction_codes_en.pdf). Some of the below properties should therefore be moved to the Authorisation instance in which they are first recorded.

| Restriction Code |                 ERA Vocabulary                 | Ontology implementation                                                        | Domain           | Status |
| :--------------- | :--------------------------------------------: | :----------------------------------------------------------------------------- | :--------------- | :----: |
| 1.1.[number]     |         `era:minimumHorizontalRadius`          | (4.8.4) Use existing independent property                                      | INF (>AUTH>VT)   |   OK   |
| 1.2              |       `era:minVehicleImpedanceVoltages`,       | `era:MinVehicleImpedance` _could_ be reused.                                   | ENE (>AUTH>VT)   |  NOK   |
|                  |       `era:minVehicleInputCapacitance`,        |                                                                                |                  |        |
|                  |         `era:minVehicleInputImpedance`         |                                                                                |                  |        |
| 1.3.[number]     | `era:vehicleTypeMaximumSpeedAndCantDeficiency` | (4.6.4) The value depends on the [INF,ENE,CCS] and Area Of Use, and            | SSWCC (>AUTH>VT) |   OK   |
|                  |                                                | must therefore be created as a property of `era:Authorisation` (using TC).     |                  |        |
|                  |                                                | NOT to be confused with: `era:maximumDesignSpeed`                              |                  |        |
| 1.4.[number]     |        `era:maximumLocomotivesCoupled`         | (4.1.5) Use existing property                                                  | SSWCC (>AUTH>VT) |   OK   |
| -                |                       -                        | -                                                                              | -                |        |
| 2.1              |              `era:gaugingProfile`              | (4.2.1) Use existing SKOS CS `GaugingProfiles`                                 | INF (>AUTH>VT)   |   OK   |
|                  |                                                | (**Kinematic gauge** (coding WAG TSI))                                         |                  |        |
| 2.2.1-8          |              `era:wheelSetGauge`               | (4.1.3) Use existing SKOS CS `NominalTrackGauges`                              | INF (>AUTH>VT)   |   OK   |
| 2.3              |        `era:etcsEquipmentOnBoardLevel`         | Use value `era-eeobl-eratv:None` from SKOS CS `ETCSEquipmentLevels`            | CCS (>AUTH>VT)   |   OK   |
| 2.4              |        `era:etcsEquipmentOnBoardLevel`         | Use other values from SKOS CS `ETCSEquipmentLevels`                            | CCS (>AUTH>VT)   |   OK   |
| 2.4.20           |           `era:voiceRadioCompatible`           | Use existing property                                                          | CCS (>AUTH>VT)   |   OK   |
| 2.4.21           |           `era:dataRadioCompatible`            | Use existing property                                                          | CCS (>AUTH>VT)   |   OK   |
| 2.5.1xx          |          `era:protectionLegacySystem`          | Use existing property                                                          | CCS (>AUTH>VT)   |   OK   |
| 2.5.2xx          |            `era:legacyRadioSystem`             | Use existing property                                                          | CCS (>AUTH>VT)   |   OK   |
| 2.6.1xx          |                       -                        | Use value `era-tpls-eratv:SSC-BL3` from SKOS CS `TrainProtectionLegacySystems` | SHACL            |   -    |
| 2.6.2xx          |                       -                        | Use value `era-lrs-eratv:TETRA-URCA` from SKOS CS `LegacyRadioSystems`         | SHACL            |   -    |
| 2.7.1, 3, 5-7    |          `era:operationalRestriction`          | Use existing SKOS CS `era-skos-Restrictions.ttl`                               | SSWCC (>AUTH>VT) |   OK   |
| -                |                       -                        | -                                                                              | -                |        |
| 3.1.1-4          |             `era:temperatureRange`             | Use existing SKOS CS `era-tr:TemperatureRanges`                                | INF (>AUTH>VT)   |   OK   |
| -                |                       -                        | -                                                                              | -                |        |
| 4.1              |    New ?p needed, with range `xsd:boolean`     | Authorisation decision implies time-based restriction of use                   |                  |  NOK   |
| 4.2              |    New ?p needed, with range `xsd:boolean`     | Authorisation decision mentions condition-based restriction of use             |                  |  NOK   |
| 4.3              |    New ?p needed, with range `xsd:boolean`     | Authorisation decision only allows local, historical, touristic use            |                  |  NOK   |
| -                |                       -                        | -                                                                              | -                |        |
| 5.1              |                 New ?p needed                  | Onboard recording device: no SKOS SC available                                 |                  |  NOK   |

For the data extraction out of ERATV: The complete list of parameters is in fact already encoded in the ERATV-string currently containing the `Coded conditions for use and other restrictions` and should be extracted from these (Restriction Code is added as annotation `era:harmonisedRestrictionCode`). The data should be assigned to the domains as above, and as such linked (in)directly to the relevant VehicleType / TechnicalCharacteristics.

### ERATV data: `era:VehicleType` (Sections 1 and 4)

Other data in ERATV than that above is to be migrated mainly to instances of `era:VehicleType` or through `TechnicalCharacteristic` as belonging to an `Authoriscation`.

- Updated ERATV parameters as per Application Guide for 2011/665/EU, amended by (EU) 2019/776, (EU) 2021/701 and (EU) 2023/1696:
  - [ ] 4.5.1.1 EN line categories must be added
  - [X] Added 4.5.2.4 and 4.5.2.5
  - [X] Deprecated according to the amendments:
    - 4.1.2.2
    - 4.1.4 (operating conditions), 4.1.6 - 4.1.10
    - 4.3.2 (altitude)
    - 4.6.1-3
    - 4.7.3.1-2
    - 4.8.3 and 4.8.7-8
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
  - [X] Split the parameter 4.7.5 as already 4.7.7 was split, and added link to payloads/LoadConditions SKOS CS.
  - [ ] Created SKOS Concept Scheme for payloads/LoadConditions.
  - [X] Restored and refined 4.9.3.1-2.
  - [X] updated `era:safeConsistLengthInformationNecessary` to the VehicleType class, and SKOS Concept SCheme updated to provide correct meaning, as it responds to data for ERATV parameter 4.13.1.10.
  - [X] updated `era:etcsMVersion` to the CCSSubsystem class, as it responds to [INF,ENE,CCS]-data for ERATV parameter 4.13.1.11.
  - [X] updated `era:usesGroup555` to the CCSSubsystem class, as it responds to [INF,ENE,CCS]-data for ERATV parameter 4.13.2.12.
  - [X] updated `era:atoSystemVersion` to the CCSSubsystem class, as it responds to [INF,ENE,CCS]-data for ERATV parameter 4.13.3.1.
- [X] added `dcterms:relation` and `dcterms:requires` for dependent parameters, like 4.7.4.X.2 which depend on 4.7.4.X.1
- For the administrative data of a type (Section 1):
  - Already in use:
    - [X] `era:alternativeName` (for the name: `rdfs:label`), can be kept.
    - [ ] `era:typeVersionNumber`, MUST be changed into `dcterms:identifier`.
    - [X] `era:category` & `era:subCategory`, can be kept
  - [X] For "Date of record": use `dcterms:issued`, as `dcterms:created` must be used for the creation of the instance in the KG.
  - [ ] For "Registration Method": to be determined. A SKOS CS is needed but it may overlap with the class `era:AuthorisationCase` (see below).
  - [X] For "Registered Vehicle Type": use `era:previousVehicleType`.
  - [X] Added: `era:vehicleTypePlatform`
  - [X] Already in use: `era:manufacturer`
- For the Holder of the Authorisation, use `era:vehicleTypeAuthorisationHolder` (a property of Authorisation, NOT VehicleType).

### EVR `era:Vehicle` and `era:VehicleRegistration`

Only those properties existing in [Table 1 "Parameters of the EVR"](https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=CELEX%3A32018D1614#d1e32-62-1), not yet properties for a VehicleRegistration, an Authorisation or VehicleType, should exist for a Vehicle. Current EVR data is to be migrated into these Classes.

> [!WARNING]
> The reason is the amount of triple duplication, and hence also the amount of triples to delete/update if a new Registration for many vehicles is created, or a new renewal/upgrade is executed (requiring a new AuthorisationCase/VehicleType).

- [X] Every instance of `era:Vehicle` has the `era:vehicleType`-PROPERTY linking to the type instance, which must link using  `era:currentAuthorisation` to the last Authorisation granted on the type.
- [ ] (SHACL-rule) `era:vehicleType` should be consistent with the last VehicleRegistration, itself referring potentially to the AuthorisationCase which was its basis.
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
- [ ] `era:quieterRoutesExemptedCountry` should be deduced through `era:operationalRestriction`, which links to existing SKOS values 2.7.2 & 2.7.4 in `era-skos-Restrictions.ttl`!
- [ ] `era:compositeBrakeBlockRetrofitted` should be deduced through `era:operationalRestriction`, which links to existing SKOS value 2.7.1 in `era-skos-Restrictions.ttl`!

### ERATV `era:AuthorisationCase` and `era:Authorisation` (Sections 2-3)

#### Sources of information

The `era:Authorisation` instances can be created from OSS-datasets, when the Authorisation is issued. In that case, also the `era:AuthorisationCase` can be instantiated, as the unique type, case (C2T or other) and authorised vehicles (in a set) come from that process.

The `era:Authorisation` instances can further be (re)created from the Sections 2 and 3 from ERATV-datasets. No AuthorisationCase data can be deduced from ERATV.

#### Why `AuthorisationCase` ?

This class is used only internally and should be considered 'unstable'.

This class (subClassOf `vp:Case`) allows to make an abstraction of data existing in a VTA or VA (subClassOf `vp:Permission`), as this data is the same:

Per AuthorisationCase, may need to be provided:

- [ ] Instances created from OSS data will use `vp:case` with their `era:Authorisation`
- [ ] exactly one vehicle type;
- [X] Authorised AreaOfUse, as a combination of several `era:areaOfUseMemberState` and border sections+stations under `era:limitedAreaOfUse`.
- [ ] ONE or MORE Vehicles, in the latter case preferably grouped in a `VehicleCollection`. The Vehicles instances are possibly ONLY having pre-reserved numbers, and are further not detailed yet.
- [ ] instances of `vp:submittedIn` are EC Certificates and EC Declarations supporting the Case.
- [ ] the authorisationCase as in the SKOS CS `<SKOS:VehicleAuthorisationCases>`.

The existence of this intermediary class (OSS-based) allows competency questions on the latter authorisation evidence, based on a query regarding the EVN or the Vehicle Type.

> [!WARNING]
> For datasets from ERATV, a limited `era:AuthorisationCase` should be generated, containing only the VehicleType, AreaOfUse. The Vehicles authorised are not recorded in ERATV, and can only be linked by a search in EVR.

#### Actions in the ERA Vocabulary

- [X] Creation of the new Class `era:AuthorisationCase`:
  - Properties of `era:AuthorisationCase` (`vp:concerns`):
    - [ ] `era:vehicleType` (update domain)
    - [ ] `era:AreaOfUse` (update domain)
    - For data extraction from OSS:
    - [ ] `era:vehicleCollection`
    - [ ] `era:vehicleAuthorisationCase` (`vp:permissionType`) to the SKOS-CS of the authorisation cases as in 2018/545.
- Added to `era:Authorisation`:
  - [X] Added the Class `era:AuthorisationStatus`, with:
    - [X] `era:authorisationStatus`
    - [X] (3.1.2.1) `era:authorisationStatusCoding` for the Status: { Valid, Suspended, Revoked, To be renewed }, as extended in `era-states:States` (SKOS Concept Scheme)
    - [X] (3.1.2.1 &   3.1.3.X.2) `era:authorisationStatusDate` (`xsd:date`), replaces the string encoding of the date in Status.
    - [X] (3.1.2.2) `era:authorisationValidityDate` (`xsd:date`)
    - [X] (3.1.3.1.1) `era:authorisationStatusDate` for the issueDate (`xsd:date`):
      - Date on which the type authorisation being recorded was issued (not the date of entry in ERATV)
    - [ ] Reason of the status change and description by the Applicant.
  - [X] (3.1.1) `era:authorisedCountry`, renamed to `era:memberStateOfAuthorisation`
  - [ ] (3.1.3.1) isOriginalAuthorisation (`xsd:boolean`) replaces a separate entry for this Authorisation. Should be false for subsequent modifications.
  - [X] (3.1.3) `era:previousAuthorisation`: `<IRI to previous Authorisation>`: This previous Authorisation instance should use `dcterms:isReplacedBy`.
  - [X] (3.1.3.1.2 & 3.1.3.X.3) `era:vehicleTypeAuthorisationHolder <IRI to authorisation Holder (era:Body)>` (/ORGS)
  - [X] (3.1.3.1.3 & 3.1.3.X.4) `era:authorisationDocumentReference` (`xsd:string`) - Authorisation (modification) document reference
  - [X] (3.1.3.1.4 & 3.1.3.X.5) `era:certificate` (`<IRI to EC DEC/ EC TEC certificate>`) - Reference(s) of type examination or design examination type
  - [X] (3.1.3.1.5 & 3.1.3.X.6) `era:nationalRulesAssessed` (`<http://data.europa.eu/949/concepts/national-rule-classifications/national-rule-classifications>`) - Applicable national rules (if applicable) - Selection from a predefined list (multiple selection possible) based on [Commission Decision 2015/2299/EU](http://data.europa.eu/eli/dec/2009/965/oj).
  - [X] (3.1.3.1.6 & 3.1.3.X.7) `rdfs:comment` (`xsd:string`) Comments
  - [X] (3.1.3.1.7 & 3.1.3.X.8) `era:proposerDocumentReference` (`xsd:string`) Declaration by the proposer - Reference to the written declaration by the proposer referred to in Article 16 of Regulation (EU) No 402/2013 (CSM-REA)
- Conformity with TSI (of the vehicleType):
  - [ ] The links to /ERADIS modules 1 and 2 certificates (Section 2.2) should be reconstructed by using the `currentAuthorisation`'s `era:certicate` as above.
  - [ ] Solution for LD4RAIL-990 (see [this separate proposal](Modes_of_Operation_[INF-ENE-CCS].md) for solution)

  > [!IMPORTANT]
  > It is **this link** which allows to automatically trace the EC DoV (EC Declaration as in the EVR registration data) to which these certificates are linked.

## Properties, no longer applicable after an amendment of legislation

In the ERA ontology before 5.0.0, these were marked `owl:deprecated "true"^^xsd:boolean`, where a comment explained the amendment leading to the depreciation.

In 5.0.0, we change this to:

- [X] remove the triples with ?p `owl:deprecated`
- [X] update the ns status to "`archaic`"
- [X] the amending legislation is in the comment.
- [ ] the last legislation in which the property exists is under `dcterms:source`. When a more recent amendment can be found in the KG (/EURLEX or /ELI), the property can be considered 'archaic' and no longer to be registered for new instances.

## Organisations and roles

- Having as its range `era:Body`:
  - [X] Added `era:vehicleKeeper`
  - [X] Added `era:vehicleMaintainer`
  - [X] Added `era:vehicleOwner`

> [!WARNING]
> The model requires `era:role` and not properties like the ones above, this needs to be corrected.
