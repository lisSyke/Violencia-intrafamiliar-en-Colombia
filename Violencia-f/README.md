# Análisis de la violencia intrafamiliar en Bogotá

Análisis exploratorio de los casos de violencia intrafamiliar reportados en Bogotá a partir de datos abiertos. El objetivo es identificar **patrones temporales, geográficos y demográficos** que ayuden a comprender cómo se distribuye este fenómeno en la ciudad.

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/drive/1UpZF3dZPlb714MEqsH40LFmsOJarVTeC)

## Tabla de contenido

- [Preguntas que responde el análisis](#preguntas-que-responde-el-análisis)
- [Datos](#datos)
- [Requisitos](#requisitos)
- [Uso](#uso)
- [Estructura del notebook](#estructura-del-notebook)
- [Metodología](#metodología)
- [Hallazgos principales](#hallazgos-principales)
- [Limitaciones y consideraciones](#limitaciones-y-consideraciones)
- [Posibles mejoras](#posibles-mejoras)

## Preguntas que responde el análisis

1. ¿Cómo ha evolucionado el número de casos registrados entre 2013 y 2025?
2. ¿Qué localidades concentran más casos, en términos absolutos y ajustados por población?
3. ¿Existe relación entre el sexo y el grupo de edad de las víctimas?

## Datos

El proyecto usa dos conjuntos de datos abiertos de Bogotá, ambos en formato CSV separado por punto y coma (`;`):

| Archivo | Descripción | Tamaño | Encoding |
|---|---|---|---|
| `num_casos_violenciaintra.csv` | Casos registrados de violencia intrafamiliar (2013-2025) | 465.514 registros, 23 columnas | `latin-1` |
| `demografia_bogota.csv` | Proyecciones de población por localidad, sexo y edad | 131.502 registros, 10 columnas | `utf-8` |

**Variables destacadas del dataset de casos:** año, grupo de edad, sexo, localidad, UPZ, tipo de aseguramiento, entidad administradora, relación con el agresor, orientación sexual, gestante, estrato, país de procedencia, ciclo vital, estado civil, nivel educativo, consumo de sustancias (agresor y víctima) y tipo/lugar de ocurrencia de la violencia (emocional, física, sexual, económica, negligencia, abandono).

> Se usan **proyecciones de población para 2025**, ya que no se contaba con datos oficiales de ese año.

## Requisitos

- Python 3.x
- Librerías:
  - `pandas`
  - `numpy`
  - `matplotlib`
  - `seaborn`

Para ejecutarlo localmente:

```bash
pip install pandas numpy matplotlib seaborn jupyter
```

## Uso

### En Google Colab

1. Abre el notebook con el botón **Open in Colab** de arriba.
2. Sube los archivos `num_casos_violenciaintra.csv` y `demografia_bogota.csv` al entorno de ejecución.
3. Ejecuta todas las celdas en orden (*Entorno de ejecución → Ejecutar todo*).

### En local

1. Clona el repositorio y coloca los dos CSV en la misma carpeta que el notebook.
2. Reemplaza o elimina las líneas `from google.colab import files`, que solo funcionan en Colab.
3. Abre `Violencia_intrafamiliar.ipynb` con Jupyter y ejecuta las celdas.

## Estructura del notebook

1. **Introducción:** contexto y objetivo.
2. **Carga y exploración de datos:** lectura de los CSV (`sep=';'`), `info()`, `describe()`.
3. **Evolución temporal:** gráfico de barras de casos por año.
4. **Distribución geográfica:**
   - Casos por localidad (acumulado).
   - Top 10 de localidades por año.
   - Foco en 2021-2025.
5. **Análisis ajustado por población:** unión con el dataset demográfico, cálculo de tasas por localidad en 2025 y clasificación de localidades.
6. **Perfil de la víctima:** mapas de calor sexo vs. grupo etario (todos los años y 2024).

## Metodología

- **Limpieza y carga:** lectura con el encoding y separador correctos de cada archivo.
- **Agregaciones:** conteo de casos con `groupby` por año y localidad.
- **Tasas:** se cruzan los casos de 2025 con la población proyectada de cada localidad (`pd.merge`) para obtener una medida comparable entre localidades de distinto tamaño.
- **Clasificación de localidades** según su posición en casos y en tasa (top 10 de cada una):
  - **Alto en ambos:** crítico en casos y en tasa.
  - **Alto en tasa:** pocos casos, pero alta incidencia relativa (posible distorsión por población pequeña).
  - **Alto en casos:** muchos casos, pero tasa diluida por una población grande.
  - **Media:** el resto.
- **Visualización:** gráficos de barras y mapas de calor con `matplotlib` y `seaborn`.

## Hallazgos principales

- **Volumen de datos:** 465.514 registros; el 3,8 % de las víctimas eran gestantes, el 5,6 % de los agresores habían consumido sustancias y el 0,7 % de las víctimas también.
- **Tendencia temporal:** se observa una caída notable hacia 2020 (probablemente asociada a las barreras de denuncia durante la pandemia de COVID-19) y un aumento desde 2021, posiblemente por mayor visibilización y acceso a canales de atención.
- **Geografía:** en términos absolutos, Kennedy, Suba, Ciudad Bolívar y Bosa lideran en número de casos.
- **Tasas por localidad (2025):** Sumapaz tiene la tasa más alta, pero con población muy pequeña. Ciudad Bolívar, Usme, Bosa y Kennedy aparecen como localidades con **alto número de casos y alta tasa**, lo que sugiere una concentración mayor del fenómeno.
- **Perfil de la víctima:** los casos se concentran en la infancia y la adolescencia, con más mujeres que hombres, y aumentan de nuevo en adultos jóvenes (18-26 y 27-44 años). Tienden a disminuir desde los 44 años.

## Limitaciones y consideraciones

- **Subregistro:** los datos reflejan casos *reportados*, no la totalidad de la violencia real. Una caída en los registros no implica necesariamente una mejora.
- **Sesgos de denuncia:** en menores de edad, las denuncias suelen venir de terceros (familiares, colegios, instituciones), lo que aumenta su visibilidad. En hombres y adultos mayores puede haber menor denuncia por estigma o falta de reconocimiento como víctimas.
- **Localidades pequeñas:** tasas altas en poblaciones reducidas (p. ej., Sumapaz) deben interpretarse con cautela.
- **Calidad de los datos:** se observan posibles irregularidades en algunos rangos etarios (p. ej., 30 a 34 años, con un número atípicamente bajo de casos).
- **Población de referencia:** las tasas usan proyecciones de 2025, no cifras oficiales.
- **Unidad de la tasa:** en la versión actual del notebook, la expresión `* 100.000` se interpreta en Python como `100.0` (el punto es separador decimal), por lo que la tasa resultante es **por cada 100 habitantes** y no por cada 100.000. Para obtener la tasa por 100.000 habitantes, debe escribirse `* 100000`. El ranking de localidades no cambia, solo la escala.

## Posibles mejoras

- Corregir la escala de la tasa a 100.000 habitantes.
- Calcular tasas para todos los años con la población proyectada de cada año.
- Analizar por tipo de violencia, relación con el agresor y nivel educativo.
- Explorar el consumo de sustancias y otras variables socioeconómicas (estrato, aseguramiento).
- Agregar mapas coropléticos por localidad y UPZ.

## Aviso

Este proyecto trata un tema sensible. Si tú o alguien que conoces vive una situación de violencia intrafamiliar en Colombia, puedes comunicarte con la **Línea 155** (orientación para mujeres víctimas de violencia) o con la **Línea 141** del ICBF (protección de niños, niñas y adolescentes).
