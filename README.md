# Proyecto final cen5789 - Genómica y Bioinformática

## **Pregunta de investigación:**
**¿Cuáles genes están diferencialmente expresados en *Citrus sinensis* bajo condiciones de estrés abiótico, y qué procesos biológicos están implicados en la respuesta a ese estrés?**

## Datos
 Genoma de referencia: https://www.ncbi.nlm.nih.gov/datasets/genome/GCF_022201045.2/
> 
 BioProjects: PRJNA862483 & PRJNA703546
>
 Anotación: https://www.ncbi.nlm.nih.gov/datasets/gene/GCF_022201045.2/

## Metodología
> 1. Descarga y preparación de datos
> ~~2. Control de calidad~~
>  ~~- fastQC~~
>    ~~- multiQC~~
>    ~~- trimmomatic~~
> ~~3. Alineamiento~~
>    ~~- Index con hisat2~~
>    ~~- Alineamiento con hisat2~~
>    ~~- Conversión de formatos con samtools~~
> 4. Cuantificación de expresión
>    - Salmon
> 5. Análisis de expresión diferencial
>    - Normalización y análisis estadístico con DESeq2
>      - Comparación de condiciones experimentales
>      - Extracción de DEGs
> 6. Anotación funcional de DEGs
>    - Extracción de IDs
>    - Anotación
>    - Análisis de enriquecimiento GO y KEGG
>   


