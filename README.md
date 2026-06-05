# Migración y Optimización de Software de Control para una Célula de Paletizado (Fanuc → KUKA KRC5)

Proyecto de migración y desarrollo de software de control para una célula industrial de paletizado de cajas utilizando un robot KUKA KR 140 R3200-2 PA controlado mediante KRC5 y programado en KRL (KUKA Robot Language).

El trabajo se ha desarrollado mediante técnicas de Virtual Commissioning, validando el comportamiento del sistema sobre un gemelo digital antes de su implantación en planta.

## Objetivos del proyecto
* Migrar la lógica de control existente desde una plataforma Fanuc.
* Adaptar el software a la arquitectura KUKA KRC5.
* Desarrollar una estructura modular y mantenible en KRL.
* Simular la célula completa mediante un gemelo digital.
* Optimizar trayectorias y tiempos de ciclo.
* Validar la lógica de comunicación con la línea de producción.
## Tecnologías utilizadas
* KUKA KRC5
* KUKA Robot Language (KRL)
* iiQWorks.Sim / KUKA.Sim
* PROFINET
* Virtual Commissioning
* GitHub
## Funcionalidades implementadas
### Arquitectura modular
El software se divide en módulos independientes encargados de:
* Gestión del ciclo principal.
* Cálculo de posiciones de recogida.
* Generación dinámica del mosaico de paletizado.
* Secuencias de recogida.
* Secuencias de deposición.
* Gestión de rechazos.
* Mantenimiento y reinicio.
* Simulación de señales mediante SPS.SUB.
### Movimientos optimizados
Se emplean movimientos basados en tecnología Spline:
* SPTP
* SLIN
permitiendo trayectorias más fluidas y reduciendo las paradas intermedias respecto a la programación clásica mediante PTP y LIN.
### Gestión de eventos en tiempo real
Implementación de:
* Interrupciones (INTERRUPT)
* Triggers sobre trayectoria (TRIGGER)
* Temporizadores ($TIMER[])
* Señales de E/S industriales
### Simulación de periféricos
El intérprete SPS.SUB se utiliza para:
* Simular sensores.
* Simular actuadores neumáticos.
* Gestionar enclavamientos.
* Emular el comportamiento de la línea de producción.
## Estructura del repositorio
KRC/
├── R1/
│   ├── Program/
│   │   ├── RSR0001.src
│   │   ├── Paletiza.src
│   │   ├── Calc_Mosaico.src
│   │   ├── Calc_Rec.src
│   │   ├── Recoger.src
│   │   ├── Dejar.src
│   │   ├── Rechazo.src
│   │   └── Mantenimiento.src
│   │
│   └── System/
│       └── SPS.SUB
│
└── STEU/
    └── MADA/
        └── $CONFIG.DAT
## Descripción de módulos
### Módulo	Función
* RSR0001	Programa principal y gestión de estados
* PALETIZA	Secuencia principal de producción
* CALC_MOSAICO	Cálculo dinámico de posiciones de paletizado
* CALC_REC	Cálculo de posiciones de recogida
* RECOGER	Ciclo de captura de cajas
* DEJAR	Ciclo de deposición
* RECHAZO	Gestión de cajas rechazadas
* SPS.SUB	Simulación de señales y lógica en segundo plano
## Validación mediante gemelo digital
El software se validó utilizando un controlador virtual KUKA OfficeLite integrado en el entorno de simulación. Las pruebas realizadas incluyeron:

* Validación de trayectorias.
* Detección de colisiones.
* Verificación de alcances.
* Comprobación de singularidades.
* Validación del protocolo de señales.
* Medición de tiempos de ciclo.
## Resultados obtenidos
* Migración completa del software a KUKA KRC5.
* Funcionamiento validado en entorno virtual.
* Reducción estimada del tiempo de ciclo mediante movimientos Spline.
* Eliminación de colisiones en las trayectorias de trabajo.
