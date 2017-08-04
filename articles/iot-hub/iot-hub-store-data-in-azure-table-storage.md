---
title: Salvare i messaggi dell'hub IoT nell'archivio dati di Azure | Microsoft Docs
description: Usare l'app per le funzioni di Azure per salvare i messaggi dell'hub IoT nell'archiviazione tabelle di Azure. I messaggi dell'hub IoT contengono informazioni, come i dati di sensori, inviate dal dispositivo IoT.
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: archiviazione dei dati IoT, archiviazione dei dati di sensori IoT
ms.assetid: 62fd14fd-aaaa-4b3d-8367-75c1111b6269
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/12/2017
ms.author: xshi
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: ed867e1e7702d3f178feeab007ac94fb1d67c140
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---
# <a name="save-iot-hub-messages-that-contain-sensor-data-to-your-azure-table-storage"></a>Salvare i messaggi dell'hub IoT che contengono dati di sensori nell'archiviazione tabelle di Azure

![Diagramma end-to-end](media/iot-hub-get-started-e2e-diagram/3.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Contenuto dell'esercitazione

Si apprenderà come creare un account di archiviazione di Azure e un'app per le funzioni di Azure per archiviare i messaggi dell'hub IoT nell'archiviazione tabelle di Azure.

## <a name="what-you-do"></a>Operazioni da fare

- Creare un account di archiviazione di Azure
- Preparare la connessione dell'hub IoT per la lettura dei messaggi.
- Creare e distribuire un'app per le funzioni di Azure.

## <a name="what-you-need"></a>Elementi necessari

- [Configurare il dispositivo](iot-hub-raspberry-pi-kit-node-get-started.md) in modo da soddisfare i requisiti seguenti:
  - Una sottoscrizione di Azure attiva
  - Un hub IoT nella sottoscrizione 
  - Un'applicazione in esecuzione che invia messaggi all'hub IoT di Azure

## <a name="create-an-azure-storage-account"></a>Creare un account di archiviazione di Azure

1. Nel [portale di Azure](https://portal.azure.com/) fare clic su **Nuova** > **Archiviazione** > **Account di archiviazione**.

2. Immettere le informazioni necessarie per l'account di archiviazione:

   ![Creare un account di archiviazione nel portale di Azure](media\iot-hub-store-data-in-azure-table-storage\1_azure-portal-create-storage-account.png)

   * **Nome**: nome dell'account di archiviazione. Il nome deve essere univoco a livello globale.

   * **Gruppo di risorse**: usare lo stesso gruppo di risorse usato da hub IoT.

   * **Aggiungi al dashboard**: selezionare questa opzione per semplificare l'accesso all'hub IoT dal dashboard.

3. Fare clic su **Crea**.

## <a name="prepare-your-iot-hub-connection-to-read-messages"></a>Preparare la connessione dell'hub IoT per la lettura dei messaggi

L'hub IoT espone un endpoint predefinito compatibile con l'hub eventi per consentire alle applicazioni di leggere i messaggi dell'hub IoT. Nel frattempo, le applicazioni usano gruppi di consumer per leggere i dati dall'hub IoT. Prima di creare un'app per le funzioni di Azure per la lettura dei dati dall'hub IoT, è necessario:

- Ottenere la stringa di connessione dell'endpoint dell'hub IoT.
- Creare un gruppo di consumer per l'hub IoT.

### <a name="get-the-connection-string-of-your-iot-hub-endpoint"></a>Ottenere la stringa di connessione dell'endpoint dell'hub IoT

1. Aprire l'hub IoT.

2. Nel riquadro **Hub IoT**, in **Messaggistica**, fare clic su **Endpoint**.

3. Nel riquadro destro, in **Endpoint predefiniti**, fare clic su **Eventi**.

4. Nel riquadro **Proprietà** prendere nota dei valori seguenti:
   - Endpoint compatibile con l'hub eventi
   - Nome compatibile con l'hub eventi

   ![Ottenere la stringa di connessione dell'endpoint dell'hub IoT nel portale di Azure](media\iot-hub-store-data-in-azure-table-storage\2_azure-portal-iot-hub-endpoint-connection-string.png)

5. Nel riquadro **Hub IoT**, in **Impostazioni**, fare clic su **Criteri di accesso condivisi**.

6. Fare clic su **iothubowner**.

7. Prendere nota del valore presente in **Chiave primaria**.

8. Creare la stringa di connessione dell'endpoint dell'hub IoT nel modo seguente:

   `Endpoint=<Event Hub-compatible endpoint>;SharedAccessKeyName=iothubowner;SharedAccessKey=<Primary key>`

   > [!NOTE]
   > Sostituire `<Event Hub-compatible endpoint>` e `<Primary key>` con i valori annotati in precedenza.

### <a name="create-a-consumer-group-for-your-iot-hub"></a>Creare un gruppo di consumer per l'hub IoT

1. Aprire l'hub IoT.

2. Nel riquadro **Hub IoT**, in **Messaggistica**, fare clic su **Endpoint**.

3. Nel riquadro destro, in **Endpoint predefiniti**, fare clic su **Eventi**.

4. Nel riquadro **Proprietà** immettere un nome in **Gruppi di consumer** e prendere nota del nome.

5. Fare clic su **Salva**.

## <a name="create-and-deploy-an-azure-function-app"></a>Creare e distribuire un'app per le funzioni di Azure

1. Nel [portale di Azure](https://portal.azure.com/) fare clic su **Nuovo** > **Calcolo** > **App per le funzioni**.

2. Immettere le informazioni necessarie per l'app per le funzioni.

   ![Creare un'app per le funzioni nel portale di Azure](media\iot-hub-store-data-in-azure-table-storage\3_azure-portal-create-function-app.png)

   * **Nome app**: nome dell'app per le funzioni. Il nome deve essere univoco a livello globale.

   * **Gruppo di risorse**: usare lo stesso gruppo di risorse usato da hub IoT.

   * **Account di archiviazione**: selezionare l'account di archiviazione creato.

   * **Aggiungi al dashboard**: selezionare questa opzione per semplificare l'accesso all'app per le funzioni dal dashboard.

3. Fare clic su **Crea**.

4. Dopo aver creato l'app per le funzioni, aprirla.

5. Nell'app per le funzioni creare una nuova funzione seguendo questa procedura:

   a. Fare clic su **Nuova funzione**.

   b. Selezionare **JavaScript** per **Linguaggio** ed **Elaborazione dati** per **Scenario**.

   c. Fare clic su **Creare questa funzione** e quindi su **Nuova funzione**.

   d. Selezionare **JavaScript** per il linguaggio e **Elaborazione dati** per lo scenario.

   e. Fare clic sul modello **EventHubTrigger-JavaScript**.

   f. Immettere le informazioni necessarie per il modello.

      * **Assegnare un nome alla funzione**: nome della funzione.

      * **Nome hub eventi**: nome compatibile con l'hub eventi annotato in precedenza.

      * **Connessione dell'hub eventi**: fare clic su **Nuovo** per aggiungere la stringa di connessione dell'endpoint dell'hub IoT creata.

   g. Fare clic su **Crea**.

6. Configurare un output della funzione seguendo questa procedura:

   a. Fare clic su **Integrazione** > **Nuovo output** > **Archiviazione tabelle di Azure** > **Seleziona**.

      ![Aggiungere l'archiviazione tabelle all'app per le funzioni nel portale di Azure](media\iot-hub-store-data-in-azure-table-storage\4_azure-portal-function-app-add-output-table-storage.png)

   b. Immettere le informazioni necessarie.

      * **Nome del parametro della tabella**: usare `outputTable`, che verrà usato nel codice di Funzioni di Azure.
      
      * **Nome tabella**: usare `deviceData`.

      * **Connessione dell'account di archiviazione**: fare clic su **nuovo** e selezionare o immettere l'account di archiviazione. Se l'account di archiviazione non viene visualizzato, vedere [Requisiti dell'account di archiviazione](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal#storage-account-requirements).
      
   c. Fare clic su **Salva**.

7. In **Trigger** fare clic su **Hub eventi di Azure (eventHubMessages)**.

8. In **Gruppo di consumer dell'hub eventi** immettere il nome del gruppo di consumer creato, quindi fare clic su **Salva**.

9. Fare clic su **Sviluppo** e quindi su **Visualizza file**.

10. Sostituire il codice in `index.js` con il codice seguente:

   ```javascript
   'use strict';

   // This function is triggered each time a message is received in the IoT hub.
   // The message payload is persisted in an Azure storage table
 
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

11. Fare clic su **Salva**.

È stata creata l'app per le funzioni, che archivia i messaggi ricevuti dall'hub IoT nell'archiviazione tabelle di Azure.

> [!NOTE]
> È possibile usare il pulsante **Esegui** per testare l'app per le funzioni. Quando fa clic su **Esegui**, il messaggio di test viene inviato all'hub IoT. All'arrivo del messaggio, l'app per le funzioni si avvia e salva il messaggio nell'archiviazione tabelle di Azure. Il riquadro **Log** registra i dettagli del processo.

## <a name="verify-your-message-in-your-table-storage"></a>Verificare il messaggio nell'archivio tabelle

1. Eseguire l'applicazione di esempio nel dispositivo per inviare messaggi all'hub IoT.

2. [Scaricare e installare Azure Storage Explorer](http://storageexplorer.com/).

3. Aprire Storage Explorer, fare clic su **Add an Azure Account** (Aggiungi un account Azure)  >  **Accedi** e quindi accedere all'account Azure.

4. Fare clic sulla sottoscrizione di Azure > **Account di archiviazione** > account di archiviazione > **Tabelle** > **deviceData**.

   Nella tabella `deviceData` saranno visibili i messaggi inviati dal dispositivo all'hub IoT.

## <a name="next-steps"></a>Passaggi successivi

Sono stati creati l'account di archiviazione di Azure e l'app per le funzioni di Azure, che archivia i messaggi ricevuti dall'hub IoT nell'archiviazione tabelle di Azure.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

