DQN Robot Navigation - Grupo 7 (ROS 2 Jazzy)
Este repositorio implementa un entrenamiento de navegación autónoma usando Deep Q-Network (DQN) para un robot móvil simulado en Stage, utilizando ROS 2 Jazzy.

El objetivo es que el robot aprenda a navegar hacia una meta evitando obstáculos a partir de información de LiDAR y odometría.

A diferencia de otros proyectos que usan frameworks pesados, aquí usamos scikit-learn para el agente, lo que facilita mucho la integración directa con los nodos de ROS sin romper dependencias de Python.

🛠 Instalación y Dependencias
Primero, asegúrate de tener Ubuntu 24.04 y ROS 2 Jazzy instalado.

1. Paquetes de ROS necesarios
Copia y pega esto en la terminal para instalar lo básico de Stage y mensajes de navegación:

Bash
sudo apt install \
  ros-jazzy-stage-ros \
  ros-jazzy-navigation-msgs \
  ros-jazzy-geometry-msgs \
  ros-jazzy-sensor-msgs \
  ros-jazzy-nav-msgs \
  ros-jazzy-std-srvs
2. Python
Ojo: Estamos usando sklearn para el MLPRegressor.
Bash
sudo apt install python3-sklearn python3-numpy python3-matplotlib
🚀 Cómo ponerlo a correr
Construcción del Workspace
Clona el repositorio dentro de tu workspace ROS 2:
Bash
mkdir -p ~/proyecto_ws/src
cd ~/proyecto_ws/src
git clone https://github.com/TU_USUARIO/dqn_robot_nav.git
Construye el workspace:
cd ~/proyecto_ws
colcon build
source install/setup.bash

Ejecución del entrenamiento
Para que funcione, necesitas dos terminales (no olvides hacer source en ambas):

Terminal 1: El simulador

Bash
ros2 launch stage_ros2 stage.launch.py
Asegúrate de que el mundo de Stage esté publicando en /base_scan y /odom, si no, el agente se quedará esperando datos eternamente, lo puedes comprobar en una nueva terminal con
Bash
ros2 topic echo /base_scan
ros2 topic echo /odom

Terminal 2: El entrenamiento

Bash
ros2 launch dqn_robot_nav dqn_training.launch.py
🧠 Detalles del Agente DQN
🎯 Espacio de acciones

Acciones discretas:

Acción	Movimiento
0	Avanzar
1	Girar izquierda
2	Girar derecha
3	Avanzar + izquierda
4	Avanzar + derecha

Las velocidades se pueden ajustar en environment.py.  
Si ves que el robot se queda "atrapado" girando sobre sí mismo, revisa los parámetros de recompensa en environment.py.

📂 Estructura del repositorio
dqn_robot_nav/
├── dqn_agent.py # Agente DQN (MLPRegressor + replay + target network)
├── environment.py # Entorno ROS2 (StageEnv)
├── state_processor.py # Procesamiento del estado (LiDAR + goal)
├── train_node.py # Nodo principal de entrenamiento
├── reset_stage.py # Wrapper para reset de Stage y odometría
├── launch/
│ └── dqn_training.launch.py
└── README.md

📈 Resultados
Los modelos se guardan automáticamente en carpetas con la fecha actual (results_YYYYMMDD_...).

.pkl: El modelo entrenado (pesa poco).

training_results.png: Gráfica de recompensa por episodio. Útil para ver si el robot está aprendiendo algo o si la recompensa está estancada.

📝 Notas y Errores Comunes
Lag en el Reset: A veces Stage tarda un par de segundos en publicar el primer escaneo tras un reset. El código tiene un pequeño sleep para evitar errores de puntero nulo.

Rendimiento: Si el entrenamiento va muy lento, cierra la ventana de visualización de Stage si no la necesitas.

Autores: Daniel Callata, Jhoselin Fernandez, Patricio Flores.
