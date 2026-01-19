THE MOSAIC AGING MODEL (MAM) — COMPLETE ASCII THEORY

DEFINITION

The Mosaic Aging Model (MAM) states:

A Safety Instrumented System (SIS) does not age as one unit.
Each component ages independently according to its own:

stress

environment

duty cycle

maintenance history

degradation curve

Therefore, the SIS must be modeled as a mosaic of heterogeneous aging
states, not a uniform block.

A SIS does NOT have:

one age

one failure rate

one mission time

one PFDavg

one PFH

one SIL

It has many.

WHY MOSAIC AGING EXISTS

A SIS contains multiple subsystems:

[ Sensors ]
[ Logic Solver ]
[ Final Elements ]
[ Power System ]
[ Networks ]
[ Diagnostics ]
[ Human Interaction ]
[ Environment Interface ]

Each subsystem experiences different:

temperatures

vibrations

corrosion rates

duty cycles

start/stop cycles

proof test intervals

repair histories

firmware/software updates

environmental excursions

operational stresses

Conclusion:
No two components share the same degradation curve.
No two components share the same hazard rate.
No two components share the same age.

CORE PRINCIPLE

A SIS is not a monolithic object.
It is a composite of independently aging elements.

Therefore:

Loop failure rate is NOT:
lambda_loop = constant

Instead:
lambda_loop(t) = F( lambda_1(t), lambda_2(t), ... lambda_n(t) )

Loop SIL is NOT:
SIL = fixed label

Instead:
SIL(t) = dynamic function of time and degradation.

MATHEMATICAL STRUCTURE

4.1 Component Hazard Rate
Each component i has its own hazard rate:

lambda_i(t) = f( age, cycles, environment, stress,
maintenance, diagnostics, degradation )

4.2 Loop Hazard Rate
The loop hazard rate is a function of all component hazard rates:

lambda_loop(t) = F( lambda_1(t), lambda_2(t), ... lambda_n(t) )

Where F depends on architecture:

1oo1

1oo2

2oo3

2oo4

mixed architectures

4.3 Time-Dependent SIL
SIL becomes:

SIL(t) = g( PFDavg(t), PFH(t), DC(t), SFF(t), beta(t) )

All terms are time-dependent.

THE MOSAIC EFFECT

The loop’s integrity is dominated by the worst-aging component.

This is the "weakest tile" effect.

Even if:

sensors are new

logic solver is mid-life

final elements are old

The loop’s SIL(t) collapses toward the oldest, most degraded element.

IMPLICATIONS FOR SIL VERIFICATION

6.1 Static SIL is invalid
Static SIL assumes:

constant lambda

uniform aging

fixed mission time

identical stress profiles

identical degradation curves

These assumptions are false.

Therefore:
A static SIL claim becomes mathematically false once the system begins operating.

6.2 SIL must be dynamic
SIL must be recalculated as:

components age

environments change

maintenance occurs

diagnostics detect drift

proof tests reveal hidden failures

Thus:
SIL(t) != SIL_design

OPERATIONAL CONSEQUENCES

7.1 Mission Time Becomes Dynamic
Each component has its own mission time:

T_sensor
T_logic
T_valve
T_actuator

Loop mission time is:

T_loop = min( T_1, T_2, ... T_n )

7.2 Proof Test Intervals Become Dynamic
Intervals must adapt to:

drift

degradation

failure patterns

environmental stress

7.3 Maintenance Becomes Evidence-Driven
Maintenance becomes data-driven, not calendar-driven.

DAIS-10 AS THE IMPLEMENTATION ENGINE

The Mosaic Aging Model is the theory.
DAIS-10 is the execution architecture.

DAIS-10 provides:

data ingestion

degradation modeling

hazard rate modeling

architecture modeling

risk modeling

SIL(t) engine

drift detection

decision intelligence

lifecycle integration

DAIS-10 turns Mosaic Aging into a living safety system.

COMPATIBILITY WITH IEC 61508 / 61511

The Mosaic Aging Model does NOT replace the standards.

It strengthens them by:

providing accurate lambda(t)

validating mission time

updating PFDavg(t)

recalculating SIL(t)

detecting drift

identifying risk creep

ensuring SIL claims remain valid

This is modernization without rewriting the standard.

FORMAL STATEMENT OF THE THEORY

The Mosaic Aging Model states:

A Safety Instrumented System ages heterogeneously across its components,
each following its own degradation trajectory.

Therefore, the system’s integrity must be evaluated as a composite of
independently evolving hazard rates, not as a uniform entity.

This requires time-dependent modeling of failure rates, risk metrics,
and SIL, supported by continuous operational data.
