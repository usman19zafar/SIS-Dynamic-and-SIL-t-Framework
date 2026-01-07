```mermaid
mindmap
  root((Safety Instrumented System - SIS))

    Static_Model
      "IEC 61508 Assumptions"
      "Fixed SIL Classification"
      "No Time-Based Degradation"
      "Event-Independent Risk"

    Dynamic_Model
      "SIL(t) Time-Banded Integrity"
      "Operational Drift"
      "Load-Based Degradation"
      "Environment Variability"
      "Maintenance Gaps"
      "Sensor Aging"
      "Real-Time Risk Evolution"

    Data_Architecture
      "Raw Layer (Historian, PLC, DCS)"
      "Bronze: Event Logs, Trip Logs"
      "Silver: Time-Series Normalization"
      "Gold: Risk Models and SIL(t) Curves"
      "Event Streams"
      "Anomaly Detection"
      "Drift Monitoring"
      "Metadata"
      "Lineage"
      "Quality Rules"

    Analytics
      "Time-Series Modeling"
      "Failure Probability Curves"
      "MTBF and MTTR Integration"
      "Predictive Risk"
      "Degradation Forecasting"
      "Trip Correlation"

    Standards_Annex
      "Correction to IEC 61508"
      "Time-Dependent SIL Requirements"
      "Continuous Validation"
      "Operational Reality Integration"
      "Digital Twin Alignment"
      "Data-Driven Safety Proof"

    Outputs
      "SIL(t) Dashboard"
      "Risk Heatmaps"
      "Degradation Timelines"
      "Maintenance Prioritization"
      "Compliance Evidence"
```
