---
title: Leggere i messaggi con salvataggio permanente in Archiviazione di Azure | Documentazione Microsoft
description: Monitorare i messaggi da dispositivo a cloud mentre vengono scritti nell&quot;archiviazione tabelle di Azure.
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: recuperare dati dal cloud, servizio cloud iot
ms.assetid: 8c5558bb-3c31-4445-90e6-b1a978738545
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 155e5d6280d86b06b1718fc3032c2c224539183d
ms.openlocfilehash: 4e1107400ef08cc3fd955d693c77f332446ecc37


---
# <a name="read-messages-persisted-in-azure-storage"></a>Leggere i messaggi con salvataggio permanente in Archiviazione di Azure
## <a name="what-you-will-do"></a>Contenuto dell'esercitazione
Monitorare i messaggi da dispositivo a cloud che vengono inviati dal dispositivo Raspberry Pi 3 all'hub IoT mentre vengono scritti nell'archiviazione tabelle di Azure. In caso di problemi, cercare le soluzioni nella pagina sulla [risoluzione dei problemi](iot-hub-raspberry-pi-kit-c-troubleshooting.md).

## <a name="what-you-will-learn"></a>Contenuto dell'esercitazione
Questo articolo illustra come usare l'attività gulp read-message per leggere i messaggi con salvataggio permanente nell'archiviazione tabelle di Azure.

## <a name="what-you-need"></a>Elementi necessari
Prima di avviare questo processo è necessario aver completato [Eseguire l'applicazione di esempio di Azure per il lampeggiamento nel dispositivo Raspberry Pi 3](iot-hub-raspberry-pi-kit-c-lesson3-run-azure-blink.md).

## <a name="read-new-messages-from-your-storage-account"></a>Leggere i nuovi messaggi dall'account di archiviazione
Nell'articolo precedente è stata eseguita un'applicazione di esempio sul dispositivo Pi. L'applicazione di esempio ha inviato messaggi all'hub IoT di Azure. I messaggi inviati all'hub IoT vengono archiviati nell'archiviazione tabelle di Azure tramite l'app per le funzioni di Azure. Per leggere i messaggi dall'archiviazione tabelle di Azure è necessaria la stringa di connessione di Archiviazione di Azure.

Per leggere i messaggi nell'archiviazione tabelle di Azure, seguire questi passaggi:

1. Ottenere la stringa di connessione usando i comandi seguenti:

   ```bash
   az storage account list -g iot-sample --query [].name
   az storage account show-connection-string -g iot-sample -n {storage name}
   ```

   Il primo comando recupera il valore di `storage name` che viene usato nel secondo comando per ottenere la stringa di connessione. Usare `iot-sample` come valore di `{resource group name}` se non si è modificato il valore.
2. Aprire il file di configurazione `config-raspberrypi.json` in Visual Studio Code usando il comando seguente:

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-raspberrypi.json
   
   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-raspberrypi.json
   ```
3. Sostituire `[Azure storage connection string]` con la stringa di connessione ottenuta nel passaggio 1.
4. Salvare il file.`config-raspberrypi.json`
5. Inviare nuovamente i messaggi e leggerli dall'archiviazione tabelle di Azure usando il comando seguente:
   
   ```bash
   gulp run --read-storage
   ```
   
   La logica per la lettura dall'archiviazione tabelle di Azure si trova nel file `azure-table.js`.
   
   ![gulp run --read-storage](media/iot-hub-raspberry-pi-lessons/lesson3/gulp_read_message_c.png)

## <a name="summary"></a>Riepilogo
È stato connesso il dispositivo Pi all'hub IoT nel cloud ed è stata usata l'applicazione di esempio per il lampeggiamento per inviare messaggi da dispositivo a cloud. È stata anche usata l'app per le funzioni di Azure per archiviare i messaggi in ingresso dell'hub IoT nell'archiviazione tabelle di Azure. È ora possibile inviare i messaggi da cloud a dispositivo dall'hub IoT al dispositivo Pi.

## <a name="next-steps"></a>Passaggi successivi
[Eseguire un'applicazione di esempio per ricevere messaggi da cloud a dispositivo](iot-hub-raspberry-pi-kit-c-lesson4-send-cloud-to-device-messages.md)




<!--HONumber=Dec16_HO1-->


