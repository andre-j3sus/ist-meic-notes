# Augmented Reality

- **Motivation**: provide a more immersive experience to the user by overlaying digital information on the real world;
- Augmented reality extends visual reality by adding **virtual objects** to the real world, providing a **new user experience without replacing the real world**.

### Characteristics of Mobile AR Applications

- **Input**: sensors (accelerometer, gyroscope, compass, GPS, camera);
- **Processing**: real-time processing of sensor data and rendering of virtual objects;
- **Output**: display of the real world with virtual objects overlaid.

---

## Tracking and Registration

- **Identifying the location and position** of the device to **decide what to render and where**;
- Can be sensor-based or **vision-based**;
- Virtual-based tracking can be **marker-based** or **feature-based**;
  - **Marker-based**: uses a **predefined marker** to identify the position and orientation of the device; markers are precise by impractical outdoors;
  - **Feature-based**: uses **natural features** in the environment to identify the position and orientation of the device; less precise but more versatile.
- There are also **hybrid approaches** that combine sensor and vision-based tracking.

### SLAM (Simultaneous Localization and Mapping)

- A way to map the environment and track the device at the same time;
- Navigate an unknown environment while building a map of it - the more previous data, the better the tracking.
