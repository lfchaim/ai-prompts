# Prompt
Criar uma API RESTful em Python, que receba uma imagem e faça as seguintes melhorias, conforme os parâmetros abaixo:
- parâmetro: brigthness
- parâmetro: contrast
- parâmetro: thresholding
- parâmetro: gamma
- parâmetro: histogram
- parâmetro: gaussian_blur
- parâmetro: sharpening
- parâmetro: grayscale
Caso o parâmetro não seja enviado, não aplicar o algoritmo de melhoria listado.

# Abacus (Claude Sonnet 4.6)
Aqui está a API RESTful em Python usando **FastAPI** com todas as melhorias de imagem solicitadas:


```python
from fastapi import FastAPI, File, UploadFile, Form
from fastapi.responses import StreamingResponse
import cv2
import numpy as np
from PIL import Image, ImageEnhance
import io
from typing import Optional

app = FastAPI(title="Image Enhancement API")


def apply_brightness(image: np.ndarray, value: float) -> np.ndarray:
    """Adjust brightness. value > 1 increases, < 1 decreases."""
    pil_img = Image.fromarray(cv2.cvtColor(image, cv2.COLOR_BGR2RGB))
    enhancer = ImageEnhance.Brightness(pil_img)
    enhanced = enhancer.enhance(value)
    return cv2.cvtColor(np.array(enhanced), cv2.COLOR_RGB2BGR)


def apply_contrast(image: np.ndarray, value: float) -> np.ndarray:
    """Adjust contrast. value > 1 increases, < 1 decreases."""
    pil_img = Image.fromarray(cv2.cvtColor(image, cv2.COLOR_BGR2RGB))
    enhancer = ImageEnhance.Contrast(pil_img)
    enhanced = enhancer.enhance(value)
    return cv2.cvtColor(np.array(enhanced), cv2.COLOR_RGB2BGR)


def apply_thresholding(image: np.ndarray, threshold: int) -> np.ndarray:
    """Apply binary thresholding (0-255)."""
    gray = cv2.cvtColor(image, cv2.COLOR_BGR2GRAY)
    _, thresh = cv2.threshold(gray, threshold, 255, cv2.THRESH_BINARY)
    return cv2.cvtColor(thresh, cv2.COLOR_GRAY2BGR)


def apply_gamma(image: np.ndarray, gamma: float) -> np.ndarray:
    """Apply gamma correction. gamma < 1 darkens, > 1 brightens."""
    inv_gamma = 1.0 / gamma
    table = np.array([
        ((i / 255.0) ** inv_gamma) * 255
        for i in range(256)
    ]).astype("uint8")
    return cv2.LUT(image, table)


def apply_histogram_equalization(image: np.ndarray) -> np.ndarray:
    """Apply CLAHE histogram equalization on luminance channel."""
    lab = cv2.cvtColor(image, cv2.COLOR_BGR2LAB)
    l, a, b = cv2.split(lab)
    clahe = cv2.createCLAHE(clipLimit=2.0, tileGridSize=(8, 8))
    l_eq = clahe.apply(l)
    lab_eq = cv2.merge((l_eq, a, b))
    return cv2.cvtColor(lab_eq, cv2.COLOR_LAB2BGR)


def apply_gaussian_blur(image: np.ndarray, kernel_size: int) -> np.ndarray:
    """Apply Gaussian blur. kernel_size must be odd (e.g., 3, 5, 7)."""
    if kernel_size % 2 == 0:
        kernel_size += 1  # Ensure odd
    return cv2.GaussianBlur(image, (kernel_size, kernel_size), 0)


def apply_sharpening(image: np.ndarray, strength: float) -> np.ndarray:
    """Apply unsharp mask sharpening. strength controls intensity."""
    blurred = cv2.GaussianBlur(image, (0, 0), 3)
    sharpened = cv2.addWeighted(image, 1 + strength, blurred, -strength, 0)
    return sharpened


def apply_grayscale(image: np.ndarray) -> np.ndarray:
    """Convert image to grayscale (returned as 3-channel for consistency)."""
    gray = cv2.cvtColor(image, cv2.COLOR_BGR2GRAY)
    return cv2.cvtColor(gray, cv2.COLOR_GRAY2BGR)


@app.post("/enhance", summary="Apply image enhancements")
async def enhance_image(
    file: UploadFile = File(..., description="Image file to enhance"),
    brightness: Optional[float] = Form(None, description="Brightness factor (e.g., 1.5 = +50%)"),
    contrast: Optional[float] = Form(None, description="Contrast factor (e.g., 1.5 = +50%)"),
    thresholding: Optional[int] = Form(None, description="Binary threshold value (0-255)"),
    gamma: Optional[float] = Form(None, description="Gamma correction (e.g., 1.5)"),
    histogram: Optional[bool] = Form(None, description="Apply CLAHE histogram equalization (true/false)"),
    gaussian_blur: Optional[int] = Form(None, description="Gaussian blur kernel size (odd number, e.g., 5)"),
    sharpening: Optional[float] = Form(None, description="Sharpening strength (e.g., 0.5 to 2.0)"),
    grayscale: Optional[bool] = Form(None, description="Convert to grayscale (true/false)"),
):
    # Read uploaded image
    contents = await file.read()
    np_arr = np.frombuffer(contents, np.uint8)
    image = cv2.imdecode(np_arr, cv2.IMREAD_COLOR)

    if image is None:
        from fastapi import HTTPException
        raise HTTPException(status_code=400, detail="Invalid image file.")

    # Apply enhancements only if parameter is provided
    if brightness is not None:
        image = apply_brightness(image, brightness)

    if contrast is not None:
        image = apply_contrast(image, contrast)

    if thresholding is not None:
        image = apply_thresholding(image, thresholding)

    if gamma is not None:
        image = apply_gamma(image, gamma)

    if histogram is True:
        image = apply_histogram_equalization(image)

    if gaussian_blur is not None:
        image = apply_gaussian_blur(image, gaussian_blur)

    if sharpening is not None:
        image = apply_sharpening(image, sharpening)

    if grayscale is True:
        image = apply_grayscale(image)

    # Encode result to PNG and return
    _, encoded = cv2.imencode(".png", image)
    return StreamingResponse(
        io.BytesIO(encoded.tobytes()),
        media_type="image/png",
        headers={"Content-Disposition": "attachment; filename=enhanced.png"}
    )


@app.get("/", summary="Health check")
def root():
    return {"status": "ok", "message": "Image Enhancement API is running."}
```

---

## 📦 Instalação das dependências

```bash
pip install fastapi uvicorn opencv-python pillow numpy python-multipart
```

## ▶️ Executar o servidor

```bash
uvicorn main:app --reload --host 0.0.0.0 --port 8000
```

---

## 📋 Parâmetros da rota `POST /enhance`

| Parâmetro | Tipo | Descrição | Exemplo |
|---|---|---|---|
| `file` | File | Imagem (obrigatório) | `.jpg`, `.png` |
| `brightness` | float | Fator de brilho | `1.5` (+50%) |
| `contrast` | float | Fator de contraste | `1.5` (+50%) |
| `thresholding` | int | Limiar binário (0–255) | `128` |
| `gamma` | float | Correção gamma | `1.2` |
| `histogram` | bool | Equalização CLAHE | `true` |
| `gaussian_blur` | int | Tamanho do kernel (ímpar) | `5` |
| `sharpening` | float | Intensidade do sharpening | `1.0` |
| `grayscale` | bool | Converter para escala de cinza | `true` |

---

## 🧪 Exemplo com `curl`

```bash
curl -X POST "http://localhost:8000/enhance" \
  -F "file=@foto.jpg" \
  -F "brightness=1.3" \
  -F "contrast=1.2" \
  -F "sharpening=0.8" \
  -F "grayscale=true" \
  --output resultado.png
```

> A documentação interativa estará disponível em **`http://localhost:8000/docs`** (Swagger UI) após iniciar o servidor.
