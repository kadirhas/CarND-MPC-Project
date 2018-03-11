# CarND-Controls-MPC
Self-Driving Car Engineer Nanodegree Program

[image1]: ./docs/equations.png "Result with 100 particles"

---
## Requirements
### Dependencies

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


### Basic Build Instructions

1. Clone this repo.
2. Make a build directory: `mkdir build && cd build`
3. Compile: `cmake .. && make`
4. Run it: `./mpc`.

## The Model
In this project, a basic bicycle kinematic model has been used. The states related with the vehicle are, x position, y position, yaw angle and velocity of the vehicle. During the implementation it is assumed that the origin is on the vehicle to make the calculations easier. The actuators for the model are steering angle and the throttle of the vehicle, which is also used as the brakes of the vehicle. The relationship between the acceleration and the throttle is assumed to be linear. The update equations for the errors and the vehicle model can be seen below:

![alt text][image1]

## Timestep Length and Elapsed Duration (N & dt)
In this implementation N is chosen as 10 and the timestep lenght is chosen as 100 milliseconds. Timestep length is chosen as 100 milliseconds because there is a delay on the controller which is also 100 milliseconds. Using higher resolution than the delay might provide controller inputs that might not be effective enough, since the optimization would also consider the following steps. With N being 10, the elapsed duration of the model is 1 second which is good enough to predict the curves to slow down or prepare for the curve. Higher time resolution or longer elapsed duration would be more demanding for cpu. 

## Polynomial Fitting and MPC Preprocessing
The polynomial is fitted to the waypoints. Before fitting the polynomial on the waypoints, all of the points are preprocessed to convert the points according to vehicle, to make the vehicle to be on coordinates 0,0. This can be found on lines 95-102 in main.cpp.

## Model Predictive Control with Latency
There is 100 milliseconds latency between the controller and the actuators. To deal with that issue, first I've tried to give the actuators the calculated controller outputs on N = 2 instead of N = 1. That approach didn't work because between the first and second timesteps, the vehicle was not moving according to the calculated first output, hence the calculated controller input of N=2 was not suitable anymore. Because of this, using the kinematic equations, I've estimated the vehicles position first and then used this estimation as the initial position while solving the MPC. This approach worked better and even though the controller does not perform well enough when there is no delay, it is working on satisfactory levels. This part can be seen on lines 120-127 in main.cpp.

## The vehicle must successfully drive a lap around the track.
The vehicle successfully drives around the track with a safe manner. 