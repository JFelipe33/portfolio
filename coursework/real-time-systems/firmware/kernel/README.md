# firmware/kernel

```
kernel/
├── README.md
├── CMakeLists.txt
├── Kconfig                         # opciones del lab (caché ART, jitter inyectado)
├── prj.conf                        # configuración del nodo (hilos, tracing CTF, thread analyzer)
├── notrace.conf                    # mismo nodo sin tracing
├── display.conf                    # HMI SSD1306
├── nocache.conf                    # caché ART desactivada (STM32L4)
├── pot_esp32.overlay               # potenciómetro como presión (ESP32)
├── pot_nucleo.overlay              # potenciómetro como presión (Nucleo)
├── boards/                         # mapa de pines por placa
│   ├── esp32s3_devkitc_esp32s3_procpu.overlay
│   ├── esp32c6_devkitc_hpcore.overlay
│   ├── nucleo_l476rg.overlay
│   └── stm32c0116_dk.overlay
└── src/
    ├── main.c                      # nodo multihilo
    ├── main_superloop.c            # superloop de referencia
    └── cache_stm32l4.c             # desactivación de la caché ART
```
