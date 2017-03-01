---
title: Creare il primo modulo di Azure IoT Gateway | Microsoft Docs
description: Creare un modulo e aggiungerlo a un&quot;app di esempio per personalizzare i comportamenti del modulo.
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: 
ms.assetid: cd7660f4-7b8b-4091-8d71-bb8723165b0b
ms.service: iot-hub
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/17/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 6b12d39ce8c6062df5fb78de654bf22183b6ecd9
ms.openlocfilehash: 42c22192bc3cccdea97f7d8dac575fa7cbc9a6c6
ms.lasthandoff: 02/21/2017


---
# <a name="lesson-5-create-your-first-azure-iot-gateway-module"></a>Lezione 5: creare il primo modulo di Azure IoT Gateway

## <a name="what-you-will-do"></a>Contenuto dell'esercitazione

- Compilare ed eseguire l'app di esempio hello_world in Intel NUC.
- Creare un modulo e compilarlo in Intel NUC.
- Aggiungere il nuovo modulo all'app di esempio hello_world ed eseguire l'esempio in Intel NUC. Il nuovo modulo stampa i messaggi "hello_world" con un timestamp.

## <a name="what-you-will-learn"></a>Concetti legati all'esercitazione

- Come compilare ed eseguire un'app di esempio in Intel NUC.
- Come creare un modulo.
- Come aggiungere un modulo a un'app di esempio.

## <a name="what-you-need"></a>Elementi necessari

L'SDK di Azure IoT Gateway che è stato installato nel computer host.

## <a name="folder-structure"></a>Struttura di cartelle

Nella sottocartella Lezione 5 del codice di esempio, che è stato clonato nella lezione 1, si trovano una cartella `module` e una cartella `sample`.

![my_module](media/iot-hub-gateway-kit-lessons/lesson5/my_module.png)

- La cartella `module/my_module` contiene il codice sorgente e lo script per compilare il modulo.
- La cartella `sample` contiene il codice sorgente e lo script per compilare l'app di esempio.

## <a name="compile-and-run-the-helloworld-sample-app-on-intel-nuc"></a>Compilare ed eseguire l'app di esempio hello_world in Intel NUC

L'esempio `hello_world` crea un gateway basato sul file `hello_world.json` che specifica i due moduli predefiniti associati all'app. Il gateway registra un messaggio "hello world" in un file ogni 5 secondi. In questa sezione si descrive come compilare ed eseguire l'app `hello_world` con il relativo modulo predefinito.

Per compilare ed eseguire l'app `hello_world`, seguire questi passaggi nel computer host:

1. Inizializzare i file di configurazione eseguendo i comandi seguenti:

   ```bash
   cd iot-hub-c-intel-nuc-gateway-getting-started
   cd Lesson5
   npm install
   gulp init
   ```

1. Aggiornare il file di configurazione del gateway con l'indirizzo MAC di Intel NUC. Ignorare questo passaggio se è già stata consultata la lezione su come [configurare ed eseguire un'applicazione di esempio BLE][config_ble].

   1. Aprire il file di configurazione del gateway eseguendo il comando seguente:

      ```bash
      # For Windows command prompt
      code %USERPROFILE%\.iot-hub-getting-started\config-gateway.json

      # For MacOS or Ubuntu
      code ~/.iot-hub-getting-started/config-gateway.json
      ```

   1. Aggiornare l'indirizzo MAC del gateway quando si deve [configurare Intel NUC come gateway IoT][setup_nuc] e salvare il file.

1. Compilare il codice sorgente di esempio eseguendo il comando seguente:

   ```bash
   gulp compile
   ```

   Il comando trasferisce il codice sorgente di esempio a Intel NUC ed esegue `build.sh` per compilarlo.

1. Eseguire l'app `hello_world` in Intel NUC eseguendo il comando seguente:

   ```bash
   gulp run
   ```

   Il comando esegue `../Tools/run-hello-world.js` specificato in `config.json` per avviare l'app di esempio in Intel NUC.

   ![run_sample](media/iot-hub-gateway-kit-lessons/lesson5/run_sample.png)

## <a name="create-a-new-module-and-compile-it-on-intel-nuc"></a>Creare un nuovo modulo e compilarlo in Intel NUC

I passaggi seguenti illustrano come creare un nuovo modulo e compilarlo in Intel NUC. Il modulo stampa i messaggi con un timestamp al momento della loro ricezione. In questa sezione è possibile creare il primo modulo di gateway personalizzato.

Tutti i moduli dell'SDK di Azure IoT Gateway devono implementare le interfacce seguenti:

   ```C
   pfModule_ParseConfigurationFromJson Module_ParseConfigurationFromJson
   pfModule_FreeConfiguration Module_FreeConfiguration
   pfModule_Create Module_Create
   pfModule_Destroy Module_Destroy
   pfModule_Receive Module_Receive
   ```

Facoltativamente è possibile implementare l'interfaccia seguente:

   ```C
   pfModule_Start Module_Start
   ```

Il diagramma seguente mostra i percorsi di stato fondamentali di un modulo. I rettangoli rappresentano i metodi implementati per eseguire operazioni quando il modulo si sposta tra stati diversi. Gli ovali sono gli stati principali nei quali si può trovare il modulo.

![state_path](media/iot-hub-gateway-kit-lessons/lesson5/state_path.png)

A questo punto si può creare un modulo basato sul modello:

1. Aprire la cartella del modello eseguendo il comando seguente:

   ```bash
   code module/my_module
   ```

   ![code_module](media/iot-hub-gateway-kit-lessons/lesson5/code_module.png)

   - `src/my_module.c` funge da modello per facilitare la creazione di un modulo. Il modello dichiara le interfacce. È sufficiente aggiungere la logica alla funzione `MyModule_Receive`.
   - `build.sh` è lo script di compilazione necessario per compilare il modulo in Intel NUC .
1. Aprire il file `src/my_module.c` e includere due file di intestazione:

   ```C
   #include <stdio.h>
   #include "azure_c_shared_utility/xlogging.h"
   ```

1. Aggiungere il codice seguente alla funzione `MyModule_Receive`:

   ```C
   if (message == NULL)
   {
      LogError("invalid arg message");
   }
   else
   {
      // get the message content
      const CONSTBUFFER * content = Message_GetContent(message);
      // get the local time and format it
      time_t temp = time(NULL);
      if (temp == (time_t)-1)
      {
          LogError("time function failed");
      }
      else
      {
          struct tm* t = localtime(&temp);
          if (t == NULL)
          {
              LogError("localtime failed");
          }
          else
          {
              char timetemp[80] = { 0 };
              if (strftime(timetemp, sizeof(timetemp) / sizeof(timetemp[0]), "%c", t) == 0)
              {
                  LogError("unable to strftime");
              }
              else
              {
                  printf("[%s] %.*s\r\n", timetemp, (int)content->size, content->buffer);
              }
          }
      }
   }
   ```

1. Aggiornare il file `config.json` per specificare la cartella `workspace` nel computer host e il percorso di distribuzione in Intel NUC. Durante la compilazione, i file della cartella `workspace` verranno trasferiti al percorso di distribuzione.

   1. Aprire il file `config.json` eseguendo il comando seguente:

      ```bash
      code config.json
      ```

   1. Aggiornare `config.json` con la configurazione seguente:

      ```json
      "workspace": "./module/my_module",
      "deploy_path": "module/my_module"
      ```

      ![config_json](media/iot-hub-gateway-kit-lessons/lesson5/config_json.png)

1. Compilare il modulo eseguendo il comando seguente:

   ```bash
   gulp compile
   ```

   Il comando trasferisce il codice sorgente in Intel NUC ed esegue `build.sh` per compilare il modulo.

## <a name="add-the-module-to-the-helloworld-sample-app-and-run-the-app-on-intel-nuc"></a>Aggiungere il modulo all'app di esempio hello_world ed eseguire l'app in Intel NUC

Per eseguire questa attività seguire questi passaggi:

1. Elencare tutti i file binari disponibili del modulo (file con estensione .so) in Intel NUC eseguendo il comando seguente:

   ```bash
   gulp modules --list
   ```

   Il percorso binario di `my_module` che è stato compilato dovrebbe essere elencato come indicato di seguito:

   ```path
   /root/gateway_sample/module/my_module/build/libmy_module.so
   ```

   Se si modifica il nome utente di accesso predefinito in `config-gateway.json`, il percorso binario inizierà con `home/<your username>` anziché con `root`.

1. Aggiungere `my_module` all'app di esempio `hello_world`:

   1. Aprire il file `hello_world.json` eseguendo il comando seguente:

      ```bash
      code sample/hello_world/src/hello_world.json
      ```

   1. Aggiungere il codice seguente alla sezione `modules`:

      ```json
      {
       "name": "my_module",
       "loader": {
       "name": "native",
       "entrypoint": {
       "module.path": "/root/gateway_sample/module/my_module/build/libmy_module.so"
         }
        },
       "args": null
      }
      ```

      Il valore di `module.path` dovrebbe essere `/root/gateway_sample/module/my_module/build/libmy_module.so`. Il codice dichiara `my_module` da associare al gateway e la posizione del file binario del modulo specificato in `module.path`.
   1. Aggiungere il codice seguente alla sezione `links`:

      ```json
      {
        "source": "hello_world",
        "sink": "my_module"
      }
      ```

      Questo codice specifica che i messaggi vengono trasferiti dal modulo `hello_world` a `my_module`.

      ![hello_world_json](media/iot-hub-gateway-kit-lessons/lesson5/hello_world_json.png)

1. Eseguire l'app di esempio `hello_world` eseguendo il comando seguente:

   ```bash
   gulp run --config sample/hello_world/src/hello_world.json
   ```

   Il parametro `--config` forza l'esecuzione dello script `run-hello-world.js` usando il file `hello_world.json`.

   ![hello_world_new](media/iot-hub-gateway-kit-lessons/lesson5/hello_world_new.png)

A questo punto È ora possibile visualizzare il comportamento di questo nuovo modulo, che stampa semplicemente i messaggi "hello world" con un timestamp, con un risultato diverso dal modulo "hello_world" originale.

## <a name="next-steps"></a>Passaggi successivi

È stato creato un nuovo modulo, che è poi stato aggiunto all'esempio hello_world e l'app di esempio viene eseguita con il nuovo modulo sul gateway. Per altre informazioni sui moduli di Azure IoT Gateway è possibile trovare altri esempi di modulo in: [https://github.com/Azure/azure-iot-gateway-sdk/tree/master/modules](https://github.com/Azure/azure-iot-gateway-sdk/tree/master/modules).

<!-- Images and links -->

[config_ble]: iot-hub-gateway-kit-c-lesson3-configure-ble-app.md
[setup_nuc]: iot-hub-gateway-kit-c-lesson1-set-up-nuc.md

