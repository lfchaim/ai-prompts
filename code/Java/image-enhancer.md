# Prompt
Crie um projeto Java Spring Boot com API RESTful para receber uma imagem e aplicar filtros de aprimoramento para melhorar a captura via OCR. A API deve:

1. Aceitar imagens nos formatos JPEG, PNG e TIFF via requisição HTTP POST.
2. Oferecer suporte a 10 algoritmos de aprimoramento de imagem, incluindo:
   - Ajuste de brilho (brightness)
   - Ajuste de contraste (contrast)
   - Thresholding
   - Desfoque gaussiano (Gaussian blur)
   - Nitidez (sharpness)
   - Remoção de ruído (noise reduction)
   - Equalização de histograma (histogram equalization)
   - Correção de gama (gamma correction)
   - Filtro bilateral (bilateral filter)
   - Normalização de imagem (image normalization)

3. Permitir que os algoritmos sejam selecionados via parâmetros de consulta (query parameters) na URL, usando nomes intuitivos (por exemplo, `brightness`, `contrast`, etc.). 
4. Aplicar os algoritmos selecionados na ordem em que são especificados na URL.
5. Retornar a imagem aprimorada como um arquivo para download, mantendo o formato original da imagem.
6. Incluir cabeçalhos HTTP apropriados para o download do arquivo, incluindo `Content-Disposition` e `Content-Type`.
7. Implementar tratamento de erros para:
   - Formatos de imagem não suportados
   - Parâmetros de consulta inválidos ou valores fora do intervalo válido
   - Falhas durante o processamento da imagem
   - Exceções gerais durante a execução da requisição

8. Documentar a API usando Swagger ou OpenAPI, incluindo exemplos de uso e descrições detalhadas dos parâmetros de consulta disponíveis.
9. Implementar logging para registrar eventos importantes, como início e fim do processamento de imagens, e quaisquer exceções ocorridas durante a execução.
10. Fornecer um exemplo de chamada à API via `curl` ou Postman, demonstrando como passar os parâmetros de consulta e receber a imagem aprimorada.

# Adapta
## Visão geral

Abaixo está um projeto completo (exemplo base) de uma API RESTful com Spring Boot (Java 17+) que:

- Recebe imagens JPEG, PNG e TIFF via POST (multipart/form-data).
- Aplica 10 algoritmos de aprimoramento de imagem pensados para melhorar OCR, escolhidos por parâmetros de consulta (query parameters) e executados na ordem em que aparecem na URL.
- Retorna a imagem aprimorada para download, preservando o formato original e com cabeçalhos HTTP apropriados.
- Inclui tratamento de erros robusto, logging, e documentação via OpenAPI/Swagger (com exemplos).
- Exemplo de chamada via curl/Postman incluso.

Para os filtros e qualidade de processamento, o projeto usa OpenCV (via bytedeco opencv-platform, que traz os binários nativos automaticamente) e os plugins TwelveMonkeys ImageIO para suporte a TIFF.

> Observação: Este exemplo processa a primeira “página” de um TIFF multipágina (comportamento padrão do OpenCV imdecode). Se você precisar lidar com TIFF multipágina (vários frames), avise e eu amplio o exemplo.

---

## Stack e decisões principais

- Spring Boot 3.x (Java 17+)
- OpenCV via bytedeco (opencv-platform) para filtros avançados:
  - brightness, contrast, thresholding (inclui Otsu), Gaussian blur, sharpness (unsharp mask), noise reduction (Fast Non-Local Means), histogram equalization (no canal de luminância), gamma correction, bilateral filter, normalization.
- TwelveMonkeys ImageIO para leitura/escrita de TIFF.
- Ordem dos filtros: preservada pela ordem em que os parâmetros aparecem na query string (parse manual de `request.getQueryString()`).
- Retorno com Content-Type e Content-Disposition adequados e preservando a extensão/codec original (.jpg/.jpeg, .png, .tif/.tiff).
- OpenAPI (springdoc) com exemplos e descrição dos parâmetros.
- Logging SLF4J/Logback (padrão do Spring Boot).

---

## Estrutura de diretórios (sugestão)

```
ocr-image-enhancer/
 ├─ pom.xml
 ├─ src/
 │  ├─ main/
 │  │  ├─ java/
 │  │  │  └─ com/example/ocrimageenhancer/
 │  │  │     ├─ OcrImageEnhancerApplication.java
 │  │  │     ├─ controller/
 │  │  │     │  └─ ImageEnhancementController.java
 │  │  │     ├─ service/
 │  │  │     │  ├─ ImageProcessingService.java
 │  │  │     │  └─ FilterParser.java
 │  │  │     ├─ model/
 │  │  │     │  ├─ FilterSpec.java
 │  │  │     │  └─ FilterType.java
 │  │  │     ├─ exception/
 │  │  │     │  ├─ GlobalExceptionHandler.java
 │  │  │     │  ├─ UnsupportedImageFormatException.java
 │  │  │     │  ├─ InvalidParameterException.java
 │  │  │     │  └─ ImageProcessingException.java
 │  │  │     └─ util/
 │  │  │        └─ ImageIOUtils.java
 │  │  └─ resources/
 │  │     └─ application.yml
 └─ README.md (opcional)
```

---

## pom.xml

Inclui Spring Boot Web, validação, OpenAPI, OpenCV (bytedeco) e TwelveMonkeys (TIFF).

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
         https://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>com.example</groupId>
  <artifactId>ocr-image-enhancer</artifactId>
  <version>1.0.0</version>
  <name>ocr-image-enhancer</name>
  <description>API REST para aprimoramento de imagens voltado a OCR</description>

  <properties>
    <java.version>17</java.version>
    <spring-boot.version>3.2.5</spring-boot.version>
    <springdoc.version>2.5.0</springdoc.version>
    <twelvemonkeys.version>3.10.1</twelvemonkeys.version>
    <bytedeco.opencv.version>4.9.0-1.5.10</bytedeco.opencv.version>
  </properties>

  <dependencyManagement>
    <dependencies>
      <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-dependencies</artifactId>
        <version>${spring-boot.version}</version>
        <type>pom</type>
        <scope>import</scope>
      </dependency>
    </dependencies>
  </dependencyManagement>

  <dependencies>
    <!-- Spring Web -->
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-web</artifactId>
    </dependency>

    <!-- Bean Validation -->
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-validation</artifactId>
    </dependency>

    <!-- OpenAPI/Swagger UI -->
    <dependency>
      <groupId>org.springdoc</groupId>
      <artifactId>springdoc-openapi-starter-webmvc-ui</artifactId>
      <version>${springdoc.version}</version>
    </dependency>

    <!-- OpenCV (bytedeco com binários nativos) -->
    <dependency>
      <groupId>org.bytedeco</groupId>
      <artifactId>opencv-platform</artifactId>
      <version>${bytedeco.opencv.version}</version>
    </dependency>

    <!-- TwelveMonkeys para TIFF -->
    <dependency>
      <groupId>com.twelvemonkeys.imageio</groupId>
      <artifactId>imageio-core</artifactId>
      <version>${twelvemonkeys.version}</version>
    </dependency>
    <dependency>
      <groupId>com.twelvemonkeys.imageio</groupId>
      <artifactId>imageio-tiff</artifactId>
      <version>${twelvemonkeys.version}</version>
    </dependency>

    <!-- Testes (opcional) -->
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-test</artifactId>
      <scope>test</scope>
    </dependency>
  </dependencies>

  <build>
    <plugins>
      <!-- Spring Boot plugin -->
      <plugin>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-maven-plugin</artifactId>
      </plugin>

      <!-- Compilar com Java 17 -->
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
        <configuration>
          <source>${java.version}</source>
          <target>${java.version}</target>
        </configuration>
      </plugin>
    </plugins>
  </build>
</project>
```

---

## application.yml (logging)

```yaml
server:
  port: 8080

spring:
  servlet:
    multipart:
      max-file-size: 30MB
      max-request-size: 30MB

logging:
  level:
    root: INFO
    com.example.ocrimageenhancer: DEBUG
```

---

## Classe principal

Carrega plugins do ImageIO (para TIFF) e garante que OpenCV seja carregado assim que a aplicação inicia.

```java
package com.example.ocrimageenhancer;

import org.bytedeco.javacpp.Loader;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

import javax.imageio.ImageIO;
import org.bytedeco.opencv.global.opencv_core;

@SpringBootApplication
public class OcrImageEnhancerApplication {

    public static void main(String[] args) {
        // Garante registro de plugins (TIFF via TwelveMonkeys)
        ImageIO.scanForPlugins();

        // Carrega nativos do OpenCV cedo (bytedeco)
        Loader.load(opencv_core.class);

        SpringApplication.run(OcrImageEnhancerApplication.class, args);
    }
}
```

---

## Modelo: tipos de filtro e especificação

Definimos os filtros suportados e um contêiner simples para o nome e valor bruto (string) do parâmetro, mantendo a ordem.

```java
package com.example.ocrimageenhancer.model;

public enum FilterType {
    BRIGHTNESS("brightness"),
    CONTRAST("contrast"),
    THRESHOLD("threshold"),
    GAUSSIAN("gaussian"),
    SHARPNESS("sharpness"),
    DENOISE("denoise"),
    HISTEQ("histeq"),
    GAMMA("gamma"),
    BILATERAL("bilateral"),
    NORMALIZE("normalize");

    private final String paramName;

    FilterType(String paramName) {
        this.paramName = paramName;
    }

    public String param() {
        return paramName;
    }

    public static FilterType fromParam(String p) {
        for (FilterType t : values()) {
            if (t.paramName.equalsIgnoreCase(p)) return t;
        }
        return null;
    }
}
```

```java
package com.example.ocrimageenhancer.model;

public class FilterSpec {
    private final FilterType type;
    private final String rawValue; // pode ser null para flags (ex.: histEq sem valor)

    public FilterSpec(FilterType type, String rawValue) {
        this.type = type;
        this.rawValue = rawValue;
    }

    public FilterType getType() {
        return type;
    }

    public String getRawValue() {
        return rawValue;
    }

    @Override
    public String toString() {
        return type.name() + (rawValue != null ? "=" + rawValue : "");
    }
}
```

---

## Parser da query string (ordem preservada)

Lê `request.getQueryString()` e converte em uma lista ordenada de FilterSpec, validando nomes.

```java
package com.example.ocrimageenhancer.service;

import com.example.ocrimageenhancer.exception.InvalidParameterException;
import com.example.ocrimageenhancer.model.FilterSpec;
import com.example.ocrimageenhancer.model.FilterType;

import java.net.URLDecoder;
import java.nio.charset.StandardCharsets;
import java.util.ArrayList;
import java.util.List;

public class FilterParser {

    public static List<FilterSpec> parseInOrder(String rawQuery) {
        List<FilterSpec> list = new ArrayList<>();
        if (rawQuery == null || rawQuery.isBlank()) return list;

        String[] pairs = rawQuery.split("&");
        for (String pair : pairs) {
            if (pair.isBlank()) continue;
            String[] kv = pair.split("=", 2);
            String k = URLDecoder.decode(kv[0], StandardCharsets.UTF_8);
            String v = kv.length > 1 ? URLDecoder.decode(kv[1], StandardCharsets.UTF_8) : null;

            // Ignore o parâmetro reservado "file" caso seja passado por engano na query
            if ("file".equalsIgnoreCase(k)) continue;

            FilterType type = FilterType.fromParam(k);
            if (type == null) {
                throw new InvalidParameterException("Parâmetro de filtro não suportado: " + k);
            }
            list.add(new FilterSpec(type, v));
        }
        return list;
    }
}
```

---

## Exceções e tratamento global

```java
package com.example.ocrimageenhancer.exception;

public class UnsupportedImageFormatException extends RuntimeException {
    public UnsupportedImageFormatException(String message) { super(message); }
}
```

```java
package com.example.ocrimageenhancer.exception;

public class InvalidParameterException extends RuntimeException {
    public InvalidParameterException(String message) { super(message); }
}
```

```java
package com.example.ocrimageenhancer.exception;

public class ImageProcessingException extends RuntimeException {
    public ImageProcessingException(String message, Throwable cause) { super(message, cause); }
    public ImageProcessingException(String message) { super(message); }
}
```

```java
package com.example.ocrimageenhancer.exception;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.http.HttpHeaders;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.HttpMediaTypeNotSupportedException;
import org.springframework.web.bind.annotation.ControllerAdvice;
import org.springframework.web.bind.annotation.ExceptionHandler;

import java.time.Instant;
import java.util.HashMap;
import java.util.Map;

@ControllerAdvice
public class GlobalExceptionHandler {
    private static final Logger log = LoggerFactory.getLogger(GlobalExceptionHandler.class);

    private ResponseEntity<Object> body(HttpStatus status, String msg) {
        Map<String, Object> map = new HashMap<>();
        map.put("timestamp", Instant.now().toString());
        map.put("status", status.value());
        map.put("error", status.getReasonPhrase());
        map.put("message", msg);
        return new ResponseEntity<>(map, new HttpHeaders(), status);
    }

    @ExceptionHandler(UnsupportedImageFormatException.class)
    public ResponseEntity<Object> handleUnsupported(UnsupportedImageFormatException ex) {
        log.warn("Formato não suportado: {}", ex.getMessage());
        return body(HttpStatus.UNSUPPORTED_MEDIA_TYPE, ex.getMessage());
    }

    @ExceptionHandler(InvalidParameterException.class)
    public ResponseEntity<Object> handleInvalid(InvalidParameterException ex) {
        log.warn("Parâmetro inválido: {}", ex.getMessage());
        return body(HttpStatus.BAD_REQUEST, ex.getMessage());
    }

    @ExceptionHandler(ImageProcessingException.class)
    public ResponseEntity<Object> handleProcessing(ImageProcessingException ex) {
        log.error("Falha de processamento: {}", ex.getMessage(), ex);
        return body(HttpStatus.UNPROCESSABLE_ENTITY, ex.getMessage());
    }

    @ExceptionHandler(HttpMediaTypeNotSupportedException.class)
    public ResponseEntity<Object> handleMediaType(HttpMediaTypeNotSupportedException ex) {
        return body(HttpStatus.UNSUPPORTED_MEDIA_TYPE, "Tipo de mídia não suportado.");
    }

    @ExceptionHandler(Exception.class)
    public ResponseEntity<Object> handleGeneric(Exception ex) {
        log.error("Erro inesperado", ex);
        return body(HttpStatus.INTERNAL_SERVER_ERROR, "Erro inesperado ao processar a requisição.");
    }
}
```

---

## Utilitário para formatação/codec

- Descobre extensão e content-type originais com segurança.
- Normaliza extensão (ex.: “jpeg” → “jpg”, “tiff” → “tif”).
- Retorna cabeçalhos apropriados.

```java
package com.example.ocrimageenhancer.util;

import com.example.ocrimageenhancer.exception.UnsupportedImageFormatException;
import org.springframework.http.MediaType;
import org.springframework.web.multipart.MultipartFile;

public class ImageIOUtils {

    public static String extensionFrom(MultipartFile file) {
        String name = file.getOriginalFilename();
        if (name == null) throw new UnsupportedImageFormatException("Arquivo sem nome.");
        int dot = name.lastIndexOf('.');
        if (dot < 0) throw new UnsupportedImageFormatException("Extensão ausente no arquivo: " + name);
        String ext = name.substring(dot + 1).toLowerCase();
        // Normalizações
        if (ext.equals("jpeg")) ext = "jpg";
        if (ext.equals("tiff")) ext = "tif";
        if (!(ext.equals("jpg") || ext.equals("png") || ext.equals("tif"))) {
            throw new UnsupportedImageFormatException("Extensão não suportada: " + ext + " (suporta: jpg, png, tif)");
        }
        return ext;
    }

    public static MediaType mediaTypeForExt(String ext) {
        return switch (ext) {
            case "jpg" -> MediaType.IMAGE_JPEG;
            case "png" -> MediaType.IMAGE_PNG;
            case "tif" -> MediaType.valueOf("image/tiff");
            default -> MediaType.APPLICATION_OCTET_STREAM;
        };
    }

    public static String safeOutputName(String originalName) {
        if (originalName == null || originalName.isBlank()) return "enhanced-image";
        int dot = originalName.lastIndexOf('.');
        String base = dot > 0 ? originalName.substring(0, dot) : originalName;
        return "enhanced_" + base;
    }
}
```

---

## Serviço de processamento (OpenCV)

Implementa cada filtro e a aplicação do pipeline na ordem fornecida.

Parâmetros aceitos e validações:
- brightness: int [-255, 255]
- contrast: double [0.0, 5.0]
- threshold: 
  - “otsu” para Otsu binarization
  - inteiro [0..255] para limiar global
- gaussian: “k[:sigma]” com k ímpar [3..31], sigmaX >= 0.0 (padrão 0 implica derivar do kernel)
- sharpness: double [0.0..5.0] (unsharp mask, 0 desativa)
- denoise:
  - “nlm[:h]” onde h em [1..30] (padrão 10)
  - sem valor → usa h=10
- histEq: sem valor (aplica equalização no canal Y)
- gamma: double [0.1..5.0]
- bilateral: “d[:sigmaColor[:sigmaSpace]]” com d ímpar [3..31], sigmaColor [1..250], sigmaSpace [1..250]
- normalize:
  - sem valor → min-max para [0..255]
  - “min:max” → ex.: 0:255

```java
package com.example.ocrimageenhancer.service;

import com.example.ocrimageenhancer.exception.ImageProcessingException;
import com.example.ocrimageenhancer.exception.InvalidParameterException;
import com.example.ocrimageenhancer.model.FilterSpec;
import com.example.ocrimageenhancer.model.FilterType;
import org.bytedeco.opencv.global.opencv_core;
import org.bytedeco.opencv.global.opencv_imgcodecs;
import org.bytedeco.opencv.global.opencv_imgproc;
import org.bytedeco.opencv.opencv_core.*;
import org.bytedeco.opencv.opencv_photo.Photo;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.stereotype.Service;

import java.util.List;

import static org.bytedeco.opencv.global.opencv_core.*;
import static org.bytedeco.opencv.global.opencv_imgproc.*;

@Service
public class ImageProcessingService {
    private static final Logger log = LoggerFactory.getLogger(ImageProcessingService.class);

    public byte[] process(byte[] inputBytes, String ext, List<FilterSpec> pipeline) {
        try {
            // Decodifica para Mat
            Mat src = opencv_imgcodecs.imdecode(new Mat(inputBytes), opencv_imgcodecs.IMREAD_UNCHANGED);
            if (src == null || src.empty()) {
                throw new ImageProcessingException("Falha ao decodificar a imagem (possível arquivo corrompido).");
            }

            // Padroniza para BGR de 8 bits (mantendo info de se era colorida)
            boolean wasColor = ensure8UAndColor(src);

            log.debug("Imagem decodificada: {}x{} channels={}, depth={}",
                    src.cols(), src.rows(), src.channels(), src.depth());

            Mat current = src.clone();

            for (FilterSpec spec : pipeline) {
                log.debug("Aplicando filtro: {}", spec);
                current = applyFilter(current, wasColor, spec);
            }

            // Garante 8U e 3 canais se originalmente era colorida
            if (wasColor && current.channels() == 1) {
                Mat tmp = new Mat();
                cvtColor(current, tmp, COLOR_GRAY2BGR);
                current = tmp;
            }
            if (current.depth() != CV_8U) {
                Mat tmp = new Mat();
                current.convertTo(tmp, CV_8U);
                current = tmp;
            }

            // Re-encode preservando extensão original
            String dotExt = "." + ext.toLowerCase();
            MatOfByte buf = new MatOfByte();
            boolean ok = opencv_imgcodecs.imencode(dotExt, current, buf);
            if (!ok) throw new ImageProcessingException("Falha ao codificar a imagem de saída.");
            byte[] out = new byte[(int) buf.total()];
            buf.data().get(out);
            return out;

        } catch (InvalidParameterException e) {
            throw e;
        } catch (ImageProcessingException e) {
            throw e;
        } catch (Exception e) {
            throw new ImageProcessingException("Erro inesperado durante o processamento.", e);
        }
    }

    private boolean ensure8UAndColor(Mat m) {
        // Converte profundidade para 8U se necessário
        if (m.depth() != CV_8U) {
            Mat tmp = new Mat();
            m.convertTo(tmp, CV_8U);
            m.assignTo(tmp);
        }

        // Trata canais:
        // 1 canal → grayscale; 3 canais → BGR; 4 canais → BGRA → converter p/ BGR
        if (m.channels() == 1) {
            return false;
        } else if (m.channels() == 3) {
            return true;
        } else if (m.channels() == 4) {
            Mat tmp = new Mat();
            cvtColor(m, tmp, COLOR_BGRA2BGR);
            m.assignTo(tmp);
            return true;
        } else {
            // Canálização exótica: força BGR
            Mat tmp = new Mat();
            cvtColor(m, tmp, COLOR_RGBA2BGR);
            m.assignTo(tmp);
            return true;
        }
    }

    private Mat applyFilter(Mat src, boolean wasColor, FilterSpec spec) {
        return switch (spec.getType()) {
            case BRIGHTNESS -> applyBrightness(src, parseIntInRange(spec.getRawValue(), -255, 255, "brightness"));
            case CONTRAST -> applyContrast(src, parseDoubleInRange(spec.getRawValue(), 0.0, 5.0, "contrast"));
            case THRESHOLD -> applyThreshold(src, wasColor, spec.getRawValue());
            case GAUSSIAN -> {
                KernelSigma ks = parseKernelSigma(spec.getRawValue(), 3, 31, "gaussian");
                yield applyGaussian(src, ks.kernel, ks.sigma);
            }
            case SHARPNESS -> applySharpness(src, parseDoubleInRange(spec.getRawValue(), 0.0, 5.0, "sharpness"));
            case DENOISE -> applyDenoise(src, spec.getRawValue(), wasColor);
            case HISTEQ -> applyHistogramEqualization(src);
            case GAMMA -> applyGamma(src, parseDoubleInRange(spec.getRawValue(), 0.1, 5.0, "gamma"));
            case BILATERAL -> {
                BilateralParams bp = parseBilateral(spec.getRawValue());
                yield applyBilateral(src, bp.d, bp.sigmaColor, bp.sigmaSpace);
            }
            case NORMALIZE -> applyNormalize(src, spec.getRawValue());
        };
    }

    private Mat applyBrightness(Mat src, int beta) {
        Mat dst = new Mat();
        src.convertTo(dst, -1, 1.0, beta);
        return dst;
    }

    private Mat applyContrast(Mat src, double alpha) {
        Mat dst = new Mat();
        src.convertTo(dst, -1, alpha, 0.0);
        return dst;
    }

    private Mat applyThreshold(Mat src, boolean wasColor, String raw) {
        // Convertemos para grayscale para limiarização
        Mat gray = new Mat();
        if (src.channels() == 3) {
            cvtColor(src, gray, COLOR_BGR2GRAY);
        } else if (src.channels() == 1) {
            gray = src.clone();
        } else {
            Mat bgr = new Mat();
            cvtColor(src, bgr, COLOR_BGRA2BGR);
            cvtColor(bgr, gray, COLOR_BGR2GRAY);
        }

        Mat bin = new Mat();
        if (raw == null || raw.isBlank() || "otsu".equalsIgnoreCase(raw)) {
            threshold(gray, bin, 0, 255, THRESH_BINARY | THRESH_OTSU);
        } else {
            int t = parseIntInRange(raw, 0, 255, "threshold");
            threshold(gray, bin, t, 255, THRESH_BINARY);
        }

        // Mantém consistência com canal original
        if (wasColor) {
            Mat bgr = new Mat();
            cvtColor(bin, bgr, COLOR_GRAY2BGR);
            return bgr;
        }
        return bin;
    }

    private Mat applyGaussian(Mat src, int ksize, double sigma) {
        Size k = new Size(ksize, ksize);
        Mat dst = new Mat();
        opencv_imgproc.GaussianBlur(src, dst, k, sigma);
        return dst;
    }

    private Mat applySharpness(Mat src, double amount) {
        if (amount <= 0.0) return src.clone();
        // Unsharp mask: dst = (1 + a)*src + (-a)*blur
        Mat blur = new Mat();
        GaussianBlur(src, blur, new Size(0, 0), 1.0);
        Mat dst = new Mat();
        addWeighted(src, 1.0 + amount, blur, -amount, 0.0, dst);
        return dst;
    }

    private Mat applyDenoise(Mat src, String raw, boolean wasColor) {
        int h = 10; // default
        if (raw != null && !raw.isBlank()) {
            // aceita "nlm" ou "nlm:valor" ou apenas "10"
            String v = raw.toLowerCase();
            if (v.contains(":")) {
                String[] p = v.split(":");
                if (p.length >= 2) {
                    h = parseIntInRange(p[1], 1, 30, "denoise.h");
                }
            } else if ("nlm".equals(v)) {
                // usa default
            } else {
                h = parseIntInRange(v, 1, 30, "denoise.h");
            }
        }
        Mat dst = new Mat();
        if (src.channels() == 1) {
            Photo.fastNlMeansDenoising(src, dst, h);
        } else {
            Photo.fastNlMeansDenoisingColored(src, dst, h, h, 7, 21);
        }
        return dst;
    }

    private Mat applyHistogramEqualization(Mat src) {
        if (src.channels() == 1) {
            Mat dst = new Mat();
            opencv_imgproc.equalizeHist(src, dst);
            return dst;
        } else {
            // Equaliza luminância (Y) em YCrCb
            Mat ycrcb = new Mat();
            cvtColor(src, ycrcb, COLOR_BGR2YCrCb);
            MatVector ch = new MatVector(3);
            split(ycrcb, ch);
            Mat y = ch.get(0);
            opencv_imgproc.equalizeHist(y, y);
            ch.put(0, y);
            merge(ch, ycrcb);
            Mat bgr = new Mat();
            cvtColor(ycrcb, bgr, COLOR_YCrCb2BGR);
            return bgr;
        }
    }

    private Mat applyGamma(Mat src, double gamma) {
        // LUT 0..255
        UByteRawIndexer lut = new Mat(1, 256, CV_8U).createIndexer();
        for (int i = 0; i < 256; i++) {
            int val = (int) Math.round(255.0 * Math.pow(i / 255.0, 1.0 / gamma));
            lut.put(i, (byte) (val & 0xFF));
        }
        Mat lutMat = lut.getMat();

        Mat dst = new Mat();
        LUT(src, lutMat, dst);
        return dst;
    }

    private Mat applyBilateral(Mat src, int d, double sigmaColor, double sigmaSpace) {
        Mat dst = new Mat();
        opencv_imgproc.bilateralFilter(src, dst, d, sigmaColor, sigmaSpace);
        return dst;
    }

    private Mat applyNormalize(Mat src, String raw) {
        double min = 0.0, max = 255.0;
        if (raw != null && !raw.isBlank()) {
            String[] p = raw.split(":");
            if (p.length == 2) {
                min = parseDoubleInRange(p[0], 0.0, 255.0, "normalize.min");
                max = parseDoubleInRange(p[1], 0.0, 255.0, "normalize.max");
                if (max <= min) throw new InvalidParameterException("normalize: max deve ser > min.");
            } else {
                throw new InvalidParameterException("normalize deve ser 'min:max' (ex.: 0:255).");
            }
        }
        Mat dst = new Mat();
        opencv_core.normalize(src, dst, min, max, NORM_MINMAX, -1, null);
        // Garante 8U
        if (dst.depth() != CV_8U) {
            Mat tmp = new Mat();
            dst.convertTo(tmp, CV_8U);
            dst = tmp;
        }
        return dst;
    }

    // ------------------ Parsers auxiliares ------------------

    private static class KernelSigma {
        int kernel; double sigma;
        KernelSigma(int k, double s) { kernel = k; sigma = s; }
    }

    private KernelSigma parseKernelSigma(String raw, int min, int max, String name) {
        int k = 5; double sigma = 0.0;
        if (raw != null && !raw.isBlank()) {
            String[] p = raw.split(":");
            k = parseIntInRange(p[0], min, max, name + ".kernel");
            if (k % 2 == 0) throw new InvalidParameterException(name + ".kernel deve ser ímpar.");
            if (p.length >= 2) {
                sigma = parseDoubleInRange(p[1], 0.0, 100.0, name + ".sigma");
            }
        } else {
            k = 5; sigma = 0.0;
        }
        return new KernelSigma(k, sigma);
    }

    private static class BilateralParams {
        int d; double sigmaColor; double sigmaSpace;
        BilateralParams(int d, double sc, double ss) { this.d = d; this.sigmaColor = sc; this.sigmaSpace = ss; }
    }

    private BilateralParams parseBilateral(String raw) {
        int d = 9; double sc = 75.0; double ss = 75.0;
        if (raw != null && !raw.isBlank()) {
            String[] p = raw.split(":");
            d = parseIntInRange(p[0], 3, 31, "bilateral.d");
            if (d % 2 == 0) throw new InvalidParameterException("bilateral.d deve ser ímpar.");
            if (p.length >= 2) sc = parseDoubleInRange(p[1], 1.0, 250.0, "bilateral.sigmaColor");
            if (p.length >= 3) ss = parseDoubleInRange(p[2], 1.0, 250.0, "bilateral.sigmaSpace");
        }
        return new BilateralParams(d, sc, ss);
    }

    private int parseIntInRange(String raw, int min, int max, String name) {
        if (raw == null || raw.isBlank())
            throw new InvalidParameterException(name + " requer um valor.");
        try {
            int v = Integer.parseInt(raw.trim());
            if (v < min || v > max) {
                throw new InvalidParameterException(name + " fora do intervalo [" + min + "," + max + "].");
            }
            return v;
        } catch (NumberFormatException e) {
            throw new InvalidParameterException(name + " deve ser inteiro.");
        }
    }

    private double parseDoubleInRange(String raw, double min, double max, String name) {
        if (raw == null || raw.isBlank())
            throw new InvalidParameterException(name + " requer um valor.");
        try {
            double v = Double.parseDouble(raw.trim());
            if (v < min || v > max) {
                throw new InvalidParameterException(name + " fora do intervalo [" + min + "," + max + "].");
            }
            return v;
        } catch (NumberFormatException e) {
            throw new InvalidParameterException(name + " deve ser numérico.");
        }
    }
}
```

---

## Controller REST com OpenAPI e logging

- Endpoint: POST /api/v1/images/enhance
- Parâmetros via query em ordem.
- Arquivo via MultipartFile “file”.
- Retorna imagem com Content-Type original e Content-Disposition de download.

```java
package com.example.ocrimageenhancer.controller;

import com.example.ocrimageenhancer.exception.InvalidParameterException;
import com.example.ocrimageenhancer.exception.UnsupportedImageFormatException;
import com.example.ocrimageenhancer.model.FilterSpec;
import com.example.ocrimageenhancer.service.FilterParser;
import com.example.ocrimageenhancer.service.ImageProcessingService;
import com.example.ocrimageenhancer.util.ImageIOUtils;
import io.swagger.v3.oas.annotations.Operation;
import io.swagger.v3.oas.annotations.Parameter;
import io.swagger.v3.oas.annotations.media.Content;
import io.swagger.v3.oas.annotations.media.Schema;
import io.swagger.v3.oas.annotations.parameters.RequestBody;
import io.swagger.v3.oas.annotations.responses.ApiResponse;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.http.*;
import org.springframework.web.bind.annotation.*;
import org.springframework.web.multipart.MultipartFile;

import jakarta.servlet.http.HttpServletRequest;
import java.util.List;

@RestController
@RequestMapping("/api/v1/images")
public class ImageEnhancementController {
    private static final Logger log = LoggerFactory.getLogger(ImageEnhancementController.class);
    private final ImageProcessingService service;

    public ImageEnhancementController(ImageProcessingService service) {
        this.service = service;
    }

    @Operation(
            summary = "Aprimora uma imagem para OCR aplicando filtros selecionados",
            description = """
                Envie uma imagem (JPEG, PNG, TIFF) via multipart/form-data (campo 'file') e escolha os filtros na query string, na ordem em que devem ser aplicados.
                
                Filtros e formatos de parâmetro:
                - brightness: inteiro [-255..255] (ex.: brightness=20)
                - contrast: número [0.0..5.0] (ex.: contrast=1.2)
                - threshold: 'otsu' ou inteiro [0..255] (ex.: threshold=otsu, threshold=128)
                - gaussian: 'k[:sigma]' k ímpar [3..31], sigma>=0 (ex.: gaussian=5, gaussian=7:1.5)
                - sharpness: número [0.0..5.0] (ex.: sharpness=1.2)
                - denoise: 'nlm[:h]' ou apenas h em [1..30] (ex.: denoise=nlm:12, denoise=10)
                - histEq: sem valor (ex.: histEq)
                - gamma: número [0.1..5.0] (ex.: gamma=1.5)
                - bilateral: 'd[:sigmaColor[:sigmaSpace]]' com d ímpar [3..31], sigmaColor/sigmaSpace [1..250] (ex.: bilateral=9:75:75)
                - normalize: 'min:max' (ex.: normalize=0:255) ou sem valor para min-max padrão.
                
                Exemplos de ordenação pela URL:
                - ?gaussian=5&sharpness=1.2&threshold=otsu
                - ?brightness=15&contrast=1.3&gamma=1.2&histeq
                """
    )
    @ApiResponse(responseCode = "200", description = "Imagem aprimorada retornada com sucesso",
            content = @Content(mediaType = "application/octet-stream"))
    @ApiResponse(responseCode = "400", description = "Parâmetros inválidos")
    @ApiResponse(responseCode = "415", description = "Formato de imagem não suportado")
    @ApiResponse(responseCode = "422", description = "Falha durante o processamento da imagem")
    @PostMapping(value = "/enhance", consumes = MediaType.MULTIPART_FORM_DATA_VALUE)
    public ResponseEntity<byte[]> enhanceImage(
            HttpServletRequest request,
            @RequestPart("file")
            @RequestBody(description = "Arquivo de imagem", required = true,
                    content = @Content(mediaType = MediaType.MULTIPART_FORM_DATA_VALUE,
                            schema = @Schema(type = "string", format = "binary")))
            MultipartFile file
    ) {
        long start = System.currentTimeMillis();

        if (file == null || file.isEmpty()) {
            throw new InvalidParameterException("Nenhum arquivo foi enviado (campo 'file').");
        }

        String ext = ImageIOUtils.extensionFrom(file);

        // Verificação por content-type também (alguns clientes não enviam corretamente)
        String ct = file.getContentType() != null ? file.getContentType() : "";
        if (!(ct.contains("jpeg") || ct.contains("png") || ct.contains("tiff") || ct.isBlank())) {
            throw new UnsupportedImageFormatException("Content-Type não suportado: " + ct);
        }

        // Parse dos filtros na ordem da query
        List<FilterSpec> pipeline = FilterParser.parseInOrder(request.getQueryString());

        try {
            log.info("Início processamento: arquivo={}, ext={}, pipeline={}", file.getOriginalFilename(), ext, pipeline);

            byte[] inputBytes = file.getBytes();
            byte[] outputBytes = service.process(inputBytes, ext, pipeline);

            MediaType mediaType = ImageIOUtils.mediaTypeForExt(ext);
            String baseName = ImageIOUtils.safeOutputName(file.getOriginalFilename());
            String outFilename = baseName + "." + ext;

            HttpHeaders headers = new HttpHeaders();
            headers.setContentType(mediaType);
            headers.setContentDisposition(ContentDisposition.attachment().filename(outFilename).build());

            long took = System.currentTimeMillis() - start;
            log.info("Fim processamento: arquivo={}, tempoMs={}", file.getOriginalFilename(), took);

            return new ResponseEntity<>(outputBytes, headers, HttpStatus.OK);
        } catch (Exception e) {
            long took = System.currentTimeMillis() - start;
            log.error("Falha no processamento ({} ms): {}", took, e.getMessage(), e);
            throw e;
        }
    }
}
```

---

## Como usar os parâmetros de consulta (resumo)

- Ordem de aplicação = ordem na URL.
- Nomes simples:
  - brightness, contrast, threshold, gaussian, sharpness, denoise, histEq, gamma, bilateral, normalize
- Exemplos:
  - Desfocar levemente, aumentar nitidez, depois binarizar Otsu:
    - ?gaussian=5&sharpness=1.2&threshold=otsu
  - Iluminar e aumentar contraste:
    - ?brightness=20&contrast=1.3
  - Denoise padrão (NLM) e equalização de histograma:
    - ?denoise=nlm&histeq
  - Gamma correction e bilateral filter:
    - ?gamma=1.4&bilateral=9:80:80

---

## Documentação Swagger/OpenAPI

- Ao rodar a aplicação, acesse:
  - Swagger UI: http://localhost:8080/swagger-ui/index.html
  - OpenAPI JSON: http://localhost:8080/v3/api-docs

No Swagger UI você verá o endpoint POST /api/v1/images/enhance com a descrição e exemplos.

---

## Exemplos de uso (curl e Postman)

- curl (PNG → PNG, com Otsu e nitidez):

```
curl -X POST "http://localhost:8080/api/v1/images/enhance?gaussian=5&sharpness=1.2&threshold=otsu" \
  -H "Content-Type: multipart/form-data" \
  -F "file=@/caminho/para/entrada.png" \
  -o "/caminho/para/saida.png"
```

- curl (JPEG → JPEG, brilho/contraste/gamma):

```
curl -X POST "http://localhost:8080/api/v1/images/enhance?brightness=15&contrast=1.25&gamma=1.2" \
  -H "Content-Type: multipart/form-data" \
  -F "file=@/caminho/para/foto.jpg" \
  -o "/caminho/para/foto_enhanced.jpg"
```

- Postman:
  1. Método: POST
  2. URL: http://localhost:8080/api/v1/images/enhance?denoise=nlm:12&histeq&threshold=otsu
  3. Body → form-data → key: file (Type: File) → selecione a imagem
  4. Send → O download virá no corpo da resposta (ou salve como arquivo via “Save Response”).

---

## Como rodar

1. Requisitos:
   - Java 17+
   - Maven 3.8+
2. Build e execução:
   - mvn clean package
   - mvn spring-boot:run
3. Acesse Swagger UI e faça seus testes:
   - http://localhost:8080/swagger-ui/index.html

> Nota: O primeiro build baixa os binários nativos do OpenCV (bytedeco), podendo demorar um pouco.

---

## Boas práticas e observações de OCR

- Pipelines comuns para OCR:
  - Remoção de ruído leve → blur gaussiano (3/5) → threshold Otsu
  - Ou: NLM denoise → equalização de histograma → normalize → threshold Otsu
  - Para fotos: ajuste de gamma e contraste antes de binarizar ajuda bastante.
- TIFF multipágina: o exemplo processa a primeira página. Se precisar processar todas as páginas (ou retornar um novo TIFF multipágina), posso ajustar.
- Preservação de formato: apenas a extensão/codec é preservada; a profundidade/canais são normalizados, mas mantemos 3 canais para imagens originalmente coloridas para evitar surpresas em visualizadores.

---

## Testes rápidos (ideias)

- Envie um PNG com texto pouco contrastado:
  - ?histeq&gamma=1.3&threshold=otsu
- Fotografia de documento:
  - ?denoise=10&gaussian=3&sharpness=1.0&contrast=1.2&gamma=1.3&threshold=otsu
- Impressos com granulação:
  - ?bilateral=9:60:60&normalize=0:255&threshold=otsu

---

## Conclusão

- A API atende aos 10 algoritmos requisitados, com seleção por query string e aplicação na ordem fornecida.
- Retorna a imagem com o mesmo formato, headers de download corretos e fornece documentação e logging.
- Tratamento de erros cobre formatos e parâmetros inválidos, falhas de processamento e exceções gerais.

Se quiser, posso:
- Adicionar testes unitários para cada filtro.
- Incluir presets de pipeline, como “preset=ocr_default”.
- Suportar TIFF multipágina (entrada e saída).
- Fornecer um Dockerfile para empacotamento e execução conteinerizada.

# Prompt
Para rodar o OpenCV em linux Ubuntu, é necessário fazer alguma instalação?

# Adapta
Sim — depende de como você pretende usar o OpenCV no seu projeto Java.

Existem três caminhos comuns no Ubuntu, do “sem instalação no SO” até o “controle total”:

1) Sem instalação nativa (recomendado para Java/Spring Boot)
- Quando usar: você quer o jeito mais simples, que funciona em qualquer máquina/contêiner sem precisar instalar pacotes do sistema.
- Como funciona: use a dependência Maven “opencv-platform” do projeto Bytedeco (JavaCPP Presets). Ela traz as bibliotecas nativas do OpenCV empacotadas para Linux, Windows e macOS.
- Vantagens:
  - Evita problemas de LD_LIBRARY_PATH.
  - Funciona bem em produção (inclusive Docker) sem customização do sistema.
  - Atualizações fáceis via Maven/Gradle.
- Desvantagem:
  - Binários maiores no artefato final.

Exemplo com Maven:
```xml
<dependencies>
  <!-- OpenCV com nativos inclusos (Linux, Windows, macOS) -->
  <dependency>
    <groupId>org.bytedeco</groupId>
    <artifactId>opencv-platform</artifactId>
    <version>4.9.0-1.5.10</version>
  </dependency>
</dependencies>
```

Uso em Java (não precisa chamar System.loadLibrary):
```java
import org.bytedeco.opencv.global.opencv_core;

public class TesteOpenCV {
  public static void main(String[] args) {
    System.out.println("OpenCV version: " + opencv_core.getVersionString());
  }
}
```

2) Instalação via pacotes do Ubuntu (libopencv-dev)
- Quando usar: você prefere usar as bibliotecas do sistema (por exemplo, imagens mais enxutas, padrão de TI da empresa, etc.).
- Passos básicos:
```bash
sudo apt update
sudo apt install -y libopencv-dev
```
Isso instala os headers e as bibliotecas nativas (libopencv_*.so). Para uso com Java, você precisa do JAR do binding Java do OpenCV fornecido pela distro. Esse pacote varia conforme a versão do Ubuntu (em algumas versões é “libopencv-java” ou similar). Verifique com:
```bash
apt search opencv | grep -i java
```
Se houver um pacote Java específico, instale-o também. Em muitos casos, o JAR é instalado em algum caminho como /usr/share/java/ (o nome exato muda por versão). Você pode:
- Referenciar o JAR no seu projeto (escopo system no Maven), ou
- Preferir a abordagem Bytedeco (opção 1) para simplificar o build.

Carregando a lib nativa no Java (binding oficial do OpenCV):
```java
import org.opencv.core.Core;

public class TesteOpenCV {
  public static void main(String[] args) {
    System.loadLibrary(Core.NATIVE_LIBRARY_NAME); // normalmente "opencv_java4"
    System.out.println(Core.getBuildInformation());
  }
}
```

Se você receber erro “no opencv_java in java.library.path”, aponte o local da JNI:
- Caminho comum no Ubuntu: /usr/lib/jni
- Ajuste em runtime:
```bash
export LD_LIBRARY_PATH=/usr/lib/jni:$LD_LIBRARY_PATH
# ou ao iniciar a JVM:
java -Djava.library.path=/usr/lib/jni -jar app.jar
```

Dica para build/tests Maven (Surefire):
```xml
<build>
  <plugins>
    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-surefire-plugin</artifactId>
      <version>3.2.5</version>
      <configuration>
        <argLine>-Djava.library.path=/usr/lib/jni</argLine>
      </configuration>
    </plugin>
  </plugins>
</build>
```

3) Compilar o OpenCV a partir do código-fonte (controle máximo)
- Quando usar: você precisa de recursos específicos (ex: contrib), otimizações customizadas, ou compatibilidade com uma CPU/ambiente específico.
- Dependências típicas:
```bash
sudo apt update
sudo apt install -y build-essential cmake git pkg-config \
  libjpeg-dev libpng-dev libtiff-dev \
  libavcodec-dev libavformat-dev libswscale-dev \
  libgtk-3-dev libcanberra-gtk3-module \
  libxvidcore-dev libx264-dev libx265-dev \
  libopenexr-dev libtbb2 libtbb-dev \
  libdc1394-22-dev libv4l-dev \
  libeigen3-dev
```

- Clonar e compilar (com Java e TIFF habilitados):
```bash
git clone https://github.com/opencv/opencv.git
git clone https://github.com/opencv/opencv_contrib.git
cd opencv && git checkout 4.9.0 && cd ..
cd opencv_contrib && git checkout 4.9.0 && cd ..

mkdir -p opencv/build && cd opencv/build

cmake -D CMAKE_BUILD_TYPE=Release \
      -D CMAKE_INSTALL_PREFIX=/usr/local \
      -D BUILD_SHARED_LIBS=ON \
      -D BUILD_JAVA=ON \
      -D OPENCV_GENERATE_PKGCONFIG=ON \
      -D WITH_TIFF=ON \
      -D OPENCV_EXTRA_MODULES_PATH=../../opencv_contrib/modules \
      -D BUILD_TESTS=OFF \
      -D BUILD_PERF_TESTS=OFF \
      ..

make -j"$(nproc)"
sudo make install
sudo ldconfig
```

- Após a instalação, o JAR do OpenCV (geralmente opencv-4xx.jar) costuma ficar em /usr/local/share/java/ e a lib JNI em /usr/local/lib/. Para o Java encontrá-las:
```bash
export LD_LIBRARY_PATH=/usr/local/lib:$LD_LIBRARY_PATH
# e se necessário:
java -Djava.library.path=/usr/local/lib -cp .:/usr/local/share/java/opencv-490.jar TesteOpenCV
```

Observações importantes para OCR e formatos de imagem
- JPEG/PNG/TIFF: os pacotes do Ubuntu e o Bytedeco já incluem suporte aos codecs comuns; TIFF costuma vir habilitado. Ao compilar do zero, garanta WITH_TIFF=ON e libtiff-dev instalado.
- Headless/servidor: evite usar funções de GUI (imshow, waitKey). Não é necessário instalar GTK se você não precisa de janelas. Para processamento em servidor, foque em imgcodecs/imgproc.
- Performance: para processamento pesado, considere:
  - Compilar com TBB/OpenMP.
  - Usar tipos Mat em tons de cinza quando possível para OCR.
  - Aplicar pipeline fixo (ex: normalização -> equalização -> denoise -> sharpening -> threshold).

Verificação rápida da instalação
- Bytedeco (sem System.loadLibrary explícito):
```java
import org.bytedeco.opencv.global.opencv_core;
public class Check {
  public static void main(String[] args) {
    System.out.println(opencv_core.getVersionString());
  }
}
```
- Binding oficial do OpenCV (carregando a JNI):
```java
import org.opencv.core.Core;
public class Check {
  public static void main(String[] args) {
    System.loadLibrary(Core.NATIVE_LIBRARY_NAME);
    System.out.println(Core.getBuildInformation());
  }
}
```

Exemplo de Docker (duas opções)

- Opção A: Sem instalar nada no SO (usando Bytedeco):
```dockerfile
FROM eclipse-temurin:17-jre-jammy
WORKDIR /app
# Copie seu JAR (fat/uber-jar) que já contém a dependência opencv-platform
COPY target/app.jar /app/app.jar
CMD ["java", "-jar", "/app/app.jar"]
```

- Opção B: Usando bibliotecas do Ubuntu:
```dockerfile
FROM eclipse-temurin:17-jre-jammy
RUN apt-get update && apt-get install -y --no-install-recommends libopencv-dev && rm -rf /var/lib/apt/lists/*
ENV LD_LIBRARY_PATH=/usr/lib/jni:$LD_LIBRARY_PATH
WORKDIR /app
COPY target/app.jar /app/app.jar
CMD ["java", "-Djava.library.path=/usr/lib/jni", "-jar", "/app/app.jar"]
```

Problemas comuns e como resolver
- UnsatisfiedLinkError: no opencv_java in java.library.path
  - Ajuste -Djava.library.path para o diretório onde está a lib nativa (ex.: /usr/lib/jni, /usr/local/lib).
  - Use a dependência Bytedeco, que carrega os nativos automaticamente.
- “Illegal instruction” ao rodar em servidor antigo
  - Pode ocorrer com binários pré-compilados. Solução: compilar o OpenCV na própria máquina/contêiner alvo.
- Falha ao ler TIFF
  - Garanta que a build tenha WITH_TIFF=ON (fonte), ou use pacotes do Ubuntu/Bytedeco.
- Conflito de versões (várias cópias do OpenCV)
  - Evite misturar JAR nativo do sistema com Bytedeco ao mesmo tempo.
  - Verifique o classpath e os libs carregados.

Recomendação prática para seu projeto Spring Boot
- Para simplificar CI/CD e evitar dependências do SO, use a dependência Maven org.bytedeco:opencv-platform. Assim, você não precisa instalar nada extra no Ubuntu, e o serviço sobe de forma idêntica localmente, em servidor ou Docker.
- Se a política da sua infra exigir libs do sistema, instale libopencv-dev e a variante Java correspondente, ajuste o LD_LIBRARY_PATH e carregue a lib com System.loadLibrary(Core.NATIVE_LIBRARY_NAME).

Se quiser, posso:
- Checar o seu pom.xml e indicar a melhor configuração para o seu caso.
- Fornecer um Dockerfile final alinhado com o seu pipeline de build.
- Incluir um healthcheck que valida a presença da JNI do OpenCV na inicialização do Spring Boot.

# Prompt
Fornecer um Dockerfile final alinhado com o seu pipeline de build.

# Adapta
Abaixo segue um Dockerfile “pronto para produção” em duas variantes, para você escolher conforme seu pipeline de build. A primeira (recomendada) usa o OpenCV empacotado pela dependência Java (Bytedeco opencv-platform), dispensando instalação de libs nativas no container. A segunda mostra como montar a imagem usando OpenCV do sistema (Ubuntu), caso sua política exija dependências nativas via apt.

Observações importantes antes de escolher:
- Se você usa a dependência Bytedeco opencv-platform no pom.xml/gradle, prefira a VARIANTE A. Ela é mais simples, portátil e evita dores com versões de SO e glibc.
- Evite imagens Alpine quando usar Bytedeco/OpenCV nativo: o Bytedeco é compilado para glibc (Debian/Ubuntu), e Alpine usa musl. Use base Debian/Ubuntu (Temurin “jammy”).
- O Dockerfile abaixo assume que seu projeto é Maven, com o jar final em target/app.jar (pode ajustar facilmente).
- Se você expõe Actuator, o HEALTHCHECK usa /actuator/health. Se não, substitua pela sua rota de saúde (ex.: /health).

VARIANTE A (RECOMENDADA): Multi-stage com Maven + Runtime Temurin. Sem instalar OpenCV no SO
- Use quando: seu pom.xml traz a dependência do Bytedeco opencv-platform (traz as libs nativas de OpenCV embutidas).
- Vantagens: build reproduzível, imagem enxuta, sem apt-get de OpenCV.

Dockerfile:
```dockerfile
# =========================
# Stage 1: Build (Maven)
# =========================
FROM maven:3.9.6-eclipse-temurin-21 AS builder

WORKDIR /workspace

# Copia apenas o pom para aproveitar cache de dependências
COPY pom.xml ./
# Baixa dependências em cache (sem compilar ainda)
RUN --mount=type=cache,target=/root/.m2 mvn -B -q -e -DskipTests dependency:go-offline

# Agora copia o restante do código
COPY src ./src

# Compila e empacota o jar
RUN --mount=type=cache,target=/root/.m2 mvn -B -q -e -DskipTests package

# =========================
# Stage 2: Runtime (JRE)
# =========================
FROM eclipse-temurin:21-jre-jammy

# Diretório da aplicação
WORKDIR /app

# (Opcional) Instala curl apenas se você for usar HEALTHCHECK HTTP.
# Caso não use healthcheck, comente as duas linhas abaixo para manter a imagem menor.
RUN apt-get update && apt-get install -y --no-install-recommends curl ca-certificates \
    && rm -rf /var/lib/apt/lists/*

# Copia o jar construído
COPY --from=builder /workspace/target/*.jar /app/app.jar

# Cria usuário não root
RUN groupadd -g 1001 appgrp && useradd -u 1001 -g appgrp -m appusr
USER appusr

# Porta padrão do Spring Boot
EXPOSE 8080

# Tuning padrão para containers (ajuste conforme sua infra)
ENV JAVA_TOOL_OPTIONS="-XX:+UseContainerSupport -XX:MaxRAMPercentage=75 -XX:InitialRAMPercentage=50 -XX:+ExitOnOutOfMemoryError"

# (Opcional) Se você usa Spring Boot Actuator, ative o health endpoint e descomente o HEALTHCHECK
# HEALTHCHECK --interval=30s --timeout=3s --start-period=20s --retries=3 \
#   CMD curl -fsS http://localhost:8080/actuator/health || exit 1

# Inicia a aplicação
ENTRYPOINT ["sh", "-c", "java $JAVA_TOOL_OPTIONS -jar /app/app.jar"]
```

Notas rápidas para a VARIANTE A:
- Não há apt-get de OpenCV. As bibliotecas nativas já virão junto com a dependência do Bytedeco (ex.: org.bytedeco:opencv-platform).
- Se você usa perfis/propriedades Spring no container, pode injetar em runtime:
  - docker run -e "SPRING_PROFILES_ACTIVE=prod" ...
  - docker run -e "JAVA_TOOL_OPTIONS=-XX:MaxRAMPercentage=70 ..." ...

Como construir e executar:
- Build:
  - docker build -t suaorg/ocr-enhancer:latest .
- Run:
  - docker run --rm -p 8080:8080 suaorg/ocr-enhancer:latest
- Teste rápido (envio de imagem e filtros via curl, ajustando a URL e params do seu controller):
  - curl -X POST "http://localhost:8080/api/v1/enhance?brightness=0.1&contrast=1.2&gaussianBlur=3" \
    -H "Content-Type: multipart/form-data" \
    -F "file=@/caminho/para/imagem.png" \
    -o saida.png

Sugestão de .dockerignore (reduz contexto de build):
```
target/
.idea/
.vscode/
.git/
.gitignore
*.iml
*.log
node_modules/
```

VARIANTE B: Multi-stage com Maven + Runtime Ubuntu com OpenCV instalado via apt
- Use quando: sua política exige bibliotecas do sistema (OpenCV) instaladas no SO do container.
- Observação: você precisará garantir que seu código Java carrega a lib nativa correta. Se estiver usando wrappers que dependem da lib do sistema, talvez precise setar LD_LIBRARY_PATH.

Dockerfile:
```dockerfile
# =========================
# Stage 1: Build (Maven)
# =========================
FROM maven:3.9.6-eclipse-temurin-21 AS builder

WORKDIR /workspace

COPY pom.xml ./
RUN --mount=type=cache,target=/root/.m2 mvn -B -q -e -DskipTests dependency:go-offline

COPY src ./src
RUN --mount=type=cache,target=/root/.m2 mvn -B -q -e -DskipTests package

# =========================
# Stage 2: Runtime (Ubuntu + OpenCV nativo)
# =========================
FROM ubuntu:22.04

# Mantém a imagem mais previsível
ENV DEBIAN_FRONTEND=noninteractive

# Instala JRE e OpenCV do sistema
RUN apt-get update && apt-get install -y --no-install-recommends \
    ca-certificates curl \
    openjdk-21-jre-headless \
    libopencv-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app
COPY --from=builder /workspace/target/*.jar /app/app.jar

# Cria usuário não root
RUN groupadd -g 1001 appgrp && useradd -u 1001 -g appgrp -m appusr
USER appusr

EXPOSE 8080

# Em alguns ambientes, garantir que as libs do OpenCV sejam resolvidas:
# (dependendo do layout do Ubuntu, /usr/lib/jni já ajuda para libopencv_java*.so)
ENV LD_LIBRARY_PATH="/usr/lib/jni:/usr/lib/x86_64-linux-gnu:${LD_LIBRARY_PATH}"

ENV JAVA_TOOL_OPTIONS="-XX:+UseContainerSupport -XX:MaxRAMPercentage=75 -XX:+ExitOnOutOfMemoryError"

# HEALTHCHECK (ajuste a rota se necessário)
# HEALTHCHECK --interval=30s --timeout=3s --start-period=20s --retries=3 \
#   CMD curl -fsS http://localhost:8080/actuator/health || exit 1

ENTRYPOINT ["sh", "-c", "java $JAVA_TOOL_OPTIONS -jar /app/app.jar"]
```

Dicas de alinhamento com pipeline CI/CD
- Cache de dependências Maven:
  - O Dockerfile já usa dependency:go-offline antes de copiar src, maximizando cache de camadas em pipelines.
  - Em CI (ex.: GitHub Actions, GitLab CI), você pode também montar um cache de /root/.m2 no estágio builder para acelerar builds.
- Tags imutáveis:
  - Gere uma tag com o SHA do commit e opcionalmente uma “latest” estável:
    - suaorg/ocr-enhancer:1.2.3
    - suaorg/ocr-enhancer:git-<shortSHA>
    - suaorg/ocr-enhancer:latest
- Segurança/menor superfície:
  - Mantenha o usuário não-root (já aplicado).
  - Evite instalar ferramentas extras no runtime. Se não usar HEALTHCHECK HTTP, remova curl para reduzir imagem.
- Observabilidade:
  - Se usar Spring Boot Actuator, exponha /actuator/health e /actuator/prometheus (se tiver Micrometer/Prometheus) e mapeie no Kubernetes/Compose.
- Memória/GC:
  - JAVA_TOOL_OPTIONS já usa porcentagens amigáveis em containers. Ajuste conforme limitação de memória do orquestrador.

Troubleshooting comum
- Erro “no jnijavacpp in java.library.path” (Bytedeco):
  - Verifique se você realmente incluiu org.bytedeco:opencv-platform (a variante “platform”, não apenas “opencv”).
  - Não use Alpine/musl. Prefira Temurin jammy (Debian/Ubuntu).
- Erro de carregamento de libopencv_java*.so (Variante B):
  - Confirme se libopencv-dev instalou a lib no caminho esperado (/usr/lib/jni ou /usr/lib/x86_64-linux-gnu).
  - Exponha LD_LIBRARY_PATH adequadamente (como no Dockerfile).
- TIFF multipágina:
  - Caso use TIFF multipágina, confirme que sua pipeline de leitura/escrita está ciente disso. Se depender de plugins de ImageIO (ex.: TwelveMonkeys), apenas a dependência Java basta (não precisa apt).

Quer que eu gere um Dockerfile exatamente “plug-and-play” para o seu repositório?
- Me diga:
  1) Você usa Maven ou Gradle?
  2) O nome do jar final (se não for target/app.jar).
  3) Você usa Bytedeco opencv-platform no pom/gradle (sim/não)?
  4) Tem Actuator exposto? Qual endpoint de health?
  5) Porta e contexto da sua API (ex.: 8080, /api/v1/enhance)?
Com essas respostas, eu te devolvo um único Dockerfile, ajustado 100% ao seu projeto.
