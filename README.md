# Sistema Telerrobótico de Control Supervisado: Unity + ROS2

Este repositorio contiene el desarrollo de un sistema de telerrobótica basado en una arquitectura **Maestro-Esclavo** bajo el modelo de **Control Supervisado de Sheridan**. El proyecto permite la teleoperación de un robot móvil (Rover) y su brazo articulado a través de una interfaz interactiva en Unity, incorporando herramientas de asistencia perceptiva, monitorización en tiempo real y optimización del canal de comunicación.

## Estructura del Repositorio

El proyecto se divide en dos bloques principales que representan los lados Maestro (interfaz) y Esclavo/Bridge (comunicaciones y procesamiento):

```text
├── unity_interface/                  # Proyecto de Unity (Lado Maestro)
│   ├── Assets/
│   │   ├── Scripts/
│   │   │   ├── RoverController.cs    # Control de movimiento base (/cmd_vel)
│   │   │   ├── SpeedSlider.cs        # Modificación de ganancias de velocidad en tiempo real
│   │   │   ├── CameraSubscriber.cs   # Suscriptor de vídeo comprimido (/image_raw/compressed)
│   │   │   ├── CameraSwapper.cs      # Intercambiador de pantallas (reducción de carga cognitiva)
│   │   │   ├── SettingsCamera.cs     # Publicador de resolución (/camera_resolution_sc)
│   │   │   ├── Slider_Brazo.cs       # Control proporcional de articulación 2J1
│   │   │   ├── Brazo.cs              # Control incremental por teclado de articulación 2J1
│   │   │   └── ArmController_girar.cs # Control de herramientas y modos ON/OFF (articulación 2J2)
│   │   └── ...
│   └── ...
│
└── ros2_workspace/                   # Espacio de trabajo de ROS2 (Lado Esclavo y Bridge)
    └── src/
        └── ros_tcp_endpoint/         # Paquete del Bridge de Comunicación
            ├── launch/
            │   └── endpoint.launch.py # Orquestador (Levanta el Servidor TCP y la Gráfica)
            ├── ros_tcp_endpoint/
            │   └── grafica.py         # Nodo de telemetría externo (Mapa de luz con Matplotlib)
            ├── CMakeLists.txt
            └── package.xml
´´´
## Arquitectura del Sistema
El flujo de información implementa un bucle cerrado de control con tolerancia a fallos distribuyendo la carga de procesamiento:$$\text{Operador (Teclado/UI)} \longrightarrow \text{Unity (Maestro)} \longrightarrow \text{ROS-TCP-Connector} \longrightarrow \text{Wi-Fi/Bluetooth} \longrightarrow \text{Robot (Esclavo)}$$Lado Maestro (Unity): Centraliza la interacción binaria (teclado) y proporcional (sliders) del usuario. Envía comandos de alto y bajo nivel a ROS2 y renderiza el feedback visual de las cámaras remotas.Bridge de Comunicación (ros_tcp_endpoint): Actúa como middleware de traducción de datos entre los scripts en C# de Unity y los nodos de ROS2.Nodo de Telemetría Externo (grafica.py): Un proceso desacoplado de Unity que adquiere datos del sensor de luz en tiempo real. Al correr de forma independiente, si la interfaz gráfica de Unity sufre caídas de frames debido a la latencia de red, la monitorización de datos críticos no se interrumpe.⚙️ Requisitos PreviosSistema Operativo: Ubuntu 22.04 LTS (o compatible con ROS2) / Windows 10/11 (para Unity).ROS2: Humble / Iron (según la distribución utilizada).Unity: Versión 2022.3 LTS o superior con el paquete Unity Robotics Visualization y URDF Importer.Python 3: Librerías rclpy y matplotlib.🛠️ Instalación y Ejecución1. Configuración del Lado Esclavo (ROS2)Clona este repositorio en tu espacio de trabajo de ROS2 y construye el paquete del endpoint:Bash# Navegar al espacio de trabajo
cd ~/tu_workspace_ros2/src/

# Compilar el paquete
´´´
cd ..
colcon build --packages-select ros_tcp_endpoint
source install/setup.bash
´´´

# Lanzar el orquestador (TCP Endpoint + Gráfica de Luz)
ros2 launch ros_tcp_endpoint endpoint.launch.py
Nota: Al ejecutar este comando, el archivo launch abrirá de forma automática una ventana emergente en primer plano con la gráfica interactiva de matplotlib lista para registrar datos de intensidad lumínica.2. Configuración del Lado Maestro (Unity)Abre la carpeta unity_interface/ desde Unity Hub.En la barra superior, ve a Robotics -> ROS Settings.Configura la ROS IP (por defecto 127.0.0.1) y el ROS Port (10000).Abre la escena principal y pulsa el botón Play para iniciar la teleoperación asistida.📊 Justificación Teórica ImplementadaReducción de Carga Cognitiva: Se migró el control de movimiento de botones web al teclado. El operador memoriza las teclas básicas, manteniendo fijamente su atención en el feedback de las cámaras remotas (diseño optimizado bajo la Ley de Fitts).Compromiso Calidad-Latencia: El script SettingsCamera.cs permite modular dinámicamente la resolución de vídeo enviada desde el robot, mitigando los efectos de redes compartidas saturadas (Jitter y retrasos).Estrategia "Move and Wait": El sistema cuenta con sliders de control de velocidad para ejecutar movimientos de aproximación milimétricos, permitiendo al operador maniobrar de manera segura mediante pequeños impulsos temporales y pausas de estabilización visual.Estructura SCADA: El ecosistema replica un entorno industrial donde la interfaz procesa de manera predictiva y asistida la adquisición de datos sensoriales (Mapa de luz mediante el topic /light_sensor) de forma paralela a las órdenes de acción física.
