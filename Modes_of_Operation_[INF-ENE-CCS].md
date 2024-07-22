# Properties of `era:VehicleType` depending on Mode of Operation

## Problem setting

Many `ERATV` parameters have values which depend on the "mode of operation", expressed as a combination of trackside [INF, ENE, CCS] which the vehicles are able to support.

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

Others such parameters are:
`4.10.14`, `4.1.2.1`, `4.1.5`, `4.5.3.4`, `4.5.5`, `4.5.6`, `4.6.4`, `4.6.5`, `4.7.2.1.6`, `4.7.5`, `4.7.6`, `4.7.7`, `4.7.8`

Other parameters, like `era:contactStripMaterial`, only depend on the active ENE system:

```json
4.10.10 Material of pantograph contact strip the vehicle may be equipped with (to be indicated for each energy supply system the vehicle is equipped for) 
15kV-16.7Hz: Plain carbon
DC 3kV: Carbon with additive material
```

Parameter 4.6.4 (Combination of maximum speed and maximum cant deficiency for which the vehicle was assessed) requires a regrouping class `era:MaximumSpeedAndCantDeficiency`.
`era:vehicleTypeMaximumSpeed` is linked to this class.

### Non-technical parameters

Some Non-technical parameters of a `era:Authorisation` also distinguish between modes of operation:

- (2.1 `era:conformityWithTSI` ) Conformity with TSI and Sections not complied with
- (2.3 `era:applicableSpecificCases`) Applicable specific cases (specific cases conformity with which has been assessed)
- (3.1.2.3 ) Coded conditions for use and other restrictions:
  - 1.1 `era:minimumHorizontalRadius`
  - see Table in [CHANGELOG](CHANGELOG.md)
- (3.1.2.4 `era:nonCodedRestrictions`) Non-coded conditions for use and other restrictions
- (3.1.3.X.6 - `era:nationalRulesAssessed`) Parameters for which conformity to applicable national rules has been assessed

## How to tackle in the data model

### (APPLIED) Reusing the ERA 3.1.0 ontology

For parameters that have no dependency whatsoever, their domain remains `era:VehicleType`.

For parameters that have different values depending on their operational regime [ENE,INF,CCS]:

- [ ] the `era:SubsetWithCommonCharacteristics` if the parameter is pure Rolling Stock / Wagon and has no relation with ENE, CCS, INF.
- one of the 3 `era:TechnicalCharacteristic` classes, if the parameter clearly relates to that subsystem's authorisation:
  - [ ] `era:CCSSubsystem`, whereby `era:etcsEquipmentOnBoardLevel` and ` era:etcsLevel` could be merged.
  - [ ] `era:InfraSubsystem`
  - [ ] `era:ENESubsystem`

Parameters **always** defining exactly one Subsystem, have that subsystem as their domain.

> ![INFO]
> It is even to be examined whether an Authorisation as a whole is not a subClassOf this class.

The following is an example of a type instance.

```js
eravt:vt-xyz-... 
    a era:VehicleType ;
    era:hasSetOfParameters  [
      // era:parameter <era:TechnicalCharacteristic>
      // 1435mm / AC 25kV-50Hz / some ETCS reference, national ATP systems...
      a era:SubsetWithCommonCharacteristic ;
      era:parameter [
        a era:InfraSubsystem ; 
        era:wheelsetGauge era-ntg-eratv:1435mm ;
        era:gaugingProfile era-gaugings-eratv:gi2 ;
        era:temperatureRange era-tr-rinf:20 ;
      ] , [
        a era:EnergySubsystem ;
        era:energySupplySystem era-ess-eratv:25kv-50hz ;
        era:vehicleContactForce 70 ; // property is defined as having unit Newton.
        era:hasCurrentLimitation "true"^^xsd:boolean ; 
      ] ,
      [
        a era:CCSSubsystem ;
        era:etcsEquipmentOnBoardLevel era-eeobl-eratv:Decision_2012_696_EU_Set_1 , 
                                      era-eeobl-eratv:PZB , 
                                      era-eeobl-ertv:RSDD-SCMT ;
      ],
    ],
    [
      // era:parameter <era:TechnicalCharacteristic>
      a era:SubsetWithCommonCharacteristic ;
      era:parameter  [
        a era:EnergySubsystem ;
        era:energySupplySystem era-ess-eratv:ac-25kv-50hz ,  
                               era-ess-eratv:dc-1-5kv-specific-case-fr ;
        era:hasCurrentLimitation "true"^^xsd:boolean ;
      ] ,
    ],
    [
      a era:SubsetWithCommonCharacteristic ;
      // era:parameter <era:TechnicalCharacteristic>
      era:parameter [
        a era:InfraSubsystem ; # 1435mm
        era:wheelsetGauge era-ntg-eratv:1435mm ;
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

### (NOT APPLIED) Regrouping Class

To regroup a set of one to three SKOS Concepts in one instance, it is possible:

- [ ] to create a new, regrouping Class `era:VehicleTypeModesOfOperation`:
  - [ ] INF (gauge), reuse `era:wheelsetGauge` ([SKOS](https://github.com/Certiman/ERA-SKOS-3.0.0-ccs/blob/main/era-skos/era-skos-NominalTrackGauges.ttl))
  - [ ] ENE (Onboard power system), reuse `era:energySupplySystem` ([SKOS](https://github.com/Certiman/ERA-SKOS-3.0.0-ccs/blob/main/era-skos/era-skos-EnergySupplySystems.ttl))
  - CCS (TP), reuse:
    - [ ] For non-ETCS ATP: `era:protectionLegacySystem` ([Legacy ATP SKOS](https://github.com/Certiman/ERA-SKOS-3.0.0-ccs/blob/main/era-skos/era-skos-OtherProtectionControlWarnings.ttl))
    - [ ] For ETCS-ATP :`era:etcsEquipmentOnBoardLevel` ([ETCS Systems SKOS](https://github.com/Certiman/ERA-SKOS-3.0.0-ccs/blob/main/era-skos/era-skos-ETCSEquipmentLevels.ttl))

This class would be instantiated as follows, whereby the vehicle type can use the MoO-instance through the `era:parameter` property:

> [!WARNING]
> To be checked if `era:parameter` is available.

```js
era:mo-uuid4(a) a era:VehicleTypeModesOfOperation ;
               era:energySupplySystem era-ess-eratv:15kv-16-7hz ;
               era:wheelsetGauge era-ntg-eratv:1435mm ;
               era:etcsEquipmentOnBoardLevel era-eeobl-eratv:Decision_2012_696_EU_Set_1 ;

era:vt-XX-YYY-etc a era:VehicleType ;
                  era:vehicleContactForce [ 
                        era:parameter era:mo-uuid4(a) ;
                        qudt:value "70"^^xsd:integer ;
                        qudt:hasUnit qudt-unit:N .
                   ], ...

```

Applications should reuse identical instances of this class `VehicleTypeModesOfOperation` by pre-checking for the three properties. For ENE only, new Concepts could be defined representing any INF/CCS subsystem implementation:

```js
era:mo-uuid4(b) a era:VehicleTypeModesOfOperation ;
               era:energySupplySystem era-ess-eratv:15kv-16-7hz ;
               era:wheelsetGauge era-ntg-eratv:any ;
               era:etcsEquipmentOnBoardLevel era-eeobl-eratv:any ;
```

In some cases, when the value of the parameter is not different for any of the modes of operation, we could use a specific instance of the moO class:

```js
era:mo-anyMoO a era:VehicleTypeModesOfOperation ;
               era:energySupplySystem era-ess-eratv:any ;
               era:wheelsetGauge era-ntg-eratv:any ;
               era:etcsEquipmentOnBoardLevel era-eeobl-eratv:any ;

era:vt-11-075-0004-1-001-001 a era:vehicleType ; 
          era:maximumLocomotivesCoupled [ 
              era:parameter era:mo-anyMoO ;
              qudt:value 1
          ]
```