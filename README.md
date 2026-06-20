# Teach a Quadcopter How to Fly

The Quadcopter or Quadrotor Helicopter is becoming an increasingly popular aircraft for both personal and professional use. Its maneuverability lends itself to many applications, from last-mile delivery to cinematography, from acrobatics to search-and-rescue.

Most quadcopters have 4 motors to provide thrust, although some other models with 6 or 8 motors are also sometimes referred to as quadcopters. Multiple points of thrust with the center of gravity in the middle improves stability and enables a variety of flying behaviors.

But it also comes at a price–the high complexity of controlling such an aircraft makes it almost impossible to manually control each individual motor's thrust. So, most commercial quadcopters try to simplify the flying controls by accepting a single thrust magnitude and yaw/pitch/roll controls, making it much more intuitive and fun.

The next step in this evolution is to enable quadcopters to autonomously achieve desired control behaviors such as takeoff and landing. You could design these controls with a classic approach (say, by implementing PID controllers). Or, you can use reinforcement learning to build agents that can learn these behaviors on their own.

In this project, you design a reinforcement learning agent to autonomously control and fly a quadcopter within a simulated physics environment.

## Project Structure

The project environment is divided into distinct modules handling physics simulation, task/reward engineering, and deep reinforcement learning logic:

* `Quadcopter_Project.ipynb`: The main notebook containing the project workspace, step-by-step instructions, visualization plots, and agent evaluations.
* `physics_sim.py`: Contains the engine simulator modeling the vehicle's forces, gravity, torque, and telemetry. **Do not modify this file**.
* `task.py`: Defines your custom target flight configuration (e.g., takeoff, hover, or landing), state spaces, and the crucial step-reward logic.
* `agents/`: Directory housing the reinforcement learning agents:
* `policy_search.py`: A provided baseline agent utilizing simple deterministic policy search methods.
* `agent.py`: Your custom reinforcement learning implementation (e.g., Deep Deterministic Policy Gradient / DDPG).

## Controlling the Quadcopter

The quadcopter simulator abstracts control by forcing the agent to dictate the precise revolutions per second (RPS) for each of the four rotors:

The action vector space returned by your agent's `act()` method is a list of 4 floating-point speeds:


$$\text{Action} = [\text{Rotor Speed}_1, \text{Rotor Speed}_2, \text{Rotor Speed}_3, \text{Rotor Speed}_4]$$

The vehicle's state variables tracked over execution episodes (`data.txt`) include:

* **Pose (Position & Angles):** $x, y, z$, roll ($\phi$), pitch ($\theta$), yaw ($\psi$)
* **Linear/Angular Velocities:** velocities along all directional axes

## Quick Start & Usage

### 1. Requirements

Ensure you have a Python environment setup with necessary libraries (`numpy`, `matplotlib`, `pandas`, and your chosen Deep Learning framework like `TensorFlow`/`Keras` or `PyTorch`).

### 2. Running a Basic Simulation

The workspace includes a sample `Basic_Agent` that inputs random throttle variables to give you a feel for how telemetry logs capture state configurations:

```python
from task import Task
import numpy as np

# Define initial starting states
runtime = 5.                                     
init_pose = np.array([0., 0., 10., 0., 0., 0.])  
init_velocities = np.array([0., 0., 0.])         
init_angle_velocities = np.array([0., 0., 0.])   

task = Task(init_pose, init_velocities, init_angle_velocities, runtime)
# Instantiate and step your agent through the environment...
```

### 3. Step-by-Step Training Workflow

1. **Define your objective in `task.py`:** Update the `get_reward()` function to punish instability, drift, and crashes, while rewarding your quadcopter for reaching or maintaining target altitudes.
2. **Build the Actor-Critic Networks in `agents/agent.py`:** Because the action space (rotor speed) is continuous, an actor-critic technique like **DDPG (Deep Deterministic Policy Gradient)** is highly effective.
3. **Train & Plot Results:** Run the main training loop in the Jupyter notebook, and inspect how the vehicle's position changes over time across epochs.

## Critical Challenges

* **Reward Surface Sparsity:** Tuning the reward structure is often the hardest element. Small variations in penalty multipliers heavily influence whether your agent chooses to learn to hover or spirals immediately into a crash state.
* **Continuous Control Complexity:** Balancing continuous actions over four cross-dependent forces requires stable neural network convergence and well-tuned exploration noise policies (such as the Ornstein-Uhlenbeck process).
