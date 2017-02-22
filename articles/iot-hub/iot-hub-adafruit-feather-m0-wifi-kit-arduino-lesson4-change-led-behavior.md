---
title: 'Connettere Arduino (C) ad Azure IoT: lezione 4: Modificare l&quot;app | Documentazione Microsoft'
description: Personalizzare i messaggi per modificare il comportamento di accensione e spegnimento del LED.
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: controllare il LED con Arduino
ms.assetid: d7a25430-450e-43c4-a3ed-1eed995b8b7e
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/13/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 3585dfbac8816140c0a62931920aff1a6bf7d540


---
# <a name="change-the-on-and-off-behavior-of-the-led"></a>Modificare il comportamento di accensione e spegnimento del LED
## <a name="what-you-will-do"></a>Contenuto dell'esercitazione
Personalizzare i messaggi per modificare il comportamento di accensione e spegnimento del LED. In caso di problemi con la scheda Arduino per Adafruit Feather M0 WiFi, cercare le soluzioni nella [pagina sulla risoluzione dei problemi](iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md).

## <a name="what-you-will-learn"></a>Contenuto dell'esercitazione
Usare funzioni aggiuntive di Arduino per modificare il comportamento di accensione e spegnimento del LED.

## <a name="what-you-need"></a>Elementi necessari
È necessario aver completato la sezione [Eseguire l'applicazione di esempio nella scheda Arduino per ricevere messaggi da cloud a dispositivo][receive-cloud-to-device-messages].

## <a name="add-functions-to-mainc-and-gulpfilejs"></a>Aggiungere funzioni a main.c e gulpfile.js
1. Aprire l'applicazione di esempio in Visual Studio Code usando i comandi seguenti:

   ```bash
   cd Lesson4
   code .
   ```
2. Aprire il file `app.ino` e aggiungere le funzioni seguenti dopo la funzione blinkLED():

   ```arduino
   static void turnOnLED()
   {
     digitalWrite(LED_PIN, HIGH);
   }

   static void turnOffLED()
   {
     digitalWrite(LED_PIN, LOW);
   }
   ```

   ![File app.ino con funzioni aggiunte][app-ino-file]
3. Aggiungere le condizioni seguenti prima del blocco `else if` della funzione `receiveMessageCallback`:

   ```arduino
   else if (strcmp((const char*)value, "\"on\"") == 0)
   {
       turnOnLED();
   }
   else if (0 == strcmp((const char*)value, "\"off\"") == 0)
   {
       turnOffLED();
   }
   ```

   A questo punto è stata configurata l'applicazione di esempio per rispondere a più istruzioni tramite messaggi. L'istruzione "on" accende il LED, mentre l'istruzione "off" lo spegne.
4. Aprire il file gulpfile.js e quindi aggiungere una nuova funzione prima della funzione `sendMessage`:

   ```javascript
   var buildCustomMessage = function (messageId) {
     if ((messageId & 1) && (messageId < MAX_MESSAGE_COUNT)) {
       return new Message(JSON.stringify({ command: 'on', messageId: messageId }));
     } else if (messageId < MAX_MESSAGE_COUNT) {
       return new Message(JSON.stringify({ command: 'off', messageId: messageId }));
     } else {
       return new Message(JSON.stringify({ command: 'stop', messageId: messageId }));
     }
   };
   ```

   ![File gulpfile.js con funzione aggiunta][gulp-file-js]
5. Nella funzione `sendMessage` sostituire la riga `var message = buildMessage(sentMessageCount);` con la nuova riga illustrata nel frammento seguente:

   ```javascript
   var message = buildCustomMessage(sentMessageCount);
   ```
6. Salvare tutte le modifiche.

### <a name="deploy-and-run-the-sample-application"></a>Distribuire ed eseguire l'applicazione di esempio
Distribuire ed eseguire l'applicazione di esempio nella scheda Arduino usando il comando seguente:

```bash
gulp run
# You can monitor the serial port by running listen task:
gulp listen

# Or you can combine above two gulp tasks into one:
gulp run --listen
```

Il LED deve rimanere acceso per due secondi e quindi spento per altri due secondi. L'ultimo messaggio "stop" arresta l'esecuzione dell'applicazione di esempio.

![accensione e spegnimento][on-and-off]

Congratulazioni. I messaggi inviati dall'hub IoT alla scheda Arduino sono stati personalizzati.

### <a name="summary"></a>Riepilogo
Questa sezione facoltativa illustra come personalizzare i messaggi per consentire all'applicazione di esempio di controllare il comportamento di accensione e spegnimento del LED in modo diverso.

<!-- Images and links -->

[receive-cloud-to-device-messages]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson4-send-cloud-to-device-messages.md
[app-ino-file]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson4/updated_app_ino.png
[gulp-file-js]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson4/updated_gulpfile_js.png
[on-and-off]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson4/gulp_on_and_off_arduino.png


<!--HONumber=Jan17_HO4-->


