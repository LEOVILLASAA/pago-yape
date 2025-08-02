<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Activación de Pago QR - Frenos Automotriz LVS</title>

  <!-- PDF.js -->
  <script src="https://cdnjs.cloudflare.com/ajax/libs/pdf.js/3.11.174/pdf.min.js"></script>
  <script>
    pdfjsLib.GlobalWorkerOptions.workerSrc = 'https://cdnjs.cloudflare.com/ajax/libs/pdf.js/3.11.174/pdf.worker.min.js';
  </script>

  <style>
    body {
      font-family: Arial, sans-serif;
      max-width: 600px;
      margin: 0 auto;
      padding: 20px;
      background-color: #f5f5f5;
    }
    .container {
      background-color: white;
      padding: 30px;
      border-radius: 10px;
      box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
    }
    .header {
      text-align: center;
      color: #333;
      margin-bottom: 30px;
    }
    .qr-container {
      text-align: center;
      margin: 30px 0;
    }
    .qr-code {
      max-width: 300px;
      height: auto;
      border: 2px solid #ddd;
      border-radius: 10px;
    }
    .payment-info {
      background-color: #f8f9fa;
      padding: 20px;
      border-radius: 8px;
      margin: 20px 0;
    }
    .info-row {
      display: flex;
      justify-content: space-between;
      margin: 10px 0;
      padding: 5px 0;
      border-bottom: 1px solid #eee;
    }
    .total {
      font-size: 1.2em;
      font-weight: bold;
      color: #007bff;
    }
    .activate-btn {
      background-color: #28a745;
      color: white;
      padding: 15px 30px;
      border: none;
      border-radius: 5px;
      font-size: 1.1em;
      cursor: pointer;
      width: 100%;
      margin-top: 20px;
    }
    .activate-btn:hover {
      background-color: #218838;
    }
    .status {
      text-align: center;
      padding: 10px;
      margin: 20px 0;
      border-radius: 5px;
      font-weight: bold;
    }
    .status.pending {
      background-color: #fff3cd;
      color: #856404;
      border: 1px solid #ffeaa7;
    }
    .status.active {
      background-color: #d4edda;
      color: #155724;
      border: 1px solid #c3e6cb;
    }
    .upload-section {
      margin-bottom: 20px;
      text-align: center;
    }
    .upload-label {
      display: inline-block;
      padding: 10px 20px;
      background-color: #007bff;
      color: white;
      border-radius: 5px;
      cursor: pointer;
      margin-top: 10px;
    }
    .file-input {
      display: none;
    }
    .instructions {
      margin-top: 30px;
      font-size: 14px;
      color: #333;
    }
  </style>
</head>
<body>
  <div class="container">
    <div class="header">
      <h1>Frenos Automotriz LVS</h1>
      <h2>Activación de Pago QR</h2>
    </div>

    <div class="upload-section">
      <input type="file" id="file-input" class="file-input" accept=".pdf" onchange="handleFileUpload(event)">
      <label for="file-input" class="upload-label">📄 Seleccionar Archivo PDF</label>
      <p style="margin-top: 10px; color: #666; font-size: 14px;">
        Sube un PDF que contenga el monto a pagar.
      </p>
    </div>

    <div class="payment-info">
      <h3>Información del Comercio</h3>
      <div class="info-row"><span>Comercio:</span><span>YAPERO</span></div>
      <div class="info-row"><span>Ciudad:</span><span>Lima</span></div>
      <div class="info-row"><span>País:</span><span>PE</span></div>
      <div class="info-row total" id="monto-detectado" style="display:none;">
        <span>Total Detectado:</span><span id="monto-texto"></span>
      </div>
    </div>

    <div class="qr-container">
      <h3>Código QR para Pago</h3>
      <img src="data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAcIAAAHCAQAAAABUY/ToAAADdElEQVR4nO2cyY3bQBBFf5kEdCQBBzChkBk4JMMhOQMyFAcwQPMogI3vQ/UmjsYHWwNp5F8HittDt4BCrd004u9k/fKXICBSpEiRIkWKFPl4pCXpgXUEAEQzG9M7/gBbfmu+yZgin4ycSJIBsBnRbEZHLogGbGYkz+keSV6S95ityIci4WoxBQBT6MgFABk6YgodgYF+5veAjq5wy+f6nyI/juwP14bhtQeGHVxfdnD9FnuuL2fD9PNWY4p8LvKoQ8R2oqUz80ukw63GFPlcZPZlHg8lD5YcGnc/YOKePF19Wb5M5CW5mrWJ2PfQ0ebt5IdkhxA9Lbv/bEU+FOm+rDY8COzA+rKD2KJ5KAREA4bLtsjn+p8iP45Ek65PAYBnXgHwwHpBx3RWDpQvE9lK0gh05DLQk/mc73c8ZPnSIZFXJGnEwBRJL0MKoslQNQwAoJha5FWpvqxWG8mAXHJ0lcoPlkE6JPIobCQrTWubBrah9BRUpxZ5lMYOJYfWZbMUikUqWqZ4SOR7pEfNm5mHQkWRvI2/jjF19YGONt97tiIfisz1oQ2wibE3oNsNw6u5Ek3+E4FpiX6P95utyEck0fioBTWZz66N3AEMO5AKR4qHRB7lEA81bTGghNh+TzVGkVelyegXoGmyekzd5mXJBMkOibyUpk7t6hPyGjSgOLQANKVs6ZDIC2kq0am5UZZ9VEUqaZriIZFvpcY+STdQTdB+UYFUfUjkdcnxUNeEPTlDa+Oh1DSTLxP5HjmcjWXhos3D2bCOuZ5Y1zEutxxT5HOQjbdCMUE5wd+b6lHd+iE7JPJSmvqQS7Nses9KE7rSglU8JPIoTTxUrE9tmlXN8ZcX7S8TeZS8N2g4G9LaaYDYxnR/CoBNAQS2r7thCP8+psjnJLcTyV+nZIeQFzO6eN9+jIbpV38k7zFbkY9EHitA3s1AXonvy/HLIqJ0KV8m8g15+O4HF0Tjsp1SmraOHYGt9xDb5jvPVuRjkcd9rnkzUM3V9iajV41R5BXJeRmaPD41XovSIOdl9YF0SGSRow61+8tQVoGk1kd9Kh0S+Qcyms2Ixh9jcm02IxrWFzJ/5urmY4r8xGSpDxHp6zBb7/Uhm8JoXL8FAMPe2xRiD9WHRL6V2utAyehrlyzvOWPTy1dMLfJCTN84FylSpEiRIkX+5+RvggPJAB+uTPoAAAAASUVORK5CYII=" alt="Código QR de Pago" class="qr-code
">

      <div id="status" class="status pending">
        Estado: Pendiente de Activación
      </div>
      <button class="activate-btn" onclick="activatePayment()">Activar Pago Automático</button>
    </div>

    <div class="instructions">
      <h4>📋 Instrucciones de Uso</h4>
      <ol>
        <li>Sube un PDF con el monto</li>
        <li>Haz clic en “Activar Pago Automático”</li>
        <li>Escanea el QR con tu app de pagos</li>
      </ol>
    </div>
  </div>

  <script>
    let extractedAmount = null;

    function showStatus(message, type = 'pending') {
      const status = document.getElementById('status');
      status.textContent = message;
      status.className = `status ${type}`;
    }

    async function handleFileUpload(event) {
      const file = event.target.files[0];
      if (!file || file.type !== 'application/pdf') {
        showStatus('Por favor, sube un archivo PDF válido.', 'pending');
        return;
      }

      const reader = new FileReader();
      reader.onload = async function () {
        try {
          const typedarray = new Uint8Array(reader.result);
          const pdf = await pdfjsLib.getDocument(typedarray).promise;
          let fullText = '';

          for (let i = 1; i <= pdf.numPages; i++) {
            const page = await pdf.getPage(i);
            const textContent = await page.getTextContent();
            const pageText = textContent.items.map(item => item.str).join(' ');
            fullText += ' ' + pageText;
          }

          console.log('Texto completo del PDF:', fullText);

          // Expresión regular flexible para detectar el monto
          const montoRegex = /(total|importe|monto|pagar)[^\d]{0,10}([sS]\/?\s*\.?\s*\d{1,3}(?:[\.,]\d{3})*)/i;
          const match = fullText.match(montoRegex);

          if (match) {
            const rawAmount = match[2].replace(/[^\d,\.]/g, '').replace(/\./g, '').replace(',', '.');
            extractedAmount = parseFloat(rawAmount);

            showStatus(`Monto detectado: S/ ${extractedAmount.toFixed(2)}`, 'pending');

            // Mostrar monto visualmente
            document.getElementById('monto-texto').textContent = `S/ ${extractedAmount.toFixed(2)}`;
            document.getElementById('monto-detectado').style.display = 'flex';
          } else {
            extractedAmount = null;
            showStatus('⚠️ No se detectó monto en el PDF.', 'pending');
            document.getElementById('monto-detectado').style.display = 'none';
          }
        } catch (error) {
          console.error('Error procesando el PDF:', error);
          showStatus('❌ Error al procesar el PDF.', 'pending');
        }
      };
      reader.readAsArrayBuffer(file);
    }

    function activatePayment() {
      const button = document.querySelector('.activate-btn');

      if (!extractedAmount) {
        alert('⚠️ Sube un PDF con monto antes de activar el pago.');
        return;
      }

      button.textContent = 'Activando...';
      button.disabled = true;

      setTimeout(() => {
        showStatus(`Estado: Pago Activado - Monto: S/ ${extractedAmount.toFixed(2)}`, 'active');
        button.textContent = 'Pago Activado ✓';
        button.style.backgroundColor = '#28a745';

        console.log('Pago QR activado:', {
          comercio: 'YAPERO',
          monto: extractedAmount,
          moneda: 'PEN',
          fecha: new Date().toISOString()
        });

        alert(`✅ Pago QR activado por S/ ${extractedAmount.toFixed(2)}. Escanea el código con tu app de pagos.`);
      }, 2);
    }
  </script>
</body>
</html>
