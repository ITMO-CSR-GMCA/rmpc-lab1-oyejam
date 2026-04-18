[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-22041afd0340ce965d47ae6ef1cefeee28c7c493a6346c4f15d667ab976d596c.svg)](https://classroom.github.com/a/L0glmnMn)
[![Open in Visual Studio Code](https://classroom.github.com/assets/open-in-vscode-2e0aaae1b6195c2367325f4f02e2d04e9abb55f0b24a779b69b11b9e10269abc.svg)](https://classroom.github.com/online_ide?assignment_repo_id=23580547&assignment_repo_type=AssignmentRepo)
# RMPC-lab1

First laboratory work for course Robot Motion Planning and Control.

Assignment

1. For the selected robot option, load the manipulator model from the toolbox **different then Puma560** and output the Denavit-Hartenberg parameters.
2. Specify the masses of the links, the positions of their centers of mass, the tensors of inertia, the moments of inertia of the drives, the coefficients of viscous friction of the drives, the coefficients of Coulomb friction of the drives, the gear ratios of the reducers and the constraints on the generalized coordinates. It is recommended to refer to the data sheets of the manipulators used.
3. Specify and display arbitrary initial and final configurations of the robot.
4. Plan a trajectory between the specified configurations.
5. Solve the inverse dynamics problem using the Newton-Euler method for the following scenarios: <br>
+ non-zero velocities and accelerations $\dot{q} \neq 0, \ddot{q} \neq 0$;
+ non-zero velocities and negligible accelerations $\dot{q} \neq 0, \ddot{q} \approx 0$ - quasi-statics;
+ zero velocities and accelerations $\dot{q} = 0, \ddot{q} = 0$ - maintaining a given position;
6. Determine the numerical values ​​of the elements of the matrices $M(q), C(q, \dot{q}), G(q)$ at each calculated moment of time.
7. Plot graphs of the moments of the manipulator links along the trajectory for each scenario (see point #5).
8. Create a report in .ipynb format, with detailed comments.
9. All steps and conclusions are formulated based on the results of the work in README.md
  

# SOLUTION TO LAB 1

# Lab 1 – Dynamic Model of a Multi-Link Manipulator (UR5) 

---

## 1. Introduction

This laboratory work focuses on the kinematic and dynamic analysis of a six-degree-of-freedom serial robotic manipulator. The primary objective is to develop a complete dynamic model, perform trajectory generation, and evaluate joint torques under different motion assumptions using the Newton–Euler formulation.

The study is implemented using the Robotics Toolbox for Python, with the UR5 selected as the reference manipulator due to its industrial relevance and well-defined kinematic structure.

The analysis includes Denavit–Hartenberg (DH) modeling, dynamic parameter specification, trajectory planning, inverse dynamics computation, and evaluation of inertia, Coriolis, and gravitational effects.

---

## 2. Kinematic and Dynamic Modeling

### 2.1 Denavit–Hartenberg Representation

The manipulator is modeled using the standard DH convention, which defines the spatial relationship between consecutive links using four parameters: link length (a_i), link offset (d_i), link twist (\alpha_i), and joint angle (\theta_i).

The transformation between adjacent links is expressed as:

$$
T_i =
\begin{bmatrix}
R_i & p_i \
0 & 1
\end{bmatrix}
$$

The overall forward kinematics is obtained as:

$$
T = \prod_{i=1}^{6} T_i
$$

The DH parameters were extracted directly from the toolbox model and verified for consistency.

---

### 2.2 Dynamic Parameters

Each link of the manipulator is characterized by the following physical quantities:

* Mass (m_i)
* Center of mass (r_i)
* Inertia tensor (I_i)
* Motor inertia (J_{m,i})
* Viscous friction coefficient (B_i)
* Coulomb friction coefficient (T_{c,i})
* Gear ratio (G_i)
* Joint limits (q_{min}, q_{max})

Although the default model provides baseline parameters, several entries were incomplete or idealized. Therefore, physically consistent values were assigned to ensure a well-posed dynamic formulation suitable for simulation.

---

## 3. Trajectory Generation and Motion Definition

Two joint-space configurations were defined:

$$
q_{\text{start}} = [0, -\frac{\pi}{4}, \frac{\pi}{6}, 0, \frac{\pi}{4}, 0]
$$

$$
q_{\text{end}} = \left[\frac{\pi}{3}, -\frac{\pi}{6}, -\frac{\pi}{4}, \frac{\pi}{4}, -\frac{\pi}{3}, \frac{\pi}{6}\right]
$$

A smooth trajectory was generated between these configurations using polynomial interpolation, yielding continuous profiles for position, velocity, and acceleration:

$$
q(t), \quad \dot{q}(t), \quad \ddot{q}(t)
$$

Due to execution constraints in Google Colab, manipulator visualization was implemented using forward kinematics (`fkine_all`) combined with manual 3D plotting, ensuring accurate geometric representation of joint motion.

---

## 4. Dynamic Analysis and Inverse Dynamics

The manipulator dynamics are governed by the standard rigid-body equation:

\tau = M(q)\ddot{q} + C(q, \dot{q})\dot{q} + G(q)

where:

* (M(q)): inertia matrix
* (C(q, \dot{q})): Coriolis and centrifugal matrix
* (G(q)): gravitational torque vector
* (\tau): joint torque vector

Inverse dynamics were computed using the Newton–Euler algorithm under three scenarios:

### 4.1 Full Dynamic Case

$$
\dot{q} \neq 0,\quad \ddot{q} \neq 0
$$

All dynamic effects are active, including inertia, velocity coupling, and gravity.

### 4.2 Quasi-Static Case

$$
\dot{q} \neq 0,\quad \ddot{q} \approx 0
$$

Acceleration effects are neglected, reducing torque contribution primarily to velocity-dependent and gravitational terms.

### 4.3 Static Case

$$
\dot{q} = 0,\quad \ddot{q} = 0
$$

Only gravitational loading contributes to joint torques.

---

## 5. Results, Discussion, and Conclusion

### 5.1 Results

The computed torque profiles reveal distinct dynamic behaviors across the three cases:

* The full dynamic case produces the highest torque magnitudes due to combined inertial and Coriolis effects.
* The quasi-static case exhibits reduced torque levels as acceleration contributions are removed.
* The static case yields the lowest torque, corresponding solely to gravity compensation.

The inertia matrix (M(q)), Coriolis matrix (C(q, \dot{q})), and gravity vector (G(q)) vary continuously along the trajectory, reflecting the configuration-dependent nature of robot dynamics.

---

### 5.2 Critical Discussion and Limitations

Although the model captures the fundamental structure of manipulator dynamics, several simplifying assumptions affect its physical accuracy. In particular, some dynamic parameters such as inertia tensors and friction coefficients were approximated due to incomplete default values in the simulation model. In real industrial systems, these parameters are typically obtained through experimental identification or manufacturer specifications.

Furthermore, joint friction was modeled using simplified viscous and Coulomb formulations, which do not fully represent nonlinear effects such as stiction, backlash, or temperature-dependent behavior. Additionally, the trajectory was generated using basic interpolation without optimization for energy efficiency or dynamic constraints.

The visualization was adapted for a headless computational environment, requiring manual reconstruction of the robot geometry using forward kinematics. While this preserves kinematic correctness, it lacks the interactivity of full simulation environments.

Despite these limitations, the model provides a valid and insightful framework for understanding the fundamental principles of robot dynamics.

---

### 5.3 Conclusion

This study successfully developed a complete dynamic model of a six-DOF manipulator and analyzed its behavior under different motion conditions. The results demonstrate the significant influence of inertia, velocity coupling, and gravitational effects on joint torque requirements.

The combination of kinematic modeling, trajectory generation, and Newton–Euler inverse dynamics provides a strong foundation for advanced robotic motion planning and control design. The findings highlight the importance of accurate parameter modeling in achieving realistic dynamic simulation results.

---
