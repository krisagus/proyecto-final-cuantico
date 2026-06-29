# Proyecto QUBO-QAOA: Matching Bipartito 4x4 (Mercado Inmobiliario)

## Dataset
* **Nombre del dataset:** Asignación Óptima de Propiedades Inmobiliarias (Semi-real).
* **Fuente oficial o confiable:** Perfiles construidos a partir de tendencias de mercado inmobiliario en Baja California (SNIIV / SEDATU) y ofertas públicas de bienes raíces.
* **Institución responsable:** N/A (Datos semi-reales generados para simulación ética).
* **URL de la fuente:** Basado en métricas de https://sniiv.sedatu.gob.mx/
* **URL raw del CSV usado en data/:** https://raw.githubusercontent.com/krisagus/proyecto-final-cuantico/refs/heads/main/data/dataset_real_4x4.csv
* **Licencia o condiciones de uso:** Open Data / Uso Educativo.
* **Fecha de consulta:** Junio 2026.
* **Dominio del problema:** Asignación de bienes raíces.

## Modelado
* **Conjunto A:** 4 perfiles genéricos de compradores con distintas necesidades (Familia, Ejecutivo, Retirados, Inversionista).
* **Criterio para elegir exactamente 4 elementos de A:** Se seleccionaron los 4 arquetipos de demanda más comunes en la región fronteriza y costera.
* **Conjunto B:** 4 propiedades disponibles con características contrastantes (Casa grande, Loft, Casa de retiro, Propiedad para remodelar).
* **Criterio para elegir exactamente 4 elementos de B:** Representan el inventario estancado que una agencia necesita colocar rápidamente.
* **Definición de x_ij = 1:** El comprador $i$ adquiere la propiedad $j$.
* **Interpretación de x_ij = 0:** El comprador $i$ NO adquiere la propiedad $j$.

## Matriz de score
* **Columnas usadas:** `a_nombre`, `b_nombre`, `score`.
* **Fórmula exacta de S_ij:** El score (del 1 al 10) se calculó ponderando tres factores: Ajuste de Presupuesto (40%), Necesidad de Espacio (30%), y Preferencia Geográfica/Estilo de vida (30%).
* **Normalización aplicada:** Escala lineal simple de 1 a 10.
* **Matriz S 4x4:** Evaluada en el código. Los scores más altos están en la diagonal lógica del problema.

## Restricciones
* **Restricción por filas:** Cada comprador solo puede adquirir exactamente UNA propiedad.
* **Restricción por columnas:** Cada propiedad solo puede ser vendida a exactamente UN comprador.
* **Otras restricciones, si existen:** Ninguna.
* **Justificación de por qué el problema es matching bipartito:** Existen dos conjuntos disjuntos (Compradores y Propiedades) y buscamos emparejarlos 1 a 1 maximizando la satisfacción global (el score).
* **Justificación de por qué es razonable modelarlo como QUBO:** Las restricciones de exclusividad mutua se pueden traducir perfectamente a penalizaciones cuadráticas, forzando a que las filas y columnas sumen 1.

## Resultados
* **Solución clásica exacta:** Score total de 37.5 (Energía QUBO de -37.5).
* **Resultado QAOA local:** La mejor muestra del algoritmo QAOA local alcanzó exactamente el óptimo matemático, registrando una energía de -37.5 y un score de 37.5.
* **Comparación clásico vs QAOA local:** El algoritmo QAOA logró resolver el problema de asignación inmobiliaria con éxito rotundo al encontrar el óptimo global. Aunque la probabilidad de medir el estado óptimo exacto es del 0.10% (0.001014), este resultado es un éxito teórico rotundo: al modelar una matriz de 4x4, el espacio de búsqueda abarca 16 qubits ($2^{16} = 65,536$ estados posibles). Una búsqueda puramente aleatoria tendría una probabilidad de apenas $\approx 0.0015\%$; por lo tanto, el circuito QAOA con solo $p=1$ logró amplificar de forma masiva la probabilidad de éxito. La probabilidad acumulada de obtener cualquier solución válida (factible) fue del 2.25% (0.022534).
* **Si se usó hardware real o pipeline híbrido:** N/A (Se usó simulación local ligera).

## Interpretación mínima del resultado

* **¿Cuál fue la mejor asignación encontrada?**
  La mejor relación de correspondencia exacta (uno a uno) vincula a cada elemento del Grupo A con el Grupo B sin conflictos de solapamiento. Siguiendo los índices del problema de bienes raíces, la configuración óptima empareja directamente a los elementos correspondientes: A1-B1, A2-B2, A3-B3 y A4-B4.

* **¿Cuál fue su score en el dominio?**
  El score máximo en el dominio de la función de aptitud fue de 37.5 (equivalente a una energía QUBO exacta de -37.5).

* **¿La asignación cumple todas las restricciones?**
  Sí. El modelo arrojó un estado de factibilidad igual a `True`. Esto significa que las penalizaciones cuadráticas añadidas al QUBO funcionaron correctamente, forzando al sistema a respetar la exclusividad mutua (cada comprador recibe exactamente una propiedad y ninguna propiedad es asignada dos veces).

* **¿QAOA local observó el óptimo clásico?**
  Sí. Al analizar las muestras individuales recolectadas por el algoritmo, la mejor muestra del QAOA local registró una energía de -37.5 y un score de 37.5, igualando con precisión absoluta la solución óptima del solucionador clásico exacto.

* **¿Qué tan frecuente fue observar soluciones factibles?**
  La probabilidad de medir una solución que fuera matemáticamente válida (factible) fue del 2.25% (0.022534), mientras que la probabilidad específica de observar el óptimo global absoluto fue del 0.10% (0.001014). Aunque estos porcentajes parecen bajos en términos absolutos, representan una amplificación masiva frente a una búsqueda aleatoria pura en un espacio de Hilbert de 16 qubits ($2^{16} = 65,536$ combinaciones posibles).

* **¿Qué limitaciones tiene el modelo?**
  El modelo opera bajo una profundidad de circuito mínima ($p=1$), lo que limita la capacidad del entrelazamiento cuántico para distribuir mejor las probabilidades hacia el estado base. Asimismo, asume que la matriz de costos y las penalizaciones por violación de restricciones son estáticas y conocidas a priori, ignorando fluctuaciones dinámicas del entorno real.

* **¿Qué cambiaría si el dataset creciera?**
  Si el dataset creciera (por ejemplo, a una matriz de asignación de 10x10), el número de qubits requeridos aumentaría cuadráticamente ($N^2 = 100$ qubits). El espacio de soluciones explotaría a $2^{100}$ estados, volviendo la simulación clásica exacta matemáticamente inviable. En ese escenario, sería obligatorio migrar a hardware cuántico real, incrementar la profundidad del circuito ($p > 1$) y aplicar técnicas robustas de optimización de parámetros y mitigación de errores.

* **¿Qué riesgos éticos existen y cómo se mitigaron?**
  *Riesgos:* La automatización de asignaciones en sectores sensibles como la vivienda puede perpetuar sesgos socioeconómicos si los scores de afinidad discriminan por capacidad adquisitiva o si el algoritmo desplaza comunidades vulnerables. 
  *Mitigación:* El modelo se acota estrictamente a un marco académico y experimental. Los datos utilizados son sintéticos y formulados con fines ilustrativos. 

* **Si se usó hardware real, ¿cómo compara contra QAOA local?**
  N/A. Para este proyecto se utilizó exclusivamente simulación local basada en vectores de estado (`Statevector`).

* **Si se usó reparación clásica, ¿qué parte del resultado corresponde al postprocesamiento híbrido?**
  N/A. El flujo del algoritmo converge directamente de la optimización del circuito variacional sin requerir un algoritmo complementario de reparación clásica de bits.

> **Nota de Descargo de Responsabilidad (Disclaimer):** La salida de este algoritmo QAOA es el resultado de un ejercicio académico de computación cuántica y no debe presentarse, bajo ninguna circunstancia, como una recomendación de vivienda en el mundo real.
> 

## Ética y limitaciones
* **Riesgos éticos:** Riesgo de sesgo si se usaran historiales crediticios reales o datos personales identificables, violando la privacidad de clientes.
* **Medidas de mitigación:** Se utilizaron "arquetipos" de compradores genéricos en lugar de individuos reales. No hay datos financieros sensibles.
* **Limitaciones del modelo:** En la vida real, un inversionista podría querer comprar múltiples propiedades, o una propiedad podría no venderse a nadie si los scores son muy bajos.

## Ejecución
* **Instrucciones para abrir el archivo .ipynb en Google Colab:** Subir el notebook directamente a Colab o usar el enlace directo de integración de GitHub en Colab.
* **Instrucciones para ejecutar todas las celdas sin errores:** Presionar `Runtime -> Run all`. El notebook está configurado para leer automáticamente el archivo de la carpeta `/data` y evitar la instancia molecular de ejemplo.
