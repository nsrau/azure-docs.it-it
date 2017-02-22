---
title: 'Dispositivo SensorTag e gateway Azure IoT: lezione 3: Leggere i messaggi | Documentazione Microsoft'
description: Eseguire un codice di esempio sul computer host per leggere i messaggi dall&quot;hub IoT.
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: dati nel cloud, raccolta dati nel cloud, servizio cloud iot, dati iot
ms.assetid: cc88be24-b5c0-4ef2-ba21-4e8f77f3e167
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: 0a5dd7e010ba46b724bb6a2526dc2a2730acc10f


---

# <a name="read-messages-from-your-iot-hub"></a>Leggere i messaggi dall'hub IoT

## <a name="what-you-will-do"></a>Contenuto dell'esercitazione

- Eseguire il codice di esempio sul computer host per leggere i messaggi dall'hub IoT.

In caso di problemi, cercare le soluzioni nella pagina sulla [risoluzione dei problemi](iot-hub-gateway-kit-c-troubleshooting.md).

## <a name="what-you-will-learn"></a>Contenuto dell'esercitazione

Come usare lo strumento gulp per leggere i messaggi dall'hub IoT.

## <a name="what-you-need"></a>Elementi necessari

- L'applicazione di esempio BLE eseguita nella lezione 3.

## <a name="get-your-iot-hub-and-device-connection-strings"></a>Ottenere le stringhe di connessione dell'hub IoT e del dispositivo

La stringa di connessione del dispositivo viene usata dal dispositivo (TI SensorTag o dispositivo simulato) per la connessione all'hub IoT. La stringa di connessione all'hub IoT viene usata per la connessione al registro di identità nell'hub IoT per gestire i dispositivi a cui è consentito connettersi all'hub IoT.

- Elencare tutti gli hub IoT nel gruppo di risorse usando il comando seguente:

   ```bash
   az iot hub list -g iot-gateway --query [].name
   ```

   Usare `iot-gateway` come valore di `{resource group name}` se non si è modificato il valore.
- Ottenere la stringa di connessione all'hub IoT usando il comando seguente:

   ```bash
   az iot hub show-connection-string --name {my hub name} -g iot-gateway
   ```

   `{my hub name}` è il nome specificato nella lezione 2.

## <a name="configure-the-device-connection-for-the-sample-code"></a>Configurare la connessione al dispositivo per il codice di esempio.

Aggiornare il file di configurazione del dispositivo `config-azure.json` per poter leggere i messaggi dall'hub IoT nel computer host. A questo scopo, seguire questa procedura:

1. Aprire `config-azure.json` in Visual Studio Code usando il comando seguente in una finestra della console:

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-azure.json
   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-azure.json
   ```

2. Sostituire i valori seguenti nel file `config-azure.json`:

   ![Screenshot della configurazione di Azure](media/iot-hub-gateway-kit-lessons/lesson3/config_azure.png)

   Sostituire `[IoT hub connection string]` con la stringa di connessione all'hub IoT ottenuta.

## <a name="read-messages-from-your-iot-hub"></a>Leggere messaggi dall'hub IoT

Se si ha un dispositivo TI SensorTag, verificare di averlo già acceso. Eseguire l'applicazione di esempio gateway e leggere i messaggi dell'hub IoT usando il comando seguente:

```bash
gulp run --iot-hub
```

Il commando esegue l'applicazione di esempio BLE che legge e comprime i dati sulla temperatura da SensorTag o dal dispositivo simulato e invia il messaggio all'hub IoT ogni 2 secondi. Genera anche un processo figlio per ricevere il messaggio.

I messaggi inviati e ricevuti vengono visualizzati tutti immediatamente nella stessa finestra della console nel computer host. L'istanza dell'applicazione di esempio terminerà automaticamente in 40 secondi.

![Applicazione di esempio BLE con messaggi inviati e ricevuti](media/iot-hub-gateway-kit-lessons/lesson3/gulp_run_read_hub.png)

## <a name="summary"></a>Riepilogo

È stato eseguito un codice di esempio per leggere i messaggi dall'hub IoT. Ora è possibile leggere i messaggi salvati nell'archivio tabelle di Azure.

## <a name="next-steps"></a>Passaggi successivi
[Creare un'app per le funzioni di Azure e un account di Archiviazione di Azure](iot-hub-gateway-kit-c-lesson4-deploy-resource-manager-template.md)





<!--HONumber=Jan17_HO4-->


