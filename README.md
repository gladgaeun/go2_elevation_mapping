# go2_elevation_mapping
ROS 2 elevation mapping and GPU-accelerated terrain analysis using CuPy for Unitree Go2.



## Features

* **GPU-Accelerated Elevation Mapping:** Real-time 2.5D elevation map generation using CuPy.
* **Traversability Analysis:** Integrated terrain traversability filtering for quadruped robot locomotion.
* **Unitree Go2 Configurations:** Pre-configured topics, frames (`odom`, `base_link`), and sensor fusion parameters tailored for the Unitree Go2 robot.



## Acknowledgements

This repository builds upon and modifies the original elevation mapping implementation by Takahiro Miki and Robotic Systems Lab (ETH Zurich):
* **Original Repository:** [ANYbotics/elevation_mapping_cupy](https://github.com/ANYbotics/elevation_mapping_cupy)
* **License:** MIT License (Copyright (c) 2023, Takahiro Miki, Robotic Systems Lab, ETH Zurich)

I modified and configured the pipeline for Unitree Go2 quadruped robot terrain mapping in ROS 2.
