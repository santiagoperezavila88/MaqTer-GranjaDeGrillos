# MaqTer-GranjaDeGrillos
#  Diseño de una Granja Sustentable de Grillos

**Incubación modular por racks, simulación CFD y sistema fotovoltaico aislado**

Proyecto final del curso *Diseño de Máquinas Térmicas* (Tecnológico de Monterrey, marzo de 2026). Diseño conceptual de una nave para la crianza de grillos (*Acheta domesticus*) que mantiene temperatura y humedad controladas **climatizando únicamente el microclima de cada rack** en lugar de toda la bodega.

`Transferencia de calor` · `CFD` · `ANSYS Fluent` · `MATLAB` · `Energía solar fotovoltaica` · `Sistemas de control`

---

##  Resumen

Bravo Farm and Laboratories planea una granja de grillos en **Valle de Bravo, México** (19°09'32.4"N, 100°00'27.4"W). El reto principal es el consumo energético necesario para mantener condiciones superiores a las del ambiente exterior (mínimas históricas de 1.3 °C).

La propuesta se apoya en tres ideas:

1. **Incubación distribuida:** cada rack es un módulo aislado con su propio microclima.
2. **Convección natural + humidificación por evaporación:** charolas con agua calentada por resistencias en la base de cada rack generan calor y vapor que ascienden por las cajas.
3. **Energía renovable:** sistema fotovoltaico en configuración isla con baterías de litio.

##  Objetivo

Diseñar conceptualmente una nave industrial sustentable que mantenga **≈ 30 °C** (banda de control 28.5–31 °C) y **≈ 50 % de humedad relativa**, reduciendo consumo energético, costos operativos e impacto ambiental.

##  Diseño de la instalación

| Elemento | Especificación |
|---|---|
| Bodega | ≈ 10.5 m × 10.6 m, pared de 3 m de altura |
| Racks | 20 racks de 1.5 × 1.5 × 2.5 m |
| Cajas incubadoras | 12 por rack → **240 cajas** de 80 × 40 × 30 cm (con cartón tipo huevo como refugio) |
| Pasillos | 0.96 m de ancho |
| Calefacción | Resistencias sumergibles en 4 charolas con agua por rack |
| Ventilación | 2 ventiladores de extracción en la parte superior de cada rack, con tomas de aire de la bodega en la base y costados |
| Materiales | Muros de adobe (20 cm) + paneles sándwich de poliuretano, techo de lámina galvanizada aislada, piso de concreto |

### Mecanismos de transferencia de calor considerados

- **Conducción:** muros, piso, techo, cajas y racks.
- **Convección natural:** ascenso del aire caliente desde las charolas a través de las cajas (mecanismo principal del diseño).
- **Convección forzada:** extracción con ventiladores para regular temperatura y humedad.
- **Radiación:** ganancia solar en techo y muros.

##  Análisis térmico (CFD)

Simulación en **ANSYS Fluent**, en estado estacionario, sobre la geometría de un rack (volumen de aire, cajas y charolas), bajo un escenario desfavorable.

**Condiciones de frontera**

| Zona | Material | Condición |
|---|---|---|
| Entrada (velocity-inlet) | — | 0.1 m/s, 15 °C |
| Salida (exhaust-outlet) | — | Presión manométrica 0 Pa |
| Aislante | Poliuretano (0.1 m) | Convección, h = 5 W/m²·K, T∞ = 15 °C |
| Piso | Concreto | 15 °C |
| Charola | Polietileno | 43 °C (obtenida por proceso iterativo inverso para hallar la potencia de las resistencias) |

**Mallado:** se compararon dos mallas (0.14 m y 0.9 m, quad dominant) y se eligió la malla 2 por su equilibrio entre calidad y costo computacional.

**Resultados**

- Temperaturas cercanas a **30 °C** dentro de las cajas incubadoras.
- Balance de masa con flujo neto ≈ 0 kg/s (se cumple la ecuación de continuidad).
- Pérdida de calor por rack: **≈ 41.85 W** → potencia de diseño de **45 W por rack**.

##  Consumo energético y sistema fotovoltaico

| Parámetro | Valor |
|---|---|
| Potencia para temperatura (20 racks × 45 W) | 900 W |
| Potencia para humedad | 218.741 W |
| **Potencia total del sistema** | **1.118 kW** |
| Consumo diario (operación continua) | 26.849 kWh |
| Consumo mensual | 805.493 kWh |
| Horas solares pico (peor mes: diciembre) | 4.5 h |
| Paneles | 2 × Canadian Solar bifaciales de 710 W (1420 W) |
| Inversor | 2000 W |
| Baterías | 2 × litio 48 V / 100 Ah (5.12 kWh c/u) |

##  Sistema de control

Lazo cerrado con estrategia **ON/OFF** implementada en **Arduino**:

- **Sensores:** LM35 (temperatura) y FC28 (humedad).
- **Referencias:** ≈ 30 °C y ≈ 50 % HR.
- **Actuadores:** ventiladores (bajan temperatura y humedad) y resistencias (suben temperatura y favorecen la evaporación).
- Cada rack se controla de forma independiente, lo que permite adaptarlo a cada etapa de crecimiento.

##  Costos

| Concepto | Costo estimado (MXN) |
|---|---|
| Sistema fotovoltaico (paneles, inversor, baterías) | $208,086.57 |
| Propuesta 1: aislamiento solo en racks | $305,737.57 |
| Propuesta 2: aislamiento en racks + bodega completa | $340,547.81 |

Costos aproximados con cotizaciones vigentes al momento del proyecto; no incluyen instalación.

##  Limitaciones y siguientes pasos

Este es un diseño conceptual. Puntos identificados para una siguiente iteración:

- **Reconciliar el dimensionamiento fotovoltaico con la carga continua.** El reporte dimensionó paneles y baterías con 5.031 kWh/día (1.118 kW × 4.5 HSP), mientras que la carga continua calculada es 26.849 kWh/día. Falta validar generación, autonomía y almacenamiento contra esa demanda (o justificar el ciclo de trabajo real con el consumo de régimen estimado en MATLAB, ≈ 2.5 kWh/día).
- **Sensor de humedad:** el FC28 es un sensor de humedad de suelo; para humedad relativa del aire conviene un sensor como SHT31 o DHT22.
- **Control:** evaluar histéresis o control PID para reducir ciclos de encendido/apagado.
- **Validación experimental** de la simulación con un prototipo de un rack.
- **Coherencia de potencias:** unificar 45 W/rack (temperatura) frente a 55.9 W/rack (temperatura + humedad) y la potencia comercial de resistencia por charola.



##  Herramientas


ANSYS Fluent · MATLAB · Arduino · Canadian Solar (selección de módulos)


## 📎 Anexos

- [Código MATLAB y anexos del proyecto](https://drive.google.com/file/d/1v9DrlZzugqZi4reu0_6h9MOfzvtfEOvT/view?usp=sharing)
