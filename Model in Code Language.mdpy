"""
Mosaic Aging Model (MAM)
------------------------

A Safety Instrumented System (SIS) does not age as one unit.
Each component ages independently according to its own:
- stress
- environment
- duty cycle
- maintenance history
- degradation curve

Therefore, the SIS must be modeled as a mosaic of heterogeneous aging
states, not a uniform block.

Implications:
- No single "age" for the SIS
- No single failure rate (lambda)
- No single mission time
- No single PFDavg / PFH
- No single SIL

Instead, each component i has:
    lambda_i(t), PFDavg_i(t), PFH_i(t), T_i, SIL_i(t)

The loop integrity is the combined effect of all these heterogeneous
aging states (the "mosaic effect").
"""

from dataclasses import dataclass
from typing import Callable, List, Dict, Optional
import math


# -------------------------------------------------------------------
# 1. Core Data Structures
# -------------------------------------------------------------------

@dataclass
class DegradationState:
    """
    Represents the degradation-related state of a single component.

    This is where "living data" lands:
    - age_hours: operating time
    - cycles: number of mechanical/functional cycles
    - environment_factor: multiplier for harsh conditions
    - stress_factor: multiplier for load/usage
    - maintenance_quality: 0..1 (1 = perfect)
    - diagnostics_coverage: 0..1 (fraction of dangerous failures detected)
    """
    age_hours: float
    cycles: float
    environment_factor: float
    stress_factor: float
    maintenance_quality: float
    diagnostics_coverage: float


@dataclass
class Component:
    """
    A single SIS component (sensor, logic solver, final element, etc.)
    modeled as an independently aging element.
    """
    id: str
    role: str  # "sensor", "logic", "final_element", etc.
    base_lambda: float  # baseline failure rate [1/h] under reference conditions
    degradation_state: DegradationState

    def lambda_t(self, t_hours: float) -> float:
        """
        Time-dependent hazard rate lambda_i(t).

        This is a placeholder structure; in a real implementation,
        this would use:
        - age progression
        - cycles
        - environment_factor
        - stress_factor
        - maintenance_quality
        - diagnostics_coverage
        and possibly Bayesian updating from field data.
        """
        ds = self.degradation_state

        # Example multiplicative model (simplified):
        env = ds.environment_factor
        stress = ds.stress_factor
        maint = max(ds.maintenance_quality, 1e-3)  # avoid divide-by-zero

        # "Living" lambda: base * environment * stress / maintenance
        lam = self.base_lambda * env * stress / maint

        # Optionally, age-dependent ramp (e.g., wear-out):
        # lam *= (1.0 + 0.0001 * (ds.age_hours + t_hours))

        return lam

    def pfdavg_t(self, t_hours: float, proof_test_interval_hours: float) -> float:
        """
        Time-dependent PFDavg_i(t) for this component.

        Simplified low-demand approximation:
            PFDavg ≈ lambda_DU * T / 2

        Here we treat lambda_t as the dangerous undetected rate proxy.
        """
        lam = self.lambda_t(t_hours)
        T = proof_test_interval_hours
        return lam * T / 2.0

    def pfh_t(self, t_hours: float) -> float:
        """
        Time-dependent PFH_i(t) (dangerous failure per hour).
        For simplicity, we treat lambda_t as PFH proxy.
        """
        return self.lambda_t(t_hours)


@dataclass
class LoopArchitecture:
    """
    Describes the voting architecture of the loop.

    Examples:
    - "1oo1"
    - "1oo2"
    - "2oo3"
    - "2oo4"
    """
    voting: str


@dataclass
class SISLoop:
    """
    A Safety Instrumented Function (SIF) / SIS loop modeled as
    a mosaic of independently aging components.
    """
    id: str
    components: List[Component]
    architecture: LoopArchitecture
    target_sil: Optional[int] = None

    def loop_pfdavg_t(self, t_hours: float, proof_test_interval_hours: float) -> float:
        """
        Time-dependent PFDavg(t) for the loop.

        This function should implement the correct architecture-specific
        equations (1oo1, 1oo2, 2oo3, etc.). Here we show a simple
        placeholder: series approximation (worst-case).
        """
        pfds = [
            c.pfdavg_t(t_hours, proof_test_interval_hours)
            for c in self.components
        ]

        # Simplified: series system (all must work) -> sum of PFDs
        # Real implementation would use proper voting logic.
        return sum(pfds)

    def loop_pfh_t(self, t_hours: float) -> float:
        """
        Time-dependent PFH(t) for the loop.

        Again, this should be architecture-aware; here we use a simple
        series approximation.
        """
        pfhs = [c.pfh_t(t_hours) for c in self.components]
        return sum(pfhs)

    def sil_t(self, t_hours: float, proof_test_interval_hours: float) -> int:
        """
        Time-dependent SIL(t) derived from PFDavg(t) or PFH(t).

        Here we use PFDavg(t) and IEC 61508 low-demand bands as an example:

            SIL 4: 1e-5  <= PFDavg < 1e-4
            SIL 3: 1e-4  <= PFDavg < 1e-3
            SIL 2: 1e-3  <= PFDavg < 1e-2
            SIL 1: 1e-2  <= PFDavg < 1e-1

        This is a simplified mapping for demonstration.
        """
        pfd = self.loop_pfdavg_t(t_hours, proof_test_interval_hours)

        if 1e-5 <= pfd < 1e-4:
            return 4
        if 1e-4 <= pfd < 1e-3:
            return 3
        if 1e-3 <= pfd < 1e-2:
            return 2
        if 1e-2 <= pfd < 1e-1:
            return 1
        return 0  # "no SIL" or below SIL 1


# -------------------------------------------------------------------
# 2. Mosaic Aging Logic (Weakest Tile Effect)
# -------------------------------------------------------------------

def loop_mission_time(components: List[Component],
                      mission_times_hours: Dict[str, float]) -> float:
    """
    Mosaic mission time:
    The loop mission time is the minimum of component mission times.

        T_loop = min(T_1, T_2, ... T_n)

    mission_times_hours: mapping from component.id -> mission time
    """
    return min(mission_times_hours[c.id] for c in components)


def weakest_tile_component(components: List[Component],
                           t_hours: float,
                           proof_test_interval_hours: float) -> Component:
    """
    Identify the "weakest tile" at time t:
    the component with the highest PFDavg(t).
    """
    worst = None
    worst_pfd = -1.0

    for c in components:
        pfd = c.pfdavg_t(t_hours, proof_test_interval_hours)
        if pfd > worst_pfd:
            worst_pfd = pfd
            worst = c

    return worst


# -------------------------------------------------------------------
# 3. DAIS-10 Integration Hooks (Living Data)
# -------------------------------------------------------------------

@dataclass
class DAIS10Context:
    """
    Placeholder for DAIS-10 integration.

    In a real implementation, this would connect to:
    - CMMS
    - historian
    - diagnostics
    - test records
    - environment data
    and update DegradationState for each component.
    """
    data_sources: Dict[str, str]  # e.g., {"cmms": "...", "historian": "..."}

    def update_degradation_state(self, component: Component, t_hours: float) -> None:
        """
        Update the component.degradation_state using living data.

        This is where:
        - age_hours
        - cycles
        - environment_factor
        - stress_factor
        - maintenance_quality
        - diagnostics_coverage

        would be refreshed from real systems.
        """
        # Pseudocode / placeholder:
        # ds = component.degradation_state
        # ds.age_hours = query_age(component.id, t_hours)
        # ds.cycles = query_cycles(component.id)
        # ds.environment_factor = compute_env_factor(component.id)
        # ds.stress_factor = compute_stress_factor(component.id)
        # ds.maintenance_quality = compute_maintenance_quality(component.id)
        # ds.diagnostics_coverage = compute_diagnostics_coverage(component.id)
        pass


# -------------------------------------------------------------------
# 4. Formal Theory as Code-Level Comment
# -------------------------------------------------------------------

"""
FORMAL STATEMENT (CODE EDITION)
--------------------------------

The Mosaic Aging Model (MAM) asserts:

1) A SIS ages heterogeneously across its components.
2) Each component i has its own time-dependent hazard rate lambda_i(t).
3) The loop hazard rate lambda_loop(t) is a function of all lambda_i(t),
   dependent on the voting architecture.
4) PFDavg(t), PFH(t), and SIL(t) are therefore time-dependent properties
   of the loop, not static labels.
5) The loop mission time T_loop is the minimum of component mission times.
6) The loop integrity is dominated by the worst-aging component
   ("weakest tile effect").
7) Static SIL claims (time-invariant) are mathematically invalid once
   the system begins operating.
8) A living data architecture (e.g., DAIS-10) is required to:
   - continuously update degradation states,
   - recalculate lambda_i(t),
   - recompute PFDavg(t), PFH(t), and SIL(t),
   - and maintain SIL validity over time.

This module encodes the structure needed to implement MAM in software.
"""
