# DeepSpeed
## Geometry-Aware Reinforcement Learning for High-Performance Autonomous Racing

**Course Project — Reinforcement Learning for Robotics**  
Arizona State University — School of Electrical, Computer, and Energy Engineering  

---

## Team

- Kaiyuan Tan  
- Perfect Obumneme  
- Emmanuel Adeloju  
- Sung Park  
- Atharva Hundare  

Contacts:  
[ktan24, pobumnem, eadeloju, spark259, ahundare]@asu.edu

---

## Abstract

We present **DeepSpeed**, a geometry-driven reinforcement learning framework for autonomous racing built on the AWS DeepRacer platform.

Unlike conventional reward engineering approaches that directly incentivize speed, progress, or on-track heuristics, our method formulates driving as a **pure steering alignment problem derived from geometric first principles**.

We show that proper steering alignment with future track curvature implicitly produces speed, stability, and faster lap times.

### DeepSpeed:

- does not reward speed  
- does not reward progress  
- does not reward wheels-on-track  
- It rewards only optimal steering decisions  

This minimalistic reward design produces:

- Smoother trajectories  
- Earlier corner entry  
- Natural apex cutting  
- Improved stability across unseen tracks  

---

# Table of Contents

1. Problem Formulation  
2. Environment  
3. Geometric Foundations  
4. Track Processing  
5. Lookahead Target Selection  
6. Optimal Steering Derivation  
7. Reward Function Design  
8. RL Configuration  
9. Training  
10. Evaluation Results  
11. Key Insight  
12. Future Work  
13. Credits  

---

# 1. Problem Formulation

We cast autonomous racing as a Markov Decision Process (MDP):

```
M = (S, A, P, R, gamma)
```

Where:

- s_t = vehicle pose + track observations  
- a_t = steering, throttle  
- R(s_t, a_t) = reward  
- gamma = discount factor  

Goal:

```
pi* = argmax_pi  E[ sum_{t=0}^{T} gamma^t R(s_t, a_t) ]
```

Instead of maximizing velocity or progress directly, we optimize future heading alignment.

---

# 2. Environment

**Platform:** AWS DeepRacer simulator  

- Camera-based perception  
- Continuous control  
- Multiple racetracks  
- Time-trial evaluation  

**Performance Metric:**  
Lap Time (seconds) ↓

---

# 3. Geometric Foundations

At each timestep the vehicle solves a local geometric control problem.

## State Representation

Car pose:

```
p = (x, y) in R^2
theta in [-pi, pi]
```

Track waypoints:

```
W = {w1, w2, ..., wN}
```

### Core Primitives

Our policy depends only on:

- Euclidean distance  
- Polar direction  
- Shortest rotation angle  

---

# 4. Track Processing

## Problem

Raw waypoints are sparse, leading to straight-line approximations.

This causes:

- Jagged control  
- Inaccurate curvature estimation  

## Solution — Upsampling

We densify:

```
W -> W_tilde
```

using interpolation to approximate a continuous curve.

Benefits:

- Smoother geometry  
- Stable targets  
- Reduced oscillations  

---

# 5. Lookahead Target Selection

We introduce a lookahead radius `r`.

### Algorithm

1. Find nearest waypoint  
2. Move forward distance r  
3. Select target w_t  

This predicts future curvature rather than immediate position.

---

# 6. Optimal Steering Derivation

Let:

```
d = w_t - p
```

Target direction:

```
phi = atan2(d_y, d_x)
```

Heading error:

```
delta_theta = phi - theta
```

Optimal steering:

```
delta* = clip(delta_theta)
```

Steering becomes pure angle minimization.

---

# 7. Reward Function Design

## Core Philosophy

We reward only steering correctness.

### Final Reward

```
R = exp(-k * abs(delta_theta))
```

Where:

- Small error → large reward  
- Large misalignment → exponential penalty  

### Not Incentivized

- Speed  
- Progress  
- On-track checks  

### Why This Works

Correct alignment naturally yields:

- Earlier corner entry  
- Apex cutting  
- Straight exits  
- Higher stable speeds  

**Good steering ⇒ Good speed**

---

# 8. RL Configuration

| Category | Value |
|----------|--------|
| Algorithm | PPO |
| Framework | TensorFlow |
| Action Space | Continuous |
| Speed | 0.5–3.7 m/s |
| Steering | -27° to 28° |
| Batch Size | 64 |
| Entropy | 0.01 |
| Discount | 0.99 |
| Learning Rate | 3e-4 |
| Epochs | 10 |

---

# 9. Training

**Training Track:** Circuit de Barcelona-Catalunya  
**Training Time:** ~190 minutes  

Procedure:

1. Design reward  
2. Train PPO  
3. Evaluate on unseen tracks  
4. Iterate  

---

# 10. Evaluation Results

Generalization tested on multiple unseen tracks.

## Lap Time Gap vs World Record

| Case | Performance |
|------|-------------|
| Best | ~7% slower |
| Worst | ~32% slower |

### Observations

- Stable across tracks  
- Fewer spin-outs  
- Smooth trajectories  
- Strong cornering behavior  

---

# 11. Key Insight

Most teams trade:

speed ↔ off-track penalties  

DeepSpeed achieves both by optimizing geometry instead of heuristics.

---

# 12. Future Work

## Corner Stability

```
abs(delta_t - delta_{t-1})
```

## Speed Awareness

```
v * cos(delta_theta)
```

Additional ideas:

- Throttle regularization  
- Fast completion bonus  

---

# 13. Credits & Contributions

| Member | Contribution |
|--------|--------------|
| Kaiyuan Tan | Model creation + training |
| Perfect Obumneme | Reward iteration, equations, plots |
| Emmanuel Adeloju | Multi-track evaluation experiments |
| Sung Park | Reward tuning + hyperparameters |
| Atharva Hundare | Final reward design + evaluation |

---

## Citation

If referencing this work:

DeepSpeed: Geometry-Aware Reinforcement Learning for Autonomous Racing,  
ASU EEE RL Project, 2025.
