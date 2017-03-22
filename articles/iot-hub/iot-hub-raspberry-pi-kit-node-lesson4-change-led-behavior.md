---
title: 'Connettere Raspberry Pi (Node) ad Azure IoT: lezione 4: Modificare l&quot;app | Documentazione Microsoft'
description: Personalizzare i messaggi per modificare il comportamento di accensione e spegnimento del LED.
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: LED di controllo con Raspberry Pi, LED di controllo Raspberry Pi, LED di controllo Raspberry Pi
ms.assetid: 3b42a4ad-0197-42f6-8ca9-04c883e879e8
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 53c9408bedc6c61cdb0e755b46b3090e8723e271
ms.lasthandoff: 01/24/2017


---
# <a name="change-the-on-and-off-behavior-of-the-led"></a>Modificare il comportamento di accensione e spegnimento del LED
## <a name="what-you-will-do"></a>Contenuto dell'esercitazione
Personalizzare i messaggi per modificare il comportamento di accensione e spegnimento del LED. In caso di problemi cercare le soluzioni nella pagina sulla [risoluzione dei problemi](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="what-you-will-learn"></a>Contenuto dell'esercitazione
Usare funzioni aggiuntive di Node.js per modificare il comportamento di accensione e spegnimento del LED.

## <a name="what-you-need"></a>Elementi necessari
È necessario aver completato la sezione [Eseguire l'applicazione di esempio per ricevere messaggi da cloud a dispositivo](iot-hub-raspberry-pi-kit-node-lesson4-send-cloud-to-device-messages.md).

## <a name="add-nodejs-functions"></a>Aggiungere funzioni di Node.js
1. Aprire l'applicazione di esempio in Visual Studio Code usando i comandi seguenti:
   
   ```bash
   cd Lesson4
   code .
   ```
2. Aprire il file `app.js` e quindi aggiungere le funzioni seguenti alla fine:
   
   ```javascript
   function turnOnLED() {
     wpi.digitalWrite(CONFIG_PIN, 1);
   }
   
   function turnOffLED() {
     wpi.digitalWrite(CONFIG_PIN, 0);
   }
   ```
   
   ![File app.js con funzioni aggiunte](media/iot-hub-raspberry-pi-lessons/lesson4/updated_app_js.png)
3. Aggiungere le condizioni seguenti prima di quella predefinita nel blocco switch-case della funzione `receiveMessageCallback`:
   
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
   
   ![File gulpfile.js con funzione aggiunta](media/iot-hub-raspberry-pi-lessons/lesson4/updated_gulpfile.png)
5. Nella funzione `sendMessage` sostituire la riga `var message = buildMessage(sentMessageCount);` con la nuova riga illustrata nel frammento seguente:
   
   ```javascript
   var message = buildCustomMessage(sentMessageCount);
   ```
6. Salvare tutte le modifiche.

### <a name="deploy-and-run-the-sample-application"></a>Distribuire ed eseguire l'applicazione di esempio
Distribuire ed eseguire l'applicazione di esempio nel dispositivo Pi eseguendo questo comando:

```bash
gulp deploy && gulp run
```

Il LED deve rimanere acceso per due secondi e quindi spento per altri due secondi. L'ultimo messaggio "stop" arresta l'esecuzione dell'applicazione di esempio.

![Applicazione di esempio con messaggi di accensione e spegnimento](media/iot-hub-raspberry-pi-lessons/lesson4/gulp_on_and_off.png)

Congratulazioni. I messaggi inviati dall'hub IoT al dispositivo Pi sono stati personalizzati.

### <a name="summary"></a>Riepilogo
Questa sezione facoltativa illustra come personalizzare i messaggi per consentire all'applicazione di esempio di controllare il comportamento di accensione e spegnimento del LED in modo diverso.


