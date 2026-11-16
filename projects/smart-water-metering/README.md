# Smart Water Metering

Medición y cobro de agua por usuario (prepago) para distritos de riego y acueductos veredales.

| | |
|---|---|
| **Estado** | 🟡 En planeación |
| **Cursos / contexto** | IoT Systems Design (4201327) · Real-Time Systems (4101137) |
| **Equipo** | <nombres> |
| **Hardware** | ESP32-S3, ESP32-C6 |
| **Software** | Zephyr RTOS, OpenThread, CoAP |
| **Inicio** | 2026-11 |

## Estructura

El proyecto se entrega a dos cursos con presentaciones distintas, así que cada uno tiene su
carpeta y lo común queda en `shared/`.

```
smart-water-metering/
├── README.md
├── iot/                         # IoT Systems Design
│   ├── ddr_template.md          # plantilla del curso: Design & Decision Record
│   ├── business-case/           # documento del Business viewpoint
│   ├── pitch/                   # presentación y demo
│   └── evidence/                # mediciones y capturas
├── rts/                         # Real-Time Systems
│   ├── ret.md                   # plantilla del curso: Timing Evidence Report
│   ├── presentation/
│   │   ├── talk.md              # plantilla del curso: deck Marp
│   │   └── theme/course.css     # tema Marp que usa la plantilla
│   ├── analysis/                # utilización, RTA, scripts
│   └── evidence/                # capturas del analizador, trazas
└── shared/                      # común a los dos cursos
    ├── firmware/
    ├── hardware/
    └── tools/
```
