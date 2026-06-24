
# BOOTCAMP EN MODELOS DE LENGUAJE BIOLÓGICOS (Bio-LLMs)

> **Contexto de diseño.** Este bootcamp está diseñado para el proyecto *"Modelos fundacionales de lenguaje genómico y proteómico para venómica integrativa de caracoles cónidos"*, ejecutable sobre la infraestructura de CICESE (NVIDIA A100 40 GB, 2× Xeon Gold 5420+, 1 TB RAM). La progresión escala desde fundamentos NLP hasta despliegue en tareas downstream (clasificación de superfamilias de conotoxinas, generación *de novo* tipo *ConoGen*, predicción funcional tipo *ConoPred*).

---

## 1. MARCO TEÓRICO: 7 LITERATURAS CIENTÍFICAS FUNDAMENTALES

Tras la curación sistemática de la literatura del proyecto y triangulación con la revisión de campo de Benegas et al. (Trends in Genetics, 2024), se seleccionan siete publicaciones pilares, ordenadas por dependencia lógica (fundación → especialización → frontera → razonamiento). Las primeras cinco (L1–L5) cubren la columna vertebral arquitectónica y de tokenización; las dos últimas (L6–L7), incorporadas en junio de 2026, capturan el cambio de paradigma hacia **Bio-LLMs de razonamiento multimodal** que acoplan LLMs general-purpose con foundation models biológicos congelados.

### **L1. Vaswani et al. (2017)** — *Fundamento arquitectónico*
**Cita:** Vaswani, A., Shazeer, N., Parmar, N., Uszkoreit, J., Jones, L., Gomez, A. N., Kaiser, Ł., & Polosukhin, I. (2017). Attention Is All You Need. *Advances in Neural Information Processing Systems (NeurIPS)*, 30, 5998–6008.

**Avance clave:** Introducción del mecanismo de *self-attention* multi-cabezal que reemplaza recurrencia y convolución. Habilita el procesamiento paralelo de secuencias y la captura de dependencias globales en costo O(n²). Es la columna vertebral de toda arquitectura Bio-LLM posterior basada en encoder (BERT-like) o decoder (GPT-like).

**Módulo obligatorio:** **Módulo 1** (lectura completa + replicación en PyTorch del bloque encoder mínimo).

---

### **L2. Ji, Zhou, Liu & Davuluri (2021)** — *Primer Bio-LM genómico fundacional*
**Cita:** Ji, Y., Zhou, Z., Liu, H., & Davuluri, R. V. (2021). DNABERT: pre-trained Bidirectional Encoder Representations from Transformers model for DNA-language in genome. *Bioinformatics*, 37(15), 2112–2120.

**Avance metodológico clave:** Adaptación canónica del paradigma BERT al ADN mediante tokenización por *k*-meros superpuestos (k = 3, 4, 5, 6) y enmascaramiento de *spans* contiguos de k tokens, eliminando la tarea Next Sentence Prediction y forzando al modelo a predecir secuencias de k bases adyacentes a partir del contexto. Demuestra que la transferencia de aprendizaje desde el genoma humano funciona en organismos lejanos, sentando la viabilidad del esquema *pretraining → fine-tuning* en genómica.

**Módulo obligatorio:** **Módulo 2** (tokenización y MLM) y **Módulo 4** (fine-tuning para clasificación, *baseline* obligatorio frente a modelos posteriores).

---

### **L3. Dalla-Torre et al. (2023/2025)** — *Escalamiento y multiespecie*
**Cita:** Dalla-Torre, H., Gonzalez, L., Mendoza-Revilla, J., Lopez Carranza, N., Grzywaczewski, A. H., Oteri, F., Dallago, C., Trop, E., Sirelkhatim, H., Richard, G., Skwark, M., Beguir, K., Lopez, M., & Pierrot, T. (2025). Nucleotide Transformer: building and evaluating robust foundation models for human genomics. *Nature Methods*, 22, 287–297. (Preprint bioRxiv 2023.01.11.523679).

**Avance metodológico clave:** Validación empírica de las *scaling laws* en gLMs. Pre-entrenamiento de modelos de 500 M a 2.5 B parámetros sobre 3 202 genomas humanos del 1000 Genomes Project más 850 genomas multiespecie, con tokenización no-superpuesta de 6-meros (vocabulario de 4 104 tokens, 300 B tokens procesados durante entrenamiento). Demuestra que la diversidad evolutiva en el corpus de pre-entrenamiento incrementa transferencia incluso en regímenes de baja supervisión — principio directamente aplicable al proyecto con los 300 *bioproject* de *Conidae*.

**Módulo obligatorio:** **Módulo 3** (estrategias de pre-entrenamiento escalado) y **Módulo 5** (benchmarking en tareas downstream).

---

### **L4. Zhou et al. (2024)** — *Tokenización moderna y benchmark estandarizado*
**Cita:** Zhou, Z., Ji, Y., Li, W., Dutta, P., Davuluri, R. V., & Liu, H. (2024). DNABERT-2: Efficient Foundation Model and Benchmark for Multi-Species Genomes. *International Conference on Learning Representations (ICLR 2024)*.

**Avance metodológico clave:** Reemplazo de la tokenización por *k*-meros con Byte Pair Encoding (BPE) vía SentencePiece, reduciendo la longitud tokenizada ~5× y eliminando la fuga de información característica de los *k*-meros superpuestos. Incorpora ALiBi para extrapolación a secuencias largas, FlashAttention para eficiencia de memoria, y propone el benchmark Genome Understanding Evaluation (GUE) con 36 datasets en 9 tareas. Logra rendimiento comparable a Nucleotide Transformer con 21× menos parámetros y 92× menos tiempo GPU de pre-entrenamiento — crítico para el presupuesto computacional del proyecto.

**Módulo obligatorio:** **Módulo 2** (BPE vs k-mer trade-offs) y **Módulo 5** (suite GUE como protocolo de evaluación estándar).

---

### **L5. Nguyen, Poli et al. (2023)** — *Frontera de contexto ultra-largo (SSM)*
**Cita:** Nguyen, E., Poli, M., Faizi, M., Thomas, A., Birch-Sykes, C., Wornow, M., Patel, A., Rabideau, C., Massaroli, S., Bengio, Y., Ermon, S., Baccus, S. A., & Ré, C. (2023). HyenaDNA: long-range genomic sequence modeling at single nucleotide resolution. *Advances in Neural Information Processing Systems (NeurIPS 2023)*, 36, 43177–43201.

**Avance arquitectónico clave:** Sustitución de la atención cuadrática por la jerarquía Hyena (un State Space Model con convoluciones largas implícitas y *gating*), permitiendo contextos de hasta 1 millón de nucleótidos a resolución de nucleótido único. Rompe la barrera de los ~12 kb de NT-v2 y abre la modelización de interacciones enhancer–promoter de largo alcance. Complementa con la línea H-Net que demuestra que el ADN —al carecer de claves naturales de segmentación— se beneficia de fragmentación dinámica jerárquica aprendida en lugar de tokenización fija, alternativa explícitamente considerada en la metodología del proyecto.

**Módulo obligatorio:** **Módulo 3** (arquitecturas alternativas a Transformer), **Módulo 6** (modelado generativo de precursores conotoxínicos completos), y **Módulo 8** como prerrequisito (el razonamiento sobre precursores conotoxínicos completos —péptido señal + propéptido + péptido maduro + región C-terminal— y sobre contextos de pathway o interactoma exige horizontes de contexto que sólo arquitecturas tipo HyenaDNA/Mamba/EVO permiten).

---

### **L6. Fesser, Zhang et al. (2026)** — *Dinámicas de post-training en Bio-LLMs de razonamiento*
**Cita:** Fesser, L., Zhang, H., Li, M. M., Wang, E., Perozzi, B., Azizi, S., Kakade, S. M., & Zitnik, M. (2026). How Post-Training Shapes Biological Reasoning Models. *Preprint arXiv:2606.16517*.

**Avance metodológico clave:** Estudio controlado sobre >100 modelos de razonamiento biológico que aísla por primera vez los roles distintos de las tres etapas de post-training —**continued pre-training (CPT) → supervised fine-tuning (SFT) → reinforcement learning (GRPO)**— a través de las tres modalidades (DNA con Evo2, RNA con TranscriptFormer, proteínas con ESM-3). Aporta seis hallazgos cuantitativos que reformulan la práctica del fine-tuning: (i) SFT mejora rendimiento ID pero degrada OOD progresivamente (hasta ~18 puntos porcentuales en RNA); (ii) RL partiendo de un checkpoint SFT fuerte recupera generalización OOD con la mayor parte de la ganancia en las primeras 2–4 épocas; (iii) CPT sobre texto biológico general (subset FineFineWeb biology) precondiciona la efectividad de SFT y RL, especialmente OOD; (iv) backbones más fuertes elevan el techo absoluto pero preservan las dinámicas de entrenamiento; (v) la capacidad de adaptación óptima es asimétrica entre etapas (LoRA rank alto para SFT, bajo para RL); (vi) bajo presupuesto fijo, **1–3 épocas de SFT seguidas de mayor RL** maximizan el trade-off ID-OOD. Estos hallazgos contradicen la intuición default del fine-tuning monolítico que predomina en la literatura clásica DNABERT/NT.

**Módulo obligatorio:** **Módulo 4** (como referencia crítica sobre cuándo SFT empieza a degradar OOD), **Módulo 5** (definición rigurosa de splits ID/OOD biológicamente significativos) y **Módulo 8** (pipeline canónico CPT-SFT-RL).

---

### **L7. Fallahpour et al. (2026)** — *Razonamiento multimodal aplicado a función proteica*
**Cita:** Fallahpour, A., Seyed-Ahmadi, A., Idehpour, P., Ibrahim, O., Gupta, P., Naimer, J., Zhu, K., Shah, A., Ma, S., Adduri, A., et al. (2026). BioReason-Pro: Advancing Protein Function Prediction with Multimodal Biological Reasoning. *bioRxiv* 2026.03.19.712954.

**Avance metodológico clave:** Primer LLM multimodal de razonamiento dedicado a predicción de función proteica. Establece el **patrón arquitectónico canónico** que comparten los Bio-LLMs de razonamiento modernos (BioReason, MEDEA, rbio1, ProCyon): un backbone LLM general-purpose (Qwen3-4B-Thinking) acoplado a un foundation model biológico congelado (ESM-3 small, embeddings residuo-nivel de la capa 37) mediante una proyección lineal entrenable; los hidden states biológicos se *prepend* a los embeddings de tokens textuales antes de entrar al LLM. La adaptación es SFT sobre 130K trazas de razonamiento sintéticas (generadas por GPT-5 desde contexto multimodal: organismo, dominios InterPro con rangos residuo, predicciones jerárquicas de GO-GPT autorregresivo, partners de STRING) más GRPO con reward F_max propagado sobre la ontología GO. Logra 73.6% F_max, supera anotaciones UniProt en 79% de evaluaciones expertas humanas, y la atención per-residuo localiza contactos en estructuras cryo-EM resueltas — evidencia de razonamiento mecanístico interpretable. El patrón se transfiere directamente al proyecto: ESM-2/3 congelado + Qwen3 + proyección entrenable sobre péptidos conotoxinas, con trazas que justifican clasificación de superfamilia y diana iónica predicha.

**Módulo obligatorio:** **Módulo 6** (como referencia para enriquecer ConoPred con trazas de razonamiento) y **Módulo 8** (caso de estudio central del módulo).

---

### Literatura complementaria de soporte transversal

Además de las cinco anteriores, se incorpora como **manual de referencia conceptual** la revisión Benegas, G., Ye, C., Albors, C., Li, J. C., & Song, Y. S. (2025). Genomic Language Models: Opportunities and Challenges. *Trends in Genetics*, que sintetiza la taxonomía contemporánea de gLMs (DNABERT, NT, HyenaDNA, EVO, Caduceus, GPN-MSA, LucaOne, MegaDNA). Y como **estudio de caso aplicado obligatorio** el trabajo de Benegas et al. (2023) GPN, primer gLM entrenado en *Arabidopsis thaliana* + 7 Brassicales con CNN dilatada que supera scores de conservación clásicos (phyloP, phastCons) en predicción no-supervisada de efectos de variantes — directamente análogo al problema de variantes en conotoxinas hipervariables.

---

## 2. ESTRUCTURA MODULAR DEL BOOTCAMP

El currículo se organiza en **ocho módulos progresivos** (no siete), porque la línea genómica y la proteómica del proyecto requieren tracks paralelos en la fase de aplicación, y porque el cambio de paradigma hacia Bio-LLMs de razonamiento multimodal —documentado en L6 y L7— exige un módulo dedicado al final de la progresión. Duración total estimada: **21–25 semanas** (alineable con las Actividades 1–8 del cronograma posdoctoral, Feb 2026 – Mar 2028).

### **Módulo 1 — Fundamentos del Transformer y NLP para biología** (2 semanas)

| Elemento | Contenido |
|---|---|
| **Objetivo** | Dominar la matemática del *self-attention*, *positional encoding* y los esquemas pre-train/fine-tune, contrastando RNN/CNN/Transformer en el manejo del contexto. |
| **Temario técnico** | Producto escalado de atención; multi-head attention; *layer norm* vs *RMSNorm*; positional encoding sinusoidal vs aprendido vs RoPE vs ALiBi; arquitecturas encoder-only (BERT), decoder-only (GPT-2) y encoder-decoder (T5). Diferencias MLM vs CLM y su impacto en representaciones bidireccionales para secuencias biológicas. |
| **Literatura asociada** | **L1** (Vaswani 2017, lectura íntegra); GPT-2 (Radford 2019) como contraste autorregresivo. |
| **Proyecto práctico** | Implementar desde cero (en PyTorch puro, sin `nn.Transformer`) un bloque encoder de 2 capas y entrenarlo en un toy task de predicción de codones enmascarados sobre ~10 MB de CDS de *Conus*. Verificar con perplejidad. |

---

### **Módulo 2 — Preprocesamiento y tokenización biológica** (2 semanas)

| Elemento | Contenido |
|---|---|
| **Objetivo** | Comparar empíricamente esquemas de tokenización y entender el sesgo de redundancia evolutiva en corpora de venómica. |
| **Temario técnico** | (a) Tokenización a nivel nucleótido/aminoácido; (b) *k*-meros superpuestos (DNABERT) vs no-superpuestos (NT); (c) Byte Pair Encoding (BPE/SentencePiece) — selección de tamaño de vocabulario (2⁸ a 2¹⁵); (d) tokenización dinámica vía H-Net y fragmentación aprendida; (e) curación: deduplicación CD-HIT, balanceo por superfamilia, máscaras de regiones de baja complejidad; (f) preparación de corpora *Conus* desde NCBI BioProject PRJNA437715 y PRJNA526781. |
| **Literatura asociada** | **L2** (k-meros DNABERT), **L4** (BPE en DNABERT-2), H-Net como mecanismo de fragmentación jerárquica dinámica para secuencias sin claves naturales de segmentación como el ADN. |
| **Proyecto práctico** | Construir tres tokenizadores (6-mer no-superpuesto, BPE vocab 4 096, BPE vocab 32 000) sobre transcriptomas de ductos venenosos de 30 especies de *Conus* y reportar: tasa de compresión, distribución de longitudes, *out-of-vocabulary* en secuencias *holdout*. |

---

### **Módulo 3 — Arquitecturas fundacionales y pre-entrenamiento eficiente** (3 semanas)

| Elemento | Contenido |
|---|---|
| **Objetivo** | Seleccionar críticamente la arquitectura según horizonte de contexto requerido (motivo local vs operón completo vs cromosoma) y restricciones de cómputo. |
| **Temario técnico** | (a) Encoder MLM (BERT/DNABERT) — Cross-Entropy con enmascaramiento 15 %; (b) Decoder CLM (GPT/ProGen/Evo) — *next-token prediction* y su ventaja generativa; (c) State Space Models — Hyena, Mamba, Mamba-2, Caduceus y propiedades de equivarianza a complemento reverso; (d) Híbridos Transformer+SSM (EVO); (e) Funciones de pérdida específicas — *span-corruption* T5, MLM con *spans*, *coCLM* con *codon-aware* loss; (f) *Scaling Laws* de Chinchilla aplicadas a Bio-FMs y trabajos compute-optimal en proteínas (Cheng et al. 2024). |
| **Literatura asociada** | **L3** (NT, escalamiento multiespecie), **L5** (HyenaDNA, contexto ultra-largo), EVO como arquitectura híbrida Hyena+Transformer pre-entrenada con 8 kb y extendida a 131 kb, y Caduceus basado en Mamba con equivarianza a complemento reverso. |
| **Proyecto práctico** | Pre-entrenar un **gLM de juguete** (≈50–100 M parámetros, escala "Nano-NT") sobre 850 genomas filtrados + corpus *Conidae* multi-especie. Comparar curvas de pérdida MLM vs CLM y *throughput* tokens/segundo entre Transformer-encoder y un baseline Mamba-2. Métrica: perplejidad sobre holdout de transcriptomas no vistos. |

---

### **Módulo 4 — Estrategias de Fine-Tuning y PEFT** (3 semanas)

| Elemento | Contenido |
|---|---|
| **Objetivo** | Dominar el espectro adaptación: full fine-tuning ↔ probing lineal ↔ PEFT, optimizando para los conjuntos pequeños y desbalanceados típicos de venómica (ConoServer ~3 000 registros). |
| **Temario técnico** | (a) Full fine-tuning vs *frozen-backbone + head*; (b) **LoRA** (rangos 4–64), **QLoRA** (cuantización NF4 + LoRA), **Prefix Tuning**, **Adapters**, **IA³**; (c) cabezas para tareas a nivel secuencia (clasificación de superfamilia A/M/O/T/conantokin), a nivel token (predicción de sitios de procesamiento del péptido señal / propéptido / péptido maduro) y regresión (afinidad por canales iónicos); (d) manejo de desbalance — *focal loss*, sobremuestreo estratificado por superfamilia, muestras negativas duras de UniProt; (e) **validación cruzada por homología** para evitar fugas entre *splits*; (f) **diagnóstico temprano del trade-off SFT–OOD**: detección de over-specialization mediante monitoreo simultáneo de curvas ID y OOD a lo largo del entrenamiento, identificación del punto donde OOD comienza a degradarse (típicamente 2–4 épocas en tareas de razonamiento biológico, según L6), y selección de checkpoint óptimo por validación OOD en lugar de ID; (g) lectura crítica: por qué SFT clásico es necesario pero **no suficiente**, motivando el pipeline CPT-SFT-RL completo del Módulo 8. |
| **Literatura asociada** | **L2** (protocolo de fine-tuning DNABERT base), DNABERT-2 con LoRA como protocolo eficiente para modelos grandes en GPUs de consumo, Boshar et al. (2024) sobre evaluación de gLMs en tareas downstream proteómicas y la combinación aditiva NT-v2 + ESM-2 vía concatenación de embeddings finales con cabeza de regresión de una capa; **L6** (Fesser, Zhang et al. 2026) como referencia crítica sobre los hallazgos cuantitativos del trade-off SFT-OOD — particularmente Finding 1 (SFT sobreespecializa progresivamente con caída OOD de ~18 pp en RNA) y Finding 5 (capacidad LoRA óptima asimétrica entre etapas, rank alto para SFT). |
| **Proyecto práctico** | Fine-tunear el gLM del Módulo 3 con **tres estrategias** (full FT, LoRA r=16, QLoRA) sobre clasificación binaria de "es-conotoxina-o-no" (positivos ConoServer ≥ 3 000 / negativos UniProt no-toxina). Reportar AUROC, MCC, F1 macro, parámetros entrenables y memoria pico. Comparar con un baseline BLASTp + perfil HMM. **Extensión obligatoria**: replicar el experimento monitoreando simultáneamente un *split OOD por género retenido* (p. ej. *Conus betulinus* fuera del entrenamiento) y reportar la curva OOD junto con la ID. Identificar el epoch en que OOD se desacopla de ID — este es el diagnóstico que motiva el Módulo 8. |

---

### **Módulo 5 — Evaluación, benchmarking e interpretabilidad** (2 semanas)

| Elemento | Contenido |
|---|---|
| **Objetivo** | Establecer un protocolo reproducible de evaluación intrínseca + extrínseca y diagnosticar qué aprende el modelo, con énfasis en **distinguir fit a la distribución de entrenamiento (ID) de generalización biológica real (OOD)**. |
| **Temario técnico** | (a) Métricas intrínsecas: perplejidad, *bits per nucleotide*, *masked accuracy*; (b) Métricas extrínsecas: AUROC, MCC, *top-k accuracy*, *Matthews* multiclase, calibración (ECE), y **F_max propagado sobre la ontología** (jerárquico, propagación de hijos a padres) como métrica primaria para tareas de anotación funcional tipo GO; (c) Suite **GUE / GUE+** de DNABERT-2 (28 datasets), **BEND**, **Nucleotide Transformer Benchmark**; (d) probing lineal vs *full FT* como diagnóstico; (e) interpretabilidad — *attention rollout*, *in silico mutagenesis*, *sequence logos* tipo GPN/UCSC, atribución de saliencia integrada, y **atención per-residuo** localizada a contactos físicos como en BioReason-Pro; (f) **diseño riguroso de splits ID/OOD biológicamente significativos**: la fuga por homología (CD-HIT al 30%) es necesaria pero no suficiente — la generalización biológica exige construir splits OOD sobre **ejes biológicos completos retenidos** (especie/clado filogenético/superfamilia/diana canal-iónico), siguiendo el principio de L6 según el cual "unseen pathways, diseases, species, and perturbations often involve different mechanisms"; (g) reporte obligatorio de la **brecha ID-OOD** como métrica primaria, no sólo el rendimiento absoluto, dado que un modelo con ID alta y OOD baja indica over-specialization (Finding 1 de L6). |
| **Literatura asociada** | **L4** (benchmark GUE como estándar), GPN como ejemplo de mutagénesis *in silico* no-supervisada para predicción de efecto de variantes y visualización vía sequence logos en UCSC Genome Browser; **L6** (Fesser, Zhang et al. 2026) como referencia metodológica sobre construcción de splits OOD biológicamente significativos en DNA (por pathway), RNA (por disease/cell type) y proteínas (por species/taxa); **L7** (Fallahpour et al. 2026) para el protocolo de F_max propagado con CAFA-style temporal holdout. |
| **Proyecto práctico** | Construir un **mini-benchmark "ConoGUE"** con 4 tareas: clasificación superfamilia, predicción de sitio de clivaje del propéptido, predicción de cisteínas (puentes disulfuro) y predicción binaria diana-canal-iónico. Para **cada tarea, definir dos splits OOD complementarios**: (i) por identidad de secuencia (CD-HIT ≤ 30%) y (ii) por taxonomía retenida (un género entero de *Conus* fuera del entrenamiento, p. ej. *Conus betulinus* o el clado *Pionoconus*). Evaluar el modelo del Módulo 4 y publicar tabla con AUROC/MCC/F1 macro **e F_max propagado** sobre la jerarquía de superfamilia/dianas, reportando explícitamente la brecha ID-OOD con intervalos de confianza por *bootstrap*. |

---

### **Módulo 6 — Aplicación: Modelos generativos y diseño *de novo* de conotoxinas** (3 semanas)

| Elemento | Contenido |
|---|---|
| **Objetivo** | Construir un pipeline tipo **ConoGen + ConoPred** alineado con la Fase 2–4 del cronograma posdoctoral. Este módulo establece la base discriminativa/generativa clásica; su extensión razonante (**ConoReason**) se construye en el Módulo 8. |
| **Temario técnico** | (a) Fine-tuning generativo CLM sobre péptidos maduros de ConoServer; (b) muestreo controlado (temperatura, top-k, top-p, *classifier-free guidance*); (c) modelos pLM autorregresivos tipo ProGen; (d) filtrado generativo: clasificador discriminativo (WAE / encoder + cabeza); (e) integración con predictores estructurales (AlphaFold 2/3, ESMFold) para validación de plegamiento disulfuro; (f) métricas de novedad — distancia mínima a ConoServer, perplejidad bajo el discriminador, recuperación de motivos conservados (C-C-CC-C-C de la framework I). |
| **Literatura asociada** | **L5** (HyenaDNA como base de modelos generativos a nivel genoma); ProGen (Madani et al. 2020/2023) como referencia pLM autorregresiva; EVO y MegaDNA como ejemplos de generación a escala genómica completa; **L7** (BioReason-Pro) como modelo de referencia para la futura extensión de ConoPred hacia razonamiento multimodal con trazas estructuradas — particularmente el uso de atención per-residuo para localizar contactos funcionales y la integración de contexto multimodal (organismo, dominios InterPro, partners de interacción). |
| **Proyecto práctico** | Generar 10 000 secuencias candidatas con ConoGen-LoRA, puntuar con ConoPred, filtrar las top-200 por novedad estructural (ESMFold) y composición de cisteínas. Análisis de las top-20 candidatas. **Nota de continuidad**: la versión razonante de ConoPred (denominada **ConoReason**) se desarrolla en el Módulo 8 como extensión natural, sustituyendo la cabeza clasificadora por un LLM con trazas de razonamiento estructuradas. |

---

### **Módulo 7 — Modelos multimodales y unificados** (2 semanas)

| Elemento | Contenido |
|---|---|
| **Objetivo** | Integrar el espacio genómico-proteómico y prepararse para la Actividad 7 del cronograma (manuscrito sobre modelos unificados). |
| **Temario técnico** | (a) Concatenación de embeddings (joint NT-v2 + ESM-2); (b) modelos verdaderamente unificados — LucaOne (mixed pretraining DNA+RNA+proteína), CD-GPT, gLM2 con tokens intercalados intergénico/proteína; (c) integración con estructura 3D (SaProt, ESM-3); (d) modelos instruccionales tipo ChatNT que combinan gLM + LLM de lenguaje natural; (e) consideraciones bioéticas y de doble uso en diseño de toxinas. |
| **Literatura asociada** | Boshar et al. (2024) que demuestra que entrenar un modelo conjunto genómico-proteómico supera a cada enfoque individual al capturar representaciones de secuencia diferentes pero complementarias, alcanzando estado del arte en predicción de estabilidad de proteína; LucaOne (He et al. 2024) entrenado sobre secuencias genómicas y proteicas de 169 861 especies con mixed pretraining. |
| **Proyecto práctico** | Implementar un *joint encoder* CDS-conotoxina + AA-conotoxina con cabezas compartidas para clasificación funcional. Reportar Δrendimiento vs Módulo 4. |

---

### **Módulo 8 — Bio-Reasoning LLMs: razonamiento multimodal con CPT-SFT-RL** (3 semanas)

| Elemento | Contenido |
|---|---|
| **Objetivo** | Construir un Bio-LLM de razonamiento que acople un LLM general-purpose con foundation models biológicos congelados vía proyección entrenable, adaptado con el pipeline canónico **CPT → SFT → RL-GRPO**. Este módulo materializa el cambio de paradigma de junio de 2026: la salida del modelo deja de ser una etiqueta o secuencia y pasa a ser una **traza de razonamiento estructurada** que justifica la inferencia. |
| **Temario técnico** | (a) **Patrón arquitectónico canónico**: LLM general-purpose (Qwen3-1.7B/4B, Gemma 4 E2B) + frozen foundation model (ESM-2/3 para proteínas, Evo2 para DNA, TranscriptFormer para RNA) + proyección lineal entrenable; los hidden states biológicos se *prepend* a los embeddings de tokens textuales antes del LLM; (b) **Continued pre-training (CPT)** sobre texto biológico general (subset FineFineWeb biology, ~200K documentos): cuándo y por qué precondiciona efectividad de SFT/RL aguas abajo, especialmente OOD y especialmente para modelos pequeños (Finding 3 de L6); (c) **SFT con trazas de razonamiento**: generación sintética de trazas mediante un LLM auxiliar (GPT-4/5 o similar) a partir de anotaciones estructuradas (InterPro + STRING + GO + ConoServer), formato pregunta-respuesta con razonamiento explícito antes del veredicto; (d) **RL con GRPO**: objetivo *clipped surrogate*, ventajas centradas por grupo (g rollouts por prompt, normalización por desviación estándar del lote), *KL anchor* k3 contra política de referencia congelada, *reward design* combinado (correctness exacto + adherencia de formato + métricas jerárquicas como F_max propagado); (e) **Diseño asimétrico de capacidad LoRA** (Finding 5 de L6): rangos altos (128–256) para SFT, rangos bajos (16) para RL, fundamentado en que SFT debe absorber estructura de tarea y razonamiento multimodal mientras RL solo refina; (f) **Asignación óptima de presupuesto** (Finding 6 de L6): 1–3 épocas de SFT seguidas de mayor RL maximiza trade-off ID-OOD bajo presupuesto fijo; (g) **Splits ID/OOD biológicamente significativos** y monitoreo continuo de la frontera ID-OOD durante el entrenamiento; (h) **Consideraciones de doble uso para razonamiento generativo**: las trazas que explican mecanismos de toxicidad son ellas mismas un riesgo dual, lo que refuerza las salvaguardas bioéticas del Módulo 7. |
| **Literatura asociada** | **L6** (Fesser, Zhang et al. 2026) como referencia metodológica primaria sobre el pipeline CPT-SFT-RL y los seis hallazgos cuantitativos; **L7** (Fallahpour et al. 2026 BioReason-Pro) como caso de estudio detallado del patrón LLM + ESM-3 congelado + proyección con reward F_max propagado sobre GO; BioReason original (Fallahpour et al. 2025) para DNA con Evo2; MEDEA (Sui et al. 2026) para target identification con TranscriptFormer; rbio1 (Istrate et al. 2025) para razonamiento sobre estados celulares; ProCyon (Queen et al. 2025) para fenotipos proteicos; PPO original (Schulman et al. 2017) y GRPO (DeepSeek-R1, Guo et al. 2025) para fundamentos de RL. |
| **Proyecto práctico** | **ConoReason** — un modelo de razonamiento que complementa el ConoPred clásico del Módulo 6. **Arquitectura**: Qwen3-1.7B (con CPT previo de 1 época sobre un subset bilingüe español/inglés de literatura toxinológica + descripciones UniProt de toxinas + venom-related FineFineWeb) acoplado a **ESM-2 650M congelado** vía proyección lineal entrenable. **Entrada**: embeddings residuo-nivel del péptido conotoxina + contexto textual estructurado (especie de origen, tejido productor inferido, motivos cisteína detectados, dominios InterPro si los hay). **Salida estructurada**: (i) traza de razonamiento en español que explica la inferencia paso a paso; (ii) clasificación de superfamilia (A/M/O/T/conantokin/I); (iii) predicción de canal iónico diana con probabilidad calibrada. **Entrenamiento en dos fases**: SFT con LoRA r=128 sobre ~3 000 trazas sintéticas (generadas con un LLM auxiliar a partir de las anotaciones canónicas de ConoServer y literatura primaria de toxinas caracterizadas) durante 2–3 épocas únicamente, seguido de GRPO con LoRA r=16 durante 8–16 épocas; *reward* combinado: correctness exacto sobre superfamilia (peso 0.5) + F1 jerárquico sobre diana iónica (peso 0.3) + adherencia de formato y conciso ≤512 tokens (peso 0.2). **Splits**: ID estándar más dos OOD complementarios — género retenido (*Conus betulinus*) y superfamilia retenida (T). **Métricas comparativas obligatorias**: contrastar ConoReason vs ConoPred clásico del Módulo 6 sobre las mismas cuatro tareas del ConoGUE (Módulo 5), reportando la brecha ID-OOD para cada uno y discutiendo el costo computacional adicional (tokens generados, latencia de inferencia) frente al beneficio en generalización OOD. |

---

## 3. INFRAESTRUCTURA Y REQUISITOS DE CÓMPUTO

### Disponibilidad confirmada en CICESE
NVIDIA A100 (6 912 núcleos CUDA, 40 GB de memoria GPU) más 2 procesadores Intel Xeon Gold 5420+ (28 × 2 núcleos, 1 TB de RAM). Esta configuración es suficiente para los Módulos 1–7 con las optimizaciones siguientes.

### Recomendaciones de hardware por módulo

| Módulo | GPU mínima | Optimizaciones críticas | Cómputo estimado |
|---|---|---|---|
| 1–2 | 1× A100 40 GB (o L4 24 GB) | FP32, batch pequeño | Horas |
| 3 (pre-train juguete) | 1× A100 40 GB | **Mixed-precision BF16**, *gradient checkpointing*, FlashAttention-2, ZeRO-1 | 3–7 días para 50–100 M params |
| 4 (PEFT) | 1× A100 40 GB | **QLoRA NF4** (≈4× reducción de memoria), bitsandbytes, *gradient accumulation* | Horas-días |
| 5 (eval) | 1× A100 40 GB | Inferencia FP16, *batch* grande | Horas |
| 6–7 | 1× A100 40 GB | KV-cache para generación, sampling vectorizado | Días |
| 8 (CPT-SFT-RL) | 1× A100 40 GB | **CPT**: BF16 + ZeRO-2 + gradient checkpointing sobre Qwen3-1.7B (≈3 días/época sobre 200K docs). **SFT**: LoRA r=128 con FlashAttention-2, batch efectivo 16 vía acumulación, ESM-2 congelado en FP16 (≈12 h/época sobre 3K trazas). **GRPO**: 4–8 rollouts por prompt, max-completion 512 tokens, KL anchor β=10⁻⁴, secuencias reducidas a contexto ≤1024 (≈1–2 días/época). | 7–14 días totales para el pipeline completo |

### Stack de software recomendado

**Núcleo:** Python 3.11, PyTorch 2.4+ (con `torch.compile`), CUDA 12.4, NCCL.
**Bibliotecas Bio-LLM:** HuggingFace `transformers`, `peft` (LoRA/QLoRA), `accelerate`, `bitsandbytes`, `datasets`, `evaluate`.
**Optimización de entrenamiento:** `flash-attn` (FlashAttention-2/3), `deepspeed` (ZeRO stage 1–2 cuando se acumule más de un A100), `xformers`.
**Modelos preentrenados de partida (HuggingFace):** `InstaDeepAI/nucleotide-transformer-v2-50m-multi-species`, `zhihan1996/DNABERT-2-117M`, `LongSafari/hyenadna-medium-160k-seqlen-hf`, `facebook/esm2_t33_650M_UR50D`, `hugohrban/progen2-medium`, `Qwen/Qwen3-1.7B`, `Qwen/Qwen3-4B-Thinking`, `google/gemma-4-e2b` (todos requeridos para el Módulo 8).
**Bioinformática soporte:** Biopython, ESMFold/ColabFold local, CD-HIT, MMseqs2, HMMER.
**Razonamiento y RL (Módulo 8):** `trl` (TRL: Transformer Reinforcement Learning, contiene implementación de GRPO), `vllm` para generación rápida durante rollouts, `verl` o `OpenRLHF` como alternativas, `wandb` para tracking del reward y la frontera ID-OOD durante entrenamiento.
**Tracking y reproducibilidad:** Weights & Biases o MLflow, DVC para versionado de datos, Hydra para configuración.

### Estrategia frente a limitaciones de memoria de 40 GB

Para un A100 40 GB único, las decisiones críticas son:

1. **No replicar Nucleotide Transformer 2.5 B desde cero.** En su lugar, partir del checkpoint NT-v2-100M o 250M y continuar pre-entrenamiento (*continued pretraining*) sobre el corpus *Conidae*. Esto reduce 100× el costo y aprovecha la diversidad de las 850 especies ya destiladas por InstaDeep.
2. **QLoRA siempre que sea posible para fine-tuning** — permite entrenar adaptadores sobre modelos de 7 B en 40 GB.
3. **Contextos largos vía HyenaDNA o Mamba-2** en lugar de Transformer puro cuando se requieran >12 kb.
4. **Pre-cómputo de embeddings** para tareas de *probing* (extraer embeddings una vez, entrenar cabezas múltiples veces) reduce drásticamente el costo iterativo.

### Cuándo escalar más allá de un A100

Si se requiere reproducir el pre-entrenamiento NT-2500M desde cero, se necesitarían ≥8× A100 80 GB durante semanas — fuera del alcance del proyecto. Recomendación: gestionar tiempo en clústeres LANCAD/ABACUS-LNS o vía Google TRC (TPU v3/v4 gratuita para investigación académica), tal como hizo el grupo de InstaDeep para NT-v2 (entrenamiento en TPU v4).

---

## 4. DIRECCIÓN EXPLORATORIA Y PRÓXIMAS FRONTERAS

El campo Bio-LLM avanza a velocidad superior a la del ciclo de revisión por pares; este bootcamp debe revisarse cada 6 meses. Cinco frentes a vigilar activamente durante la estancia 2026–2028:

**1. Multimodalidad estructural integrada.** La línea SaProt, ESM-3 y AlphaFold-3 incorpora tokens estructurales (Foldseek 3Di) directamente al pre-entrenamiento, fundiendo secuencia y estructura. Para conotoxinas —donde el patrón de cisteínas y puentes disulfuro define la función— esta es probablemente la dirección de mayor impacto. Vigilar también modelos *all-atom* tipo Boltz-1 y RoseTTAFold All-Atom.

**2. Modelos verdaderamente unificados nucleótido–proteína.** LucaOne, CD-GPT y gLM2 representan la primera generación de modelos entrenados con tokens intercalados de ADN, ARN y proteína sobre cientos de miles de especies. Su capacidad para razonar a través del dogma central (CDS → mRNA → proteína) los hace especialmente atractivos para venómica integrativa, donde el ducto venenoso expresa simultáneamente transcritos y precursores procesados.

**3. Tokenización aprendida y sin tokens.** H-Net y arquitecturas byte-level que aprenden fragmentación dinámica jerárquica eliminan el sesgo del vocabulario fijo, especialmente relevante en ADN que carece de claves naturales de segmentación y donde la misma secuencia puede tener funciones distintas según contexto génico. Las versiones futuras de Evo-2 ya operan a nivel de par de bases con jerarquía implícita.

**4. Modelos a escala de genoma completo.** Evo-2 (40 B, 9.3 T tokens, contexto 1 Mb) y sucesores empujan hacia el procesamiento de genomas procariotas/virales completos en un solo *forward pass*. Para *Conus* (genomas ~2.5 Gb) sigue siendo inviable, pero exomas y transcriptomas completos sí entran en contexto.

**5. Modelos instruccionales y agentes biológicos.** ChatNT y sistemas que combinan gLM/pLM con LLM de lenguaje natural permiten formular tareas como prompts en español/inglés y obtener predicciones cuantitativas. Esta capa de interfaz es clave para la meta del proyecto de **transferencia tecnológica vía software libre accesible** (componentes Sisdai-compatibles).

**6. Consideraciones éticas y de doble uso.** El diseño *de novo* de toxinas con afinidad dirigida a canales iónicos humanos cae explícitamente dentro de las categorías de investigación de doble uso (DURC). El bootcamp debe incluir desde el Módulo 6 un módulo bioético formal: control de acceso a los modelos generativos (no liberar pesos públicamente para variantes optimizadas a toxicidad), uso de *unlearning* dirigido en checkpoints publicados, y alineación con las recomendaciones del NSABB (US) y guías UNESCO sobre IA en ciencias de la vida.

---

### Cronograma sugerido de implementación

| Bloque | Módulos | Ventana | Entregable acumulado |
|---|---|---|---|
| **A — Fundamentos** | 1, 2 | Feb–Mar 2026 | Corpus *Conidae* tokenizado en 3 esquemas |
| **B — Pre-entrenamiento** | 3 | Abr–Ago 2026 | gLM de 50–100 M (alineado con Actividad 2) |
| **C — Adaptación** | 4, 5 | Sep 2026–Ene 2027 | ConoGUE benchmark con splits ID/OOD biológicos + paper 1 (Actividad 4) |
| **D — Aplicación** | 6 | Feb–Jun 2027 | Pipeline ConoGen + ConoPred clásico (Fases 2–4) |
| **E — Frontera multimodal** | 7 | Jul–Oct 2027 | Modelo unificado + paper 2 borrador (Actividad 7) |
| **F — Razonamiento biológico** | 8 | Nov 2027–Mar 2028 | **ConoReason** (extensión razonante de ConoPred) + paper 3 borrador sobre Bio-LLMs de razonamiento aplicados a venómica |

El Bloque F extiende el cronograma original del posdoc tres meses más allá del cierre formal (Ago 2027), aprovechando la ventana de redacción del paper 2 (Jul 2027–Ene 2028) para solapar el desarrollo de ConoReason. El proyecto práctico del Módulo 8 está diseñado para **reutilizar todos los artefactos previos**: el ESM-2 congelado del Módulo 4–6, las trazas sintéticas que se pueden generar desde las anotaciones ConoServer ya curadas en el Módulo 2, los splits OOD definidos en el Módulo 5, y el conjunto de candidatas top-20 producidas por ConoGen en el Módulo 6 (sobre las cuales ConoReason produce justificaciones de razonamiento). Esto minimiza el costo de cómputo adicional al estrictamente necesario para CPT-SFT-RL sobre Qwen3-1.7B.

---

**Declaración de uso de IA y limitaciones del plan.** Este plan fue elaborado con asistencia de Claude (Anthropic) integrando la literatura adjunta al proyecto y conocimiento de campo. Las siete literaturas pilares (L1–L7) fueron seleccionadas por relevancia arquitectónica, metodológica o de paradigma, y por estar documentadas en el corpus del proyecto; otras selecciones defendibles incluirían ESM-2 (Lin et al. 2023, *Science*), Evo-2 (Brixi et al. 2026, *Nature*), DeepSeek-R1 (Guo et al. 2025, *Nature*) para fundamentos de RL en razonamiento, y BioReason original (Fallahpour et al. 2025) para DNA. La incorporación de L6 y L7 en junio de 2026 reconoce el cambio de paradigma hacia Bio-LLMs de razonamiento multimodal — un campo que no existía como categoría reconocible cuando se trazaron las primeras versiones de este bootcamp y que ya domina la frontera 2026. El cronograma asume disponibilidad continua del A100 y sin interrupciones por mantenimiento; cualquier escalamiento a modelos >250 M parámetros desde cero requeriría tiempo de cómputo adicional fuera de CICESE. El Módulo 8 (Bloque F) introduce dependencia adicional de Qwen3 y Gemma 4 como backbones LLM, ambos disponibles bajo licencia Apache-2.0 y compatibles con la infraestructura del proyecto. 