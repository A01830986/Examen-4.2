# Examen-4.2

## 👥 Equipo
* Bruno Rubio García
* Unai Para Manchado
* Mikel Para Manchado
* Iñigo González Olarte

PuzzleBot Real Motor Control with ESP8266 and ROS2

This project implements real-time velocity control of two DC motors with encoders using an ESP8266 microcontroller and ROS 2 (Humble).

The system reproduces the behavior of a differential robot control system in real hardware, allowing the analysis of:

desired motor velocity

measured motor velocity

control error

control effort

The data is visualized in real time and stored for later analysis.

System Overview

The architecture connects ROS 2 running on a PC with an ESP8266 motor controller through a serial interface.

The ESP8266 executes the low-level velocity control loop, while ROS 2 handles:

sending reference commands

receiving telemetry

plotting real-time graphs

storing experiment data

ROS2 (PC)
   │
   │  /wheel_ref
   ▼
ESP Bridge Node
   │
   │ Serial (USB)
   ▼
ESP8266 Controller
   │
   │ PWM
   ▼
Motors + Encoders
   │
   │ velocity telemetry
   ▼
ROS2 /wheel_telem
Hardware Used

ESP8266 (NodeMCU)

2 DC motors

2 incremental encoders

H-Bridge motor driver

External motor power supply

PC running ROS2 Humble

Encoder specifications
506 pulses per revolution
Motor maximum speed ≈ 130 RPM
Pin Configuration
Encoders
Encoder	ESP8266 Pin
Left Encoder	D3
Right Encoder	D4
Motor Driver
Function	ESP8266 Pin
ENA	D5
IN1	D1
IN2	D2
ENB	D6
IN3	D7
IN4	D0
Control Algorithm

Each motor is controlled using a PID velocity controller.

Control loop frequency:

100 Hz

The ESP8266 computes:

error = reference_velocity - measured_velocity
control = kp*error + ki*integral + kd*derivative

The resulting control signal is applied to the motors using PWM.

Telemetry Format

The ESP8266 sends telemetry to ROS2 using the following format:

TEL t v_cmd w_cmd wl_ref wr_ref wl_meas wr_meas eL eR uL uR

Where:

Variable	Description
t	timestamp
v_cmd	linear velocity command
w_cmd	angular velocity command
wl_ref	left wheel reference velocity
wr_ref	right wheel reference velocity
wl_meas	measured left wheel velocity
wr_meas	measured right wheel velocity
eL	left velocity error
eR	right velocity error
uL	control signal left motor
uR	control signal right motor
ROS2 Nodes
esp_bridge

Handles communication with the ESP8266.

Responsibilities:

reads telemetry from serial

publishes /wheel_telem

subscribes to /wheel_ref

sends motor commands

wheel_plotter

Real-time visualization node.

Plots:

reference vs measured velocity

control error

control signal

Also stores experiment data in:

~/wheel_telem_log.csv
Installation

Clone the repository:

git clone https://github.com/YOUR_USERNAME/puzzlebot_real_control.git

Navigate to the workspace:

cd ros2_ws

Build the ROS2 package:

colcon build

Source the workspace:

source install/setup.bash

Install serial dependency:

pip3 install pyserial
Running the System
1 Start ESP bridge
ros2 run puzzlebot_control esp_bridge
2 Start the real-time plotter
ros2 run puzzlebot_control wheel_plotter
3 Send motor references

Example command:

ros2 topic pub /wheel_ref std_msgs/msg/Float32MultiArray "{data: [5.0,5.0]}" -r 5

This sends a reference velocity of 5 rad/s to both motors.

Example Experiments
Step Response Test
ros2 topic pub /wheel_ref std_msgs/msg/Float32MultiArray "{data: [5.0,5.0]}" -r 5

Observe:

rise time

steady-state error

controller effort

Differential Motion
ros2 topic pub /wheel_ref std_msgs/msg/Float32MultiArray "{data: [5.0,-5.0]}" -r 5

Simulates a robot turning.

Data Logging

All telemetry is saved automatically in:

~/wheel_telem_log.csv

This file can be analyzed using:

Python

MATLAB

Excel

Jupyter Notebook

Example Graphs

The system generates real-time plots of:

motor velocity tracking

control error

control effort

These plots allow analysis of:

controller performance

stability

response time

Future Improvements

Possible extensions:

automatic trajectory generators

advanced controllers (SMC, adaptive control)

ROS2 full integration with mobile robot model

hardware-in-the-loop testing with Gazebo

Author

Project developed for robot control experimentation using ROS2 and ESP8266.
