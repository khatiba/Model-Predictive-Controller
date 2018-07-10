[//]: # (Image References)
[model]: ./model-equations.png


# Model Predictive Controller 
Udacity Self-Driving Car Engineer Nanodegree Program


![](./animation.gif)


## Project Introduction
In this project I implement a model predictive controller in C++. The controller must drive a car around a track without running off the road or up onto the curbs. The simulator also introduces latency between issuing and executing commands to simulate driver behavior.


## Model

![alt text][model]

The MPC uses the following vehicle equations of motion. The vehicle state is given by `x, y` position, `ψ` heading, velocity `v`. The controls are stearing angle `δ` and accelleration `a`.

The constraints on stearing angle is `[-25°, +25°]`, and accelleration `[-1, 1]`.

L<sub>f</sub> = 2.67 is given and is the distance between the front of the vehicle and its center of gravity.


**MPC**

The MPC optimizes a cost function that penalizes cross track error, steering angle, usage of actuators and differences between actuations to smooth the control. After some testing and tweaking of the cost function weights, I used the following:

```
ref_v    = 70
w_cte    = 40
w_epsi   = 10
w_delta  = 60000
w_ddelta = 2000
w_da     = 0.00001
```

Note that I heavily penalized `delta` I found this necessary to reduce erratic and large stearing angles which led to unstable driving.

I used these weights because I wanted to run the track at high speed and similar to race cars, eg: driving the apex of a turn rather than staying center through a curve.

For the final submission, I set the reference velocity at 70. For stricter center lane driving, I keep the high weight on delta, but also increase the `cte` and `epsi` weights.

**Prediction Horizon**

At low speeds, the prediction horizon (T) can be lower since the state of the car is more easily adjusted when approaching unknown conditions. The model must predict further into the future at high speeds to account for unknown road conditions that would be harder to control. However, the model should predict at most a few seconds into the future since road conditions are likely to change enough that any further wouldn't add value.

Changing `dt` affects how often a control is executed, larger values lead to "discretization error". Choosing smaller values makes approximation of the reference trajectory more accurate but at the cost of performance since more time steps are needed to extend the prediction horizon. For this project, it is set to match the latency of `100ms`.

Tuning the time horizon parameters is a trade-off between performance and accuracy. I experimented with many combinations of horizon length and speeds. I found values that worked well for a reference speed of 70mph since the majority of roads in the US are at or below that limit.

```
N = 18
dt = 0.1

Horizon (T) = 1.8 seconds
```

**Latency**

The waypoints coordinates are in the map reference, these need to be transformed into the vehicle's coordinate system to make further calculations easier. After transforming the waypoints, the vehicle model is used to predict it's state one timestep into the future. The timestep `dt` matches the latency in the simulation of `100ms`. By predicting and optimizing around the future state, this accounts for the latency in the controller. Another way would be using the previously executed control and the cars current state.


#### Installation
This project requires the Udacity Term 2 Simulator which can be downloaded [here](https://github.com/udacity/self-driving-car-sim/releases).

Docker is the easiest, simply cd to the directory where you clone this repo and run:

``docker run -t -d --rm -p 4567:4567 -v `pwd`:/work udacity/controls_kit:latest``


## Basic Build Instructions

1. Clone this repo.
2. Make a build directory: `mkdir build && cd build`
3. Compile: `cmake .. && make`
4. Run it: `./pid`
