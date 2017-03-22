---
title: 'Dispositivo simulato e gateway Azure IoT: lezione 4: Archivio tabelle | Documentazione Microsoft'
description: Salvare i messaggi da Intel NUC all&quot;hub IoT, scriverli nell&quot;archivio tabelle di Azure e quindi leggerli dal cloud.
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: recuperare dati dal cloud, servizio cloud iot
ms.assetid: 78e4b6ea-968d-401e-a7dc-8f9acdb3ec1a
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: b12e16a5a532448cf2e939cfcad322225b9ee811
ms.lasthandoff: 01/25/2017


---

# <a name="read-messages-persisted-in-azure-table-storage"></a>Leggere i messaggi con salvataggio permanente nell'archivio tabelle di Azure

## <a name="what-you-will-do"></a>Contenuto dell'esercitazione

- Eseguire l'applicazione di esempio gateway nel gateway che invia messaggi all'hub IoT.
- Eseguire il codice di esempio nel computer host per leggere i messaggi nell'archivio tabelle di Azure.

In caso di problemi, cercare le soluzioni nella pagina sulla [risoluzione dei problemi](iot-hub-gateway-kit-c-sim-troubleshooting.md).

## <a name="what-you-will-learn"></a>Contenuto dell'esercitazione

Come usare lo strumento gulp per eseguire il codice di esempio per leggere i messaggi nell'archivio tabelle di Azure.

## <a name="what-you-need"></a>Elementi necessari

Sono state eseguite le attività seguenti:

- [Creazione dell'app per le funzioni di Azure e dell'account di archiviazione di Azure](iot-hub-gateway-kit-c-sim-lesson4-deploy-resource-manager-template.md).
- [Esecuzione dell'applicazione di esempio gateway](iot-hub-gateway-kit-c-sim-lesson3-configure-simulated-device-app.md).
- [Lettura di messaggi dall'hub IoT](iot-hub-gateway-kit-c-sim-lesson3-read-messages-from-hub.md).

## <a name="get-your-azure-storage-connection-strings"></a>Ottenere le stringhe di connessione di archiviazione di Azure

All'inizio di questa lezione è stato creato un account di archiviazione di Azure. Per ottenere la stringa di connessione dell'account di archiviazione di Azure, usare i comandi seguenti:

* Elencare tutti gli account di archiviazione.

```bash
az storage account list -g iot-gateway --query [].name
```

* Ottenere la stringa di connessione di archiviazione di Azure.

```bash
az storage account show-connection-string -g iot-gateway -n {storage name}
```

Usare `iot-gateway` come valore di `{resource group name}` se nella lezione 2 non si è modificato il valore.

## <a name="configure-the-device-connection"></a>Configurare la connessione del dispositivo

Aggiornare il file `config-azure.json` in modo che il codice di esempio eseguito nel computer host possa leggere i messaggi nell'archivio tabelle di Azure. Per configurare la connessione al dispositivo, seguire questa procedura.

1. Aprire il file di configurazione del dispositivo `config-azure.json` eseguendo questi comandi:

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-azure.json
   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-azure.json
   ```

   ![configurazione](media/iot-hub-gateway-kit-lessons/lesson4/config_azure.png)

2. Sostituire `[Azure storage connection string]` con la stringa di connessione di archiviazione di Azure ottenuta.

   `[IoT hub connection string]` dovrebbe già essere stata sostituita nella sezione [Leggere messaggi dall'hub IoT di Azure](iot-hub-gateway-kit-c-sim-lesson3-read-messages-from-hub.md) della lezione&3;.

## <a name="read-messages-in-your-azure-table-storage"></a>Leggere i messaggi nell'archivio tabelle di Azure

Eseguire l'applicazione di esempio gateway e leggere i messaggi usando il comando seguente:

```bash
gulp run --table-storage
```

L'hub IoT attiva l'applicazione per le funzioni di Azure per salvare il messaggio nell'archivio tabelle di Azure quando arriva il nuovo messaggio.
Il comando `gulp run` esegue l'applicazione di esempio gateway che invia i messaggi all'hub IoT. Con il parametro `table-storage`, genera anche un processo figlio per ricevere il messaggio salvato nell'archivio tabelle di Azure.

I messaggi inviati e ricevuti vengono visualizzati tutti immediatamente nella stessa finestra della console nel computer host. L'istanza dell'applicazione di esempio terminerà automaticamente in 40 secondi.

   ![Lettura gulp](media/iot-hub-gateway-kit-lessons/lesson4/gulp_run_read_table_simudev.png)


## <a name="summary"></a>Riepilogo

È stato eseguito il codice di esempio per leggere i messaggi nell'archivio tabelle di Azure, salvati dall'applicazione per le funzioni di Azure.

