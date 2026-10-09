# Recibir las respuestas del cuestionario en Google Sheets

La landing es una página estática (GitHub Pages) y no tiene servidor propio. Para **recolectar** las respuestas del cuestionario de acceso se usa una hoja de cálculo de Google con un pequeño script. Mientras no se configure, las respuestas **no se envían a ningún lado**: solo se guarda en el navegador de quien responde que ya completó el cuestionario.

## Pasos (10 minutos, con la cuenta centrodeinnovacioncomunitaria@gmail.com)

1. Crear una hoja de cálculo nueva en Google Drive, por ejemplo «Cuestionario UMC».
2. En la hoja: **Extensiones → Apps Script**. Borrar lo que aparece y pegar el código de abajo. Guardar.
3. **Implementar → Nueva implementación → Tipo: Aplicación web**.
   - Ejecutar como: **Yo**.
   - Quién tiene acceso: **Cualquier usuario**.
   - Autorizar los permisos que pide Google.
4. Copiar la **URL de la aplicación web** (termina en `/exec`).
5. Pegarla en `assets/umc-config.js`, en `cuestionarioUrl: '...'`, y publicar el cambio (`git commit` y `git push`).

Cada respuesta aparecerá como una fila nueva en la hoja.

## Código para Apps Script

```javascript
const COLUMNAS = ['fecha', 'nombre', 'satelite', 'expectativas', 'aprendizaje', 'tiempo', 'percepcion', 'comentario', 'inversion', 'autoriza'];

function doPost(e) {
  const hoja = SpreadsheetApp.getActiveSpreadsheet().getSheets()[0];
  if (hoja.getLastRow() === 0) hoja.appendRow(COLUMNAS);
  const d = JSON.parse(e.postData.contents);
  hoja.appendRow(COLUMNAS.map((c) => (c in d ? String(d[c]).slice(0, 500) : '')));
  return ContentService.createTextOutput('ok');
}
```

## Qué se pregunta

Identificación: nombre y apellido, satélite, confirmación de inscripción en el CIC y autorización de tratamiento de datos (Ley 1581 de 2012).

1. Expectativas frente a la UMC (hasta dos opciones).
2. Intereses de aprendizaje (hasta dos opciones).
3. Tiempo disponible.
4. Percepción de la idea (y comentario opcional).
5. Capacidad de ahorro o inversión inicial por quincena.

## Límites a tener en cuenta

- El acceso a los materiales se controla en el navegador. Sirve para ordenar el proceso y recoger información, pero no es un control de seguridad: quien conozca la dirección de un PDF puede abrirlo.
- La página no verifica la inscripción ni la aceptación en el satélite; la emprendedora lo confirma con una casilla. La verificación la hace el equipo del CIC con la hoja de respuestas.
