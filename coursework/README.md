# Coursework

Entregables de cursos universitarios, una carpeta por curso.

| Carpeta | Curso | Código | Contenido |
|---|---|---|---|
| [iot-systems-design/](iot-systems-design/) | IoT Systems Design | 4201327 | DDR de los labs |
| [real-time-systems/](real-time-systems/) | Real-Time Systems | 4101137 | Informes de los labs 01–06, firmware Zephyr y scripts de análisis |

Las dos carpetas conservan el historial completo de sus repositorios originales
(JFelipe33/IOT y JFelipe33/Real-Time-Systems-): `git log -- coursework/<curso>` lo muestra.

## Agregar un curso

1. Crea `coursework/<nombre-del-curso>/`, en inglés, minúsculas y con guiones (por ejemplo `digital-control`).
2. Agrega su fila a la tabla de arriba y a la tabla de cursos del [README principal](../README.md).

Si el curso ya tiene su propio repositorio y quieres traer su historial:

```bash
git subtree add --prefix=coursework/<nombre-del-curso> https://github.com/<usuario>/<repo>.git main
```
