# Migración y Optimización de Software de Control para una Célula de Paletizado (Fanuc → KUKA KRC5)

Proyecto de migración y rediseño de software de control para una célula industrial de paletizado de cajas basada en robot KUKA KR 140 R3200-2 PA con controlador KRC5, programado en KRL (KUKA Robot Language).

El sistema fue desarrollado y validado mediante Virtual Commissioning, utilizando un gemelo digital completo de la célula antes de su despliegue, permitiendo verificar comportamiento dinámico, trayectorias, tiempos de ciclo y lógica de señales sin necesidad de hardware físico.

## Objetivos del proyecto
* Migración de lógica de control desde sistema Fanuc a KUKA KRC5* Adaptar el software a la arquitectura KUKA KRC5.
* Diseño de arquitectura modular en KRL
* Generación dinámica de patrones de paletizado
* Optimización de trayectorias robotizadas y reducción de tiempos de ciclo
* Validación mediante gemelo digital
* Integración de señales industriales y control de actuadores en la célula de paletizado
## Tecnologías utilizadas
* KUKA KRC5
* KUKA Robot Language (KRL)
* iiQWorks.Sim 
* PROFINET
* Virtual Commissioning
* GitHub
## Arquitectura modular
El software se estructura en módulos independientes que separan lógica de producción, planificación de trayectorias y gestión de señales:* Gestión del ciclo principal.
* Control del ciclo principal de producción
* Generación dinámica de patrones de paletizado
* Cálculo de posiciones de recogida y deposición
* Gestión de rechazos y reintentos
* Mantenimiento y reinicio del sistema
* Simulación de sensores y gestión de señales de E/S industriales, incluyendo integración de módulos SMC NS para el control de actuadores neumáticos
### Movimientos optimizados
Se emplean movimientos basados en tecnología Spline:
* SPTP
* SLIN
permitiendo trayectorias más suaves, reducción de paradas intermedias y una mejora en los tiempos de ciclo frente a programación con PTP/LIN clásica
## Gestión de eventos en tiempo real
El sistema integra::
* Interrupciones (INTERRUPT)
* Triggers en trayectoria (TRIGGER)
* Temporizadores ($TIMER[])
* Señales de E/S industriales
## Validación mediante gemelo digital
El sistema fue validado en entorno virtual utilizando el siulador iiQWorks.sim, incluyendo:
* Verificación de trayectorias sin colisiones
* Validación de alcance y singularidades
* Validación de sincronización de señales
* Simulación completa del flujo de producción
* Estimación de tiempos de ciclo

<img width="1628" height="927" alt="Captura de pantalla 2026-05-03 152541" src="https://github.com/user-attachments/assets/570d49bf-6fe6-48d1-9990-e50b32a41972" />
El elemento terminal ha sido representado mediante una caja azul para preservar la confidencialidad del hardware propietario, manteniendo la cinemática y puntos de conexión equivalentes al sistema real.
## Estructura del repositorio
```text
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
````
## Resultados obtenidos
* Migración completa de arquitectura Fanuc → KUKA KRC5
* Validación funcional completa en entorno de simulación industrial
* Arquitectura modular escalable y mantenible
* Mejora del flujo de movimiento mediante Spline interpolation
* Sistema preparado para despliegue en entorno real
