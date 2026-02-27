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

