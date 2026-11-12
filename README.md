<div align="center">

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="assets/myoki-logo-white.png">
  <img src="assets/myoki-logo-navy.png" alt="MYOKI IMU" width="220">
</picture>

<p>
  <sub>MAKE YOUR OWN KALMAN IMPLEMENTATION</sub>
</p>

<p>
  <img src="https://img.shields.io/badge/KiCad-10-040F1D?style=flat-square" alt="KiCad 10">
  <img src="https://img.shields.io/badge/ESP32--C3-SuperMini-040F1D?style=flat-square" alt="ESP32-C3 SuperMini">
  <img src="https://img.shields.io/badge/license-MIT-0081FE?style=flat-square" alt="MIT">
</p>

</div>

---

MYOKI es una IMU modular y de código abierto para aprender filtros de Kalman en la asignatura de **Sensores y Actuadores**. Está basada en MYOSA del IEEE Sensors Council.

<div align="center">
  <img src="assets/renders/myoki-case.png" alt="MYOKI ensamblada en su carcasa" width="720">
</div>

## Qué es MYOKI

MYOKI es un *stack* de tres PCB cuadradas de 38.1 mm que se apilan con conectores hembra de 2.54 mm:

| Capa | Proyecto KiCad | Componente principal | Función |
| --- | --- | --- | --- |
| Base | [`myoki-mini-esp32-base`](hardware/myoki-mini-esp32-base) | ESP32-C3 SuperMini | MCU, alimentación (5 V / 3.3 V) y bus I²C / SPI |
| Capa IMU | [`myoki-bmi160-layer`](hardware/myoki-bmi160-layer) | GY-BMI160 | Acelerómetro + giróscopo de 6 ejes |
| Capa magnetómetro | [`myoki-gy271-layer`](hardware/myoki-gy271-layer) | GY-271 | Magnetómetro de 3 ejes |

El dibujo de ejes de la tapa marca la orientación del sistema de referencia de la placa (**+X**, **+Y** y **+Z** saliendo de la placa).

<div align="center">
<img src="assets/myoki-design-top.png" alt="Diseño de la tapa de MYOKI" width="320">
</div>

### Placa base ESP32-C3

<table>
  <tr>
    <td align="center"><img src="assets/renders/esp32-base-assembled.jpg" alt="Placa base ensamblada" width="400"></td>
    <td align="center"><img src="assets/renders/esp32-base-pcb.jpg" alt="Placa base sin componentes" width="400"></td>
  </tr>
  <tr>
    <td align="center"><sub>Ensamblada</sub></td>
    <td align="center"><sub>PCB desnuda</sub></td>
  </tr>
</table>

La base expone los dos buses a las capas de sensores:

| Conector | Pin 1 | Pin 2 | Pin 3 | Pin 4 |
| --- | --- | --- | --- | --- |
| **J1** (SPI) | MOSI | MISO | CS | GND |
| **J2** (I²C) | SDA | SCL | VCC | GND |

El ESP32-C3 SuperMini se monta con el puerto USB-C hacia el borde marcado en la serigrafía.

## Estructura del repositorio

```text
myoki-board/
├── assets/                     Logos y arte gráfico
│   ├── myoki-design-top.png    Diseño de la tapa (acrílico)
│   ├── myoki-logo-banner.png   Banner para documentación
│   ├── myoki-logo-white.png    Logo transparente para fondos oscuros
│   ├── myoki-logo-navy.png     Logo transparente para fondos claros
│   └── renders/                Renders de la carcasa y las PCB
├── hardware/                   Fuentes de diseño (KiCad 10)
│   ├── lib/                    Librería propia "myoky-lib"
│   │   ├── myoky-lib.kicad_sym     Símbolos
│   │   ├── myoky-lib.pretty/       Footprints
│   │   ├── myoky-lib.3dshapes/     Modelos 3D (STEP)
│   │   └── vendor/                 Archivos originales de los fabricantes
│   ├── myoki-mini-esp32-base/  PCB base con ESP32-C3
│   ├── myoki-bmi160-layer/     PCB del BMI160
│   └── myoki-gy271-layer/      PCB del GY-271
└── manufacturing/              Todo lo necesario para fabricar
    ├── bom/                    Lista de materiales (myoki-bom.xlsx)
    ├── gerbers/                Gerbers + drill por placa y .zip listos para enviar
    └── enclosure/              Carcasa: STL para impresión 3D y DXF para corte láser
```

## Cómo construir una MYOKI

### 1. Fabricar las PCB

Sube cada `.zip` de [`manufacturing/gerbers/`](manufacturing/gerbers) a tu fabricante (JLCPCB, PCBWay, etc.):

| Archivo | Placa |
| --- | --- |
| [`myoki-mini-esp32-base.zip`](manufacturing/gerbers/myoki-mini-esp32-base.zip) | Base ESP32-C3 |
| [`myoki-bmi160-layer.zip`](manufacturing/gerbers/myoki-bmi160-layer.zip) | Capa BMI160 |
| [`myoki-gy271-layer.zip`](manufacturing/gerbers/myoki-gy271-layer.zip) | Capa GY-271 |

Parámetros: 2 capas, FR-4, 1.6 mm, 38.1 × 38.1 mm.

### 2. Comprar los componentes

La lista completa, con costos y links de compra, está en [`manufacturing/bom/myoki-bom.xlsx`](manufacturing/bom/myoki-bom.xlsx). Resumen:

| Cant. | Componente | Placa |
| --- | --- | --- |
| 1 | ESP32-C3 SuperMini | Base |
| 1 | Módulo GY-BMI160 | Capa BMI160 |
| 1 | Módulo GY-271 | Capa GY-271 |
| 3 | Condensador 100 nF, 1206 | Base |
| 1 | Resistencia 330 Ω, 1206 | Base |
| 1 | LED rojo, 2010 (5025 métrico) | Base |
| 6 | Conector hembra 1×04, 2.54 mm | 2 por placa |
| 8 + 8 | Separadores y tornillos M3 | Stack |

### 3. Fabricar la carcasa

| Archivo | Proceso |
| --- | --- |
| [`base_plastic_v03.stl`](manufacturing/enclosure/base_plastic_v03.stl) | Impresión 3D, base |
| [`plastic_case_top.stl`](manufacturing/enclosure/plastic_case_top.stl) | Impresión 3D, tapa |
| [`acrylic_drawing.dxf`](manufacturing/enclosure/acrylic_drawing.dxf) | Corte láser en acrílico, con el [diseño de la tapa](assets/myoki-design-top.png) |

### 4. Ensamblar

1. Suelda los componentes SMD y el ESP32-C3 en la base.
2. Suelda los módulos GY-BMI160 y GY-271 en sus capas.
3. Apila las placas con los conectores de 2.54 mm y fíjalas con los separadores M3.
4. Monta el stack en la carcasa y cierra con la tapa de acrílico.

## Editar el diseño

Abre cualquier `.kicad_pro` dentro de [`hardware/`](hardware) con **KiCad 10**. Cada proyecto incluye sus propias `fp-lib-table` y `sym-lib-table`, que apuntan a `${KIPRJMOD}/../lib`, así que la librería `myoky-lib` y sus modelos 3D se cargan sin configuración extra.

---

<div align="center">

<sub>SUPPORTED BY KYMA UN · Licencia <a href="LICENSE">MIT</a> © 2026</sub>

</div>
