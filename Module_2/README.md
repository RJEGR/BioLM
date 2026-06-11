# Bootcamp Bio-LLMs — Módulo 2: Tokenización biológica

Proyecto posdoctoral CICESE — Modelos de lenguaje fundacionales para venómica integrativa de caracoles *Conus*.

Este módulo cubre la **tokenización de secuencias biológicas**, el paso que convierte ADN/proteína cruda en unidades procesables por un transformador. La metodología del proyecto especifica evaluar tres familias de tokenización: *k-meros*, tokenización basada en transformadores (BPE), y fragmentación dinámica (H-Net). Este módulo las implementa y compara todas desde cero.

## Notebooks

| # | Notebook | Tema | Tiempo estimado |
|---|----------|------|-----------------|
| 05 | `05_tokenizacion_comparada.ipynb` | k-mer superpuesto (DNABERT) vs no-superpuesto (Nucleotide Transformer) vs BPE (DNABERT-2). Implementación desde cero, demostración de *information leakage*, BPE con conteo incremental de pares, comparación empírica de compresión/OOV | 45–60 min |
| 06 | `06_hnet_e_impacto_downstream.ipynb` | Fragmentación dinámica de H-Net (router de chunking por disimilitud coseno), por qué el ADN es difícil de tokenizar, y experimento de impacto downstream: ¿la elección de tokenizador cambia la perplejidad y el costo computacional? | 60–75 min (3 entrenamientos MLM incluidos) |

La numeración continúa la del Módulo 1 (notebooks 01–04), de modo que el bootcamp completo se lee secuencialmente.

## Qué aprenderás

* **Por qué DNABERT-2 abandonó el k-mer superpuesto**: la fuga de información en MLM, demostrada cuantitativamente.
* **Cómo funciona BPE por dentro**: el algoritmo de fusión iterativa por frecuencia, implementado con el índice incremental de pares que usan las librerías de producción.
* **El trade-off real de la tokenización**: compresión vs resolución vs costo. No hay ganador universal; la elección depende de si modelas genomas largos (gLM) o conotoxinas cortas (pLM).
* **La idea central de H-Net**: chunking dinámico aprendido sin vocabulario fijo, y por qué el router debe entrenarse end-to-end.

## Decisiones de tokenización para el proyecto

| Componente | Tokenizador recomendado | Justificación |
|---|---|---|
| gLM (genómico, contexto largo) | BPE (vocab 4096–8192) | Máxima compresión para atención O(n²) sobre exomas largos |
| pLM (proteómico, conotoxinas) | Aminoácido o codón | Resolución por residuo para predecir cisteínas/puentes disulfuro |
| Línea exploratoria | H-Net / chunking dinámico | Sin vocabulario fijo, adaptativo al contexto funcional |

## Cómo ejecutar

```bash
pip install -r requirements.txt
jupyter lab    # o jupyter notebook
```

El Notebook 05 sólo requiere numpy/matplotlib (sin GPU). El Notebook 06 usa PyTorch para el experimento downstream; corre en CPU en ~5 min o en GPU en segundos.

Los corpus son **sintéticos y autocontenidos** (semilla 42, reproducibles). Para usar datos reales del proyecto, reemplaza la función `synthesize_precursor()` por la carga de secuencias de NCBI BioProject **PRJNA437715** / **PRJNA526781** (exomas/transcriptomas de veneno de *Conus*).

## Pre-requisitos

* Módulo 1 completado (especialmente el `CodonTokenizer` y el MLM del Notebook 03, reutilizados aquí).
* Python ≥ 3.9, PyTorch ≥ 2.0.

## Bibliografía del módulo

* **Ji et al. (2021)** DNABERT — *Bioinformatics* 37(15):2112–2120. (k-mer superpuesto)
* **Dalla-Torre et al. (2025)** Nucleotide Transformer — *Nature Methods* 22:287–297. (k-mer no-superpuesto 6-mer)
* **Zhou et al. (2024)** DNABERT-2 — *ICLR*. (BPE para genomas)
* **Sennrich et al. (2016)** Neural Machine Translation of Rare Words with Subword Units — *ACL*. (BPE original)
* **Hwang et al. (2025)** H-Net: Dynamic Chunking for End-to-End Hierarchical Sequence Modeling. (fragmentación dinámica)
* **Boshar et al. (2024)** Are genomic language models all you need? — *Bioinformatics* 40(9):btae529. (3-mer vs 6-mer en proteínas)

## Siguiente módulo

**Módulo 3 — Pre-entrenamiento escalado y arquitecturas alternativas** (SSM/Mamba, contexto largo, estrategias de escalado en A100).
