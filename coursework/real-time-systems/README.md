# Real-Time Systems (4101137)

Javier Andrés Muñoz Ariza · Juan Felipe Pachón Restrepo

```
real-time-systems/
├── README.md
├── firmware/
│   ├── superloop/                  # Labs 02–03: superloop en L476RG y ESP32-S3
│   ├── kernel/                     # Labs 04–06: nodo multihilo en ESP32-S3
│   └── sched_labs/                 # Lab 06: conjuntos RM/EDF e inversión de prioridad
├── labs/
│   ├── lab01_bringup.md            # Lab 01: bring-up y task set
│   ├── lab02_superloop.md          # Lab 02: superloop, línea base
│   ├── lab03_port.md               # Lab 03: port al ESP32-S3 y primer hilo
│   ├── lab04_ipc.md                # Lab 04: migración al kernel, A/B, ADR-001
│   ├── lab05_tracing.md            # Lab 05: tracing CTF
│   ├── lab06_schedulability.md     # Lab 06: planificabilidad, ADR-002, ADR-005
│   ├── img/                        # capturas del analizador lógico
│   └── captures/                   # sesiones de PulseView (.sr)
└── tools/scripts/
    ├── sched_sim.py                # simulación RM/EDF y tests de utilización
    ├── rta.py                      # análisis de tiempo de respuesta
    ├── loop_jitter_sim.py          # error del lazo vs jitter
    ├── estop_latency.py            # latencia del e-stop desde sesiones .sr
    ├── sr_pulse_widths.py          # C_i desde sesiones .sr
    ├── ctf_decode.py               # decodificador de trazas CTF
    └── ctf_preempt_stats.py        # estadísticas de despertares desde la traza
```
