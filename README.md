# Sistema Telerrobótico de Control Supervisado: Unity + ROS2

Este repositorio contiene el desarrollo de un sistema de telerrobótica basado en una arquitectura **Maestro-Esclavo** bajo el modelo de **Control Supervisado de Sheridan**. El proyecto permite la teleoperación de un robot móvil (Rover) y su brazo articulado a través de una interfaz interactiva en Unity, incorporando herramientas de asistencia perceptiva, monitorización en tiempo real y optimización del canal de comunicación.

## Estructura del Repositorio

El proyecto se divide en dos bloques principales que representan los lados Maestro (interfaz) y Esclavo/Bridge (comunicaciones y procesamiento):

```text
├── teleop/                  # Proyecto de Unity (Lado Maestro)
│   ├── Assets/
│   │   ├── Scripts/
│   │   │   ├── RoverController.cs    # Control de movimiento base (/cmd_vel)
│   │   │   ├── SpeedSlider.cs        # Modificación de velocidad en tiempo real
│   │   │   ├── CameraSubscriber.cs   # Suscriptor de vídeo  
│   │   │   ├── CameraSwapper.cs      # Intercambiador de pantallas 
│   │   │   ├── SettingsCamera.cs     # Publicador de resolución 
│   │   │   ├── Slider_Brazo.cs       # Control proporcional de articulación 2J1
│   │   │   ├── Brazo.cs              # Control incremental por teclado de articulación 2J1
│   │   │   └── ArmController_girar.cs # Control de giro del articulación J2J y 
│   │   └── ...
│   └── ...
│
└── ros2_ws/                   # Espacio de trabajo de ROS2 (Lado Esclavo y Bridge)
    └── src/
        └── ros_tcp_endpoint/         # Paquete del Bridge de Comunicación
            ├── launch/
            │   └── endpoint.launch.py # Ejecuta Servidor TCP y la Gráfica
            ├── ros_tcp_endpoint/
            │   └── grafica.py         # Nodo para mapa de luz
            ├── CMakeLists.txt
            └── package.xml
```
## Arquitectura del Sistema
El flujo de información implementa un bucle cerrado de control con tolerancia a fallos distribuyendo la carga de procesamiento:
Operador (Teclado/UI) -> Unity (Maestro) -> ROS-TCP-Connector -> Bluetooth -> Robot (Esclavo)
* Lado Maestro (Unity): Centraliza la interacción binaria (teclado) y proporcional (sliders) del usuario. Envía comandos de alto a ROS2 y renderiza el feedback visual de las cámaras remotas.
* Bridge de Comunicación (ros_tcp_endpoint): Actúa como middleware entre Unity y ROS2.
* Nodo Externo (grafica.py): Un proceso desacoplado de Unity que se suscribe a  datos del sensor de luz en tiempo real.
# Requisitos Previos
* Sistema Operativo: Ubuntu 22.04 LTS (o compatible con ROS2)
* ROS2: Humble
* Unity: Versión 2022.3 LTS o superior con el paquete Unity Robotics Visualization y URDF Importer.
* Python 3: Librerías rclpy y matplotlib.
# Instalación y Ejecución 
## Configuración del Lado Esclavo (ROS2)
Clona este repositorio en tu espacio de trabajo de ROS2 y construye el paquete del endpoint:
```Bash
# Navegar al espacio de trabajo
cd ~/tu_workspace_ros2/src/
# Compilar el paquete
cd ..
colcon build --packages-select ros_tcp_endpoint
source install/setup.bash
# Lanzar el orquestador (TCP Endpoint + Gráfica de Luz)
ros2 launch ros_tcp_endpoint endpoint.launch.py
```
Nota: Al ejecutar este comando, el archivo launch abrirá de forma automática una ventana emergente en primer plano con la gráfica interactiva de matplotlib lista para registrar datos de intensidad lumínica.
## Configuración del Lado Maestro (Unity)
* Abre el proyecto "teleop" desde Unity Hub.
* En la barra superior, ve a Robotics -> ROS Settings.
* Configura la ROS IP (por defecto 127.0.0.1) y el ROS Port (10000) y elige ROS2.
* Abre la escena principal y pulsa el botón Play para iniciar la teleoperación asistida
# Decisiones tomadas
* Reducción de Carga Cognitiva: Se migró el control de movimiento de botones web al teclado. El operador memoriza las teclas básicas, manteniendo fijamente su atención en el feedback de las cámaras remotas (diseño optimizado bajo la Ley de Fitts).
* Compromiso Calidad-Latencia: El script SettingsCamera.cs permite modular dinámicamente la resolución de vídeo enviada desde el robot, mitigando los efectos de redes compartidas saturadas (Jitter y retrasos).

