# T2-Project PCM instances

This repository contains PCM instances for the [t2-project](https://github.com/t2-project).

The intended usages are : 
- For simulation with Basic Palladio (SimuLizar)
- For simulation with Palladio's Slingshot Extension and SPD 
- In the context of the DFG project MENTOR. 
  * For this case, the modelling project contains some more obscure PCM instance or setups, that may be ignore for 'normal' usage.

## Setup for Basic Palladio

This setup uns in a plain Palladio Bench, using SimuLizar as Simulator.
It is currently not Self-Adaptive, as the project does not define any reconfiguration rules for SimuLizar.

### Models
- `default.repository`
- `default.system`
- `palladio.allocation`
- `palladio.resourceenvironment`
- `full.usagemodel`

- `monitoring/palladio.measuringpoint` and `monitoring/palladio.monitorrepository`
  * optional, as SimuLizar uses a default set, if no measuring points or monitors are provided.
  

## Setup for Palladio with Slingshot

This is for those, who have a running instance of Slingshot and are willing to use it.

### Models  
- `default.repository`
- `default.system`
- `palladio.allocation`
- `palladio.resourceenvironment`
- `full.usagemodel`

- `monitoring/palladio.measuringpoint` and `monitoring/palladio.monitorrepository`
  * mandatory, as Slingshot does not provide a default set of measuring points or monitors.
- `spdpolicies.spd` and `spdsemantics.semanticspd` as reconfiguration rule.

## Special Setup for MENTOR (Palladio with Slingshot)

### Models
see Slingshot Setup.

- `palladio.slo` for SLOs
- `default-linear.dlim` respectively `default.usageevolution` for changes in the workload over time. 
  * `default-linear.dlim` is a linear increase and decline with two peaks. The second peak is slightly higher than the first one.

## Regarding resource demand and processing rate. 

In the end, the only thing that matters is getting correct measurements, right?
Especially, since resource demand and processing rate are based on "*workunits*", but there's no clear definition on what a *workunit* actually is.

Thus, we have all processing rates at 1, and set the resource demand to recreate response times we measured at an actual t2 deployment on a local kubernetes cluster.

Example with desired response time of 0.5 sec :
```
processing rate = 1 unit/sec
resource demand = 0.5 unit
response time = demand / rate =  0.5 / 1 = 0.5 sec
```

### Restrictions
Response time of cluster and simulation do not yet match, because the simulation disregards communication delay. (i.e. linking resources)

### Sanity Check 
Results with Slingshot and plain Palladio look similar. 