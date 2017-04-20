---
title: 'Connettere Intel Edison (Node) ad Azure IoT: lezione 3: Monitorare i messaggi | Documentazione Microsoft'
description: Monitorare i messaggi da dispositivo a cloud mentre vengono scritti nell&quot;archiviazione tabelle di Azure.
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: dati nel cloud, raccolta di dati cloud, servizio cloud IoT, dati IoT
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-intel-edison-kit-node-get-started
ms.assetid: fa2c7efe-7e34-4e39-bb70-015c15ac69ed
ms.service: iot-hub
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: adf5b10721a28432e6b37ef73c6a7e7ec9f93cdd
ms.openlocfilehash: 5100e6dbd74095f419c1017a3784fac547bf200b
ms.lasthandoff: 01/25/2017


---
# <a name="read-messages-persisted-in-azure-storage"></a>Leggere i messaggi con salvataggio permanente in Archiviazione di Azure
## <a name="what-you-will-do"></a>Contenuto dell'esercitazione
Monitorare i messaggi da dispositivo a cloud che vengono inviati da Intel Edison all'hub IoT mentre vengono scritti nell'archivio tabelle di Azure. In caso di problemi, cercare le soluzioni nella [pagina sulla risoluzione dei problemi][troubleshooting].

## <a name="what-you-will-learn"></a>Contenuto dell'esercitazione
Questo articolo illustra come usare l'attività gulp read-message per leggere i messaggi con salvataggio permanente nell'archiviazione tabelle di Azure.

## <a name="what-you-need"></a>Elementi necessari
Prima di avviare questo processo è necessario aver completato [Eseguire l'applicazione di esempio di Azure per il lampeggiamento in Intel Edison][run-the-azure-blink-sample-application-on-intel-edison].

## <a name="read-new-messages-from-your-storage-account"></a>Leggere i nuovi messaggi dall'account di archiviazione
Nell'articolo precedente è stata eseguita un'applicazione di esempio in Edison. L'applicazione di esempio ha inviato messaggi all'hub IoT di Azure. I messaggi inviati all'hub IoT vengono archiviati nell'archiviazione tabelle di Azure tramite l'app per le funzioni di Azure. Per leggere i messaggi dall'archiviazione tabelle di Azure è necessaria la stringa di connessione di Archiviazione di Azure.

Per leggere i messaggi nell'archiviazione tabelle di Azure, seguire questi passaggi:

1. Ottenere la stringa di connessione usando i comandi seguenti:

   ```bash
   az storage account list -g iot-sample --query [].name
   az storage account show-connection-string -g iot-sample -n {storage name}
   ```

   Il primo comando recupera il valore di `storage name` che viene usato nel secondo comando per ottenere la stringa di connessione. Usare `iot-sample` come valore di `{resource group name}` se non si è modificato il valore.
2. Aprire il file di configurazione `config-edison.json` in Visual Studio Code usando il comando seguente:

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-edison.json

   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-edison.json
   ```
3. Sostituire `[Azure storage connection string]` con la stringa di connessione ottenuta nel passaggio 1.
4. Salvare il file.`config-edison.json`
5. Inviare nuovamente i messaggi e leggerli dall'archiviazione tabelle di Azure usando il comando seguente:

   ```bash
   gulp run --read-storage
   ```

   La logica per la lettura dall'archiviazione tabelle di Azure si trova nel file `azure-table.js`.

   ![gulp run --read-storage][gulp run]

## <a name="summary"></a>Riepilogo
È stato connesso Edison all'hub IoT nel cloud ed è stata usata l'applicazione di esempio per il lampeggiamento per inviare messaggi da dispositivo a cloud. È stata anche usata l'app per le funzioni di Azure per archiviare i messaggi in ingresso dell'hub IoT nell'archiviazione tabelle di Azure. È ora possibile inviare i messaggi da cloud a dispositivo dall'hub IoT a Edison.

## <a name="next-steps"></a>Passaggi successivi
[Eseguire un'applicazione di esempio per ricevere messaggi da cloud a dispositivo][receive-cloud-to-device-messages]
<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-node-troubleshooting.md
[run-the-azure-blink-sample-application-on-intel-edison]: iot-hub-intel-edison-kit-node-lesson3-run-azure-blink.md
[gulp run]: media/iot-hub-intel-edison-lessons/lesson3/gulp_read_message.png
[receive-cloud-to-device-messages]: iot-hub-intel-edison-kit-node-lesson4-send-cloud-to-device-messages.md
