---
title: 'Connettere Intel Edison (C) ad Azure IoT: lezione 4: Far lampeggiare il LED | Documentazione Microsoft'
description: Personalizzare i messaggi per modificare il comportamento di accensione e spegnimento del LED.
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: controllare il LED con Arduino
ms.assetid: 9826c55a-0e24-4296-ae54-29b7fe66436a
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/8/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 475b25f02715a60493e79ecd2170854019dfc4ac
ms.openlocfilehash: 278bdf74e2fa8f7074bb8f5ed8eae2d47402b299


---
# <a name="change-the-on-and-off-behavior-of-the-led"></a>Modificare il comportamento di accensione e spegnimento del LED
## <a name="what-you-will-do"></a>Contenuto dell'esercitazione
Personalizzare i messaggi per modificare il comportamento di accensione e spegnimento del LED. In caso di problemi, cercare le soluzioni nella [pagina sulla risoluzione dei problemi][troubleshooting].

## <a name="what-you-will-learn"></a>Contenuto dell'esercitazione
Usare funzioni aggiuntive per modificare il comportamento di accensione e spegnimento del LED.

## <a name="what-you-need"></a>Elementi necessari
È necessario aver completato la sezione [Eseguire l'applicazione di esempio in Intel Edison per ricevere messaggi da cloud a dispositivo][receive-cloud-to-device-messages].

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
     mraa_gpio_write(context, 1);
   }

   static void turnOffLED()
   {
     mraa_gpio_write(context, 0);
   }
   ```

   ![File main.c con funzioni aggiunte](media/iot-hub-intel-edison-lessons/lesson4/updated_app_c.png)

3. Aggiungere le condizioni seguenti prima del blocco `else if` della funzione `receiveMessageCallback`:

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

[troubleshooting]: iot-hub-intel-edison-kit-c-troubleshooting.md
[receive-cloud-to-device-messages]: iot-hub-intel-edison-kit-c-lesson4-send-cloud-to-device-messages.md
[gulpfile]: media/iot-hub-intel-edison-lessons/lesson4/updated_gulpfile_c.png
[on-and-off]: media/iot-hub-intel-edison-lessons/lesson4/gulp_on_and_off_c.png



<!--HONumber=Jan17_HO4-->


