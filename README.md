[//]: # (Image References)
[animation]: ./animation.gif
[model]: ./model.png


# Model Predictive Controller 
Udacity Self-Driving Car Engineer Nanodegree Program


## Project Introduction
In this project I implement a model predictive controller in C++. The controller must drive a car around a track without crashing. The simulator also introduces latency between issuing and executing commands.

![alt text][animation]


The MPC uses the following vehicle model. The reference model looks ahead 10 steps at a time delta of 0.1s (1s future prediction).

![alt text][model]


After some testing and tweaking of the cost function weights, I used the following:

```
ref_v    = 70;
w_cte    = 30;
w_epsi   = 20;
w_delta  = 500000;
w_ddelta = 20;
```

Note that I heavily penalized `delta` I found this necessary to reduce erratic and large stearing angles which led to unstable driving.

I used these weights because I wanted to run the track at high speed and similar to race cars, eg: driving the apex of a turn rather than staying center through a curve.

For the final submission, I set the reference velocity at 70, but it does work at 100 as well.

For stricter center lane driving, I keep the high weight on delta, but also increase the `cte` and `epsi` weights.

**Latency**

The waypoints coordinates are in the map reference, these need to be transformed into the vehicles coordinate system to make it easier to run the MPC. After transforming the waypoints, the vehicle model is used to predict it's state one timestep into the future. By predicting the future state, this accounts for the latency in the controller. Another way would be using the last executed control and the cars current state.

#### Installation
This project requires the Udacity Term 2 Simulator which can be downloaded [here](https://github.com/udacity/self-driving-car-sim/releases).

Docker is the easiest, simply cd to the directory where you clone this repo and run:

``docker run -t -d --rm -p 4567:4567 -v `pwd`:/work udacity/controls_kit:latest``


## Basic Build Instructions

1. Clone this repo.
2. Make a build directory: `mkdir build && cd build`
3. Compile: `cmake .. && make`
4. Run it: `./pid`
