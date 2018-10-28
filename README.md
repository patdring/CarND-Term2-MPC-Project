[//]: # (Image References)
[image1]: ./pictures/MPC_loop.jpg

# CarND-Controls-MPC
Self-Driving Car Engineer Nanodegree Program

---

# [Rubic](https://review.udacity.com/#!/rubrics/896/view) points

## Compilation

### Your code should compile.

- Code compiles without errors. 
- No modifications were done on the provided setup.

## Implementation

### Description of the model used in the Model Predictive Control loop.

The implementation follows the following diagram. The blocks shown can also be found in the code.
The model used is a Kinematic model considering the  interactions between the tires and the road. 

![alt text][image1]

For understanding, at least the most important parameters should be briefly explained below.

```
State:
- `x, y` : Position
- `psi` : Heading direction
- `v` : Velocity
- `cte` : Cross-track error
- `epsi` : Orientation error


Model:
- `Lf`: Length between the centre of mass of the car and the front wheels.

Output:
- `delta` : Steering angle
- `a` : Acceleration (throttle)


```

Target is to find values for `delta` and `a` which are decreasing the output of an valuation function. The valuation consists of:

- (Square) sum of `cte` and `epsi`.
- (Square) sum of the difference between actuators to penalize a lot of actuator's actions
- (Square) sum of the difference between two consecutive actuator values to penalize quick changes

The individual factors were weighted manually and by trial and error.

### Timestep Length and Elapsed Duration (N and dt)

The definition of the prediction horizion is defined by the parameters:

```
- N: Number of points
- dt: Time intervall
```

These parameters have a strong influence on the performance and have been chosen by me to take this into account.

```
- N: 7
- dt: 0.1
```

I was able to determine the following while experimenting with the values N and dt:

- A smaller dt is better because it increases the resolution. At the same time more calculation steps are necessary which has a negative effect on the performance.

- The same applies to parameter N. The larger the parameter, the longer the calculation time.

- We want these two parameters set in a way that the MPC is fast enough to control the car in real time.

### Polynomial Fitting and MPC Preprocessing

Simulator provided waypoints have to be transformed into cars coordinate system. Then a 3rd- degree polynomial function is fitted to these new, transformed waypoints. Cross-track and orientation errors are calculated and used (by the solver block in the diagram above) to define a trajectory.


### Model Predictive Control with Latency

To implement a Model Predictive Control that handles a 100ms latency the state vector values are calculated by using the model and a included delay interval of 100ms. These ist implemented in [./src/main.cpp](./src/MPC.cpp#L104) from line 104ff. .I'm projecting the car’s current state 100ms into the future before running the solver method. 

### The vehicle must successfully drive a lap around the track.

A video with the final parameters and a successfully passed lap can be found here : [./videos/mpc_controller.ogv](./videos/mpc_controller.ogv).

## Dependencies

* cmake >= 3.5
 * All OSes: [click here for installation instructions](https://cmake.org/install/)
* make >= 4.1(mac, linux), 3.81(Windows)
  * Linux: make is installed by default on most Linux distros
  * Mac: [install Xcode command line tools to get make](https://developer.apple.com/xcode/features/)
  * Windows: [Click here for installation instructions](http://gnuwin32.sourceforge.net/packages/make.htm)
* gcc/g++ >= 5.4
  * Linux: gcc / g++ is installed by default on most Linux distros
  * Mac: same deal as make - [install Xcode command line tools]((https://developer.apple.com/xcode/features/)
  * Windows: recommend using [MinGW](http://www.mingw.org/)
* [uWebSockets](https://github.com/uWebSockets/uWebSockets)
  * Run either `install-mac.sh` or `install-ubuntu.sh`.
  * If you install from source, checkout to commit `e94b6e1`, i.e.
    ```
    git clone https://github.com/uWebSockets/uWebSockets
    cd uWebSockets
    git checkout e94b6e1
    ```
    Some function signatures have changed in v0.14.x. See [this PR](https://github.com/udacity/CarND-MPC-Project/pull/3) for more details.

* **Ipopt and CppAD:** Please refer to [this document](https://github.com/udacity/CarND-MPC-Project/blob/master/install_Ipopt_CppAD.md) for installation instructions.
* [Eigen](http://eigen.tuxfamily.org/index.php?title=Main_Page). This is already part of the repo so you shouldn't have to worry about it.
* Simulator. You can download these from the [releases tab](https://github.com/udacity/self-driving-car-sim/releases).
* Not a dependency but read the [DATA.md](./DATA.md) for a description of the data sent back from the simulator.


## Basic Build Instructions

1. Clone this repo.
2. Make a build directory: `mkdir build && cd build`
3. Compile: `cmake .. && make`
4. Run it: `./mpc`.

## Tips

1. It's recommended to test the MPC on basic examples to see if your implementation behaves as desired. One possible example
is the vehicle starting offset of a straight line (reference). If the MPC implementation is correct, after some number of timesteps
(not too many) it should find and track the reference line.
2. The `lake_track_waypoints.csv` file has the waypoints of the lake track. You could use this to fit polynomials and points and see of how well your model tracks curve. NOTE: This file might be not completely in sync with the simulator so your solution should NOT depend on it.
3. For visualization this C++ [matplotlib wrapper](https://github.com/lava/matplotlib-cpp) could be helpful.)
4.  Tips for setting up your environment are available [here](https://classroom.udacity.com/nanodegrees/nd013/parts/40f38239-66b6-46ec-ae68-03afd8a601c8/modules/0949fca6-b379-42af-a919-ee50aa304e6a/lessons/f758c44c-5e40-4e01-93b5-1a82aa4e044f/concepts/23d376c7-0195-4276-bdf0-e02f1f3c665d)
5. **VM Latency:** Some students have reported differences in behavior using VM's ostensibly a result of latency.  Please let us know if issues arise as a result of a VM environment.

## Editor Settings

We've purposefully kept editor configuration files out of this repo in order to
keep it as simple and environment agnostic as possible. However, we recommend
using the following settings:

* indent using spaces
* set tab width to 2 spaces (keeps the matrices in source code aligned)

## Code Style

Please (do your best to) stick to [Google's C++ style guide](https://google.github.io/styleguide/cppguide.html).

## Project Instructions and Rubric

Note: regardless of the changes you make, your project must be buildable using
cmake and make!

More information is only accessible by people who are already enrolled in Term 2
of CarND. If you are enrolled, see [the project page](https://classroom.udacity.com/nanodegrees/nd013/parts/40f38239-66b6-46ec-ae68-03afd8a601c8/modules/f1820894-8322-4bb3-81aa-b26b3c6dcbaf/lessons/b1ff3be0-c904-438e-aad3-2b5379f0e0c3/concepts/1a2255a0-e23c-44cf-8d41-39b8a3c8264a)
for instructions and the project rubric.

## Hints!

* You don't have to follow this directory structure, but if you do, your work
  will span all of the .cpp files here. Keep an eye out for TODOs.

## Call for IDE Profiles Pull Requests

Help your fellow students!

We decided to create Makefiles with cmake to keep this project as platform
agnostic as possible. Similarly, we omitted IDE profiles in order to we ensure
that students don't feel pressured to use one IDE or another.

However! I'd love to help people get up and running with their IDEs of choice.
If you've created a profile for an IDE that you think other students would
appreciate, we'd love to have you add the requisite profile files and
instructions to ide_profiles/. For example if you wanted to add a VS Code
profile, you'd add:

* /ide_profiles/vscode/.vscode
* /ide_profiles/vscode/README.md

The README should explain what the profile does, how to take advantage of it,
and how to install it.

Frankly, I've never been involved in a project with multiple IDE profiles
before. I believe the best way to handle this would be to keep them out of the
repo root to avoid clutter. My expectation is that most profiles will include
instructions to copy files to a new location to get picked up by the IDE, but
that's just a guess.

One last note here: regardless of the IDE used, every submitted project must
still be compilable with cmake and make./

## How to write a README
A well written README file can enhance your project and portfolio.  Develop your abilities to create professional README files by completing [this free course](https://www.udacity.com/course/writing-readmes--ud777).
