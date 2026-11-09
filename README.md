# Reanálisis de GSE260761: respuesta de macrófagos a cuatro flavonoides

Proyecto final de Bioinformática 2026 · Bioingeniería, UNSTA

Autora: Candela Ahumada Horgan · Docente: Lic. Esp. Ignacio García Labari

## De qué se trata

En este trabajo reanalizo los datos de RNA-seq del estudio:

> Zeng Q., Du S., Yuan R., Zeng Y., Li X., Wu Y.-W., Chen K., Tao L., Tang Z., Deng X. (2025). Self-Healing Hydrogel Dressing with Solubilized Flavonoids for Whole Layer Regeneration of Diabetic Wound. *Advanced Healthcare Materials* 14: 2500734. DOI: [10.1002/adhm.202500734](https://doi.org/10.1002/adhm.202500734)

Los autores son de la Peking University School and Hospital of Stomatology (Beijing, China). Desarrollaron un apósito de hidrogel con flavonoides para heridas diabéticas y estudiaron cómo responden los macrófagos de ratón (línea RAW264.7) a cuatro flavonoides: catequina, quercetina, miricetina y dihidromiricetina, comparados con un control (PBS). Hay 3 réplicas por grupo, 15 muestras en total. Los datos están en GEO con el número [GSE260761](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE260761).

## Objetivo

Comprobar de forma independiente qué hace cada flavonoide en los macrófagos, a partir de los conteos crudos y no de las figuras del paper. Planteo tres hipótesis:

- **H1:** los flavonoides activan una respuesta antioxidante tipo Nrf2, con distinta intensidad según la molécula.
- **H2:** la catequina actúa por el eje CD36 → PPAR → macrófago M2, como propone el paper.
- **H3:** las proteínas de esa respuesta están conservadas en humanos, así que el resultado sería trasladable.

## Contenido del repositorio

| Archivo | Qué es |
|---|---|
| `Proyecto_Final_GSE260761.ipynb` | El notebook de Colab con todo el análisis, comentarios, gráficos y conclusiones. |
| `GSE260761_datos.csv` | La tabla de GEO completa: 21.193 genes, los conteos de las 15 muestras y la anotación funcional (KEGG, GO, Reactome). Es el archivo que lee el notebook. |
| `GSE260761_metadatos.csv` | A qué grupo pertenece cada muestra. |
| `ADHM-14-0.pdf` | El paper de referencia. |
| `README.md` | Este archivo. |

Los códigos de las muestras vienen del chino en pinyin: ECS = catequina, HPS = quercetina, YMS = miricetina, EQYMS = dihidromiricetina y PBS = control.

## Cómo correr el notebook

1. Abrir `Proyecto_Final_GSE260761.ipynb` en Google Colab (desde GitHub, o descargándolo y subiéndolo a Colab).
2. Subir `GSE260761_datos.csv` a la carpeta de archivos de Colab, el ícono de la carpeta a la izquierda.
3. Ejecutar todas las celdas en orden (Entorno de ejecución → Ejecutar todas).

Si el .csv no está en la carpeta, el notebook descarga el archivo original desde GEO y lo convierte solo. No hace falta GPU. Las librerías que Colab no trae de fábrica (`pydeseq2` y `biopython`) se instalan al principio del notebook.

## Pasos del análisis

1. Carga de datos y metadatos.
2. Limpieza y filtro de genes poco expresados: quedan 12.868 genes.
3. Normalización con DESeq2 (size factors y VST).
4. Control de calidad: correlación entre réplicas y detección de ARN contaminante de tejido digestivo.
5. Exploración con PCA y clustering jerárquico y K-means.
6. Expresión diferencial de cada flavonoide contra el control (DESeq2).
7. Comparación entre flavonoides y puntaje de respuesta Nrf2.
8. Análisis funcional con KEGG, Gene Ontology y Reactome.
9. Contraste con los genes que destaca el paper.
10. Machine learning: árbol de decisión, Random Forest, SVM y red neuronal, evaluados con validación LOOCV.
11. Secuencias de UniProt, alineamiento ratón contra humano y filogenia de HO-1.

## Resultados principales

- **H1 se cumple en 3 de 4 flavonoides.** Quercetina, miricetina y dihidromiricetina activan genes antioxidantes como *Hmox1*, *Nqo1*, *Gclm* y *Srxn1*, con más intensidad en miricetina. La catequina no activa esta respuesta.
- **H2 no se cumple.** Las muestras tienen ARN de tejido digestivo, sobre todo las de catequina, y ese artefacto explica los términos lipídicos en los que se apoya el eje CD36 → PPAR → M2. Sin los genes contaminantes, esos términos desaparecen.
- **H3 se cumple.** HO-1, NQO1 y GCLM tienen entre 82 % y 96 % de identidad entre ratón y humano, y conservan sus residuos funcionales.
- Los modelos de machine learning reconocen el tratamiento de cada muestra muy por encima del azar. El mejor es la SVM lineal, con 93 % de exactitud.

## Herramientas

Python en Google Colab: pandas, NumPy, Matplotlib, seaborn, PyDESeq2, SciPy, scikit-learn, TensorFlow/Keras y Biopython.
