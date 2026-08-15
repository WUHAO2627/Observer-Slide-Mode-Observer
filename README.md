# Sliding Mode Observer (SMO) for PMSM Sensorless Control

## Table of Contents
1. [Introduction](#1-introduction)
2. [PMSM Sensorless FOC Framework](#2-pmsm-sensorless-foc-framework)
3. [Principle of Sliding Mode Observation](#3-principle-of-sliding-mode-observation)
4. [Observer Structure](#4-observer-structure)
5. [Sliding Surface Concept](#5-sliding-surface-concept)
6. [Stability Analysis](#6-stability-analysis)
7. [Sliding Gain Selection](#7-sliding-gain-selection)
8. [Back-EMF Reconstruction and Rotor Position Estimation](#8-back-emf-reconstruction-and-rotor-position-estimation)
9. [Simulation and Test](#9-simulation-and-test)
10. [Advantages of SMO](#10-advantages-of-smo)
11. [Limitations](#11-limitations)
12. [Conclusion](#12-conclusion)

---

## 1. Introduction
Sliding Mode Observer (SMO) is a widely used sensorless control technique for Permanent Magnet Synchronous Motors (PMSMs). Instead of using mechanical position sensors such as encoders or resolvers, SMO estimates the rotor position and speed from the measured stator voltages and currents. The method is highly valued because of its robustness against parameter variations and external disturbances while maintaining relatively low computational complexity.

## 2. PMSM Sensorless FOC Framework
In a sensorless Field Oriented Control (FOC) system, the rotor electrical angle is not directly measured. Instead, a position and speed estimator reconstructs this information using motor electrical signals.

The current feedback measured in the three-phase stator is transformed into the stationary α-β reference frame through the Clarke transformation and then into the rotating d-q frame through the Park transformation. The estimated rotor angle is required for these coordinate transformations.

Refer to the doc in repo 
*Figure 1: Overall sensorless PMSM FOC system showing the position and speed estimator*

## 3. Principle of Sliding Mode Observation
The fundamental idea of SMO is to reconstruct the PMSM back-EMF components $E_\alpha$ and $E_\beta$ in the stationary reference frame. These back-EMF signals contain both rotor position and speed information.

The PMSM model in the α-β frame can be expressed as:

$$\frac{di}{dt} = f(u,i,E)$$

where:
- $u$ = stator voltage
- $i$ = stator current
- $E$ = back-EMF

The observer creates estimated currents $\hat{i}_\alpha$ and $\hat{i}_\beta$ and continuously compares them with the measured currents. With the PMSM model, the current state equation is:

For details, refer to the doc in repo.

## 4. Observer Structure
The SMO introduces a discontinuous correction term:

$$v = h \cdot sign(\hat{i} - i)$$

where:
- $h$ = sliding gain
- $sign(\cdot)$ = sign function
- $\hat{i} - i$ = current estimation error

The correction term forces the estimated current trajectory toward the actual current trajectory. When the estimation error approaches zero, the observer state reaches the sliding surface.

In the α-β stationary frame, the correction term is written in matrix form:

for details, refer to doc in repo

## 5. Sliding Surface Concept
The sliding surface is defined by the current estimation error:

$$S = \hat{i} - i = 0$$

When the observer operates correctly, the state converges toward this surface and remains close to it.

Unlike traditional sliding mode theory diagrams that use error versus error derivative, the analysis emphasizes that in PMSM SMO analysis the sliding surface should be interpreted in the plane formed by:
- Actual current
- Estimated current

This representation better reflects the physical meaning of observer convergence.

Refer to the doc in repo 

## 6. Stability Analysis
The stability of the observer is demonstrated using Lyapunov theory.

A Lyapunov function is selected as:

$$V = \frac{1}{2} s^2$$

To guarantee convergence:

$$\dot{V} < 0$$

The derivation gives the necessary condition for selecting the sliding gain $h$. The gain must be sufficiently large to overcome uncertainties and guarantee that the estimation error converges to zero.

## 7. Sliding Gain Selection
The sliding gain $h$ is one of the most critical design parameters.

### Small Gain
If $h$ is too small:
- Current estimation error may not converge
- Observer stability becomes poor
- Rotor position estimation becomes inaccurate

### Large Gain
If $h$ is too large:
- Chattering increases
- Estimated currents oscillate around the actual currents
- Back-EMF estimation noise increases

Therefore, the gain should be chosen large enough to guarantee convergence while minimizing oscillation. The gain can be derived analytically instead of relying on trial-and-error tuning.

## 8. Back-EMF Reconstruction and Rotor Position Estimation
Once the observer reaches the sliding surface, the switching signals contain information about the motor back-EMF.

After filtering the switching outputs, the observer obtains the back-EMF components:

$$E_\alpha,\, E_\beta$$

Rotor electrical angle can then be estimated as:

$$\theta_e = \arctan\left( \frac{-E_\alpha}{E_\beta} \right)$$

and electrical speed can be obtained from the variation of rotor angle over time.

## 9. Simulation and Test
Simulation and experimental tests are carried out to verify the performance of the SMO-based sensorless control system, including steady-state and dynamic response validation.

Refer to the doc in repo or you can run the project with Simulink

## 10. Advantages of SMO
SMO offers several important advantages:
- Strong robustness against parameter variations
- Good tolerance to motor resistance and inductance mismatch
- Low computational burden compared with Extended Kalman Filter (EKF)
- Suitable for embedded motor-control applications
- Effective for practical PMSM sensorless FOC systems

## 11. Limitations
Despite its advantages, SMO also has limitations:
- Chattering caused by the switching function
- Requirement for low-pass filtering of estimated back-EMF
- Reduced performance at very low speed because back-EMF magnitude becomes small
- Careful gain tuning is required to balance convergence and noise

## 12. Conclusion
The Sliding Mode Observer is a robust and computationally efficient sensorless estimation technique for PMSM drives. By constructing an observer in the stationary α-β frame and introducing a switching correction term based on current estimation error, the observer forces estimated currents to converge to measured currents. Once the sliding condition is achieved, the motor back-EMF can be reconstructed and used to estimate rotor position and speed. Proper selection of the sliding gain is crucial, as it determines both convergence performance and chattering behavior. For industrial PMSM sensorless FOC applications, SMO remains one of the most practical and widely adopted observer solutions.

