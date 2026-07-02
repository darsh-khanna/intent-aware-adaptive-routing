# Intent-Aware Adaptive Routing for Priority-Sensitive Edge Networks

A desktop simulation environment for studying **intent-aware, priority-sensitive packet routing**, motivated by 6G edge-network use cases such as emergency communications and voice-call setup. The system classifies the *urgency* of a message from its text, then uses that classification to drive an adaptive routing protocol — benchmarked against a QoS-aware baseline router across configurable network topologies and traffic scenarios.

Built with **PyQt5** for the interface and **Matplotlib** for network and statistical visualization.

---

## What It Does

**Urgency Classifier**
A rule-based text classifier scores free-text messages (e.g. *"mayday engine failure"*, *"status update nominal"*) into `NORMAL` / `MEDIUM` / `HIGH` priority tiers using weighted keyword matching. Two versions are implemented and compared:
- **V1** — keyword-weighted scoring
- **V2** — adds synonym normalization (e.g. *"blaze"* → *"fire"*) and multi-word phrase detection (e.g. *"gas leak"*, *"building is burning"*)

Both versions are evaluated against a labeled dataset of 150+ example messages, with accuracy, F1, and confusion-matrix reporting built into the results panel.

**Adaptive Routing Engine**
A cost-based next-hop selector that weighs live link/node congestion, queue depth, delay, loss rate, and geographic progress toward the destination. High-priority (emergency) packets receive a congestion-cost multiplier that lets them route around bottlenecks that would otherwise slow them down.

**Baseline Router**
A queue-aware greedy shortest-path router (ECMP-style), used as a non-strawman control — it still avoids overloaded nodes and congested links, so comparisons against it reflect real routing gains rather than an artificially weak baseline.

**Network Topology**
Randomly generated node graphs with adjustable node count and connectivity, verified with BFS to guarantee full connectivity (no orphan nodes).

**Traffic Scenarios**
Six configurable load patterns for stress-testing routing behavior:

| Scenario | Description |
|---|---|
| Uniform | Balanced load across all nodes |
| Emergency Burst | Sudden surge of emergency traffic mid-run |
| Hotspot | All traffic converges on a single congested node |
| Ramp Load | Traffic load gradually increases over time |
| Disaster | Mass-casualty event — elevated emergency fraction and packet rate |
| Voice 6G | Mixed-priority streams modeling voice-call setup traffic |

**Statistical Comparison**
Runs both routing modes across N repeated trials and reports mean ± standard deviation for packet delivery ratio, emergency-traffic latency, P95 latency, throughput, and loss rate. Significance between modes is estimated with a Welch's t-test (normal approximation, implemented without SciPy).

**Live Simulation View**
An animated view of packets traversing the network topology in real time, for visually inspecting routing behavior under load.

**Interactive Classifier Sandbox**
Type any message and see its live priority classification, score, and matched keywords.

**Results Export**
Full experiment results (parameters, per-mode metrics, deltas between modes, classifier accuracy) export to JSON for offline analysis.

---

## Interface

The application is organized into:
1. **Network configuration** — node count, connectivity, traffic rate, emergency fraction, processing capacity, number of runs
2. **Scenario selection** — choose from the six traffic patterns above
3. **Classifier sandbox** — test the urgency classifier interactively
4. **Run controls** — execute both routing modes, open the live view, export results
5. **Results panel** — comparative metrics table, classifier accuracy chart, and confusion matrix

---

## Requirements

- Python 3.8+
- PyQt5
- NumPy
- Matplotlib

```bash
pip install -r requirements.txt
```

> PyQt5 requires system-level Qt bindings. On Linux, `sudo apt install python3-pyqt5` may be needed if the pip wheel fails. As a GUI application, this requires a display (X11/Wayland/Windows/macOS desktop session) — it will not run in a headless environment.

---

## Usage

```bash
python main.py
```

1. Adjust network and traffic parameters using the sliders.
2. Select a traffic scenario.
3. Click **Run Both Modes (Adaptive + Baseline)** to execute the comparison.
4. Click **Live Simulation View** to watch packets route through the topology in real time.
5. Click **Export Results (JSON)** to save the run's metrics to disk.

---

## Design Notes

- **Latency** is reported in relative simulation time units (derived from configurable per-tick and per-hop constants), intended for comparing the adaptive and baseline modes against each other rather than as calibrated real-world timing.
- The baseline router is deliberately QoS-aware rather than a naive/random comparator, so measured improvements reflect the adaptive algorithm's routing logic rather than a weak control.
- The classifier is a transparent, rule-based system by design — every classification is traceable to the specific keywords/phrases that produced it, which made it straightforward to evaluate and iterate on (V1 → V2).

---

## Project Structure

```
.
├── dsa.py             # Main application: classifier, routing engine, simulation, GUI
└── requirements.txt   # Python dependencies
```
