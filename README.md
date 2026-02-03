# Proyecto-final-grupo-7
# DQN Robot Navigation with Stage (ROS 2)

Este repositorio implementa un **entrenamiento de navegación autónoma usando Deep Q-Network (DQN)** para un robot móvil simulado en **Stage**, utilizando **ROS 2 Jazzy** y **Python 3.12**.

El objetivo es que el robot aprenda a navegar hacia una meta evitando obstáculos a partir de información de LiDAR y odometría.

---

## 📦 Contenido del proyecto

```
dqn_robot_nav/
├── dqn_agent.py        # Agente DQN (MLPRegressor + replay + target network)
├── environment.py      # Entorno ROS2 (StageEnv)
├── state_processor.py  # Procesamiento del estado (LiDAR + goal)
├── train_node.py       # Nodo principal de entrenamiento
├── reset_stage.py      # Wrapper para reset de Stage y odometría
├── launch/
│   └── dqn_training.launch.py
└── README.md
```

---

## ⚙️ Requisitos

### Sistema
- **Ubuntu 24.04**
- **ROS 2 Jazzy**
- **Python 3.12**

### Dependencias ROS
```bash
sudo apt install \
  ros-jazzy-stage-ros \
  ros-jazzy-navigation-msgs \
  ros-jazzy-geometry-msgs \
  ros-jazzy-sensor-msgs \
  ros-jazzy-nav-msgs \
  ros-jazzy-std-srvs
```

### Dependencias Python
```bash
sudo apt install python3-sklearn python3-numpy python3-matplotlib
```

> ⚠️ Se usa **scikit-learn** en lugar de PyTorch/TensorFlow para compatibilidad con ROS 2 Jazzy.

---

## 🧱 Construcción del workspace

Clona el repositorio dentro de tu workspace ROS 2:

```bash
cd ~/proyecto_ws/src
git clone https://github.com/TU_USUARIO/dqn_robot_nav.git
```

Construye el workspace:

```bash
cd ~/proyecto_ws
colcon build --packages-select dqn_robot_nav
source install/setup.bash
```

---

## ▶️ Ejecución del entrenamiento

### 1️⃣ Lanzar Stage (mapa y robot)

Asegúrate de tener un mundo de Stage corriendo que publique:
- `/base_scan` (LaserScan)
- `/odom`

Ejemplo:
```bash
ros2 launch stage_ros2 stage.launch.py
```

---

### 2️⃣ Ejecutar el entrenamiento DQN

En otra terminal (con el workspace sourceado):

```bash
source ~/proyecto_ws/install/setup.bash
ros2 launch dqn_robot_nav dqn_training.launch.py
```

Esto lanzará:
- `reset_stage.py` → reset de Stage + odometría
- `train_node.py` → entrenamiento DQN

---

## 📊 Resultados del entrenamiento

Durante el entrenamiento se crea automáticamente una carpeta:

```
results_YYYYMMDD_HHMMSS/
├── model_episode_50.pkl
├── model_episode_100.pkl
├── model_final.pkl
└── training_results.png
```

Incluye:
- 📈 Curvas de recompensa
- ⏱️ Pasos por episodio
- ✅ Tasa de éxito

---

## 🧠 Estado del DQN

El estado del agente es:

```
[ LiDAR_bins (n) | distancia_a_goal | ángulo_a_goal ]
```

Por defecto:
- `n_lidar_bins = 10`
- `state_size = 12`

---

## 🎯 Espacio de acciones

Acciones discretas:

| Acción | Movimiento |
|------|------------|
| 0 | Avanzar |
| 1 | Girar izquierda |
| 2 | Girar derecha |
| 3 | Avanzar + izquierda |
| 4 | Avanzar + derecha |

Las velocidades se pueden ajustar en `environment.py`.

---

## 🛠️ Ajustes recomendados

Si el robot gira en círculos:
- Aumentar recompensa por progreso
- Penalizar giros repetidos
- Reducir velocidad angular
- Incrementar `epsilon_min`
- Usar más bins de LiDAR

Estos ajustes están documentados directamente en el código.

---

## 🧪 Notas importantes

- El entrenamiento es **online** y puede ser lento.
- Se recomienda entrenar sin visualización pesada.
- Stage puede tardar en publicar `/base_scan` tras un reset.

---

## 👩‍💻 Autor

Proyecto desarrollado como experimento académico de **aprendizaje por refuerzo aplicado a robótica móvil con ROS 2**.

---

## 📜 Licencia

MIT License

---

Si tienes dudas o quieres extender el proyecto (Double DQN, PPO, PyTorch, Gazebo, etc.), ¡adelante! 🚀

