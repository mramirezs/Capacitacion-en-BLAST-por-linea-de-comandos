# Capacitación en BLAST por línea de comandos: Análisis Básico de Secuencias

## **1. Introducción a BLAST**
### ¿Qué es BLAST?
- **Definición:** BLAST (*Basic Local Alignment Search Tool*) es una herramienta que encuentra regiones de similitud local entre secuencias biológicas. Se utiliza para comparar una secuencia de consulta (*query*) con una base de datos de secuencias objetivo (*target*), identificando coincidencias significativas.
- **Usos comunes:** Identificación de genes homólogos, análisis evolutivo y descubrimiento de nuevos genes.

### ¿Cuáles son las herramientas de BLAST?
- **blastn:** Comparación de secuencias de nucleótidos.
- **blastp:** Comparación de secuencias de proteínas.
- **tblastn:** Busca proteínas en bases de datos de nucleótidos traducidos.
- **blastx:** Traduce una secuencia de nucleótidos y busca en bases de datos de proteínas.

### Conceptos clave
- **Query:** Secuencia que se desea comparar.
- **Subject/Target:** Secuencia en la base de datos que se alinea con la query.
- **E-value:** Representa el número de alineamientos esperados por casualidad. Un valor bajo indica mayor confianza.
- **Identidad (% Identity):** Proporción de coincidencias exactas entre la query y la subject.

### Flujo de trabajo básico de BLAST
1. Preparar una base de datos (*makeblastdb*).
2. Ejecutar la herramienta BLAST adecuada (*blastn*, *blastp*, etc.).
3. Interpretar los resultados.

---

## **2. Actividades Prácticas**

### A. Preparación del Ambiente
1. **Instalar herramientas necesarias:**
   ```bash
   pip install bio --upgrade
   ```
2. **Descargar las secuencias de ejemplo (Ebola):**
   ```bash
   mkdir -p ref
   bio fetch AF086833 > ref/1976.gb
   bio fetch KM233118 > ref/2014.gb
   ```

### B. Creación de una Base de Datos BLAST
1. **¿Qué es una base de datos BLAST?**
   - Una colección organizada de secuencias (nucleótidos o proteínas) utilizada como referencia en las búsquedas.
   - **Herramienta:** `makeblastdb` permite crear bases de datos BLAST personalizadas.
2. **Pasos:**
   - Extraer las secuencias en formato FASTA:
     ```bash
     cat ref/1976.gb | bio fasta --features > ref/1976.fa
     ```
   - Crear la base de datos:
     ```bash
     makeblastdb -in ref/1976.fa -dbtype nucl -out ref/1976
     ```
   - Verificar la base de datos:
     ```bash
     blastdbcmd -db ref/1976 -info
     ```

### C. Realizar Consultas con BLAST
#### Ejemplo 1: Consulta básica con *blastn* (gen L, Ebola)
1. **Extraer la secuencia del gen L:**
   ```bash
   cat ref/2014.gb | bio fasta --gene L -end 120 > L.fa
   ```
2. **Ejecutar *blastn*:**
   ```bash
   blastn -db ref/1976 -query L.fa -outfmt 6
   ```
3. **Nota:** El formato `outfmt 6` genera resultados tabulares con campos como query ID, subject ID, % identity, y más.

#### Ejemplo 2: Comparación de secuencias completas (VP35)
1. **Extraer y preparar las secuencias:**
   ```bash
   bio fetch AAD14582 --format fasta > VP35_1976.fa
   esearch -db protein -query PRJNA257197 | efetch -format fasta > ref/prots_2014.fa
   makeblastdb -dbtype prot -in ref/prots_2014.fa -out ref/prots_2014
   ```
2. **Ejecutar *blastp*:**
   ```bash
   blastp -db ref/prots_2014 -query VP35_1976.fa -outfmt "6 qseqid sseqid pident"
   ```

### D. Interpretación de Resultados
1. **Analizar resultados clave:**
   - **% Identity:** Proporción de coincidencias exactas entre las secuencias.
   - **E-value:** Alineamientos esperados por casualidad; valores bajos indican confianza.
   - **Longitud de alineamiento:** Determina el tamaño de la región alineada.
2. **Filtrar y ordenar resultados relevantes:**
   ```bash
   blastp -db ref/prots_2014 -query VP35_1976.fa -outfmt "6 qseqid sseqid pident" | sort -k3 -rn | head -5
   ```

---

## **3. Conclusión y Tareas**
### Resumen de lo aprendido
- Uso básico y práctico de BLAST.
- Creación de bases de datos personalizadas.
- Interpretación de resultados clave.

### Tareas sugeridas
1. Usar BLAST con secuencias de interés.
2. Explorar parámetros avanzados, como `-task blastn-short` para secuencias cortas:
   ```bash
   blastn -task blastn-short -db ref/1976 -query L.fa -outfmt 6
   ```
