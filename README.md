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

## Ética y limitaciones
* **Riesgos éticos:** Riesgo de sesgo si se usaran historiales crediticios reales o datos personales identificables, violando la privacidad de clientes.
* **Medidas de mitigación:** Se utilizaron "arquetipos" de compradores genéricos en lugar de individuos reales. No hay datos financieros sensibles.
* **Limitaciones del modelo:** En la vida real, un inversionista podría querer comprar múltiples propiedades, o una propiedad podría no venderse a nadie si los scores son muy bajos.

## Ejecución
* **Instrucciones para abrir el archivo .ipynb en Google Colab:** Subir el notebook directamente a Colab o usar el enlace directo de integración de GitHub en Colab.
* **Instrucciones para ejecutar todas las celdas sin errores:** Presionar `Runtime -> Run all`. El notebook está configurado para leer automáticamente el archivo de la carpeta `/data` y evitar la instancia molecular de ejemplo.
