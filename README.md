<div align="center">

<img src="https://capsule-render.vercel.app/api?type=waving&color=0:0f0c29,50:1a0533,100:0d0d0d&height=180&section=header&text=Autonomous%20Racing%20Agent&fontSize=40&fontColor=ffffff&fontAlignY=38&desc=Deep%20RL%20%7C%20Unity%20ML-Agents%20%7C%20MVC%20Physics&descAlignY=58&descSize=16&descColor=a78bfa" width="100%"/>

[![Unity](https://img.shields.io/badge/Unity-ML--Agents-000000?style=for-the-badge&logo=unity&logoColor=white)](https://github.com/Unity-Technologies/ml-agents)
[![Python](https://img.shields.io/badge/Python-3.10+-3776AB?style=for-the-badge&logo=python&logoColor=white)](#)
[![Algorithm](https://img.shields.io/badge/Algorithms-SAC%20%7C%20PPO%20%7C%20SAC--MC-a78bfa?style=for-the-badge)](#)
[![Status](https://img.shields.io/badge/Status-Active%20Research-22c55e?style=for-the-badge)](#)

</div>

---

## 🎬 Demo

[![Watch the demo](https://img.youtube.com/vi/UmS7d5wSJe8/maxresdefault.jpg)](https://www.youtube.com/watch?v=UmS7d5wSJe8)

---

## Overview

Training deep RL agents to drive a **Porsche Taycan Turbo S** on a racing circuit from scratch — no imitation learning, no human demonstrations. The agent learns throttle, braking, and steering purely through trial and error in a high-fidelity Unity simulation.

The physics layer uses the **MVC Car Controller**, a professional Unity asset modelling real suspension dynamics, tyre friction curves, torque delivery, and gearbox behaviour. The agent interacts with the vehicle through a reflection-based interface with no direct access to internal physics state — the same inputs a human driver would use.

Four algorithms are benchmarked in the same environment: **PPO**, **SAC**, **TQMC**, and **SAC-MC** — the last being SAC extended with *Morphological Computation as Intrinsic Reward*, the novel method from the author's [published MSc research (GSO 2025)](https://github.com/Omar11N).

---

## Environment

### Observation Space (15 + RayCasts)

| Observation | Dim | Description |
|-------------|-----|-------------|
| Lookahead waypoints ×3 | 9 | Track direction at 20m, 50m, 100m in car-local space |
| Lateral offset | 1 | Signed distance from spline center |
| Local velocity | 3 | Car-frame velocity (x, y, z) |
| Steer angle | 1 | Normalized front wheel angle |
| Speed | 1 | Normalized to max speed |
| RayPerceptionSensor3D | + | Wall / obstacle detection |

### Action Space

| Type | Values |
|------|--------|
| Discrete (Branch 0) | `0` Idle · `1` Throttle · `2` Brake |
| Continuous (Index 0) | Steering: −1.0 → +1.0 |

### Reward Structure

| Signal | Effect |
|--------|--------|
| Forward progress along spline | `+` proportional to distance covered |
| Full throttle above 20 kph | `+0.05` per step |
| Steer delta (anti-jitter) | `−` proportional to input change |
| Steer sign flip | `−0.05` per direction reversal |
| High-speed steering (>130 kph) | `−` proportional to steer magnitude |
| Existential | `−0.001` per step |
| Off-track or stuck | `−1.0` + episode end |

---

## Training Configuration (SAC-MC)

```yaml
trainer_type: sacMC
hyperparameters:
  batch_size: 1024
  buffer_size: 12000
  learning_rate: 0.0003
  lambd: 0.99
network_settings:
  normalize: true
  hidden_units: 256
  num_layers: 2
max_steps: 15_000_000
time_horizon: 256
```

---

## Setup

```bash
pip install mlagents torch
mlagents-learn config/racing_config.yaml --run-id=racing_run_01
```

To watch a trained agent, assign the exported `.onnx` model to the agent's **Behavior Parameters → Model** in the Unity editor and press Play.

---

## Related Work

- 📄 **GSO 2025** — *Morphological Computation as Intrinsic Reward for Reinforcement Learning*
- 🤖 MuJoCo Meta-World experiments applying the same intrinsic reward to robotic manipulation

---

<div align="center">
<img src="https://capsule-render.vercel.app/api?type=waving&color=0:0f0c29,50:1a0533,100:0d0d0d&height=100&section=footer" width="100%"/>
</div>
