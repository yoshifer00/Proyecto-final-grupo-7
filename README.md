# DQN Robot Navigation – Grupo 7 (ROS 2 Jazzy)

Este repositorio implementa un entrenamiento de **navegación autónoma** usando **Deep Q-Network (DQN)** para un robot móvil simulado en **Stage**, utilizando **ROS 2 Jazzy**.

El objetivo es que el robot aprenda a navegar hacia una meta evitando obstáculos a partir de información de **LiDAR** y **odometría**.

A diferencia de otros proyectos que usan frameworks pesados (TensorFlow / PyTorch), aquí se utiliza **scikit-learn** (`MLPRegressor`) para el agente DQN, lo que simplifica la integración directa con nodos de ROS sin conflictos de dependencias.

---

## 🛠️ Instalación y dependencias

### Requisitos

* Ubuntu **24.04**
* **ROS 2 Jazzy** correctamente instalado y configurado

### Paquetes ROS necesarios

Instala los paquetes básicos para Stage y mensajes de navegación:

```bash
sudo apt install \
  ros-jazzy-stage-ros \
  ros-jazzy-navigation-msgs \
  ros-jazzy-geometry-msgs \
  ros-jazzy-sensor-msgs \
  ros-jazzy-nav-msgs \
  ros-jazzy-std-srvs
```

### Dependencias de Python

El agente DQN usa `scikit-learn` para la red neuronal:

```bash
sudo apt install python3-sklearn python3-numpy python3-matplotlib
```

---

## 🚀 Ejecución del proyecto

### 1. Construcción del workspace

Clona el repositorio dentro de un workspace de ROS 2:

```bash
mkdir -p ~/proyecto_ws/src
cd ~/proyecto_ws/src
git clone https://github.com/TU_USUARIO/dqn_robot_nav.git
```

Construye el workspace:

```bash
cd ~/proyecto_ws
colcon build
source install/setup.bash
```

---

### 2. Ejecución del entrenamiento

⚠️ Necesitarás **dos terminales** (recuerda hacer `source install/setup.bash` en ambas).

#### Terminal 1 – Simulador Stage

```bash
ros2 launch stage_ros2 stage.launch.py
```

Asegúrate de que Stage esté publicando en los tópicos:

* `/base_scan`
* `/odom`

Puedes verificarlo con:

```bash
ros2 topic echo /base_scan
ros2 topic echo /odom
```

Si estos tópicos no publican, el agente se quedará esperando datos.

---

#### Terminal 2 – Entrenamiento DQN

```bash
ros2 launch dqn_robot_nav dqn_training.launch.py
```

Esto iniciará el entrenamiento del agente DQN y el reseteo automático del robot en Stage.

---

##  Detalles del agente DQN

###  Espacio de acciones

Acciones discretas utilizadas por el agente:

| Acción | Movimiento                |
| -----: | ------------------------- |
|      0 | Avanzar                   |
|      1 | Girar izquierda           |
|      2 | Girar derecha             |
|      3 | Avanzar + girar izquierda |
|      4 | Avanzar + girar derecha   |

Las velocidades lineales y angulares pueden ajustarse en `environment.py`.

> 💡 Si el robot se queda girando en un mismo lugar, revisa la función de recompensa en `environment.py` (penalización por rotación y recompensa por progreso).

---

## 📂 Estructura real del repositorio

```text
dqn_robot_nav/
├── dqn_agent.py          # Agente DQN (MLPRegressor, replay buffer, target network)
├── environment.py        # Entorno ROS 2 (StageEnv)
├── state_processor.py    # Procesamiento del estado (LiDAR + meta)
├── train_node.py         # Nodo principal de entrenamiento
├── reset_stage.py        # Wrapper para reset de Stage y odometría
├── launch/
│   └── dqn_training.launch.py
└── README.md
```

---

## 📈 Resultados

Durante el entrenamiento se generan automáticamente carpetas de resultados:

* `results_YYYYMMDD_*/`

  * `*.pkl` → modelo entrenado
  * `training_results.png` → gráfica de recompensa por episodio

Estas gráficas permiten evaluar si el agente está aprendiendo o si la recompensa está estancada.

---


## 👥 Autores

* Daniel Callata
* Jhoselin Fernandez
* Patricio Flores
