---
title: Ordinare gli eventi di connessione dei dispositivi dall'hub IoT di Azure tramite Azure Cosmos DB | Microsoft Docs
description: Questo articolo descrive come ordinare e registrare gli eventi di connessione dei dispositivi dall'hub IoT di Azure tramite Azure Cosmos DB per mantenere lo stato di connessione più recente
services: iot-hub
ms.service: iot-hub
author: ash2017
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: asrastog
ms.openlocfilehash: 02fc47200157b813da4d0031effa39633719855d
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2018
ms.locfileid: "49340206"
---
# <a name="order-device-connection-events-from-azure-iot-hub-using-azure-cosmos-db"></a>Ordinare gli eventi di connessione dei dispositivi dall'hub IoT di Azure tramite Azure Cosmos DB

Griglia di eventi di Azure consente di compilare applicazioni basate su eventi e di integrare con facilità eventi IoT nelle soluzioni aziendali. Questo articolo illustra una procedura che consente di tenere traccia e archiviare lo stato di connessione più recente del dispositivo in Cosmos DB. Verrà usato il numero di sequenza disponibile negli eventi di connessione e disconnessione del dispositivo e verrà archiviato lo stato più recente in Cosmos DB. Viene usata una stored procedure, ovvero una logica dell'applicazione eseguita su una raccolta di Cosmos DB.

Il numero di sequenza è una rappresentazione in formato stringa di un numero esadecimale. È possibile usare il confronto tra stringhe per identificare il numero maggiore. Se si converte la stringa in formato esadecimale, il numero sarà a 256 bit. Il numero di sequenza è sempre in aumento e all'evento più recente viene associato un numero superiore rispetto a quello di altri eventi. Ciò è utile se le connessioni e le disconnessioni del dispositivo sono frequenti e se è necessario assicurarsi che solo l'evento più recente venga usato per attivare un'azione downstream, poiché Griglia di eventi di Azure non supporta l'ordinamento degli eventi.

## <a name="prerequisites"></a>Prerequisiti

* Un account Azure attivo. Se non si ha un account, è possibile [crearne uno gratuito](http://azure.microsoft.com/pricing/free-trial/).

* Un account API SQL di Azure Cosmos DB attivo. Se non ne è ancora stato creato un', vedere la procedura dettagliata [Creare un account di database](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-dotnet#create-a-database-account).

* Una raccolta nel database. Vedere la procedura dettagliata [Aggiungere una raccolta](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-dotnet#add-a-collection).

* Un hub IoT in Azure. Se l'hub non è stato ancora creato, vedere [Introduzione all'hub IoT](../iot-hub/iot-hub-csharp-csharp-getstarted.md) per la procedura dettagliata. 

## <a name="create-a-stored-procedure"></a>Creare una stored procedure

In primo luogo creare una stored procedure e impostarlo fino all'esecuzione di una logica che confronti i numeri di sequenza degli eventi in arrivo e registri l'evento più recente per ogni dispositivo nel database.

1. Nell'API SQL di Cosmos DB selezionare **Data Explorer** > **Items** > **New Stored Procedure** (Esplora dati > Elementi > Nuova stored procedure).

   ![Creare la stored procedure](./media/iot-hub-how-to-order-connection-state-events/create-stored-procedure.png)

2. Immettere un ID della stored procedure e incollare il codice seguente nel corpo della stored procedure. Si noti che questo codice deve sostituire il codice esistente nel corpo della stored procedure. Questo codice mantiene una riga per ogni ID dispositivo e registra lo stato di connessione più recente di tale ID identificando il numero di sequenza più alto. 

    ```javascript
    // SAMPLE STORED PROCEDURE
    function UpdateDevice(deviceId, moduleId, hubName, connectionState, connectionStateUpdatedTime, sequenceNumber) {
      var collection = getContext().getCollection();
      var response = {};
      
      var docLink = getDocumentLink(deviceId, moduleId);

      var isAccepted = collection.readDocument(docLink, function(err, doc) {
        if (err) {
          console.log('Cannot find device ' + docLink + ' - ');
          createDocument();
        } else {
          console.log('Document Found - ');
          replaceDocument(doc);
        }
      });

      function replaceDocument(document) {
        console.log(
          'Old Seq :' +
            document.sequenceNumber +
            ' New Seq: ' +
            sequenceNumber +
            ' - '
        );
        if (sequenceNumber > document.sequenceNumber) {
          document.connectionState = connectionState;
          document.connectionStateUpdatedTime = connectionStateUpdatedTime;
          document.sequenceNumber = sequenceNumber;

          console.log('replace doc - ');

          isAccepted = collection.replaceDocument(docLink, document, function(
            err,
            updated
          ) {
            if (err) {
              getContext()
                .getResponse()
                .setBody(err);
            } else {
              getContext()
                .getResponse()
                .setBody(updated);
            }
          });
        } else {
          getContext()
            .getResponse()
            .setBody('Old Event - current: ' + document.sequenceNumber + ' Incoming: ' + sequenceNumber);
        }
      }
      function createDocument() {
        document = {
          id: deviceId + '-' + moduleId,
          deviceId: deviceId,
          moduleId: moduleId,
          hubName: hubName,
          connectionState: connectionState,
          connectionStateUpdatedTime: connectionStateUpdatedTime,
          sequenceNumber: sequenceNumber
        };
        console.log('Add new device - ' + collection.getAltLink());
        isAccepted = collection.createDocument(
          collection.getAltLink(),
          document,
          function(err, doc) {
            if (err) {
              getContext()
                .getResponse()
                .setBody(err);
            } else {
              getContext()
                .getResponse()
                .setBody(doc);
            }
          }
        );
      }

      function getDocumentLink(deviceId, moduleId) {
        return collection.getAltLink() + '/docs/' + deviceId + '-' + moduleId;
      }
    }
    ```

3. Salvare la stored procedure: 

    ![Salvare la stored procedure](./media/iot-hub-how-to-order-connection-state-events/save-stored-procedure.png)

## <a name="create-a-logic-app"></a>Creare un'app per la logica

Per prima cosa, creare un'app per la logica e aggiungere un trigger di Griglia di eventi che monitori il gruppo di risorse per la macchina virtuale. 

### <a name="create-a-logic-app-resource"></a>Creare una risorsa di App per la logica

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Nuovo** > **Integrazione** > **App per la logica**.

   ![Creare l'app per la logica](./media/iot-hub-how-to-order-connection-state-events/select-logic-app.png)

2. Assegnare all'app per la logica un nome che sia univoco nella sottoscrizione e quindi selezionare la stessa sottoscrizione, lo stesso gruppo di risorse e la stessa posizione dell'hub IoT. 

3. Selezionare **Aggiungi al dashboard** e quindi scegliere **Crea**.

   È stata creata ora una risorsa di Azure per l'app per la logica. Al termine della distribuzione dell'app per la logica, Progettazione app per la logica mostra i modelli comuni disponibili per poter iniziare più rapidamente.

   > [!NOTE] 
   > Se si seleziona **Aggiungi al dashboard**, l'app per la logica viene aperta automaticamente in Progettazione app per la logica. In caso contrario, è possibile trovare e aprire manualmente l'app per la logica.

4. In Progettazione app per la logica, nella sezione **Modelli**, scegliere **App per la logica vuota** per creare un'app per la logica completamente nuova.

### <a name="select-a-trigger"></a>Selezionare un trigger

Un trigger è un evento specifico che avvia l'app per la logica. Per questa esercitazione, il trigger che attiva il flusso di lavoro riceve una richiesta tramite HTTP.  

1. Nella barra di ricerca dei trigger e dei connettori digitare **HTTP**.

2. Selezionare **Richiesta - Alla ricezione di una richiesta HTTP** come trigger. 

   ![Selezionare il trigger di richiesta HTTP](./media/iot-hub-how-to-order-connection-state-events/http-request-trigger.png)

3. Selezionare **Usare il payload di esempio per generare lo schema**. 

   ![Usare il payload di esempio per generare uno schema](./media/iot-hub-how-to-order-connection-state-events/sample-payload.png)

4. Incollare l'esempio di codice JSON seguente nella casella di testo e quindi selezionare **Fine**:

   ```json
   [{
    "id": "fbfd8ee1-cf78-74c6-dbcf-e1c58638ccbd",
    "topic":
      "/SUBSCRIPTIONS/DEMO5CDD-8DAB-4CF4-9B2F-C22E8A755472/RESOURCEGROUPS/EGTESTRG/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/MYIOTHUB",
    "subject": "devices/Demo-Device-1",
    "eventType": "Microsoft.Devices.DeviceConnected",
    "eventTime": "2018-07-03T23:20:11.6921933+00:00",
    "data": {
      "deviceConnectionStateEventInfo": {
        "sequenceNumber":
          "000000000000000001D4132452F67CE200000002000000000000000000000001"
      },
      "hubName": "MYIOTHUB",
      "deviceId": "48e44e11-1437-4907-83b1-4a8d7e89859e",
      "moduleId": ""
    },
    "dataVersion": "1",
    "metadataVersion": "1"
   }]
   ```

5. È possibile che venga inviata una notifica popup con la dicitura **Ricordare di includere nella richiesta un'intestazione Content-Type impostata su application/json**. Questo suggerimento può essere tranquillamente ignorato ed è possibile passare alla sezione successiva. 

### <a name="create-a-condition"></a>Creare una condizione

Nel flusso di lavoro dell'app per la logica, le condizioni consentono di eseguire azioni specifiche dopo il superamento di una determinata condizione. Dopo che la condizione è soddisfatta, è possibile definire un'azione desiderata. Per questa esercitazione, la condizione consiste nel controllare se il tipo di evento (eventType) è la connessione o la disconnessione del dispositivo. L'azione consiste invece nell'esecuzione della stored procedure nel database. 

1. Selezionare **Nuovo passaggio**, quindi **Predefiniti** e **Condizione**. 

2. Specificare la condizione come illustrato di seguito per l'esecuzione esclusiva degli eventi di connessione e disconnessione dei dispositivi:

  * Scegliere un valore: **eventType**
  * Modificare "uguale a" in **termina con**
  * Scegliere un valore: **nected**

   ![Specificare una condizione](./media/iot-hub-how-to-order-connection-state-events/condition-detail.png)

3. Se la condizione è vera, fare clic su **Aggiungi un'azione**.
  
   ![Aggiunta di un'azione se la condizione è vera](./media/iot-hub-how-to-order-connection-state-events/action-if-true.png)

4. Cercare Cosmos DB e fare clic su **Azure Cosmos DB - Esegui stored procedure**

   ![Ricerca di CosmosDB](./media/iot-hub-how-to-order-connection-state-events/cosmosDB-search.png)

5. Popolare il modulo per l'esecuzione della stored selezionando i valori dal database. Immettere il valore della chiave di partizione e i parametri come illustrato di seguito. 

   ![Popolamento dell'azione dell'app per la logica](./media/iot-hub-how-to-order-connection-state-events/logicapp-stored-procedure.png)

6. Salvare l'app per la logica. 

### <a name="copy-the-http-url"></a>Copiare l'URL HTTP

Prima di uscire da Progettazione app per la logica, copiare l'URL su cui è in ascolto l'app per la logica per un trigger. Questo URL viene usato per configurare Griglia di eventi. 

1. Espandere la casella di configurazione del trigger **Alla ricezione di una richiesta HTTP** facendo clic sull'opzione. 

2. Copiare il valore di **URL POST HTTP** facendo clic sul pulsante di copia che si trova accanto. 

   ![Copiare l'URL POST HTTP](./media/iot-hub-how-to-order-connection-state-events/copy-url.png)

3. Salvare l'URL per poterlo usare nella sezione successiva. 

## <a name="configure-subscription-for-iot-hub-events"></a>Configurare la sottoscrizione degli eventi dell'hub IoT

In questa sezione viene configurato l'hub IoT per la pubblicazione degli eventi che si verificano. 

1. Nel portale di Azure passare all'hub IoT. 

2. Selezionare **Eventi**.

   ![Aprire i dettagli di Griglia di eventi](./media/iot-hub-how-to-order-connection-state-events/event-grid.png)

3. Selezionare **Sottoscrizione di eventi**. 

   ![Creare una nuova sottoscrizione di eventi](./media/iot-hub-how-to-order-connection-state-events/event-subscription.png)

4. Creare la sottoscrizione di eventi con i valori seguenti: 

   * **Tipo di evento**: deselezionare l'opzione per l'annullamento della sottoscrizione di tutti i tipi di evento e selezionare **Dispositivo connesso** e **Dispositivo disconnesso** nel menu.

   * **Dettagli endpoint**: come Tipo di endpoint selezionare **Webhook**, fare clic su Selezione endpoint, incollare l'URL copiato dall'app per la logica e confermare la selezione.

       ![URL di Selezione endpoint](./media/iot-hub-how-to-order-connection-state-events/endpoint-url.png)

   * **Dettagli sottoscrizione evento**: fornire un nome descrittivo e selezionare **Schema griglia di eventi**.
   Il modulo sarà simile all'esempio seguente: 

       ![Modulo sottoscrizione di eventi di esempio](./media/iot-hub-how-to-order-connection-state-events/subscription-form.png)

5. Selezionare **Crea** per salvare la sottoscrizione di eventi.

## <a name="observe-events"></a>Osservare gli eventi

Ora che la sottoscrizione dell'evento è configurata, è possibile testare la connessione di un dispositivo.

### <a name="register-a-device-in-iot-hub"></a>Registrare un dispositivo nell'hub IoT

1. Nell'hub IoT selezionare **IoT Devices** (Dispositivi IoT). 

2. Selezionare **Aggiungi**.

3. In **ID dispositivo** immettere `Demo-Device-1`.

4. Selezionare **Salva**. 

5. È possibile aggiungere più dispositivi con ID dispositivo diversi.

   ![Come ottenere un risultato](./media/iot-hub-how-to-order-connection-state-events/AddIoTDevice.png)

6. Copiare il valore di **Stringa di connessione -- Chiave primaria** per usarlo in seguito.

   ![Come ottenere un risultato](./media/iot-hub-how-to-order-connection-state-events/DeviceConnString.png)

### <a name="start-raspberry-pi-simulator"></a>Avviare il simulatore Raspberry Pi

1. È possibile usare il simulatore Web Raspberry Pi per simulare la connessione del dispositivo.

[Avviare il simulatore Raspberry Pi](https://azure-samples.github.io/raspberry-pi-web-simulator/#Getstarted)

### <a name="run-a-sample-application-on-the-raspberry-pi-web-simulator"></a>Eseguire un'applicazione di esempio nel simulatore Web Raspberry Pi

Viene attivato un evento di connessione del dispositivo.

1. Nell'area di scrittura del codice sostituire il segnaposto nella riga 15 con la stringa di connessione del dispositivo dell'hub IoT di Azure.

   ![Come ottenere un risultato](./media/iot-hub-how-to-order-connection-state-events/raspconnstring.png)

2. Fare clic su **Esegui** per eseguire l'applicazione.

Dovrebbe essere visibile l'output seguente che mostra i dati del sensore e i messaggi inviati all'hub IoT.

   ![Come ottenere un risultato](./media/iot-hub-how-to-order-connection-state-events/raspmsg.png)

   Fare clic su **Arresta** per arrestare il simulatore e attivare un evento **Dispositivo disconnesso**.

È stata eseguita un'applicazione di esempio per raccogliere i dati del sensore da inviare all'hub IoT. 

### <a name="observe-events-in-cosmos-db"></a>Osservare gli eventi in Cosmos DB

È possibile visualizzare i risultati della stored procedure eseguita in un documento di Cosmos DB. Com'è possibile osservare, per tutti i dispositivi ogni riga contiene lo stato più recente della connessione del dispositivo stesso.

   ![Come ottenere un risultato](./media/iot-hub-how-to-order-connection-state-events/cosmosDB-outcome.png)

## <a name="use-the-azure-cli"></a>Utilizzare l’interfaccia della riga di comando di Azure

Anziché tramite il [portale di Azure](http://portal.azure.com), è possibile eseguire le procedure relative all'hub IoT usando l'interfaccia della riga di comando di Azure. Per informazioni dettagliate, vedere le pagine relative all'interfaccia della riga di comando di Azure per la [creazione di una sottoscrizione di eventi](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription) e la [creazione di un dispositivo IoT](https://docs.microsoft.com/cli/azure/iot/device).

## <a name="clean-up-resources"></a>Pulire le risorse

Questa esercitazione ha usato risorse che generano addebiti sulla sottoscrizione di Azure. Al termine dell'esercitazione e dopo aver testato i risultati ottenuti, disabilitare o eliminare le risorse che non si vogliono mantenere. 

Per non perdere il lavoro fatto, è possibile disabilitare l'app per la logica anziché eliminarla. 

1. Passare all'app per la logica.

2. Nel pannello **Panoramica** selezionare **Elimina** o **Disabilita**. 

Ogni sottoscrizione può avere un unico hub IoT gratuito. Se per questa esercitazione è stato creato un hub gratuito, non è necessario eliminarlo per impedire eventuali addebiti.

1. Passare all'hub IoT. 

2. Nel pannello **Panoramica** selezionare **Elimina**. 

Anche se si mantiene l'hub IoT, può essere opportuno eliminare la sottoscrizione di eventi creata. 

1. Nell'hub IoT selezionare **Griglia di eventi**.

2. Selezionare la sottoscrizione di eventi da rimuovere. 

3. Selezionare **Elimina**. 

Per rimuovere un account Azure Cosmos DB dal portale di Azure, fare clic con il pulsante destro del mouse sul nome dell'account e quindi fare clic su **Elimina account**. Vedere le istruzioni dettagliate per [eliminare un account di Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/manage-account#delete).

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni su come [rispondere agli eventi dell'hub IoT usando Griglia di eventi per attivare le azioni](../iot-hub/iot-hub-event-grid.md)

* [Try the IoT Hub events tutorial (Provare l'esercitazione sugli eventi dell'hub IoT)](../event-grid/publish-iot-hub-events-to-logic-apps.md)

* Informazioni sulle altre operazioni che è possibile eseguire con [Griglia di eventi](../event-grid/overview.md)


