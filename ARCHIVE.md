# Proposals not withheld

For archiving purposes only.

## New Regrouping Class

To regroup a set of one to three SKOS Concepts in one instance, it is possible:

- [ ] to create a new, stub-Class `era:VehicleTypeModesOfOperation`:
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