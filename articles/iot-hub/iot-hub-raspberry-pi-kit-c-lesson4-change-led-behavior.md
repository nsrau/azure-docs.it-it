---
title: 'Facoltativo: Modificare il comportamento di accensione e spegnimento del LED | Documentazione Microsoft'
description: Personalizzare i messaggi per modificare il comportamento di accensione e spegnimento del LED.
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: LED di controllo con Raspberry Pi, LED di controllo Raspberry Pi, LED di controllo Raspberry Pi
ms.assetid: 0201b8ed-d5e6-4445-9a4d-1305003d1eff
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 155e5d6280d86b06b1718fc3032c2c224539183d
ms.openlocfilehash: 7f836a47c7b4d9cc27009f9e96dbefdcc5ad9219


---
# <a name="change-the-on-and-off-behavior-of-the-led"></a>Modificare il comportamento di accensione e spegnimento del LED
## <a name="what-you-will-do"></a>Contenuto dell'esercitazione
Personalizzare i messaggi per modificare il comportamento di accensione e spegnimento del LED. In caso di problemi, cercare le soluzioni nella pagina sulla [risoluzione dei problemi](iot-hub-raspberry-pi-kit-c-troubleshooting.md).

## <a name="what-you-will-learn"></a>Contenuto dell'esercitazione
Usare funzioni aggiuntive di Node.js per modificare il comportamento di accensione e spegnimento del LED.

## <a name="what-you-need"></a>Elementi necessari
È necessario aver completato la sezione [Eseguire l'applicazione di esempio per ricevere messaggi da cloud a dispositivo](iot-hub-raspberry-pi-kit-c-lesson4-send-cloud-to-device-messages.md).

## <a name="add-functions-to-mainc-and-gulpfilejs"></a>Aggiungere funzioni a main.c e gulpfile.js
1. Aprire l'applicazione di esempio in Visual Studio Code usando i comandi seguenti:

   ```bash
   cd Lesson4
   code .
   ```
2. Aprire il file `main.c` e aggiungere le funzioni seguenti dopo la funzione blinkLED():

   ```c
   static void turnOnLED()
   {
     digitalWrite(LED_PIN, HIGH);
   }

   static void turnOffLED()
   {
     digitalWrite(LED_PIN, LOW);
   }
   ```

   ![File main.c con funzioni aggiunte](media/iot-hub-raspberry-pi-lessons/lesson4/updated_app_c.png)
3. Aggiungere le condizioni seguenti prima di quella predefinita nel blocco `if` della funzione `receiveMessageCallback`:

   ```c
   else if (0 == strcmp((const char*)value, "\"on\""))
   {
       turnOnLED();
   }
   else if (0 == strcmp((const char*)value, "\"off\""))
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
   }
   ```

   ![File gulpfile.js con funzione aggiunta](media/iot-hub-raspberry-pi-lessons/lesson4/updated_gulpfile_c.png)
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

![Applicazione di esempio con messaggi di accensione e spegnimento](media/iot-hub-raspberry-pi-lessons/lesson4/gulp_on_and_off_c.png)

Congratulazioni. I messaggi inviati dall'hub IoT al dispositivo Pi sono stati personalizzati.

### <a name="summary"></a>Riepilogo
Questa sezione facoltativa illustra come personalizzare i messaggi per consentire all'applicazione di esempio di controllare il comportamento di accensione e spegnimento del LED in modo diverso.



<!--HONumber=Dec16_HO1-->


