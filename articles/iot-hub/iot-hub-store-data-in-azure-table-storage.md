---
title: Salvare i messaggi dell'hub IoT nell'archivio dati di Azure | Microsoft Docs
description: Usare l'app per le funzioni di Azure per salvare i messaggi dell'hub IoT nell'archiviazione tabelle di Azure. I messaggi dell'hub IoT contengono informazioni come i dati di sensori inviate dal dispositivo IoT.
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: archiviazione dei dati IoT, archiviazione dei dati di sensori IoT
ms.assetid: 62fd14fd-aaaa-4b3d-8367-75c1111b6269
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: a1ba750d2be1969bfcd4085a24b0469f72a357ad
ms.openlocfilehash: c1f5d737b9718ead9c59794dae23798ef26aa42a
ms.contentlocale: it-it
ms.lasthandoff: 06/20/2017


---
# <a name="save-iot-hub-messages-that-contain-information-like-sensor-data-to-azure-table-storage"></a>Salvare i messaggi dell'hub IoT che contengono informazioni come i dati di sensori nell'archiviazione tabelle di Azure

![Diagramma end-to-end](media/iot-hub-get-started-e2e-diagram/3.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-will-learn"></a>Contenuto dell'esercitazione

Si apprenderà come creare un account di archiviazione di Azure e un'app per le funzioni di Azure per archiviare i messaggi dell'hub IoT nell'archiviazione tabelle di Azure.

## <a name="what-you-will-do"></a>Contenuto dell'esercitazione

- Creare un account di archiviazione di Azure
- Preparare la connessione dell'hub IoT per la lettura dei messaggi.
- Creare e distribuire un'app per le funzioni di Azure.

## <a name="what-you-will-need"></a>Prerequisiti

- Completare l'esercitazione [Configurare il dispositivo](iot-hub-raspberry-pi-kit-node-get-started.md) che prevede i requisiti seguenti:
  - Una sottoscrizione di Azure attiva.
  - Un hub IoT di Azure nella sottoscrizione.
  - Un'applicazione in esecuzione che invia messaggi all'hub IoT di Azure.

## <a name="create-an-azure-storage-account"></a>Creare un account di archiviazione di Azure

1. Nel [portale di Azure](https://portal.azure.com/) fare clic su **Nuova** > **Archiviazione** > **Account di archiviazione**.
1. Immettere le informazioni necessarie per l'account di archiviazione:

   ![Creare un account di archiviazione nel portale di Azure](media\iot-hub-store-data-in-azure-table-storage\1_azure-portal-create-storage-account.png)

   **Nome**: nome dell'account di archiviazione. Il nome deve essere univoco a livello globale.

   **Gruppo di risorse**: usare lo stesso gruppo di risorse usato dall'hub IoT.

   **Aggiungi al dashboard**: selezionare questa opzione per semplificare l'accesso all'hub IoT dal dashboard.
1. Fare clic su **Crea**.

## <a name="prepare-for-iot-hub-connection-to-read-messages"></a>Preparare la connessione dell'hub IoT per la lettura dei messaggi

L'hub IoT espone un endpoint predefinito compatibile con Hub eventi per consentire alle applicazioni di leggere i messaggi dell'hub IoT. Nel frattempo, le applicazioni usano i gruppi di consumer per leggere dati dall'IoT Hub. Prima di creare un'app per le funzioni di Azure per la lettura dei dati dall'hub IoT, è necessario:

- Ottenere la stringa di connessione dell'endpoint dell'hub IoT.
- Creare un gruppo di consumer per l'hub IoT.

### <a name="get-the-connection-string-of-your-iot-hub-endpoint"></a>Ottenere la stringa di connessione dell'endpoint dell'hub IoT

1. Aprire l'hub IoT.
1. Nel riquadro **Hub IoT** fare clic su **Endpoint** in **MESSAGGISTICA**.
1. Nel riquadro destro fare clic su **Eventi** in **Endpoint predefiniti**.
1. Nel riquadro **Proprietà** prendere nota dei valori seguenti:
   - Endpoint compatibile con Hub eventi
   - Nome compatibile con l'hub eventi

   ![Ottenere la stringa di connessione dell'endpoint dell'hub IoT nel portale di Azure](media\iot-hub-store-data-in-azure-table-storage\2_azure-portal-iot-hub-endpoint-connection-string.png)

1. Nel riquadro **Hub IoT** fare clic su **Criteri di accesso condivisi** in **IMPOSTAZIONI**.
1. Fare clic su **iothubowner**.
1. Prendere nota del valore presente in **Chiave primaria**.
1. Creare la stringa di connessione dell'endpoint dell'hub IoT nel modo seguente:

   `Endpoint=<Event Hub-compatible endpoint>;SharedAccessKeyName=iothubowner;SharedAccessKey=<Primary key>`

   > [!Note]
   > Sostituire `<Event Hub-compatible endpoint>` e `<Primary key>` con i valori annotati.

### <a name="create-a-consumer-group-for-your-iot-hub"></a>Creare un gruppo di consumer per l'hub IoT

1. Aprire l'hub IoT.
1. Nel riquadro **Hub IoT** fare clic su **Endpoint** in **MESSAGGISTICA**.
1. Nel riquadro destro fare clic su **Eventi** in **Endpoint predefiniti**.
1. Nel pannello **Proprietà** immettere un nome in **Gruppi di consumer** e prendere nota del nome.
1. Fare clic su **Salva**.

## <a name="create-and-deploy-an-azure-function-app"></a>Creare e distribuire un'app per le funzioni di Azure

1. Nel [portale di Azure](https://portal.azure.com/) fare clic su **Nuovo** > **Calcolo** > **App per le funzioni**.
1. Immettere le informazioni necessarie per l'app per le funzioni.

   ![Creare un'app per le funzioni nel portale di Azure](media\iot-hub-store-data-in-azure-table-storage\3_azure-portal-create-function-app.png)

   **Nome app**: nome dell'app per le funzioni. Il nome deve essere univoco a livello globale.

   **Gruppo di risorse**: usare lo stesso gruppo di risorse usato dall'hub IoT.

   **Account di archiviazione**: selezionare l'account di archiviazione creato.

   **Aggiungi al dashboard**: selezionare questa opzione per semplificare l'accesso all'app per le funzioni dal dashboard.
1. Fare clic su **Crea**.
1. Aprire l'app per le funzioni dopo averla creata.
1. Creare una nuova funzione nell'app per le funzioni.
   1. Fare clic su **Nuova funzione**.
   1. Selezionare **JavaScript** per **Linguaggio** ed **Elaborazione dati** per **Scenario**.
   1. Fare clic su **Creare questa funzione** e poi su **Nuova funzione**.
   1. Selezionare **JavaScript** per il linguaggio e **Elaborazione dati** per lo scenario.
   1. Fare clic sul modello **EventHubTrigger-JavaScript**.
   1. Immettere le informazioni necessarie per il modello.

      **Assegnare un nome alla funzione**: nome della funzione.

      **Nome hub eventi**: nome annotato in precedenza compatibile con l'hub eventi.

      **Connessione dell'hub eventi**: fare clic su Nuovo per aggiungere la stringa di connessione dell'endpoint dell'hub IoT creata.
   1. Fare clic su **Crea**.
1. Configurare un output della funzione.
   1. Fare clic su **Integrazione** > **Nuovo output** > **Archiviazione tabelle di Azure** > **Seleziona**.

      ![Aggiungere un archivio tabelle all'app per le funzioni nel portale di Azure](media\iot-hub-store-data-in-azure-table-storage\4_azure-portal-function-app-add-output-table-storage.png)
   1. Immettere le informazioni necessarie.

      **Nome del parametro della tabella**: usare `outputTable` per il nome, che verrà usato nel codice di Funzioni di Azure.
      
      **Nome tabella**: usare `deviceData` per il nome.

      **Connessione dell'account di archiviazione**: fare clic su **nuovo** e selezionare o immettere l'account di archiviazione. Se non è possibile visualizzare l'account di archiviazione, vedere [Requisiti dell'account di archiviazione](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal#storage-account-requirements)
      
   1. Fare clic su **Salva**.
1. In **Trigger** fare clic su **Hub eventi di Azure (myEventHubTrigger)**.
1. In **Gruppo di consumer dell'hub eventi** immettere il nome del gruppo di consumer creato, quindi fare clic su **Salva**.
1. Fare clic su **Sviluppo** e quindi su **Visualizza file**.
1. Sostituire il codice in `index.js` con il codice seguente, quindi fare clic su **Salva**.

   ```javascript
   'use strict';

   // This function is triggered each time a message is revieved in the IoTHub.
   // The message payload is persisted in an Azure Storage Table
 
   module.exports = function (context, iotHubMessage) {
    context.log('Message received: ' + JSON.stringify(iotHubMessage));
    var date = Date.now();
    var partitionKey = Math.floor(date / (24 * 60 * 60 * 1000)) + '';
    var rowKey = date + '';
    context.bindings.outputTable = {
     "partitionKey": partitionKey,
     "rowKey": rowKey,
     "message": JSON.stringify(iotHubMessage)
    };
    context.done();
   };
   ```

L'app per le funzioni è stata creata. Archivia i messaggi ricevuti dall'hub IoT nell'archivio tabelle di Azure.

> [!Note]
> È possibile usare il pulsante **Esegui** per testare l'app per le funzioni. Quando fa clic su **Esegui**, il messaggio di test viene inviato all'hub IoT. All'arrivo del messaggio, l'app per le funzioni si avvia e quindi salva il messaggio nell'archivio tabelle di Azure. Il riquadro **Log** registra i dettagli del processo.

## <a name="verify-your-message-in-your-table-storage"></a>Verificare il messaggio nell'archivio tabelle

1. Eseguire l'applicazione di esempio nel dispositivo per inviare messaggi all'hub IoT.
1. [Scaricare e installare Microsoft Azure Storage Explorer](http://storageexplorer.com/).
1. Aprire Microsoft Azure Storage Explorer, fare clic su **Add an Azure Account** > **Sign in** (Aggiungere un account di Azure > Accedi), quindi accedere all'account Azure.
1. Fare clic sulla sottoscrizione di Azure > **Account di archiviazione** > account di archiviazione > **Tabelle** > **deviceData**.

   Nella tabella `deviceData` saranno visibili i messaggi inviati dal dispositivo all'hub IoT.

## <a name="next-steps"></a>Passaggi successivi

È stato creato l'account di archiviazione di Azure e l'app per le funzioni di Azure per archiviare i messaggi ricevuti dall'hub IoT nell'archivio tabelle di Azure.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

