---
title: 'Connettere Intel Edison (Node) ad Azure IoT: lezione 4: Far lampeggiare il LED | Documentazione Microsoft'
description: Personalizzare i messaggi per modificare il comportamento di accensione e spegnimento del LED.
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: controllare il LED con Arduino
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-intel-edison-kit-node-get-started
ms.assetid: 387cd97e-b05e-43c4-b252-f68ad45d524a
ms.service: iot-hub
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: adf5b10721a28432e6b37ef73c6a7e7ec9f93cdd
ms.openlocfilehash: 9a2c6dfe5d85a4da9714f9b8942cb2baf1a98466
ms.lasthandoff: 01/25/2017


---
# <a name="change-the-on-and-off-behavior-of-the-led"></a>Modificare il comportamento di accensione e spegnimento del LED
## <a name="what-you-will-do"></a>Contenuto dell'esercitazione
Personalizzare i messaggi per modificare il comportamento di accensione e spegnimento del LED. In caso di problemi, cercare le soluzioni nella [pagina sulla risoluzione dei problemi][troubleshooting].

## <a name="what-you-will-learn"></a>Contenuto dell'esercitazione
Usare funzioni aggiuntive per modificare il comportamento di accensione e spegnimento del LED.

## <a name="what-you-need"></a>Elementi necessari
È necessario aver completato la sezione [Eseguire l'applicazione di esempio in Intel Edison per ricevere messaggi da cloud a dispositivo][receive-cloud-to-device-messages].

## <a name="add-functions-to-appjs-and-gulpfilejs"></a>Aggiungere funzioni ad app.js e gulpfile.js
1. Aprire l'applicazione di esempio in Visual Studio Code usando i comandi seguenti:

   ```bash
   cd Lesson4
   code .
   ```
2. Aprire il file `app.js` e aggiungere le funzioni seguenti dopo la funzione blinkLED():

   ```javascript
   function turnOnLED() {
     myLed.write(1);
   }

   function turnOffLED() {
     myLed.write(0);
   }
   ```

   ![File app.js con funzioni aggiunte](media/iot-hub-intel-edison-lessons/lesson4/updated_app_node.png)
3. Aggiungere le condizioni seguenti prima di 'blink' nel blocco switch-case della funzione `receiveMessageCallback`:

   ```javascript
   case 'on':
     turnOnLED();
     break;
   case 'off':
     turnOffLED();
     break;
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
   }
   ```

   ![File gulpfile.js con funzione aggiunta][gulpfile]
5. Nella funzione `sendMessage` sostituire la riga `var message = buildMessage(sentMessageCount);` con la nuova riga illustrata nel frammento seguente:

   ```javascript
   var message = buildCustomMessage(sentMessageCount);
   ```
6. Salvare tutte le modifiche.

### <a name="deploy-and-run-the-sample-application"></a>Distribuire ed eseguire l'applicazione di esempio
Distribuire ed eseguire l'applicazione di esempio in Edison eseguendo questo comando:

```bash
gulp deploy && gulp run
```

Il LED deve rimanere acceso per due secondi e quindi spento per altri due secondi. L'ultimo messaggio "stop" arresta l'esecuzione dell'applicazione di esempio.

![accensione e spegnimento][on-and-off]

Congratulazioni. I messaggi inviati dall'hub IoT a Edison sono stati personalizzati.

### <a name="summary"></a>Riepilogo
Questa sezione facoltativa illustra come personalizzare i messaggi per consentire all'applicazione di esempio di controllare il comportamento di accensione e spegnimento del LED in modo diverso.

<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-node-troubleshooting.md
[receive-cloud-to-device-messages]: iot-hub-intel-edison-kit-node-lesson4-send-cloud-to-device-messages.md
[gulpfile]: media/iot-hub-intel-edison-lessons/lesson4/updated_gulpfile_node.png
[on-and-off]: media/iot-hub-intel-edison-lessons/lesson4/gulp_on_and_off_node.png

