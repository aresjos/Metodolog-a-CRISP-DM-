# Predicción del Riesgo de Diabetes con la Metodología CRISP-DM

**Materia:** Metodología de la Investigación
**Autores:** Fabiola López Velázquez · Jorge Luis Ortiz Libreros · Joshua Iván Olivares Blanco
**Herramientas:** R · `rpart` · `rpart.plot` · Árbol de decisión (CART)

---

## Descripción

**CRISP-DM** es una forma estandarizada de estructurar proyectos de análisis y minería de datos. En este proyecto se aplica al problema de predecir el **riesgo de diabetes** de pacientes, clasificándolo en tres niveles: `Low`, `Moderate` y `High`.

## 1. Comprensión del Negocio (*Business Understanding*)

El objetivo es analizar un conjunto de datos de pacientes para descubrir las variables asociadas al riesgo de diabetes y construir un modelo que clasifique a nuevos pacientes en las categorías `Low`, `Moderate` y `High`.

El modelo busca ser una **herramienta de apoyo** para que el personal de salud priorice el seguimiento de los pacientes que lo requieran. **No sustituye un diagnóstico médico formal.**

## 2. Comprensión de los Datos (*Data Understanding*)

Se utilizó la base de datos de diabetes obtenida de [Kaggle](https://www.kaggle.com/datasets/mansiaggarwal88/diabetes-risk-prediction), con **15,000 registros** y **19 variables** por paciente. Las variables abarcan datos demográficos, clínicos/biométricos, hábitos de estilo de vida y antecedentes familiares.

- No existen registros duplicados.
- Se identificaron valores faltantes en tres variables categóricas:

| Variable | Valores faltantes |
|---|---|
| `smoking_status` | 472 |
| `income_bracket` | 463 |
| `alcohol_consumption` | 3,788 |

## 3. Preparación de los Datos (*Data Preparation*)

- La variable `patient_id` fue descartada por ser un identificador único sin capacidad predictiva.
- Los valores faltantes se reemplazaron por la categoría `"Unknown"`.
- Las variables categóricas y la variable objetivo se transformaron a tipo `factor`, ya que el algoritmo de árbol de decisión lo requiere.
- La base se dividió mediante **muestreo estratificado**: **70 % entrenamiento** y **30 % prueba**.

## 4. Modelado (*Modeling*)

Se implementó un árbol de decisión con el algoritmo **CART** mediante la función `rpart`, con los siguientes parámetros de control:

| Parámetro | Valor |
|---|---|
| `cp` | 0.001 |
| `minsplit` | 20 |
| `maxdepth` | 4 |

Para optimizar el rendimiento y evitar el sobreajuste, el árbol inicial fue **podado**, eligiendo el valor de complejidad (`cp`) con el menor error de validación cruzada.

**Distribución de la variable objetivo:** la clase `Low` es la predominante (60 %), seguida de `Moderate` (25 %) y `High` (15 %). Esta distribución refleja un desbalance de clases natural en la población.

![Distribución del riesgo de diabetes](images/distribucion_riesgo.png)

**Árbol de decisión podado:** el nodo raíz confirma que la glucosa en ayunas (`fasting_blood_sugar >= 175`) es el primer punto de corte crítico. Las ramificaciones secundarias evalúan la hemoglobina glucosilada (`hba1c_level`) y la edad para clasificar el nivel de riesgo final.

![Árbol de decisión podado](images/arbol_podado.png)

## 5. Evaluación (*Evaluation*)

El modelo se analizó mediante una **matriz de confusión** sobre los datos de prueba, obteniendo métricas de exactitud global y métricas por categoría como sensibilidad y especificidad.

Hallazgos principales:

- La glucosa en ayunas (`fasting_blood_sugar`) es el factor más determinante, especialmente con valores altos (>= 175), que concentran la mayoría de los casos de riesgo **Alto**.
- Para distinguir entre riesgo **Moderado** y **Bajo**, el nivel de hemoglobina glucosilada (`hba1c_level`) fue el segundo criterio más relevante.
- La edad y la circunferencia de cintura ayudaron a afinar la clasificación dentro de los grupos de riesgo Bajo y Moderado.

Los parámetros biométricos de glucosa en ayunas y hemoglobina glucosilada concentran más del 80 % de la capacidad predictiva del modelo, desplazando a las variables demográficas o de hábitos.

![Importancia de las variables](images/importancia_variables.png)

## 6. Despliegue (*Deployment*)

El despliegue se materializa en la generación del informe final y la visualización de los resultados mediante gráficas del árbol podado (`rpart.plot`). Los resultados pueden implementarse en el futuro para orientar decisiones clínicas tempranas de manera accesible.

**Plan a futuro:** comparar este modelo con otros algoritmos para intentar mejorar la exactitud, manteniendo la interpretabilidad del modelo.

## Conclusión

El desarrollo de este trabajo demuestra cómo la metodología CRISP-DM permite estructurar un proyecto desde la comprensión del problema clínico hasta la evaluación e interpretación del modelo.

A través del árbol de decisión fue posible construir un modelo preciso y altamente interpretable, confirmando que la **glucosa en ayunas** (`fasting_blood_sugar`) y la **hemoglobina glucosilada** (`hba1c_level`) son los factores metabólicos con mayor peso predictivo para determinar el riesgo de diabetes.

Con CRISP-DM, los resultados no se quedan en un análisis aislado, sino que sirven como una herramienta real de apoyo en el triaje de pacientes para el personal de salud.

## Referencias

- Aggarwal, M. (s. f.). *Diabetes risk prediction*. Kaggle. https://www.kaggle.com/datasets/mansiaggarwal88/diabetes-risk-prediction
- Fayyad, U. M., Piatetsky-Shapiro, G., & Smyth, P. (1996). From data mining to knowledge discovery in databases. *AI Magazine, 17*(3), 37–54. https://doi.org/10.1609/aimag.v17i3.1230
- Fayyad, U. M., Piatetsky-Shapiro, G., & Smyth, P. (1996). The KDD process for extracting useful knowledge from volumes of data. *Communications of the ACM, 39*(11), 27–34. https://doi.org/10.1145/240455.240464
- Han, J., Kamber, M., & Pei, J. (2012). *Data mining: Concepts and techniques* (3rd ed.). Morgan Kaufmann.
- Shearer, C. (2000). The CRISP-DM model: The new blueprint for data mining. *Journal of Data Warehousing, 5*(4), 13–22.
- Wirth, R., & Hipp, J. (2000). CRISP-DM: Towards a standard process model for data mining. En *Proceedings of the 4th International Conference on the Practical Applications of Knowledge Discovery and Data Mining* (pp. 29–39).
