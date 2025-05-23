<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Lector NFC Festival</title>
  <script defer>
    let tipoConsumo = "";
    async function leerChipNFC(tipo) {
      tipoConsumo = tipo;
      if ('NDEFReader' in window) {
        const ndef = new NDEFReader();
        try {
          await ndef.scan();
          log(`Escaneando (${tipo})... toca un chip NFC`);
          ndef.onreading = event => {
            const uid = event.serialNumber;
            log(`UID leído: ${uid}`);
            consultarUsuario(uid, tipo);
          };
        } catch (error) {
          log("Error al escanear NFC: " + error);
        }
      } else {
        log("NFC no soportado en este dispositivo o navegador.");
      }
    }
    function log(texto) {
      document.getElementById("output").textContent = texto;
    }
    async function consultarUsuario(uid, tipo) {
      const url = `https://script.google.com/macros/s/AKfycbyJm3pM9GX0cJe4FDVdQth96ZWI36jGFxco1v7fxDkbD-ZH3cv1BYcs6H32wz_mE-LrHw/exec?uid=${uid}&tipo=${tipo}`;
      try {
        const respuesta = await fetch(url);
        const datos = await respuesta.json();
        if (datos.ok) {
          log(`Nombre: ${datos.nombre}\nSaldo +18: ${datos.mayor}\nSaldo menor: ${datos.menor}`);
        } else {
          log("Usuario no encontrado");
        }
      } catch (error) {
        log("Error al consultar usuario: " + error);
      }
    }
  </script>
  <style>
    body { font-family: sans-serif; text-align: center; padding: 2em; }
    button { padding: 1em; font-size: 1.2em; margin: 0.5em; border-radius: 8px; }
    #output { margin-top: 2em; font-size: 1.2em; white-space: pre-line; }
  </style>
</head>
<body>
  <h1>Lector NFC - Festival</h1>
  <button onclick="leerChipNFC('mayor')">Leer +18</button>
  <button onclick="leerChipNFC('menor')">Leer menor</button>
  <div id="output"></div>
</body>
</html>
