# t2 Palladio Model

This repository (or rather this branch) contains PCM instances for the t2-project.

TODO : insert link to ts-project github.

The intended usages are : 
- For simulation with Basic Palladio (SimuLizar)
- For simulation with Palladio's Slingshot Extension and SPD 
- In the context of the DFG project MENTOR. 
  * For this case, the modelling project contains some more obscure PCM instance or setups, that may be ignore for 'normal' usage.

TODO : 
- setting Resource Demands and Processing Rates to something more realistic than 1.0 

## Setup for Basic Palladio

Launchconfig : `./launchconfigs/t2_plain-palladio.launch` 

This setup uns in a plain Palladio Bench, using SimuLizar as Simulator.
It is currently not Self-Adaptive, as the project does not define any reconfiguration rules for SimuLizar.

### Models
- `default.repository`
- `default.system`
- `palladio.allocation`
- `palladio.resourceenvironment`
- either `full.usagemodel` or `simple.usagemodel`
  * This setup works with both usage models, the intention is to use the `full` version. 
  * The `simple` version exists solely, because Slingshot does not yet support all features used in the `full` version.

- `monitoring/palladio.measuringpoint` and `monitoring/palladio.monitorrepository`
  * optional, as SimuLizar uses a default set, if no measuring points or monitors are provided.
  

## Setup for Palladio with Slingshot

Launchconfig : `./launchconfigs/t2_plain-slingshot.launch` -- TODO


### Models  
- `default.repository`
- `default.system`
- `palladio.allocation`
- `palladio.resourceenvironment`
- `simple.usagemodel`
  - Slingshot does not yet support all features used in the `full` version, thus we created a simpler usage model. 

- `monitoring/slingshot.measuringpoint` and `monitoring/slingshot.monitorrepository`
  * mandatory, as Slingshot does not provide a default set of measuring points or monitors.
  * very brittle. only works for usagesceanrio responsetime.
- `scalingpolicies/empty.spd` or `scalingpolicies/palladio-policies.spd` as reconfiguration rule.
  * TODO : do they still work?

## Special Setup for MENTOR (Palladio with Slingshot)

Launchconfig : `./launchconfigs/t2_mentor-slingshot.launch`

### Models
- `default.repository`
- `default.system`
- `slingshot.allocation`
- `slingshot.resourceenvironment/*.resourceenvironment`
  * beware : this split infrastructure is a temporary workaround and not compatible with basic Palladio, as basic Palladio enforces some constraints not (yet?) enforced by Slingshot, c.f. below. 
- `simple.usagemodel` (see previous setup)

- `monitoring/slingshot.measuringpoint` and `monitoring/slingshot.monitorrepository`
  * mandatory, as Slingshot does not provide a default set of measuring points or monitors.
- `scalingpolicies/empty.spd` or `scalingpolicies/slingshot-policies.spd` as reconfiguration rule.
  * TODO : do they still work?

- `*.slo` and `*.cost` not for simulation, but for other purposes. If you are involved in MENTOR, you'll probably know. Otherwise, just ignore them.


## SPD Files (Palladio with Slingshot only
Some SPD files are located in `./scalingpolicies`.
ls

## Split Infrastructure Setup (MENTOR context only)

Currently, Slingshot only supports SPD ScalingPolicies with `ElasticInfrastructure` as TargetGroup.
When Scaling triggers, the Simulator scales the entire Resource Environment (or something like that).
Thus, i split the ResourceEnvironment into multiple files (`slingshot.resourceenvironment/*.resourceenvironment`) one for each service, such that i can create dedicated SPD TargetGroups per service.

For this setup, `slingshot.allocation` must be used instead of `palladio.allocation`. 
Other pcm instances remain at `default.*`


## `simple.usagemodel` (Palladio with Slingshot only)
Usage model without nesting. 
Intended for use in Slingshot, as Slingshot currently cannot handle nested ScenarioBehaviours, and the `full.usagemodel` contains a loop. I.e. this is only a substitute until Slingshot can deal with loops.

## Regarding resource demand and processing rate. 

In the end, the only thing that matters is getting correct measurements, right?
Especially, since resource demand and processing rate are based on "*workunits*", but there's no clear definition on what a *workunit* actually is.

Thus, i decided to keep all processing rate at 1, and set the resource demand to recreate response times i measured at an actual t2 deployment on our local kubernetes cluster.

Example with desired response time of 0.5 sec :
```
processing rate = 1 unit/sec
resource demand = 0.5 unit
response time = demand / rate =  0.5 / 1 = 0.5 sec
```

### Restrictions
Response time of cluster and simulation do not yet match, because slingshot cannot yet simulate communication delay (i.e. linking resources)

### Sanity Check 
Results with Slingshot and plain Palladio look similar. 