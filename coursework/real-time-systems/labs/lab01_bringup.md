# Reporte de Laboratorio 01: Bring-up, Entorno y Tabla RET

## §1 Información del Equipo y Plataforma
* **Integrantes:** Javier Andrés Muñoz Ariza · Juan Felipe Pachón Restrepo
* **Tarjetas de Desarrollo:** NUCLEO-L476RG (semanas 1–2) · ESP32-S3 DevKitC-1 (desde la semana 3)
* **Toolchain & RTOS:** Zephyr RTOS v3.x | West | OpenOCD | WSL2 (Ubuntu)

---

## §2 Tabla de Requerimientos y Ejecución (RET) — Task Set Inicial

Tareas del nodo SoilSense Control según el escenario del curso. Los $C_i$ quedan pendientes hasta medirlos con el analizador lógico (Lab 02 en el L476RG, Labs 03–06 en el ESP32-S3).

| Task ID | Task Name | Period ($T_i$) | Deadline ($D_i$) | Execution Time ($C_i$) | Type (H / F / S) | Description |
| :---: | :--- | :---: | :---: | :---: | :---: | :--- |
| **TSK-01** | Sampling | $1\text{ ms}$ | $1\text{ ms}$ | **TBD** *(Lab 02)* | **Hard (H)** | Lectura de presión a 1 kHz, detección del e-stop y paso del PWM de la válvula. |
| **TSK-02** | Control Loop | $10\text{ ms}$ | $10\text{ ms}$ | **TBD** *(Lab 02)* | **Hard (H)** | Lazo PI de presión → duty de la válvula. |
| **TSK-03** | Overpressure E-stop | Aperiódica | $5\text{ ms}$ | **TBD** *(Lab 06)* | **Hard (H)** | Cerrar la válvula ante sobrepresión. |
| **TSK-04** | Flow ISR Service | Por evento | $10\ \mu\text{s}$ | **TBD** *(Lab 02)* | **Hard (H)** | Atención de la interrupción de pulsos de caudal. |
| **TSK-05** | Telemetry Transmit | $1000\text{ ms}$ | $1000\text{ ms}$ | **TBD** *(Lab 02)* | **Soft (S)** | Línea CSV de estado hacia el Hub. |
| **TSK-06** | Display HMI | $500\text{ ms}$ | $500\text{ ms}$ | **TBD** *(Lab 03)* | **Soft (S)** | Refresco de la pantalla SSD1306. |
| **TSK-07** | Console / Shell | Aperiódica | $50\text{ ms}$ | **TBD** *(Lab 02)* | **Firm (F)** | Comandos por UART: una respuesta tardía no sirve, pero no causa daño. |

*Nota: H (Hard) exige cumplir el deadline siempre, porque un fallo causa daño físico; F (Firm) descarta el resultado tardío sin daño; S (Soft) tolera retrasos ocasionales con pérdida gradual de valor.*

---

## §3 Evidencias de Ejecución y Ciclo de Iteración

### Task A — Blinky & Hardware Bring-up
* **Resultado:** Verificación exitosa de compilación y flasheo sobre hardware STM32 mediante OpenOCD.
* **Comando de compilación:**
```text
west build -p auto -b nucleo_l476rg zephyr/samples/basic/blinky && west flash -r openocd
```

### Task B — Monitor Serial & Modificación de Mensaje
* **Resultado:** Verificación del ciclo completo de iteración (modificación de código $\rightarrow$ rebuild $\rightarrow$ flash $\rightarrow$ serial monitor).
* **Consola Serial (115200 8N1 via tio):**
```text
*** Booting Zephyr OS build v3.x.x ***
Hello World! SoilSense Bring-up verified by Javier Muñoz
Board: nucleo_l476rg | Iteration cycle < 1 min DEMONSTRATED.
```

## §4 Automatización e Infraestructura Out-of-Tree Desarrollada
Como mejora técnica al proceso de Bring-up, se desarrolló un repositorio de infraestructura independiente para automatización:
* Repositorio de automatización: JavierAndresMunozAriza/lab01_bringup
* Aportes clave desarrollados:
  * Out-of-Tree Workflow: Separación limpia entre el código de aplicación y el código fuente de Zephyr.
  * Abstracción Devicetree: Uso de alias portables (led0, sw0) para ejecutar el mismo binario en STM32 y ESP32-C6.
  * Automatización en VS Code: Integración de atajos de teclado **(.vscode/tasks.json)** para compilación y monitoreo serial automático con **tio /dev/ttyACM0**.
