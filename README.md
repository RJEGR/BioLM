
# BOOTCAMP EN MODELOS DE LENGUAJE BIOLÓGICOS (Bio-LLMs)

> **Contexto de diseño.** Este bootcamp está calibrado para el proyecto posdoctoral *"Modelos fundacionales de lenguaje genómico y proteómico para venómica integrativa de caracoles cónidos"*, ejecutable sobre la infraestructura de CICESE (NVIDIA A100 40 GB, 2× Xeon Gold 5420+, 1 TB RAM). La progresión escala desde fundamentos NLP hasta despliegue en tareas downstream (clasificación de superfamilias de conotoxinas, generación *de novo* tipo *ConoGen*, predicción funcional tipo *ConoPred*).

---

## 1. MARCO TEÓRICO: 5 LITERATURAS CIENTÍFICAS FUNDAMENTALES

Tras la curación sistemática de la literatura del proyecto y triangulación con la revisión de campo de Benegas et al. (Trends in Genetics, 2024), se seleccionan las siguientes cinco publicaciones pilares, ordenadas por dependencia lógica (fundación → especialización → frontera):

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

**Módulo obligatorio:** **Módulo 3** (arquitecturas alternativas a Transformer) y **Módulo 6** (modelado generativo de precursores conotoxínicos completos).

---

### Literatura complementaria de soporte transversal

Además de las cinco anteriores, se incorpora como **manual de referencia conceptual** la revisión Benegas, G., Ye, C., Albors, C., Li, J. C., & Song, Y. S. (2025). Genomic Language Models: Opportunities and Challenges. *Trends in Genetics*, que sintetiza la taxonomía contemporánea de gLMs (DNABERT, NT, HyenaDNA, EVO, Caduceus, GPN-MSA, LucaOne, MegaDNA). Y como **estudio de caso aplicado obligatorio** el trabajo de Benegas et al. (2023) GPN, primer gLM entrenado en *Arabidopsis thaliana* + 7 Brassicales con CNN dilatada que supera scores de conservación clásicos (phyloP, phastCons) en predicción no-supervisada de efectos de variantes — directamente análogo al problema de variantes en conotoxinas hipervariables.

---

## 2. ESTRUCTURA MODULAR DEL BOOTCAMP

El currículo se organiza en **siete módulos progresivos** (no cinco), porque la línea genómica y la proteómica del proyecto requieren tracks paralelos en la fase de aplicación. Duración total estimada: **18–22 semanas** (alineable con las Actividades 1–8 del cronograma posdoctoral, Feb 2026 – Ago 2027).

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
| **Temario técnico** | (a) Full fine-tuning vs *frozen-backbone + head*; (b) **LoRA** (rangos 4–64), **QLoRA** (cuantización NF4 + LoRA), **Prefix Tuning**, **Adapters**, **IA³**; (c) cabezas para tareas a nivel secuencia (clasificación de superfamilia A/M/O/T/conantokin), a nivel token (predicción de sitios de procesamiento del péptido señal / propéptido / péptido maduro) y regresión (afinidad por canales iónicos); (d) manejo de desbalance — *focal loss*, sobremuestreo estratificado por superfamilia, muestras negativas duras de UniProt; (e) **validación cruzada por homología** para evitar fugas entre *splits*. |
| **Literatura asociada** | **L2** (protocolo de fine-tuning DNABERT base), DNABERT-2 con LoRA como protocolo eficiente para modelos grandes en GPUs de consumo, Boshar et al. (2024) sobre evaluación de gLMs en tareas downstream proteómicas y la combinación aditiva NT-v2 + ESM-2 vía concatenación de embeddings finales con cabeza de regresión de una capa. |
| **Proyecto práctico** | Fine-tunear el gLM del Módulo 3 con **tres estrategias** (full FT, LoRA r=16, QLoRA) sobre clasificación binaria de "es-conotoxina-o-no" (positivos ConoServer ≥ 3 000 / negativos UniProt no-toxina). Reportar AUROC, MCC, F1 macro, parámetros entrenables y memoria pico. Comparar con un baseline BLASTp + perfil HMM. |

---

### **Módulo 5 — Evaluación, benchmarking e interpretabilidad** (2 semanas)

| Elemento | Contenido |
|---|---|
| **Objetivo** | Establecer un protocolo reproducible de evaluación intrínseca + extrínseca y diagnosticar qué aprende el modelo. |
| **Temario técnico** | (a) Métricas intrínsecas: perplejidad, *bits per nucleotide*, *masked accuracy*; (b) Métricas extrínsecas: AUROC, MCC, *top-k accuracy*, *Matthews* multiclase, calibración (ECE); (c) Suite **GUE / GUE+** de DNABERT-2 (28 datasets), **BEND**, **Nucleotide Transformer Benchmark**; (d) probing lineal vs *full FT* como diagnóstico; (e) interpretabilidad — *attention rollout*, *in silico mutagenesis*, *sequence logos* tipo GPN/UCSC, atribución de saliencia integrada; (f) detección de fuga de datos por homología (CD-HIT al 30 %). |
| **Literatura asociada** | **L4** (benchmark GUE como estándar), GPN como ejemplo de mutagénesis *in silico* no-supervisada para predicción de efecto de variantes y visualización vía sequence logos en UCSC Genome Browser. |
| **Proyecto práctico** | Construir un **mini-benchmark "ConoGUE"** con 4 tareas: clasificación superfamilia, predicción de sitio de clivaje del propéptido, predicción de cisteínas (puentes disulfuro) y predicción binaria diana-canal-iónico. Evaluar el modelo del Módulo 4 y publicar tabla con intervalos de confianza por *bootstrap*. |

---

### **Módulo 6 — Aplicación: Modelos generativos y diseño *de novo* de conotoxinas** (3 semanas)

| Elemento | Contenido |
|---|---|
| **Objetivo** | Construir un pipeline tipo **ConoGen + ConoPred** alineado con la Fase 2–4 del cronograma posdoctoral. |
| **Temario técnico** | (a) Fine-tuning generativo CLM sobre péptidos maduros de ConoServer; (b) muestreo controlado (temperatura, top-k, top-p, *classifier-free guidance*); (c) modelos pLM autorregresivos tipo ProGen; (d) filtrado generativo: clasificador discriminativo (WAE / encoder + cabeza); (e) integración con predictores estructurales (AlphaFold 2/3, ESMFold) para validación de plegamiento disulfuro; (f) métricas de novedad — distancia mínima a ConoServer, perplejidad bajo el discriminador, recuperación de motivos conservados (C-C-CC-C-C de la framework I). |
| **Literatura asociada** | **L5** (HyenaDNA como base de modelos generativos a nivel genoma); ProGen (Madani et al. 2020/2023) como referencia pLM autorregresiva; EVO y MegaDNA como ejemplos de generación a escala genómica completa. |
| **Proyecto práctico** | Generar 10 000 secuencias candidatas con ConoGen-LoRA, puntuar con ConoPred, filtrar las top-200 por novedad estructural (ESMFold) y composición de cisteínas. Análisis de las top-20 candidatas. |

---

### **Módulo 7 — Modelos multimodales y unificados** (2 semanas)

| Elemento | Contenido |
|---|---|
| **Objetivo** | Integrar el espacio genómico-proteómico y prepararse para la Actividad 7 del cronograma (manuscrito sobre modelos unificados). |
| **Temario técnico** | (a) Concatenación de embeddings (joint NT-v2 + ESM-2); (b) modelos verdaderamente unificados — LucaOne (mixed pretraining DNA+RNA+proteína), CD-GPT, gLM2 con tokens intercalados intergénico/proteína; (c) integración con estructura 3D (SaProt, ESM-3); (d) modelos instruccionales tipo ChatNT que combinan gLM + LLM de lenguaje natural; (e) consideraciones bioéticas y de doble uso en diseño de toxinas. |
| **Literatura asociada** | Boshar et al. (2024) que demuestra que entrenar un modelo conjunto genómico-proteómico supera a cada enfoque individual al capturar representaciones de secuencia diferentes pero complementarias, alcanzando estado del arte en predicción de estabilidad de proteína; LucaOne (He et al. 2024) entrenado sobre secuencias genómicas y proteicas de 169 861 especies con mixed pretraining. |
| **Proyecto práctico** | Implementar un *joint encoder* CDS-conotoxina + AA-conotoxina con cabezas compartidas para clasificación funcional. Reportar Δrendimiento vs Módulo 4. |

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

### Stack de software recomendado

**Núcleo:** Python 3.11, PyTorch 2.4+ (con `torch.compile`), CUDA 12.4, NCCL.
**Bibliotecas Bio-LLM:** HuggingFace `transformers`, `peft` (LoRA/QLoRA), `accelerate`, `bitsandbytes`, `datasets`, `evaluate`.
**Optimización de entrenamiento:** `flash-attn` (FlashAttention-2/3), `deepspeed` (ZeRO stage 1–2 cuando se acumule más de un A100), `xformers`.
**Modelos preentrenados de partida (HuggingFace):** `InstaDeepAI/nucleotide-transformer-v2-50m-multi-species`, `zhihan1996/DNABERT-2-117M`, `LongSafari/hyenadna-medium-160k-seqlen-hf`, `facebook/esm2_t33_650M_UR50D`, `hugohrban/progen2-medium`.
**Bioinformática soporte:** Biopython, ESMFold/ColabFold local, CD-HIT, MMseqs2, HMMER.
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
| **C — Adaptación** | 4, 5 | Sep 2026–Ene 2027 | ConoGUE benchmark + paper 1 (Actividad 4) |
| **D — Aplicación** | 6 | Feb–Jun 2027 | Pipeline ConoGen+ConoPred (Fases 2–4) |
| **E — Frontera** | 7 | Jul 2027–Ene 2028 | Modelo unificado + paper 2 (Actividad 7) |

---

**Declaración de uso de IA y limitaciones del plan.** Este plan fue elaborado con asistencia de Claude (Anthropic) integrando la literatura adjunta al proyecto y conocimiento de campo. Los cinco artículos pilares fueron seleccionados por relevancia arquitectónica/metodológica y por estar documentados en el corpus del proyecto; otras selecciones defendibles incluirían ESM-2 (Lin et al. 2023, *Science*) y Evo-2 (Brixi et al. 2025). El cronograma asume disponibilidad continua del A100 y sin interrupciones por mantenimiento; cualquier escalamiento a modelos >250 M parámetros desde cero requeriría tiempo de cómputo adicional fuera de CICESE. 