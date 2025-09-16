# Psicopiloto - Proyecto listo para desplegar (Next.js + Tailwind)

Este repositorio contiene una web sencilla y visual para Psicopiloto con 5 páginas separadas y un formulario funcional que envía a tu correo (Formspree) y guarda los envíos en Google Sheets (vía Google Apps Script webhook).

## Pasos rápidos
1. Subir el proyecto a GitHub.
2. Desplegar en Vercel (conectar el repo).
3. Configurar variables de entorno en Vercel: FORMSPREE_URL y GAS_URL.
4. Configurar Formspree y Google Sheets (instrucciones abajo).

## Google Apps Script (código para guardar en Google Sheets)
1. Crea una Google Sheet nueva.
2. Extensiones > Apps Script > crea un nuevo proyecto y pega este código en Code.gs:

function doPost(e){
  try{
    var ss = SpreadsheetApp.getActiveSpreadsheet();
    var sheet = ss.getSheetByName('Submissions') || ss.getSheets()[0];
    var body = JSON.parse(e.postData.contents);
    var nombre = body.nombre || '';
    var edad = body.edad || '';
    var email = body.email || '';
    var telefono = body.telefono || '';
    var motivo = body.motivo || '';
    var timestamp = body.timestamp || new Date().toISOString();

    sheet.appendRow([timestamp, nombre, edad, email, telefono, motivo]);
    return ContentService.createTextOutput(JSON.stringify({ok:true})).setMimeType(ContentService.MimeType.JSON);
  }catch(err){
    return ContentService.createTextOutput(JSON.stringify({ok:false, error: String(err)})).setMimeType(ContentService.MimeType.JSON);
  }
}

3. Deploy > New deployment > Select type: Web app
   - Execute as: Me
   - Who has access: Anyone
4. Copia la URL /exec y pégala en Vercel como GAS_URL

## Formspree
- Crea cuenta en https://formspree.io/
- Crea un formulario y copia el endpoint (https://formspree.io/f/xxxxxx)
- Pégalo en Vercel como FORMSPREE_URL

## Variables de entorno en Vercel
- FORMSPREE_URL = https://formspree.io/f/xxxxxx
- GAS_URL = https://script.google.com/macros/s/XXXX/exec

## Notas
- Cambia imágenes y textos en /pages y /components cuando lo necesites.
- Si aparece error 'FORMSPREE_URL no configurado' o 'GAS_URL no configurado', revisa las variables en Vercel.
