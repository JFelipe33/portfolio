# Real-Time Embedded Systems Portfolio — Zephyr RTOS, STM32 & ESP32-S3

Este repositorio contiene las prácticas, arquitecturas de software e investigaciones sobre Sistemas en Tiempo Real (RTOS) desarrolladas durante el curso (4101137). Incluye la implementación de firmware en C sobre la plataforma Zephyr RTOS, análisis de tiempos en hardware real (STM32 NUCLEO-L476RG y ESP32-S3), instrumentación física con analizador lógico y análisis de requerimientos bajo el estándar EARS.

## 👤 Autores

- **Estudiantes:** Javier Andrés Muñoz Ariza · Juan Felipe Pachón Restrepo
- **Plataforma Objetivo:** STM32L476RG (ARM Cortex-M4) / ESP32-S3 (Xtensa LX7, 240 MHz)
- **RTOS & Toolchain:** Zephyr RTOS | West | OpenOCD | WSL2 (Ubuntu)
- **Instrumentación:** Analizador lógico USB de 8 canales (PulseView y Saleae Logic 2) · Digilent WaveForms

## 🛠️ Tecnologías y Herramientas

- **Sistemas Operativos en Tiempo Real:** Zephyr RTOS (Superloop, Threads, Preemption, IPC, Tracing, EDF, Priority Inheritance)
- **Lenguaje:** C (Embedded Systems) · Python (análisis de planificabilidad y de capturas)
- **Hardware:** STM32 NUCLEO-L476RG / ESP32-S3 DevKitC-1
- **Herramientas de Análisis:** analizador lógico (jitter, latencia ISR, C_i y T_i), trazas CTF
- **Control de Versiones & Entorno:** Git, GitHub, Linux / WSL2, VS Code

## 🚦 Estado de Avance de Laboratorios

| Lab | Tema / Arquitectura | Estado | Reporte |
| :---: | :--- | :---: | :---: |
| **01** | *Bring-up: Environment, Toolchain & Board* | `[x]` Completado | [lab01_bringup.md](labs/lab01_bringup.md) |
| **02** | *Superloop Architecture, Cache Jitter & Measurement* | `[x]` Completado | [lab02_superloop.md](labs/lab02_superloop.md) |
| **03** | *The S3 Port and the First Thread* | `[x]` Completado | [lab03_port.md](labs/lab03_port.md) |
| **04** | *Full Migration and A/B Testing* | `[x]` Completado | [lab04_ipc.md](labs/lab04_ipc.md) |
| **05** | *Tracing & Per-Task Execution* | `[~]` Medido en la placa (`threads`, trazas CTF, captura GPIO de `calib`) · falta el A/B del jitter con tracing ON/OFF | [lab05_tracing.md](labs/lab05_tracing.md) |
| **06** | *Schedulability: Fig. 4.13, Inversión de Prioridad y ADR-005* | `[x]` Completado · pendiente: 3 disparos más «después» del e-stop | [lab06_schedulability.md](labs/lab06_schedulability.md) |

## 📁 Estructura

```
real-time-systems/
├── firmware/
│   ├── superloop/    # Labs 02–03: superloop + overlays por placa (L476RG, ESP32-S3, ...)
│   ├── kernel/       # Labs 04–06: nodo multihilo en el ESP32-S3 (tracing, ADR-005)
│   └── sched_labs/   # Lab 06: conjuntos sintéticos RM/EDF e inversión de prioridad
├── labs/             # informes lab01..lab06, capturas (img/) y sesiones de PulseView (captures/)
└── tools/scripts/    # análisis: planificabilidad, RTA, trazas CTF y sesiones .sr
```

## 📌 Destacados Técnicos

### 📌 Laboratorio 02: Arquitectura Superloop y Mediciones de Tiempo Real (NUCLEO-L476RG)
- **Diseño del Firmware:** temporización basada en ISR con banderas y divisor de frecuencia en C (`firmware/superloop/src/main.c`).
- **Línea base (30 s):** periodo medio de muestreo 996.03 µs y jitter máximo de **22 578 µs** con el acelerador ART activo.
- **Impacto del Caché Flash:** sin acelerador (`nocache.conf`) el jitter máximo sube a **24 239 µs**.
- **Comando bloqueante:** `calib` (1000 × `k_busy_wait(400)`) lleva el jitter a **437 588 µs** y `backlog_peak` de 30 a 440 ticks.
- **Requerimientos EARS:** nueve requerimientos con ID (`REQ-SAMP-01`, `REQ-CTRL-02`, `REQ-LAT-01`, ...), verificados con la instrumentación GPIO.

### 📌 Laboratorio 03: Port a ESP32-S3 y Primer Thread
- **Portabilidad Devicetree:** migración del superloop al ESP32-S3 con un overlay (`boards/esp32s3_devkitc_esp32s3_procpu.overlay`): **0 cambios en el código C**.
- **Superloop en el S3:** jitter máximo de 95 079 µs en reposo y 495 628 µs con `calib`; el periodo de control llega a 1147.23 ms.
- **Primer hilo (`sampling_thread`, prioridad 2):** el jitter de muestreo baja a **3.00 µs** incluso con `calib`, `backlog_peak` = 0 y `lat_peak_us` = 7 µs; el control, aún en `main`, sigue llegando a 400.57 ms.

### 📌 Laboratorio 04: Migración al kernel preemptivo y prueba A/B
- **Arquitectura (ADR-001, Accepted):** seis hilos en orden rate-monotonic (`sampling` 2, `control` 3, `flow_wq` 5, `display` 7, `telemetry` 8, `console` 9), sincronizados con `k_msgq`, `k_sem` y una workqueue propia.
- **Resultado A/B en el ESP32-S3:** con `calib`, el jitter máximo de muestreo pasa de 495 628.44 µs a **6.8 µs** y el periodo máximo de control de 1147.23 ms a **10.005 ms**.
- **Costo:** cambio de contexto visible de **6.5 µs** (≈ 1.7 % de la CPU); pilas más comprometidas: ISR0 (87 %) e `idle` (81 %).

### 📌 Laboratorio 05: Tracing (CTF sobre USB-Serial-JTAG)
- **Ruta adaptada:** el stream CTF sale por el periférico **USB-Serial-JTAG** (jack USB) y la consola queda en el jack UART, sin hardware adicional.
- **Firmware (`firmware/kernel`):** hilos con nombre legible, estado compartido en `atomic_t`, comando `threads` con stack y **CPU % por hilo** (`CONFIG_THREAD_RUNTIME_STATS`), y `notrace.conf` para el A/B del costo del tracing.
- **Hallazgos medidos:** cambio de contexto según traza 4.71 µs y latencia ISR → hilo 20.2 µs (incluyen el costo de registrar los eventos); la traza es una muestra con pérdidas (≈ 1.8 % de los ticks), su reloj se envuelve cada 76.86 s y `tracing_thread` consume el 39 % de la CPU. Se decodifica con `tools/scripts/ctf_decode.py`.
- **Captura por GPIO:** durante `calib`, D2 (`console`) queda 403.5 ms en alto mientras D0 (`sampling`) sigue pulsando.

### 📌 Laboratorio 06: Schedulability, inversión de prioridad y ADR-005
- **Task A (Fig. 4.13):** τ1 (C = 2 ms, T = 5 ms) y τ2 (C = 4 ms, T = 7 ms), U = 0.971: bajo RM `tau2` pierde su deadline en t = 7 ms y bajo EDF no hay fallos en 60 s. Además, un conjunto sintético de tres tareas en `firmware/sched_labs`: RM pierde `tau3` a los 149.997 ms y EDF cumple.
- **Task B (inversión):** `control` responde en **1.013 ms** sin lock, **14.037 ms** con `k_sem` (301 de 301 fallos) y **4.038 ms** con `k_mutex`; **B = 4.024 ms**. La RTA da cotas de 1, 15 y 5 ms (ADR-002).
- **Task C (ADR-005):** el e-stop pasó de **hasta 10.005 ms** a **4.0 µs** al poner `duty_pct = 0` en el mismo trabajo de `sampling`; `calib` pasó de 387 ms de CPU a **13 ms** con `k_usleep` y tick de 10 kHz.
- **Tareas de Daniela:** con los dos sensores nuevos U = 0.466 ≤ 0.7348 (Liu & Layland, n = 6) y producto hiperbólico 1.525 ≤ 2: el nodo los admite, con todas las respuestas de peor caso dentro de su deadline.
- **Stretch:** el error del lazo con jitter inyectado de 0, 2 y 5 ms es 12 mV en los tres casos.

### 🔧 Cómo reproducir los cálculos

```bash
python3 tools/scripts/sched_sim.py sim --tasks "2:5,4:7" --timeline 20   # Lab 06, Task A: Fig. 4.13, RM vs EDF
python3 tools/scripts/sched_sim.py sim --timeline 30                    # Lab 06, Task A: conjunto sintético
python3 tools/scripts/sched_sim.py tests --tasks "flow_isr:4.2:1000,sampling:11.7:1000,control:4.0:10000,telemetry:65:1000000,sensor_a:2000:10000,sensor_b:5000:20000"   # Lab 06 §4.2: tareas de Daniela
python3 tools/scripts/rta.py --preset taskb-pi --trace                  # Lab 06, Task B: RTA con bloqueo
python3 tools/scripts/loop_jitter_sim.py --sweep                        # Lab 06, stretch: error vs jitter
python3 tools/scripts/estop_latency.py labs/captures/lab06_taskC_estop_before.sr    # Lab 06, Task C: e-stop detección → válvula
python3 tools/scripts/sr_pulse_widths.py labs/captures/lab06_taskC_estop_before.sr  # Lab 06 §4.1: C_i del nodo en el S3
python3 tools/scripts/ctf_decode.py $ZEPHYR_BASE/subsys/tracing/ctf/tsdl/metadata channel0_0 > trace.txt   # Lab 05: traza CTF
python3 tools/scripts/ctf_preempt_stats.py trace.txt                    # Lab 05: despertares de sampling (t0..t3)
```

Los escenarios de `firmware/sched_labs` se seleccionan por Kconfig (`-DCONFIG_SCHED_LAB_RM=y`, `..._EDF`, `..._NOLOCK`, `..._INVERSION`, `..._PI`); los comandos exactos están en el informe del Lab 06.
