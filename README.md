# reinforcement-learning
a reinforcement learning final project
🚗 DeepSpeed
Geometry-Aware Reinforcement Learning for High-Performance Autonomous Racing

Course Project — Reinforcement Learning for Robotics
Arizona State University — School of Electrical, Computer, and Energy Engineering

👥 Team

DeepSpeed

Kaiyuan Tan

Perfect Obumneme

Emmanuel Adeloju

Sung Park

Atharva Hundare

📧 Contacts:
[ktan24, pobumnem, eadeloju, spark259, ahundare]@asu.edu

📌 Abstract

We present DeepSpeed, a geometry-driven reinforcement learning framework for autonomous racing built on the AWS DeepRacer platform.

Unlike conventional reward engineering approaches that directly incentivize speed, progress, or on-track heuristics, our method formulates driving as a pure steering alignment problem derived from geometric first principles.

We show that:

Proper steering alignment with future track curvature implicitly produces speed, stability, and faster lap times.

DeepSpeed:

❌ does not reward speed
❌ does not reward progress
❌ does not reward wheels-on-track
✅ rewards only optimal steering decisions

This minimalistic reward design produces:

Smoother trajectories

Earlier corner entry

Natural apex cutting

Improved stability across unseen tracks

📚 Table of Contents

Problem Formulation

Environment

Geometric Foundations

Track Processing

Lookahead Target Selection

Optimal Steering Derivation

Reward Function Design

RL Configuration

Training

Evaluation Results

Key Insight

Future Work

Credits

1️⃣ Problem Formulation

We cast autonomous racing as a Markov Decision Process (MDP):

𝑀
=
(
𝑆
,
𝐴
,
𝑃
,
𝑅
,
𝛾
)
M=(S,A,P,R,γ)

Where:

$s_t$ = vehicle pose + track observations

$a_t$ = steering, throttle

$R(s_t, a_t)$ = reward

$\gamma$ = discount factor

Goal:

𝜋
∗
=
arg
⁡
max
⁡
𝜋
𝐸
[
∑
𝑡
=
0
𝑇
𝛾
𝑡
𝑅
(
𝑠
𝑡
,
𝑎
𝑡
)
]
π
∗
=arg
π
max
	​

E[
t=0
∑
T
	​

γ
t
R(s
t
	​

,a
t
	​

)]

Instead of maximizing velocity or progress directly, we optimize future heading alignment.

2️⃣ Environment

Platform: AWS DeepRacer simulator

Camera-based perception

Continuous control

Multiple racetracks

Time-trial evaluation

Performance Metric:

Lap Time (seconds)
↓
Lap Time (seconds)↓
3️⃣ Geometric Foundations

At each timestep the vehicle solves a local geometric control problem.

State Representation

Car pose:

𝑝
=
(
𝑥
,
𝑦
)
∈
𝑅
2
,
𝜃
∈
[
−
𝜋
,
𝜋
]
p=(x,y)∈R
2
,θ∈[−π,π]

Track waypoints:

𝑊
=
{
𝑤
1
,
𝑤
2
,
.
.
.
,
𝑤
𝑁
}
W={w
1
	​

,w
2
	​

,...,w
N
	​

}
Core Primitives

Our policy depends only on:

Euclidean distance

Polar direction

Shortest rotation angle

4️⃣ Track Processing
Problem

Raw waypoints are sparse → straight-line approximations.

This causes:

Jagged control

Inaccurate curvature estimation

Solution — Upsampling

We densify:

𝑊
→
𝑊
~
W→
W
~

using interpolation to approximate a continuous curve.

Benefits:

Smoother geometry

Stable targets

Reduced oscillations

5️⃣ Lookahead Target Selection

We introduce a lookahead radius $r$.

Algorithm

Find nearest waypoint

Move forward distance $r$

Select target $w_t$

This predicts future curvature instead of immediate position.

6️⃣ Optimal Steering Derivation

Let:

𝑑
⃗
=
𝑤
𝑡
−
𝑝
d
=w
t
	​

−p

Target direction:

𝜙
=
atan2
(
𝑑
𝑦
,
𝑑
𝑥
)
ϕ=atan2(d
y
	​

,d
x
	​

)

Heading error:

Δ
𝜃
=
𝜙
−
𝜃
Δθ=ϕ−θ

Optimal steering:

𝛿
∗
=
clip
(
Δ
𝜃
)
δ
∗
=clip(Δθ)

Steering becomes pure angle minimization.

7️⃣ Reward Function Design
Core Philosophy

We reward only steering correctness.

Final Reward
𝑅
=
exp
⁡
(
−
𝑘
∣
Δ
𝜃
∣
)
R=exp(−k∣Δθ∣)

Where:

Small error → large reward

Large misalignment → exponential penalty

Not incentivized

Speed

Progress

On-track checks

Why This Works

Correct alignment naturally yields:

Earlier corner entry

Apex cutting

Straight exits

Higher stable speeds

Good steering ⇒ Good speed

8️⃣ RL Configuration
Category	Value
Algorithm	PPO
Framework	TensorFlow
Action Space	Continuous
Speed	0.5–3.7 m/s
Steering	−27° to 28°
Batch Size	64
Entropy	0.01
Discount	0.99
Learning Rate	3e-4
Epochs	10
9️⃣ Training

Training Track: Circuit de Barcelona-Catalunya
Training Time: ~190 minutes

Procedure:

Design reward

Train PPO

Evaluate on unseen tracks

Iterate

🔟 Evaluation Results

Generalization tested on multiple unseen tracks.

Lap Time Gap vs World Record
Case	Performance
Best	~7% slower
Worst	~32% slower
Observations

Stable across tracks

Fewer spin-outs

Smooth trajectories

Strong cornering behavior

1️⃣1️⃣ Key Insight

Most teams trade:

speed ↔ off-track penalties

DeepSpeed achieves both by optimizing geometry instead of heuristics.

1️⃣2️⃣ Future Work
Corner Stability
∣
𝛿
𝑡
−
𝛿
𝑡
−
1
∣
∣δ
t
	​

−δ
t−1
	​

∣
Speed Awareness
𝑣
⋅
cos
⁡
(
Δ
𝜃
)
v⋅cos(Δθ)
Additional Ideas

Throttle regularization

Fast completion bonus

1️⃣3️⃣ Credits & Contributions
Member	Contribution
Kaiyuan Tan	Model creation + training
Perfect Obumneme	Reward iteration, equations, plots
Emmanuel Adeloju	Multi-track evaluation experiments
Sung Park	Reward tuning + hyperparameters
Atharva Hundare	Final reward design + evaluation
📖 Citation

If referencing this work:

DeepSpeed: Geometry-Aware Reinforcement Learning for Autonomous Racing,
ASU EEE RL Project, 2025.

