# MCD-LABIII







##Informe Final - Laboratorio III
##2025



Integrantes
		
		
Martín Salamero
Ana Laura Ruibal Conti


CONTENIDOS


1. Qué probamos y los resultados	2
2. Qué no funcionó	3
3. Modelo final seleccionado (AutoGluon)	4
○ Procesamiento de los datos	5
○ Ingeniería de Características	5
○ Configuración del Modelo	5
4. Conclusión	6
5. Repositorio GitHub	6


Qué probamos y los resultados
Durante la cursada se evaluaron múltiples enfoques para resolver el problema propuesto en Kaggle. Las pruebas incluyeron:
Modelos base como AutoArima y SARIMAX
Modelos más avanzados como LightGBM 
Modelos automáticos como AutoGluon 



Qué no funcionó
Los modelos clásicos de series temporales (ARIMA y SARIMAX) no lograron un buen rendimiento en esta competencia. Aunque el tiempo de entrenamiento fue algo positivo, sus resultados fueron inferiores al promedio. Asimismo en un enfoque con LightGBM no logramos buenos resultados, siendo cada una de las pruebas muy costosas en lo relativo a tiempo de ejecución. Es por ello que decidimos abordar AutoGluon como una posible solución.
AutoGluon fue el modelo con mejor desempeño general acompañado de corto tiempo de entrenamiento y baja demanda computacional. Para mejorar su desempeño hicimos algunas intervenciones. Debajo se describen aquellas  no aportaron mejoras sustanciales: 
Imputación de Valores Faltantes (NaNs)
Para completar las series temporales se aplicaron las siguientes estrategias de imputación manual:
Promedio por producto (product_id)
Ceros antes del primer dato y promedio después
Interpolación lineal
Ceros antes del primer dato e interpolación después
La imputación del promedio fue la que mostró una leve mejora en el rendimiento. Sin embargo, el preprocesamiento manual no aportó beneficios significativos dentro del flujo automático de AutoGluon, que demostró ser más eficiente gestionando internamente los valores faltantes.
Por ello, se evaluaron también los métodos incorporados en la función fill_missing_values() de TimeSeriesDataFrame. Esta función ofrece cinco opciones: "auto", "ffill", "bfill", "constant" e "interpolate". La configuración por defecto ("auto") resultó ser la más efectiva. Las demás opciones no generaron mejoras notables en el desempeño del modelo.
Ingeniería de Características y Validación
Se intentó usar como variable objetivo el valor de la serie dos pasos en el futuro (Tₙ₊₂). También se probó una validación manual: se entrenó el modelo con datos desde enero de 2017 hasta agosto de 2019, se evaluó con datos de septiembre y octubre de 2019 y luego se predijo. Los puntajes obtenidos fueron 0.310 y 0.307, sin mejoras relevantes respecto al enfoque automático.
Ajustes de parámetros del modelo
El bajo costo computacional de este modelo nos permitió realizar numerosas pruebas ajustando métricas de evaluación, ventanas y demás.
quantile_levels: por defecto pronostica la  "media" —es decir, los valores esperados de la serie temporal en el futuro— como también cuantiles de la distribución del pronóstico. Se utilizó el cuartil 0.5 (mediana) en lugar de la media para mejorar la robustez de las predicciones.


Estos ajustes, no mejoraron el rendimiento inicial dando scores entre 0.26 y 0.25 




Modelo final seleccionado (AutoGluon)
De todos los modelos evaluados, AutoGluon mostró el mejor rendimiento general, con tiempos cortos de entrenamiento y baja demanda computacional, por lo que fue seleccionado como modelo final para la competencia

Para correr el modelo se realizaron las siguientes configuraciones
Procesamiento de los datos
Se realizó con las funciones de TimeSeriesDataFrame/fill_missing_values(auto) 
Ingeniería de Características 
Lags, valores previos de una determinada serie de tiempo. Se consideraron valores desde 1 a 6 meses.
Delta lags, diferencia entre los valores de la variable 'lag’. Tener en cuenta que los extremos no agregan valor por contener todas sus filas nulas.
Medias móviles Promedio de los últimos períodos consecutivos de la serie temporal. Se consideraron valores de 1 a 3. 
Se agregó una encuesta de ventas mayorista del Indec para suministrar al modelo tendencias generales del mercado.
https://www.indec.gob.ar/indec/web/Nivel4-Tema-3-1-34https://www.indec.gob.ar/indec/web/Nivel4-Tema-3-1-34

Configuración del Modelo

hyperparameters: Se evaluaron tanto los valores por defecto como configuraciones personalizadas.


known_covariates_names: Se incorporaron variables adicionales proporcionadas en el dataset y otras creadas durante el feature engineering.


Conclusión
El modelo final enviado a Kaggle fue Autogluon. Fue elegido porque:
Obtuvo el mejor rendimiento de todos los modelos que probamos.


Tuvo bajo tiempo de entrenamiento comparado con otros modelos complejos.


Se comportó bien con datos faltantes y las series temporales incompletas evitando largos tiempos de preparación del dataset. 
El enfoque basado en AutoGluon con ingeniería de características dirigida (lags + macro INDEC) redujo el error de 0.305 a 0.242 SMAPE (−20.8% relativo), con baja complejidad operativa (entrenamiento ≈ 18 minutos). La automatización interna de imputaciones evitó preprocesamiento manual costoso y permitió iteraciones rápidas. El modelo final que obtuvo mejor métrica en en el Autogluon y por lo tanto el utilizado para la preducción fue WeightedEnsemble, el cual ofrece un balance adecuado entre precisión y tiempo y sienta una base extensible para incorporar nuevas covariantes y jerarquías.

Repositorio GitHub
Todo el trabajo realizado se encuentra documentado en el siguiente repositorio:
🔗https://github.com/msalamero/MCD-LABIII/tree/main








