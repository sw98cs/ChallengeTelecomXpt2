# ChallengeTelecomXpt2
Challenge Alura Latam - Datos Churn

# Detección de Fuga de Clientes (Churn Prediction)

## 📋 Misión del Proyecto

Desarrollar modelos predictivos capaces de prever qué clientes tienen mayor probabilidad de cancelar sus servicios. La empresa busca anticiparse al problema de la cancelación, y este proyecto construye un pipeline robusto para esta etapa inicial de modelado.

## 🧠 Objetivos del Desafío

- Preparar los datos para el modelado (tratamiento, codificación, normalización).
- Realizar análisis de correlación y selección de variables.
- Entrenar dos o más modelos de clasificación.
- Evaluar el rendimiento de los modelos con métricas.
- Interpretar los resultados, incluyendo la importancia de las variables.
- Crear una conclusión estratégica señalando los principales factores que influyen en la cancelación.

## 🚀 Extracción y Preprocesamiento de Datos

El proyecto comienza con la carga de los datos desde el archivo `datos_tratados.csv`.

### Pasos de Preprocesamiento:

1.  **Remoción de Columnas Irrelevantes**: La columna `Id` fue eliminada al considerarse que no aporta valor predictivo al modelo.
2.  **Estandarización de Valores Categóricos**: 
    *   La columna `'tiene +60'` se transformó de valores numéricos (0, 1) a categóricos ('No', 'Si') para coherencia con otras variables binarias.
    *   Se estandarizaron las columnas de servicio con valores como `'Sin servicio de internet'` o `'Sin servicio de telefono'` a simplemente `'No'` para simplificar la codificación posterior.
3.  **Verificación de Valores Nulos**: Se realizó una verificación exhaustiva para asegurar que no existieran valores nulos en el conjunto de datos tras el preprocesamiento.

## 📊 Análisis Exploratorio de Datos (EDA)

### 1. Correlación de Variables Numéricas

Se calculó y visualizó una matriz de correlación para las variables numéricas (`Tiempo_contrato`, `Valor_mensual`, `Total_cobrado`, `Cuentas_diarias`, y `Churn` codificado a 0/1).

*   Se observó una correlación negativa leve entre `Tiempo_contrato` y `Churn`, sugiriendo que un mayor tiempo de contrato se asocia con una menor probabilidad de cancelación.
*   Las columnas `Cuentas_diarias` y `Total_cobrado` fueron eliminadas debido a su alta correlación con `Valor_mensual` y `Tiempo_contrato`, respectivamente, para evitar multicolinealidad y simplificar el modelo.

### 2. Análisis de Churn por Categoría (Variables Categóricas)

Se utilizó el test Chi-cuadrado para evaluar la significancia estadística de las variables categóricas con respecto a `Churn`.

*   **Variables Significativas (p < 0.05)**: `tipo_contrato`, `seguridad_online`, `soporte_tecnico`, `tiene +60`, `Tiene_dependientes`, `facturacion_digital`, `Tiene_pareja`, `metodo_pago`, `backup_online`, `proteccion_dispositivos`, `streaming_tv`, `streaming_peliculas`, `Tipo_internet`, `multiples_lineas`.
*   **Variables Eliminadas (p >= 0.05)**: `Genero` y `servicio_telefono` se descartaron por no mostrar una correlación significativa con la variable `Churn`.

### 3. División de Datos y Codificación

*   Los datos se dividieron en conjuntos de entrenamiento y prueba (`X_train`, `X_test`, `y_train`, `y_test`) utilizando `train_test_split` con una estratificación por la variable `Churn` para mantener la proporción de clases.
*   Se aplicó **One-Hot Encoding** a las variables categóricas restantes para convertirlas en un formato numérico adecuado para el modelado, utilizando `pd.get_dummies` y asegurando que `X_train` y `X_test` tuvieran las mismas columnas.

### 4. Verificación de Desbalance de Clases

Se confirmó el desbalance en la variable objetivo `Churn` en el conjunto de entrenamiento, con `No` representando aproximadamente el 73.4% y `Si` el 26.6%.

## 🤖 Entrenamiento de Modelos

Se entrenaron y evaluaron tres modelos de clasificación para predecir la fuga de clientes.

### Modelo 1: Regresión Logística (Sin Balancear)

*   **Resultados**: El modelo mostró una **precisión** de 0.84 para la clase 'No' y 0.65 para la clase 'Si'. El **recall** para la clase 'Si' (Churn) fue del 52%, lo que indica que solo pudo identificar correctamente un poco más de la mitad de los clientes que realmente cancelaron.
*   **Conclusión**: Aunque la precisión general es aceptable, el bajo recall para la clase 'Si' sugiere un amplio margen de mejora, ya que el modelo no es efectivo detectando la mayoría de los casos de churn.

### Modelo 2: Random Forest (Sin Balancear)

*   **Resultados**: Similar a la Regresión Logística, este modelo obtuvo una **precisión** de 0.84 para 'No' y 0.64 para 'Si', con un **recall** del 51% para la clase 'Si'.
*   **Conclusión**: El rendimiento fue similar o ligeramente inferior al modelo de Regresión Logística en la detección de churn, indicando que el desbalance de clases sigue siendo un problema significativo.

### Modelo 3: Regresión Logística (Con `class_weight='balanced'`)

*   **Resultados**: Al usar `class_weight='balanced'`, la **precisión** para la clase 'No' disminuyó a 0.91, pero el **recall** para la clase 'Si' **mejoró significativamente a 79%**. Esto significa que el modelo ahora es mucho mejor identificando a los clientes que van a cancelar, aunque puede clasificar erróneamente más clientes que no cancelarán.
*   **Conclusión**: Este modelo es preferible para el objetivo de negocio de predecir el churn, ya que maximiza la detección de clientes que cancelarán, lo que permite a la empresa tomar medidas preventivas. El compromiso es aceptar una precisión ligeramente menor para la clase 'No' a cambio de una cobertura mucho mayor de la clase 'Si'.

## 💡 Conclusión Estratégica

El modelo de **Regresión Logística con ajuste de `class_weight='balanced'`** es el más adecuado para predecir la fuga de clientes en este escenario, debido a su capacidad mejorada para identificar a los clientes que realmente cancelarán (alto recall para la clase 'Si').

Los principales factores que influyen en la cancelación de servicios, según el análisis de significancia y el rendimiento de los modelos, incluyen:

*   **Tipo de Contrato**: Los contratos mensuales suelen tener mayor riesgo de churn.
*   **Servicios Adicionales**: La presencia o ausencia de servicios como `seguridad_online`, `soporte_tecnico`, `backup_online`, `proteccion_dispositivos`, `streaming_tv`, y `streaming_peliculas` son determinantes.
*   **Demografía del Cliente**: `tiene +60`, `Tiene_dependientes` y `Tiene_pareja` influyen en la probabilidad de churn.
*   **Facturación Digital**: Clientes con facturación digital muestran patrones específicos de churn.
*   **Método de Pago**: Ciertos métodos de pago pueden estar asociados con una mayor o menor propensión a cancelar.
*   **Tipo de Internet**: El servicio de Fibra Óptica parece tener un impacto en el churn.

La empresa debería enfocar sus esfuerzos de retención en clientes con **contratos mensuales**, y analizar la oferta y valor percibido de los **servicios adicionales**, así como las **opciones de pago** y la **segmentación demográfica** para anticipar y mitigar el churn.

## ➡️ Próximos Pasos

1.  **Optimización de Hiperparámetros**: Realizar una búsqueda de hiperparámetros para la Regresión Logística y Random Forest con ajuste de `class_weight` para mejorar aún más el rendimiento.
2.  **Otros Modelos**: Explorar modelos como Gradient Boosting (XGBoost, LightGBM) que manejan bien el desbalance de clases.
3.  **Análisis de Importancia de Características**: Obtener la importancia de las características de los modelos para una interpretación más profunda de los factores de churn.
4.  **Despliegue**: Una vez satisfechos con el rendimiento, considerar el despliegue del mejor modelo en un entorno de producción para predicciones en tiempo real.
