# Monitor de correas

**Una cámara bajo el retorno de una correa transportadora y un modelo de IA en un computador local. Detecta grietas con la correa en marcha, confirma cada una en varios cuadros, la ubica en metros desde el empalme y genera la orden de trabajo.**

[![Abrir la máquina](https://img.shields.io/badge/Probar-jsnn15.github.io%2Fmonitor--correas-3fd5f0?style=for-the-badge)](https://jsnn15.github.io/monitor-correas/)
![YOLOv8](https://img.shields.io/badge/YOLOv8-BeltCrack-111?style=flat-square)
![ONNX Runtime Web](https://img.shields.io/badge/ONNX%20Runtime-en%20el%20navegador-555?style=flat-square)
![Licencia](https://img.shields.io/badge/licencia-AGPL--3.0-blue?style=flat-square)

**Español** · [English](README.en.md)

<a href="https://jsnn15.github.io/monitor-correas/"><img src="docs/demo.gif" alt="Video de una correa en marcha con una grieta marcada, su posición en el mapa de la correa y la prioridad" width="100%"></a>

## El problema

Una grieta en la correa puede terminar en un corte longitudinal y días de detención. Hoy se busca con rondas a pie cada varias horas, con la correa en marcha y poca luz.

## Cómo funciona

<img src="docs/flujo.svg" alt="Cámara, detector YOLOv8, confirmación en 3 de 5 cuadros, ubicación en metros y orden de trabajo" width="100%">

```mermaid
flowchart LR
    V["Cuadro de video<br/>correa a 5 m/s"] --> D[Detector YOLOv8]
    D --> T{"¿La misma grieta en<br/>3 de 5 cuadros?"}
    T -->|no| V
    T -->|sí| L["Posición = v · t<br/>metros desde el empalme"]
    L --> M["Mapa de la correa<br/>la grieta viaja con ella"]
    M --> P{Tamaño y repetición}
    P -->|grande y confirmada<br/>en otra vuelta| S[Detener en forma controlada]
    P -->|pequeña| O[Orden de trabajo]
```

- **Confirmación temporal.** Una detección aislada no genera aviso: la grieta debe aparecer en 3 de 5 cuadros seguidos.
- **Ubicación.** Con la velocidad de la correa y el tiempo desde que pasó el empalme se calcula la posición en metros, que es lo que necesita mantenimiento para encontrarla.
- **Prioridad.** La orden de detener solo sale si una grieta grande se confirma en otra vuelta; el resto va como orden de trabajo.

## Resultados, con sus límites

Probado con 9 videos de prueba que el modelo no vio:

| Medida | Resultado |
|---|---|
| Videos con aviso correcto | 6 de 9 |
| Avisos que eran grietas reales | 73 % |
| Correas con dibujo chevrón vistas de cerca | sin aviso correcto: casi no aparecen en el entrenamiento |
| Falsos avisos sobre correa sana durante horas | **no medible** con este conjunto: solo trae cuadros con grietas |

En un piloto real se entrena con dos a cuatro semanas de video de la correa del cliente, incluidas horas sin daño, y el umbral se fija junto a mantenimiento.

## Archivos

| Archivo | Qué es |
|---|---|
| `index.html` | Sala de control, prueba con foto propia o con la cámara en vivo (celular, tablet o PC) y resultados video por video. |
| `det.js` | Inferencia ONNX y posprocesamiento. |
| `model.onnx.part0`, `.part1` | Pesos del detector en dos partes. |
| `f/a`, `f/b`, `f/c` | Cuadros de las tres correas de la demo (CV-101, CV-102, CV-103). |
| `m/` | Cuadros de ejemplo para la prueba con foto. |

## Licencias y créditos

- Video y anotaciones: **BeltCrack** (Huang, Ji, Ma y Ye, 2026), *Pattern Recognition*, doi:[10.1016/j.patcog.2026.113598](https://doi.org/10.1016/j.patcog.2026.113598); dataset en Science Data Bank, doi:[10.57760/sciencedb.31181](https://doi.org/10.57760/sciencedb.31181), **CC BY-SA 4.0**. Los cuadros de este repositorio están reducidos y se publican bajo la misma licencia.
- Detector **Ultralytics YOLOv8 (AGPL-3.0)**; por eso el código de este repositorio es AGPL-3.0.

---

<sub>Proyecto personal de **Julián Neira Nova**, ingeniero eléctrico con 10 años en faena · Servicio y Soluciones Tecnológicas DelaCruz SpA. Nada de lo publicado proviene de clientes ni de empleadores.
[Portafolio](https://jsnn15.github.io/) · [LinkedIn](https://www.linkedin.com/in/julian-neira-nova/) · [Todas las máquinas](https://github.com/JSNN15)</sub>
