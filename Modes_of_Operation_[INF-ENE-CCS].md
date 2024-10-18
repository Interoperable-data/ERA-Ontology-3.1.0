# Properties of `era:VehicleType` depending on Operating Mode

## Problem setting

Many of the parameters in `ERATV` have values which depend on the `operating mode`, expressed as a **combination** of trackside [INF, ENE, CCS]-characteristics or **singular** [ENE]-characteristics which the vehicles belonging to that type are able to support.

The following query finds all `VehicleType` properties with range `xsd:integer` which have at least double values and concatenates them in the output for `?val`:

```sql
SERVER: https://virtuoso.ecdp.tech.ec.europa.eu/sparql
GRAPH: http://data.europa.eu/949/graph/eratv 

SELECT DISTINCT ?s ?p ?val
WHERE {
?s a era:VehicleType ;
?p ?b1, ?b2
FILTER( ?b1 != ?b2 && datatype(?b1) = xsd:integer)
BIND (concat(str(?b1), ", ",str(?b2)) as ?val)
} group by ?s
```

The query illustrates that [NO distinction was made](https://citnet.tech.ec.europa.eu/CITnet/jira/projects/LD4RAIL/issues/LD4RAIL-990?filter=allopenissues) for parameters of a type which are only valid per operating mode. In contrary, the values were just stacked up onto the same property, leading to erroneous conclusions.

In the following sections, we analyse the operating mode dependency for Technical and Non-technical Parameters, and go over all the properties to update.

### Technical parameters (Section 4)

Example of a technical parameter `era:vehicleContactForce` in Section 4:

```js
4.10.15 Mean contact force
1435mm / AC 15kV-16.7Hz / Decision 2012/463/EU Set_1: 70 Ν
1435mm / AC 15kV-16.7Hz / PZB 90                    : 70 Ν
1435mm / AC 15kV-16.7Hz / RSDD/SCMT                 : 70 Ν
1435mm / DC 3kV / Decision 2012/463/EU Set_1        : 120 Ν
1435mm / DC 3kV / PZB 90                            : 120 Ν
1435mm / DC 3kV / RSDD/SCMT                         : 120 Ν
```

In this case, the KG could limit the dependency only on ENE, as this seems a discriminatory basis.

Other parameters, like `era:contactStripMaterial`, only depend on the active ENE system:

```txt
4.10.10 Material of pantograph contact strip the vehicle may be equipped with (to be indicated for each energy supply system the vehicle is equipped for) 
15kV-16.7Hz: Plain carbon
DC 3kV: Carbon with additive material
```

Parameter 4.6.4 (Combination of maximum speed and maximum cant deficiency for which the vehicle was assessed) requires a regrouping class `era:MaximumSpeedAndCantDeficiency`.
`era:vehicleTypeMaximumSpeed` is linked to this class.

### Non-technical parameters

Some Non-technical parameters of a `era:Authorisation` also distinguish between operating modes, and must therefore have an updated domain.

Regarding the Conformity, 2.1 and 2.3 must link to paragraphs in the vp:Requirement Conformity and Specific Cases sections (these 2 SKOS CS belong in /ERALEX):

- [ ] Introduce a new Class `era:TSIConformity` or reuse `era:SubsetWithCommonCharacteristics`, used in both cases:

#### (2.1) Conformity with TSI and Sections not complied with

- [ ] requires a new ObjectProperty `era:conformityWithTSI` or reusing `era:hasSetOfParameters`:
  - [ ] /ERALEX requirements (`vp:Requirement`)
  - [ ] Conformity sections: new SKOS CS, to be created by extraction per legislation (!).
  - [ ] A flag checked `vp:isCompliant "false"^^xsd:boolean`, to clarify that the mentioned requirements are NOT complied with.

#### (2.3) Applicable specific cases (specific cases conformity with which has been assessed)

- [ ] Allows reusing `era:conformityWithTSI`:
  - [ ] /ERALEX requirements (idem)
  - [ ] SpecificCases sections: new SKOS CS  ;
  - [ ] A flag checked `vp:isCompliant "true"^^xsd:boolean`, to clarify that the mentioned requirements ARE complied with.

Note that the RINF SKOS CS <http://data.europa.eu/949/concepts/tsi-existence-and-compliances/TSIExistenceAndCompliances> cannot be used for ERATV, as the requirements are much more detailed in the VEhicle context.

Regarding the Restrictions:

- [X] (3.1.X.3) Coded conditions for use and other restrictions:
  - see Table in [CHANGELOG](CHANGELOG.md)
- [X] (3.1.X.4 `era:nonCodedRestrictions`) Non-coded conditions for use and other restrictions: a `xsd:string` to be determined per [INF, ENE, CCS]
- [ ] (3.1.3.X.6 - `era:nationalRulesAssessed`) Parameters for which conformity to applicable national rules has been assessed

## Proposed solution

### Properties remaining under 'VehicleType'

Parameters that have no dependency whatsoever, keep their domain as `era:VehicleType`. A check was executed, and it concerns:

- [X] 4.1.1 `era:drivingCabs`
- [X] 4.1.11 `era:wheelSetGaugeChangeoverFacility`

- [X] 4.5.2.1, 2, 3 and the new parameters 4.5.2.4, 5.
- [X] 4.5.5 `era:totalVehicleMass`, should remain a VehicleType property as it can not depend on subsystems active.
- [X] 4.7.1, 4.7.3(.3-4), 4.7.4.(1-3)(.1-2)

- [X] 4.8.1 `era:length` (length of Vehicle)
- [X] 4.8.2 `era:minimumWheelDiameter`

- [X] 4.9.1 `era:endCouplingType`
- [X] 4.9.2 `era:axleBearingConditionMonitoring`
- [X] 4.10.1 `era:energySupplySystem`, which for VehicleType will regroup the systems supported: several parameters must be provided further per these values.

- [X] 4.12.3.1 `era:supportedPlatformHeight`
- [X] 4.14.1 `era:trainDetectionSystemType`

### Properties dependent on `operating mode`

For parameters that have different values depending on their operating mode [ENE,INF,CCS], the domain is changed from `era:VehicleType` and becomes:

- [ ] the `era:SubsetWithCommonCharacteristics` if the parameter is pure Rolling Stock / Wagon and has no relation with ENE, CCS, INF:
  - [X] 4.1.2.1 `era:maximumDesignSpeed` (See e.g. [this VehicleType](https://eratv.era.europa.eu/Eratv/Home/View/13-115-0001-9-001))
  - [X] 4.1.5 `era:maximumLocomotivesCoupled`
  - [X] 4.5.3.4 `era:axleSpacing`, may depend only on gauge
  - [X] 4.6.4, stub Class for Combination of maximum speed and maximum cant deficiency
  - [X] 4.6.5 `era:railInclination`
  - [X] 4.7.5 stub Class `era:EBDistanceAndDeceleration`
  - [X] 4.7.7 stub Class `era:MaximumSBDistanceAndDeceleration`
- one of the 3 `era:TechnicalCharacteristic` classes, if the parameter clearly relates to that subsystem's authorisation:
  - [X] `era:CCSSubsystem`, whereby `era:etcsEquipmentOnBoardLevel` and `era:etcsLevel` could be merged.
  - [X] `era:InfraSubsystem`, with:
    - [X] 4.1.3 `era:wheelSetGauge` defines the INF Subsystem from a VehicleType perspective ()
    - [ ] 4.2.1 `era:gaugingProfile` defines the INF subsystem from a Trackside perspective (Vehicle kinematic gauge (interoperable gauge)).
    - [X] `era:temperatureRange` (to be compared with the RINF parameters `era:minimumTemperature` and `era:maximumTemperature`)
    - [X] 4.5.6 `era:massPerWheel`, can only depend on gauge (to be checked).
    - [X] 4.7.2.1.1-6 (brake performance can be dependent of the gauge!)
    - [X] 4.7.6 `era:brakeWeightPercentage`
  - [X] `era:EnergySubsystem`, with:
    - [X] 4.10.4 `era:maxCurrentStandstillPantograph`
    - [X] 4.10.5 `era:minimumContactWireHeight` and `era:maximumContactWireHeight`
    - [X] 4.10.6 `era:vehiclePantographHead`
    - [X] 4.10.7 `era:numberOfPantographsInContactWithOCL`
    - [ ] 4.10.8 `era:shortestDistanceBetweenPantographsInContactWithOCL`
    - [X] 4.10.10 `era:contactStripMaterial`
    - [X] 4.10.11 `era:hasAutomaticDroppingDevice` (see the original JIRA ticket!)
    - [X] 4.10.14 `era:hasCurrentLimitation`
    - [X] 4.10.15 `era:vehicleContactForce`

Parameters **always** defining exactly one Subsystem, have that subsystem as their domain.

> ![TIP]
> It is even to be examined whether an Authorisation as a whole is not a subClassOf this class.

The following is an example of a type instance.

```js
eravt:vt-xyz-... 
    a era:VehicleType ;
    era:hasSetOfParameters  [
      // era:parameter <era:TechnicalCharacteristic>
      // 1435mm / AC 25kV-50Hz / some ETCS reference, national ATP systems...
      // a era:SubsetWithCommonCharacteristics ;
      era:parameter [
        a era:InfraSubsystem ; 
        era:wheelSetGauge era-ntg-eratv:1435mm ; // defines the INF
        era:gaugingProfile era-gaugings-eratv:gi2 ;
        era:temperatureRange era-tr-rinf:20 ;
      ] , [
        a era:EnergySubsystem ;
        era:energySupplySystem era-ess-eratv:25kv-50hz ; // defines the ENE
        era:vehicleContactForce 70 ; // property is defined as having unit Newton.
        era:hasCurrentLimitation "true"^^xsd:boolean ; 
        era:hasAutomaticDroppingDevice "true"^^xsd:boolean 
      ] ,
      [
        a era:CCSSubsystem ;
        era:etcsEquipmentOnBoardLevel era-eeobl-eratv:Decision_2012_696_EU_Set_1 , 
                                      era-eeobl-eratv:PZB , 
                                      era-eeobl-ertv:RSDD-SCMT ; // define the CCS
      ],
    ],
    [
      // era:parameter <era:TechnicalCharacteristic>
      // a era:SubsetWithCommonCharacteristic ;
      era:parameter  [
        a era:EnergySubsystem ;
        era:energySupplySystem era-ess-eratv:dc-1-5kv-specific-case-fr ;
        era:hasCurrentLimitation "true"^^xsd:boolean ;
        era:hasAutomaticDroppingDevice "false"^^xsd:boolean 

      ] ,
    ],
    [
      // a era:SubsetWithCommonCharacteristic ;
      // era:parameter <era:TechnicalCharacteristic>
      era:parameter [
        a era:InfraSubsystem ; # 1435mm
        era:wheelSetGauge era-ntg-eratv:1435mm ;
      ] , [
        a era:EnergySubsystem ;
        era:energySupplySystem era-ess-eratv:3KVDC ;
        era:vehicleContactForce 120 ; # defined as having unit Newton.
        era:hasCurrentLimitation "false"^^xsd:boolean ; 
      ],
      [
        a era:CCSSubsystem ;
        era:etcsEquipmentOnBoardLevel era-eeobl-eratv:Decision_2012_696_EU_Set_1 , era-eeobl-eratv:PZB , era-eeobl-ertv:RSDD-SCMT
      ],
    ],    // MULTIPLE SubsetWithCommonCharacteristic are likely
 ...
```

Route compatibility query for given `?gauge`, `?enesys`, `?ccs`:

```SQL
SELECT * WHERE {
  ?t a era:VehicleType ;
     era:hasSetOfParameters/era:parameter/era:wheelsetGauge ?gauge  ;
     era:hasSetOfParameters/era:parameter/era:energySupplySystem ?enesys  ;
     era:hasSetOfParameters/era:parameter/era:etcsEquipmentOnBoardLevel ?ccs  ;
     era:hasSetOfParameters/era:parameter/era:vehicleContactForce ?force  .
}
```
