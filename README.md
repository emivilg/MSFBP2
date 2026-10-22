[![Open in MATLAB Online](https://www.mathworks.com/images/responsive/global/open-in-matlab-online.svg)](https://matlab.mathworks.com/open/github/v1?repo=DrPaulValle/MSFBP0)

# Práctica 2 — Sistema cardiovascular

Repositorio académico de la asignatura **Modelado de Sistemas Fisiológicos (IBF-1020)** del programa de **Ingeniería Biomédica**, Tecnológico Nacional de México / Instituto Tecnológico de Tijuana. La práctica desarrolla el análisis, simulación y control *in silico* de un modelo Windkessel de cuatro elementos para representar la dinámica vascular arterial.

El sistema considera la impedancia característica proximal $Z$, la inertancia arterial $L$, la distensibilidad arterial $C$ y la resistencia periférica $R$. Se analiza la presión sobre la distensibilidad arterial $P_p(t)$ ante una presión arterial de entrada $P_a(t)$, en lazo abierto y con control PID.

**Docente:** Dr. Paul Antonio Valle Trujillo  
**Correo:** paul.valle@tectijuana.edu.mx  
**Departamento:** Ingeniería Eléctrica y Electrónica  
**Institución:** Tecnológico Nacional de México / Instituto Tecnológico de Tijuana  
**Asignatura:** Modelado de Sistemas Fisiológicos (IBF-1020)  
**Programa:** Ingeniería Biomédica  
**Estudiante:** Nombre completo · Número de control · correo@tijuana.tecnm.mx  

**Repositorio de la asignatura:** https://github.com/DrPaulValle/MSF-IBF1020  
**Sede:** Blvd. Alberto Limón Padilla s/n, Tijuana, C. P. 22454, B. C., México.

> **Nota sobre el badge:** actualizar el enlace de MATLAB Online cuando se asigne o cree el repositorio específico de esta práctica.

---

<a id="contenido"></a>
## Contenido

1. [Resumen](#resumen)
2. [Competencia y objetivos](#objetivos)
3. [Relación con el temario](#temario)
4. [Modelo físico y analogía eléctrica](#analogia)
5. [Formulación matemática](#modelo)
6. [Análisis temporal y de estabilidad](#analisis)
7. [Señales de entrada y condiciones de simulación](#senales)
8. [Implementación en Python, Simulink y Multisim](#implementacion)
9. [Control en lazo cerrado y sintonización PID](#pid)
10. [Resultados y verificación cruzada](#resultados)
11. [Flujo de trabajo](#flujo)
12. [Actividades del estudiante](#actividades)
13. [Estructura sugerida del repositorio](#estructura)
14. [Requisitos](#requisitos)
15. [Reproducibilidad y entregables](#reproducibilidad)
16. [Referencias](#referencias)
17. [Uso académico](#uso-academico)

---

<a id="resumen"></a>
## Resumen

Esta práctica modela el comportamiento hemodinámico arterial mediante un modelo Windkessel de cuatro elementos. A partir de la topología indicada, se obtiene la función de transferencia entre la presión arterial de entrada $P_a(t)$ y la presión sobre la distensibilidad arterial $P_p(t)$. También se determina la estabilidad en lazo abierto, el error en estado estacionario ante escalón y la ubicación de polos para tres conjuntos de parámetros: hipotenso, normotenso e hipertenso.

La implementación se limita a simulaciones en **Python, Simulink y Multisim**. El protocolo solicita emplear una señal aleatoria uniforme, comparar lazo abierto y cerrado, sintonizar un PID en Simulink y documentar un diagrama biológico en BioRender. No se reportan resultados numéricos de simulación, ganancias PID ni montajes físicos no proporcionados por el estudiante.

**Palabras clave:** Windkessel; sistema cardiovascular; presión arterial; distensibilidad arterial; impedancia vascular; inertancia arterial; PID; Simulink; Python; Multisim; simulación *in silico*.

[Volver al contenido](#contenido)

---

<a id="objetivos"></a>
## Competencia y objetivos

### Competencia a desarrollar

Aplicar herramientas de modelado matemático, análisis de estabilidad, simulación y control retroalimentado para representar la dinámica principal de un sistema fisiológico cardiovascular mediante el modelo Windkessel de cuatro elementos.

### Objetivo general

Obtener, analizar y simular el modelo Windkessel de cuatro elementos, comparando los casos hipotenso, normotenso e hipertenso en lazo abierto y en lazo cerrado con un controlador PID sintonizado en Simulink.

### Objetivos específicos

- Identificar la entrada $P_a(t)$, la salida $P_p(t)$, los flujos y los parámetros hemodinámicos del sistema.
- Derivar la función de transferencia $P_p(s)/P_a(s)$ desde la topología del modelo.
- Formular ecuaciones diferenciales de primer orden con estados $F_L(t)$ y $P_p(t)$.
- Determinar estabilidad y error en estado estacionario en lazo abierto.
- Calcular y comparar los polos de los tres casos clínicos proporcionados.
- Simular la respuesta para $0\leq t\leq15\,\mathrm{s}$ en Python, Simulink y Multisim.
- Construir el diagrama de bloques de control y sintonizar $k_P$, $k_I$ y $k_D$ con PID Tuner.
- Elaborar un diagrama biológico del sistema con BioRender.
- Distinguir resultados de referencia, resultados reproducidos y aportaciones propias.

[Volver al contenido](#contenido)

---

<a id="temario"></a>
## Relación con el temario

| Unidad | Área | Contenidos aplicados |
|---|---|---|
| I | Sistemas de control retroalimentados | Diagramas de bloques, respuesta temporal, error estacionario, estabilidad y retroalimentación negativa |
| II | Modelado matemático | Analogía hidráulica-eléctrica, variables de estado, ecuaciones diferenciales y función de transferencia |
| III | Controladores | Control PID, sintonización con PID Tuner y comparación de desempeño |

[Volver al contenido](#contenido)

---

<a id="analogia"></a>
## Modelo físico y analogía eléctrica

El modelo de cuatro elementos posee dos elementos dinámicos: la inertancia $L$ y la distensibilidad $C$. Entre el nodo de entrada $P_a(t)$ y el nodo de salida $P_p(t)$ se encuentran $Z$ y $L$ en paralelo. Desde $P_p(t)$ hasta la referencia se ubican $C$ y $R$ en paralelo.

| Símbolo | Equivalente eléctrico | Interpretación fisiológica |
|---|---|---|
| $P_a(t)$ | Voltaje de entrada | Presión arterial de entrada |
| $P_p(t)$ | Voltaje de salida | Presión sobre la distensibilidad arterial |
| $F_a(t)$ | Corriente de entrada | Flujo hacia aorta o arteria pulmonar |
| $F_Z(t)$ | Corriente por $Z$ | Flujo asociado a la impedancia proximal |
| $F_L(t)$ | Corriente por $L$ | Flujo a través de la inertancia arterial |
| $F_C(t)$ | Corriente por $C$ | Flujo almacenado por distensibilidad arterial |
| $F_R(t)$ | Corriente por $R$ | Flujo periférico |
| $Z$ | Resistencia | Impedancia característica proximal |
| $C$ | Capacitor | Distensibilidad arterial total |
| $R$ | Resistencia | Resistencia periférica |
| $L$ | Inductor | Inertancia arterial |

> El instructivo menciona “distensibilidad aérea total”; por la topología y el contexto cardiovascular se interpreta como **distensibilidad arterial total**.

### Parámetros de referencia

| Caso | $Z$ ($\mathrm{mmHg\,s/ml}$) | $C$ ($\mathrm{ml/mmHg}$) | $R$ ($\mathrm{mmHg\,s/ml}$) | $L$ ($\mathrm{mmHg\,s^2/ml}$) |
|---|---:|---:|---:|---:|
| Hipotenso | 0.020 | 0.250 | 0.600 | 0.005 |
| Normotenso (control) | 0.033 | 1.500 | 0.950 | 0.010 |
| Hipertenso | 0.050 | 2.500 | 1.400 | 0.020 |

[Volver al contenido](#contenido)

---

<a id="modelo"></a>
## Formulación matemática

### 1. Ecuaciones constitutivas

Definiendo $\Delta P(t)=P_a(t)-P_p(t)$:

$$
F_Z(t)=\frac{P_a(t)-P_p(t)}{Z},
$$

$$
L\frac{dF_L(t)}{dt}=P_a(t)-P_p(t),
$$

$$
F_C(t)=C\frac{dP_p(t)}{dt},
\qquad
F_R(t)=\frac{P_p(t)}{R}.
$$

Por conservación de flujo en la entrada y en el nodo de salida:

$$
F_a(t)=F_Z(t)+F_L(t)=F_C(t)+F_R(t).
$$

De ello se obtiene el modelo de estados:

$$
\boxed{
\begin{aligned}
\dot F_L(t)&=\frac{P_a(t)-P_p(t)}{L},\\
\dot P_p(t)&=\frac{1}{C}\left[\frac{P_a(t)-P_p(t)}{Z}+F_L(t)-\frac{P_p(t)}{R}\right],\\
y(t)&=P_p(t).
\end{aligned}}
$$

### 2. Derivación por divisor de impedancias

La impedancia equivalente entre $P_a$ y $P_p$ es el paralelo de $Z$ y $Ls$:

$$
Z_{\mathrm{serie}}(s)=Z\parallel Ls=\frac{ZLs}{Z+Ls}.
$$

La carga es el paralelo de $R$ y $1/(Cs)$:

$$
Z_{\mathrm{carga}}(s)=R\parallel\frac{1}{Cs}
=\frac{R}{RCs+1}.
$$

Con $P_p(s)$ como voltaje sobre la carga:

$$
\frac{P_p(s)}{P_a(s)}
=
\frac{Z_{\mathrm{carga}}(s)}
{Z_{\mathrm{serie}}(s)+Z_{\mathrm{carga}}(s)}.
$$

Al simplificar:

$$
\boxed{
G(s)=\frac{P_p(s)}{P_a(s)}
=
\frac{LRs+RZ}
{CLRZs^2+L(Z+R)s+RZ}.}
$$

El cero finito del modelo es:

$$
s_z=-\frac{Z}{L}.
$$

> **Caveat de topología:** esta función sólo corresponde a $Z\parallel L$ entre $P_a$ y $P_p$, seguido de $R\parallel C$ a referencia. Si se conecta $Z$ en serie con la rama $L$ o se mide otra presión, la función de transferencia cambia.

### 3. Funciones de transferencia por caso

| Caso | $G(s)=P_p(s)/P_a(s)$ |
|---|---|
| Hipotenso | $\displaystyle\frac{0.003s+0.012}{1.5\times10^{-5}s^2+0.0031s+0.012}$ |
| Normotenso | $\displaystyle\frac{0.0095s+0.03135}{4.7025\times10^{-4}s^2+0.00983s+0.03135}$ |
| Hipertenso | $\displaystyle\frac{0.028s+0.070}{0.0035s^2+0.029s+0.070}$ |

[Volver al contenido](#contenido)

---

<a id="analisis"></a>
## Análisis temporal y de estabilidad

### 1. Error en estado estacionario

Para una entrada escalón unitario y condiciones iniciales nulas:

$$
P_a(s)=\frac{1}{s}.
$$

Como:

$$
G(0)=\frac{RZ}{RZ}=1,
$$

entonces:

$$
\lim_{t\to\infty}P_p(t)=1,
\qquad
\boxed{e_{ss}=0.}
$$

Este resultado corresponde al seguimiento de un escalón en **lazo abierto** bajo el modelo ideal. No implica error nulo ante la señal aleatoria indicada ni ante entradas variantes en el tiempo.

### 2. Polos y estabilidad

Los polos satisfacen:

$$
CLRZs^2+L(Z+R)s+RZ=0.
$$

Para $Z,C,R,L>0$, los coeficientes del denominador son positivos y los tres casos son estables. Los polos calculados con los parámetros del instructivo son:

| Caso | Polos ($\mathrm{s^{-1}}$) | Clasificación |
|---|---|---|
| Hipotenso | $-3.9463,\;-202.7203$ | Estable, sobreamortiguado |
| Normotenso | $-3.9269,\;-16.9769$ | Estable, sobreamortiguado |
| Hipertenso | $-4.1429\pm j\,1.6843$ | Estable, subamortiguado |

Los ceros son $-4.0000$, $-3.3000$ y $-2.5000\,\mathrm{s^{-1}}$ para los casos hipotenso, normotenso e hipertenso, respectivamente.

[Volver al contenido](#contenido)

---

<a id="senales"></a>
## Señales de entrada y condiciones de simulación

La señal requerida en el instructivo es **Uniform Random Number**:

| Parámetro | Valor |
|---|---:|
| Mínimo | $-0.2\,\mathrm{V}$ |
| Máximo | $1\,\mathrm{V}$ |
| Semilla | 106 |
| Tiempo de muestreo | $0.5\,\mathrm{s}$ |
| Intervalo de simulación | $0\leq t\leq15\,\mathrm{s}$ |

> La unidad V corresponde a la analogía de simulación. Debe documentarse el factor de escala si se convierte numéricamente entre voltaje y presión en mmHg.

[Volver al contenido](#contenido)

---

<a id="implementacion"></a>
## Implementación en Python, Simulink y Multisim

| Plataforma | Implementación sugerida | Verificación |
|---|---|---|
| Python | `scipy.signal.TransferFunction`, `lsim` o integración del modelo de estados | Coeficientes, polos y respuesta $P_p(t)$ |
| Simulink | Bloque **Transfer Fcn** o subsistema de ecuaciones de estado | Misma señal aleatoria y mismo intervalo |
| Multisim | Circuito análogo escalado con $Z\parallel L$ y $R\parallel C$ | Topología y respuesta de salida escalada |

Archivos sugeridos:

- `python/windkessel_p2.py`
- `simulink/practica2_windkessel.slx`
- `multisim/practica2_windkessel.ms14`
- `resultados/respuesta_lazo_abierto.png`
- `resultados/respuesta_lazo_cerrado.png`

[Volver al contenido](#contenido)

---

<a id="pid"></a>
## Control en lazo cerrado y sintonización PID

El diagrama de la práctica considera al individuo normotenso como **control**, al paciente hipotenso o hipertenso como **caso**, al PID como **protocolo** y al paciente controlado como **tratamiento**.

Para retroalimentación negativa unitaria:

$$
K(s)=k_P+\frac{k_I}{s}+k_Ds,
$$

$$
T(s)=\frac{K(s)G(s)}{1+K(s)G(s)}.
$$

Procedimiento:

1. Construir el diagrama de bloques indicado en la Figura 5.8 del instructivo.
2. Usar el bloque **PID Controller** en Simulink.
3. Abrir **Tune** / PID Tuner y seleccionar una sintonización para cada caso.
4. Registrar $k_P$, $k_I$, $k_D$, fecha, versión de MATLAB y criterios usados.
5. Repetir la simulación con la misma semilla, intervalo y señal de entrada.

> `[PENDIENTE: registrar ganancias PID verificadas y criterios de sintonización.]`

[Volver al contenido](#contenido)

---

<a id="resultados"></a>
## Resultados y verificación cruzada

### Criterios de presentación académica

| Tipo de contenido | Identificación requerida |
|---|---|
| Resultado de referencia | Atribuir al instructivo o a la bibliografía |
| Resultado propio reproducido | Incluir software, versión, parámetros, semilla y fecha |
| Modificación del estudiante | Explicar qué cambió y su efecto esperado |
| Resultado pendiente | Marcar explícitamente como `[PENDIENTE]` |

### Tabla de resultados por completar

| Caso | Plataforma | Lazo | Figura/archivo | Observaciones |
|---|---|---|---|---|
| Hipotenso | Python / Simulink / Multisim | Abierto | `[PENDIENTE]` | `[PENDIENTE]` |
| Hipotenso | Python / Simulink / Multisim | Cerrado | `[PENDIENTE]` | `[PENDIENTE]` |
| Normotenso | Python / Simulink / Multisim | Abierto | `[PENDIENTE]` | `[PENDIENTE]` |
| Normotenso | Python / Simulink / Multisim | Cerrado | `[PENDIENTE]` | `[PENDIENTE]` |
| Hipertenso | Python / Simulink / Multisim | Abierto | `[PENDIENTE]` | `[PENDIENTE]` |
| Hipertenso | Python / Simulink / Multisim | Cerrado | `[PENDIENTE]` | `[PENDIENTE]` |

La verificación cruzada debe confirmar que las tres plataformas usan la misma topología, parámetros, semilla, periodo de muestreo y escala de unidades.

[Volver al contenido](#contenido)

---

<a id="flujo"></a>
## Flujo de trabajo

```mermaid
flowchart TD
    A[Verificar topología y parámetros] --> B[Derivar G(s)]
    B --> C[Calcular polos y error estacionario]
    C --> D[Implementar en Python]
    C --> E[Implementar en Simulink]
    C --> F[Implementar circuito escalado en Multisim]
    D --> G[Simular lazo abierto]
    E --> G
    F --> G
    G --> H[Sintonizar PID en Simulink]
    H --> I[Simular lazo cerrado]
    I --> J[Comparar resultados y documentar]
    J --> K[Diagrama biológico en BioRender y reporte]
