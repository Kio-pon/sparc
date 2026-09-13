# SPARC Simulation Roadmap

The project should keep MuJoCo as the fast dynamics reference and add ROS 2 and Gazebo in stages. This avoids rewriting a working controller before its behavior is measured.

## Phase 1: Stabilize the MuJoCo baseline

Status: **Working baseline completed**

- Run the original bounding controller on CPU.
- Preserve the configured standing pose when the viewer resets.
- Restart controller timing and accumulated state after a reset.
- Use lightweight box, capsule, and foot collision shapes.
- Record joint positions, torques, body pose, velocity, and contact forces.

Acceptance check: the robot loads, runs, resets without collapsing, resumes torque on the next control frame, and cannot pass its body or legs through the floor.

## Phase 2: Add ROS 2 Jazzy around MuJoCo

Target platform: Ubuntu 24.04 with ROS 2 Jazzy.

- Create a ROS 2 workspace and a `sparc_description` package.
- Clean the existing URDF and use one joint naming convention.
- Publish `robot_description`, joint states, transforms, commands, and telemetry.
- Wrap the current Raibert and spine controllers as ROS 2 nodes.
- Keep MuJoCo as the physics engine during this phase.

Acceptance check: ROS 2 can command the MuJoCo robot and inspect its state through standard topics without changing controller mathematics.

## Phase 3: Port the robot to Gazebo

- Convert the cleaned description to Xacro where repetition helps.
- Reuse the same inertial values, joint limits, and primitive collisions.
- Add `ros2_control` and the Gazebo control plugin.
- Add only the sensors needed for hardware work, starting with IMU and joint feedback.
- Create flat-ground, slope, and low-friction test worlds.

Acceptance check: the same ROS 2 command interface drives both MuJoCo and Gazebo, with matching joint directions and limits.

## Phase 4: Validate simulation against hardware

- Confirm motor limits, gear ratios, masses, centers of mass, and friction.
- Compare measured hardware logs with both simulators.
- Tune contact, damping, latency, and sensor noise from real measurements.
- Add command limits, emergency stop behavior, and startup pose checks.

Acceptance check: simulation and hardware show comparable standing, stepping, and bounding responses under the same commands.

## Phase 5: Training and optimization

Start with CPU-friendly parameter sweeps for gait frequency, stride, impedance, and spine compliance. Use saved CSV results and replayable scenarios. Add reinforcement learning only after the deterministic controller and hardware model are trustworthy. Large training runs can use a remote GPU later without blocking the ROS 2 or Gazebo work.

## Immediate next step

Keep Phase 1 as the reference implementation. Begin Phase 2 by creating the ROS 2 Jazzy workspace and publishing the existing robot description before porting any controller logic.
