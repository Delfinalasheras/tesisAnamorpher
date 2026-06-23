# tesisAnamorpher

Detección y sanitización de ataques de *prompt injection* ocultos en imágenes mediante escalado por interpolación.

<!-- link acá el PDF de la tesis: [Trabajo Final de Ingeniería en Informática — UCA](URL) -->

## De qué se trata

Ciertos ataques esconden texto en una imagen de modo que es invisible en su resolución original, pero **aparece cuando un modelo la reduce de tamaño** (downscaling bilineal/bicúbico) antes de procesarla. El payload se cuela como instrucción hacia el modelo víctima. El ataque se reproduce con la herramienta [Anamorpher](https://github.com/trailofbits/anamorpher](https://github.com/trailofbits/anamorpher.git) (Trail of Bits).

Este repo contiene la defensa desarrollada para la tesis, un pipeline de tres etapas:

1. **Detección** — IDONO, un clasificador SVM binario sobre features de FFT + HSI, decide si la imagen es limpia o contiene un ataque.
2. **Localización del texto** — módulo de consenso OCR: se reconstruye la imagen a varios tamaños y métodos de escalado y se quedan las palabras votadas por varias reconstrucciones.
3. **Sanitización** — *inpainting* (Telea) sobre las zonas con texto detectado; cuando el consenso no logra recuperar texto, se cae a un *median blur* global como defensa de respaldo. Un *gate* de cobertura descarta las imágenes donde el texto domina demasiado el frame como para reconstruir.

<!-- TODO (opcional): una o dos líneas con el hallazgo principal (p. ej. la simetría del "cuadrante peligroso") -->

## Requisitos

- Python 3.10+
- **Tesseract OCR** instalado a nivel sistema (en Colab: `!sudo apt install -y tesseract-ocr`)
- Librerías de Python:

```
pytesseract
Levenshtein
Pillow
scikit-learn
seaborn
scipy
matplotlib
opencv-python-headless
joblib
numpy
```

<!-- TODO: subir un requirements.txt con esta lista así se instala con `pip install -r requirements.txt` -->

## Cómo correr

El código se desarrolló en **Google Colab** con la carpeta de imágenes montada desde Google Drive. Los `.py` de este repo son exports de Colab (incluyen celdas `!pip`/`!apt`), así que conviene abrirlos como notebook en Colab en vez de ejecutarlos como script suelto.

1. Cloná el repo y subí (o montá) el dataset.
2. En **todos** los archivos, reemplazá esta línea por la ruta donde tengas las imágenes:

   ```python
   BASE = "/content/drive/MyDrive/Imagenes tesis"
   ```
3. Corré el pipeline sobre una imagen desde `Pipeline principal/pipelinefinal_imagen.py`.

## Estructura del repositorio

```
Pipeline principal/
├── modelo_detector_idono.py     # entrenamiento del detector IDONO (SVM, FFT+HSI)
├── pipelinefinal_imagen.py      # pipeline completo: detección → consenso OCR → sanitización
├── Modelo_IDONO.svm      # modelo entrenado
├── SanitizadasPipeline/         # outputs de sanitización
├── train_data/ , test_data/     # dataset usado
├── POCBicubic.png, POCBilineal.png   # imágenes de prueba de concepto
└── pipeline_Imagen.png          # diagrama del pipeline

Metodos e Informacion Adicional/
└── métodos alternativos, imágenes "difíciles" y resultados de la etapa de OCR


## Créditos

- Imagenes de ataque reproducido con [https://github.com/trailofbits/anamorpher.git) de Trail of Bits.

Trabajo Final — Ingeniería en Informática, Pontificia Universidad Católica Argentina (UCA).
