# Módulo 1 — Fundamentos del Transformer y NLP para biología

Notebooks de partida para el bootcamp **Bio-LLMs** del proyecto posdoctoral CICESE sobre venómica integrativa de *Conus*.

## Contenido

| # | Notebook | Tema | Tiempo estimado |
|---|----------|------|-----------------|
| 01 | `01_attention_desde_cero.ipynb` | Producto escalado de atención, multi-head attention, visualización sobre α-conotoxina | 45–60 min |
| 02 | `02_bloque_encoder_y_posicional.ipynb` | Codificación posicional sinusoidal, FFN, LayerNorm, bloque encoder completo, pre-norm vs post-norm | 45–60 min |
| 03 | `03_pretrain_mlm_conotoxinas.ipynb` | Tokenizador de codones, collator MLM, entrenamiento end-to-end, cálculo de perplejidad | 60–90 min (entrenamiento incluido) |

## Bibliografía obligatoria asociada

* **Vaswani et al. (2017)** — *Attention Is All You Need*. NeurIPS 30.
* **Ji et al. (2021)** — *DNABERT*. Bioinformatics 37(15):2112–2120.

Léelos en paralelo a los notebooks. Cada notebook cita las secciones específicas relevantes.

## Requisitos

```bash
pip install -r requirements.txt
```

Mínimo: Python 3.10+, PyTorch ≥ 2.0, numpy, matplotlib.
Recomendado: GPU NVIDIA (A100 disponible en CICESE).

## Cómo ejecutar

```bash
jupyter lab        # o jupyter notebook, o VS Code con extensión Python
```

Abre los notebooks **en orden** (01 → 02 → 03). El Notebook 03 reutiliza componentes conceptuales de 01 y 02, pero está autocontenido (puede ejecutarse independientemente).

### Tiempos de referencia (A100 40 GB)

| Notebook | CPU | A100 |
|----------|-----|------|
| 01 | ~2 min | ~30 s |
| 02 | ~3 min | ~45 s |
| 03 (entrenamiento completo) | ~20-30 min | ~3-5 min |

En CPU el corpus sintético sigue siendo ejecutable, simplemente reduce `EPOCHS = 1` en el Notebook 03.

## Diseño pedagógico

Cada notebook sigue la estructura:

1. **Objetivos y bibliografía** (markdown).
2. **Teoría** con ecuaciones LaTeX inline y comparativa con paradigmas previos (RNN/CNN).
3. **Implementación desde cero** en PyTorch puro, anotada línea a línea.
4. **Verificación** contra implementaciones de referencia (`F.scaled_dot_product_attention`, etc.) — sanity check obligatorio.
5. **Aplicación biológica** sobre una secuencia o corpus de conotoxina realista.
6. **Ejercicios** de consolidación con dificultad creciente.
7. **Cierre** con conexión explícita al siguiente módulo del bootcamp.

## Restricciones de diseño respetadas

* Sin `nn.Transformer`, sin `nn.MultiheadAttention`, sin `F.scaled_dot_product_attention` en las implementaciones principales (sólo para sanity checks).
* Sin Hugging Face Transformers en este módulo (se introducirá en Módulo 2).
* Corpus sintético autocontenido (~10 MB) que no requiere descargas externas. Instrucciones incluidas para reemplazar por NCBI BioProject **PRJNA437715** y **PRJNA526781** cuando estés listo.
* Reproducibilidad: semilla fija (42) en todas las celdas no-determinísticas.

## Salida esperada del Notebook 03

Al finalizar el Módulo 1 tendrás:

* `outputs_modulo1/conus_synthetic_cds.txt` — corpus sintético (~10 MB).
* `outputs_modulo1/tinymlm_modulo1.pt` — checkpoint del modelo entrenado (~2-3 MB).
* Perplejidad de validación **< 30** (vs baseline uniforme de 69) — confirma que el modelo aprende.

## Solución de problemas comunes

| Síntoma | Causa probable | Solución |
|---------|----------------|----------|
| `CUDA out of memory` en Notebook 03 | Batch demasiado grande para tu GPU | Reduce `batch_size=64` a 32 o 16 |
| Perplejidad no baja | LR demasiado alto/bajo, o no se aplica el masking | Verifica con el "Test del collator" en sección 3 |
| AMP scaler avisa "inf/nan loss" | Pérdida explota en BF16/FP16 | Reduce `BASE_LR` a 1e-4 |
| Notebook 03 muy lento | Estás en CPU | Reduce `EPOCHS` a 1 y `max_len` a 64 |

## Siguiente paso

Una vez completados los tres notebooks, prosigue con el **Módulo 2 — Tokenización biológica**, donde compararás esquemas k-mer superpuesto (DNABERT), no-superpuesto 6-mer (NT) y BPE (DNABERT-2) sobre el mismo corpus.
