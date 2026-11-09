# Comparación de técnicas de generación de datasets sintéticos para visión por computador en entornos de retail

Trabajo de Fin de Máster — Máster en Big Data y Ciencia de Datos, Universidad Internacional de Valencia (VIU).
Autora: Sadith Espinoza.

## Descripción

Este repositorio contiene el código del trabajo. Se comparan tres técnicas de generación de imágenes sintéticas, una por cada familia de modelos generativos, entrenadas en las mismas condiciones sobre el dataset de productos de supermercado GrocPOS (17 clases, 128 × 128 píxeles):

| Familia | Técnica |
|---|---|
| VAE | Autocodificador variacional condicional (CVAE) |
| GAN | StyleGAN2-ADA con entrenamiento transicional  |
| Difusión | DDPM condicional con *classifier-free guidance* y muestreo DDIM |

Los datasets sintéticos se evalúan frente a imágenes reales de prueba en tres dimensiones (fidelidad visual, diversidad y fidelidad semántica), con métricas calculadas en el espacio de características de DINOv2. El proceso sigue la metodología CRISP-DM.

## Estructura del repositorio

```
├── notebooks/   Notebooks del pipeline, en orden de ejecución
├── splits/      Partición de entrenamiento, validación y prueba
├── manifest/    Inventario de imágenes, mapa de clases y modo de cada imagen
└── README.md
```

Las carpetas con archivos pesados o con resultados intermedios se encuentran en Google Drive (ver [Datos y resultados](#https://drive.google.com/drive/u/0/folders/1mCLreduL2007qwTy8Nk9CoPypbzDQglQ)).

## Notebooks

Los notebooks deben ejecutarse en este orden:

| Notebook | Fase CRISP-DM | Contenido |
|---|---|---|
| `01_inventario_grocpos` | Comprensión de los datos | Inventario del dataset, análisis exploratorio y verificación de calidad |
| `02_preparacion_grocpos` | Preparación de los datos | Exclusión de duplicados, agrupación de casi duplicados y partición estratificada |
| `03_piloto_generadores` | Modelado | Prueba corta de las tres técnicas y del flujo de evaluación |
| `03b_piloto_stylegan_fallido` | Modelado | Intento previo del piloto de StyleGAN2-ADA, conservado como registro |
| `03b_piloto_stylegan` | Modelado | Piloto de StyleGAN2-ADA con correcciones de compatibilidad |
| `04_entrenamiento_gan` | Modelado | StyleGAN2-ADA condicional estándar (colapsó y se reemplazó por la versión transicional) |
| `04b_entrenamiento_gan_transicional` | Modelado | GAN con entrenamiento transicional, usada en la comparación final |
| `05_entrenamiento_difusion` | Modelado | Entrenamiento del modelo de difusión condicional |
| `06_vae_y_seleccion` | Modelado y evaluación | Entrenamiento de la CVAE y selección de su *checkpoint* |
| `07a_seleccion_difusion` | Evaluación | Selección del *checkpoint* y de la escala de guía de la difusión, con validación |
| `07b_seleccion_gan` | Evaluación | Selección del *checkpoint* de la GAN, con validación |
| `07c_generacion_y_evaluacion` | Evaluación | Generación de los tres datasets sintéticos y evaluación final sobre el conjunto de prueba |

Cada notebook es autocontenido: incluye la configuración, la carga de datos y las funciones que necesita.

## Requisitos y ejecución

- **Entorno:** Google Colab con GPU NVIDIA L4.
- **Versiones:** Python 3.12.13, PyTorch 2.11.0, diffusers 0.39.0. Cada notebook imprime al inicio las versiones con las que se ejecutó.
- **Repositorios externos:** los notebooks los clonan automáticamente en commits fijos:
  - [NVlabs/stylegan2-ada-pytorch](https://github.com/NVlabs/stylegan2-ada-pytorch), commit `d72cc7d`
  - [mshahbazi72/transitional-cGAN](https://github.com/mshahbazi72/transitional-cGAN), commit `b879226`
  - [NVlabs/stylegan3](https://github.com/NVlabs/stylegan3), commit `c233a91` (solo `grid_sample_gradfix.py`)
- **Extractor de características:** DINOv2 ViT-L/14, cargado con `torch.hub` desde `facebookresearch/dinov2`.
- **Semilla:** 42.

Los notebooks montan Google Drive y esperan esta carpeta de proyecto:

```
MyDrive/tesis_retail/
├── checkpoints/   Puntos de control de los modelos
├── datasets/      Imágenes de entrenamiento y validación
├── eda/           Análisis exploratorio
├── embeddings/    Representaciones DINOv2
├── logs/          Registros de cada etapa
├── manifest/      Inventario de imágenes y mapa de clases
├── notebooks/     Notebooks del pipeline
├── raw/           Imágenes originales de GrocPOS
├── resultados/    Tablas y figuras finales
├── seleccion/     Selección de puntos de control
├── splits/        Partición de entrenamiento, validación y prueba
└── synthetic/     Imágenes sintéticas generadas
```

Fuera de Colab, la ruta del proyecto se indica con la variable de entorno `PROJECT_DIR`.

## Datos y resultados

- **Dataset original:** GrocPOS Grocery Products Detection V2, versión 4 «Original Images», disponible en [Roboflow Universe](https://universe.roboflow.com/fyp-groc-pos/grocpos-grocery-products-detection-v2/dataset/4). Las imágenes no se incluyen en este repositorio.
- **Resultados y archivos intermedios (Google Drive):**
  - Análisis exploratorio (`eda`): https://drive.google.com/drive/u/0/folders/1HHdjm9iov31lIP1Z07mU1JrFptCRc00w
  - Registros de cada etapa (`logs`): https://drive.google.com/drive/u/0/folders/1bnukomfwFKO1_KpQY4Lw-vswJA6t7Xp_
  - Selección de *checkpoints* (`seleccion`): https://drive.google.com/drive/u/0/folders/1ffJ0MqDHNpMhUe78rIMPZdYIhfWXqtjH
  - Tablas y figuras finales (`resultados`): https://drive.google.com/drive/u/0/folders/11Wucz1aL72NSyTWhqt2auP9GrNvhI7vD
  - *Checkpoints* de los modelos (`checkpoints`): https://drive.google.com/drive/u/0/folders/1ffJ0MqDHNpMhUe78rIMPZdYIhfWXqtjH
  - Datasets sintéticos generados (`synthetic`): https://drive.google.com/drive/u/0/folders/1_NtxF36ctchbLFvsOZpEe-Al-4K0uOU_

## Cita

Espinoza, S. (2026). *Comparación de técnicas de generación de datasets sintéticos para visión por computador en entornos de retail* [Trabajo de Fin de Máster, Universidad Internacional de Valencia].
