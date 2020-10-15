---
title: "Esercitazione: Usare eventi dell'hub IoT per attivare App per la logica di Azure"
description: Questa esercitazione mostra come usare il servizio di routing di eventi di Griglia di eventi di Azure per creare processi automatizzati ed eseguire azioni di App per la logica di Azure basate sugli eventi dell'hub IoT.
services: iot-hub, event-grid
author: philmea
ms.service: iot-hub
ms.topic: tutorial
ms.date: 09/14/2020
ms.author: philmea
ms.custom: devx-track-azurecli
ms.openlocfilehash: 5092aa0b5b23f04af1f49933bca234815f03f454
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90604596"
---
# <a name="tutorial-send-email-notifications-about-azure-iot-hub-events-using-event-grid-and-logic-apps"></a>Esercitazione: Esercitazione: Inviare notifiche di posta elettronica sugli eventi dell'hub IoT di Azure usando Griglia di eventi e App per la logica

Griglia di eventi di Azure consente di rispondere agli eventi dell'hub IoT attivando azioni nelle applicazioni aziendali downstream.

Questo articolo illustra una configurazione di esempio che usa l'hub IoT e Griglia di eventi. Al termine, si ottiene un'app per la logica di Azure configurata per l'invio di un messaggio di posta elettronica di notifica ogni volta che un dispositivo si connette all'hub IoT o di disconnette dallo stesso. La Griglia di eventi può essere usata per ricevere una notifica tempestiva sulla disconnessione dei dispositivi critici. Le metriche e la diagnostica possono richiedere diversi minuti (ad esempio 20 o di più, anche se non si vuole indicare un numero) per la visualizzazione in log/avvisi. Condizione che potrebbe essere inaccettabile per un'infrastruttura critica.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure attiva. Se non si ha una sottoscrizione, è possibile [creare un account Azure gratuito](https://azure.microsoft.com/pricing/free-trial/).

* Un account di un provider di posta elettronica supportato da App per la logica di Azure, ad esempio Office 365 Outlook o Outlook.com. Questo account di posta elettronica viene usato per inviare le notifiche degli eventi. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-an-iot-hub"></a>Creazione di un hub IoT

È possibile creare rapidamente un nuovo hub IoT usando il terminale Azure Cloud Shell nel portale.

1. Accedere al [portale di Azure](https://portal.azure.com). 

1. Selezionare il pulsante Cloud Shell in alto a destra nella pagina.

   ![Pulsante Cloud Shell](./media/publish-iot-hub-events-to-logic-apps/portal-cloud-shell.png)

1. Usare il comando seguente per creare un nuovo gruppo di risorse:

   ```azurecli
   az group create --name {your resource group name} --location westus
   ```
    
1. Per creare un hub IoT, eseguire il comando seguente:

   ```azurecli
   az iot hub create --name {your iot hub name} --resource-group {your resource group name} --sku S1 
   ```

1. Ridurre a icona il terminale Azure Cloud Shell. Si tornerà alla shell più avanti nell'esercitazione.

## <a name="create-a-logic-app"></a>Creare un'app per la logica

Successivamente, creare un'app per la logica e aggiungere un trigger di griglia di eventi HTTP che elabora le richieste dall'hub IoT. 

### <a name="create-a-logic-app-resource"></a>Creare una risorsa di App per la logica

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Crea una risorsa**, quindi digitare "app per la logica" nella casella di ricerca e premere INVIO. Selezionare **App per la logica** nei risultati.

   ![Creare l'app per la logica](./media/publish-iot-hub-events-to-logic-apps/select-logic-app.png)

1. Nella schermata successiva selezionare **Crea**. 

1. Assegnare all'app per la logica un nome che sia univoco nella sottoscrizione e quindi selezionare la stessa sottoscrizione, lo stesso gruppo di risorse e la stessa posizione dell'hub IoT. 

   ![Campi per la creazione di un'app per la logica](./media/publish-iot-hub-events-to-logic-apps/create-logic-app-fields.png)

1. Selezionare **Rivedi e crea**.

1. Verificare le impostazioni e quindi selezionare **Crea**.

1. Dopo aver creato la risorsa, selezionare **Vai alla risorsa**. 

1. Nella finestra di progettazione di App per la logica, scorrere in basso fino a visualizzare **Modelli**. Scegliere **App per la logica vuota** per creare un'app per la logica completamente nuova.

### <a name="select-a-trigger"></a>Selezionare un trigger

Un trigger è un evento specifico che avvia l'app per la logica. Per questa esercitazione, il trigger che attiva il flusso di lavoro riceve una richiesta tramite HTTP.  

1. Nella barra di ricerca dei trigger e dei connettori digitare **HTTP**.

1. Scorrere i risultati e selezionare **Richiesta - Alla ricezione di una richiesta HTTP** come trigger. 

   ![Selezionare il trigger di richiesta HTTP](./media/publish-iot-hub-events-to-logic-apps/http-request-trigger.png)

1. Selezionare **Usare il payload di esempio per generare lo schema**. 

   ![Usare un payload di esempio](./media/publish-iot-hub-events-to-logic-apps/sample-payload.png)

1. Incollare il file JSON *Device connected event schema* nella casella di testo e quindi selezionare **Fine**:

   ```json
     [{  
      "id": "f6bbf8f4-d365-520d-a878-17bf7238abd8",
      "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
      "subject": "devices/LogicAppTestDevice",
      "eventType": "Microsoft.Devices.DeviceConnected",
      "eventTime": "2018-06-02T19:17:44.4383997Z",
      "data": {
          "deviceConnectionStateEventInfo": {
            "sequenceNumber":
              "000000000000000001D4132452F67CE200000002000000000000000000000001"
          },
        "hubName": "egtesthub1",
        "deviceId": "LogicAppTestDevice",
        "moduleId" : "DeviceModuleID"
      }, 
      "dataVersion": "1",
      "metadataVersion": "1"
    }]
   ```

   Questo evento viene pubblicato quando un dispositivo è connesso a un hub IoT.

> [!NOTE]
> È possibile che venga inviata una notifica popup con la dicitura **Ricordare di includere nella richiesta un'intestazione Content-Type impostata su application/json**. Questo suggerimento può essere tranquillamente ignorato ed è possibile passare alla sezione successiva. 

### <a name="create-an-action"></a>Creare un'azione

Le azioni sono i passaggi eseguiti dopo che il trigger ha avviato il flusso di lavoro dell'app per la logica. Per questa esercitazione, l'azione è costituita dall'invio di una notifica dal provider di posta elettronica. 

1. Selezionare **Nuovo passaggio**. Viene visualizzata la finestra **Scegliere un'azione**.

1. Cercare **Outlook**.

1. Selezionare il connettore corrispondente al provider di posta elettronica in uso. Questa esercitazione usa **Outlook.com**. I passaggi per altri provider di posta elettronica sono del tutto simili. 

   ![Selezionare il connettore del provider di posta elettronica](./media/publish-iot-hub-events-to-logic-apps/outlook-step.png)

1. Selezionare l'azione **Invia un messaggio di posta elettronica (v2)**. 

1. Selezionare **Accedi** e accedere al proprio account di posta elettronica. Selezionare **Sì** per consentire all'app di accedere alle informazioni.

1. Creare il modello di messaggio di posta elettronica. 

   * **A**: immettere l'indirizzo di posta elettronica per la ricezione dei messaggi di notifica. Per questa esercitazione, usare un account di posta elettronica a cui è possibile accedere per i test. 

   * **Soggetto**: inserire il testo dell'oggetto. Facendo clic nella casella di testo Oggetto, è possibile selezionare il contenuto dinamico da includere. Ad esempio, questa esercitazione usa `IoT Hub alert: {eventType}`. Se il contenuto dinamico non viene visualizzato, selezionare il collegamento ipertestuale **Aggiungi contenuto dinamico**, che consente di attivare e disattivare questa opzione.

   * **Corpo**: scrivere il testo del messaggio di posta elettronica. Selezionare le proprietà JSON tramite lo strumento di selezione per includere il contenuto dinamico in base ai dati degli eventi. Se il contenuto dinamico non viene visualizzato, selezionare il collegamento ipertestuale **Aggiungi contenuto dinamico** sotto la casella di testo **Corpo**. Se i campi desiderati non vengono visualizzati, fare clic su *altro* nella schermata Contenuto dinamico per includere i campi dell'azione precedente.

   Il modello di messaggio di posta elettronica creato sarà simile all'esempio seguente:

   ![Immettere le informazioni del messaggio di posta elettronica](./media/publish-iot-hub-events-to-logic-apps/email-content.png)

1. Selezionare **Salva** nella finestra di progettazione di App per la logica.  

### <a name="copy-the-http-url"></a>Copiare l'URL HTTP

Prima di uscire da Progettazione app per la logica, copiare l'URL su cui è in ascolto l'app per la logica per un trigger. Questo URL viene usato per configurare Griglia di eventi. 

1. Espandere la casella di configurazione del trigger **Alla ricezione di una richiesta HTTP** facendo clic sull'opzione. 

1. Copiare il valore di **URL POST HTTP** facendo clic sul pulsante di copia che si trova accanto. 

   ![Copiare l'URL POST HTTP](./media/publish-iot-hub-events-to-logic-apps/copy-url.png)

1. Salvare l'URL per poterlo usare nella sezione successiva. 

## <a name="configure-subscription-for-iot-hub-events"></a>Configurare la sottoscrizione degli eventi dell'hub IoT

In questa sezione viene configurato l'hub IoT per la pubblicazione degli eventi che si verificano. 

1. Nel portale di Azure passare all'hub IoT. A questo scopo, selezionare **Gruppi di risorse**, quindi selezionare il gruppo di risorse per questa esercitazione e infine l'hub IoT nell'elenco di risorse.

1. Selezionare **Eventi**.

   ![Aprire i dettagli di Griglia di eventi](./media/publish-iot-hub-events-to-logic-apps/event-grid.png)

1. Selezionare **Sottoscrizione di eventi**. 

   ![Creare una nuova sottoscrizione di eventi](./media/publish-iot-hub-events-to-logic-apps/event-subscription.png)

1. Creare la sottoscrizione di eventi con i valori seguenti: 

   1. Nella sezione **DETTAGLI SOTTOSCRIZIONE EVENTI**:
      1. Specificare un **nome** per la sottoscrizione di eventi. 
      2. Selezionare **Schema griglia di eventi** come valore di **Schema evento**. 
   2. Nella sezione **DETTAGLI ARGOMENTO**:
      1. Verificare che **Tipo di argomento** sia impostato su **Hub IoT**. 
      2. Verificare che il nome dell'hub IoT sia impostato come valore per il campo **Risorsa di origine**. 
      3. Immettere un nome per l'**argomento del sistema** che verrà creato automaticamente. Per informazioni sugli argomenti di sistema, vedere [Panoramica degli argomenti di sistema](system-topics.md).
   3. Nella sezione **TIPI DI EVENTO**:
      1. Selezionare l'elenco a discesa **Filtra per tipi di evento**.
      1. Deselezionare le caselle di controllo **Dispositivo creato** e **Dispositivo eliminato**, lasciando selezionate solo le caselle di controllo **Dispositivo connesso** e **Dispositivo disconnesso**.

         ![selezionare i tipi di eventi della sottoscrizione](./media/publish-iot-hub-events-to-logic-apps/subscription-event-types.png)
   
   4. Nella sezione **DETTAGLI ENDPOINT**: 
       1. Selezionare **Webhook** come valore di **Tipo di endpoint**.
       2. Fare clic su **Seleziona endpoint**, incollare l'URL copiato dall'app per la logica e confermare la selezione.

         ![URL di Selezione endpoint](./media/publish-iot-hub-events-to-logic-apps/endpoint-webhook.png)

         Al termine, verrà visualizzato un riquadro simile all'esempio seguente: 

         ![Modulo sottoscrizione di eventi di esempio](./media/publish-iot-hub-events-to-logic-apps/subscription-form.png)

1.  Selezionare **Crea**.

## <a name="simulate-a-new-device-connecting-and-sending-telemetry"></a>Simulare un nuovo dispositivo che si connette e invia dati di telemetria

Testare l'app per la logica simulando rapidamente una connessione del dispositivo tramite l'interfaccia della riga di comando di Azure. 

1. Selezionare il pulsante Cloud Shell per riaprire il terminale.

1. Eseguire il comando seguente per creare un'identità del dispositivo simulato:
    
     ```azurecli 
    az iot hub device-identity create --device-id simDevice --hub-name {YourIoTHubName}
    ```

1. Eseguire il comando seguente per simulare la connessione del dispositivo all'hub IoT e l'invio dei dati di telemetria:

    ```azurecli
    az iot device simulate -d simDevice -n {YourIoTHubName}
    ```

1. Quando il dispositivo simulato si connette all'hub IoT, si riceverà un messaggio di posta elettronica di notifica di un evento "DeviceConnected".

1. Quando la simulazione viene completata, si riceverà un messaggio di posta elettronica di notifica di un evento "DeviceDisconnected". 

    ![Messaggio di avviso di esempio](./media/publish-iot-hub-events-to-logic-apps/alert-mail.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Questa esercitazione ha usato risorse che generano addebiti sulla sottoscrizione di Azure. Al termine dell'esercitazione e dopo aver testato i risultati ottenuti, disabilitare o eliminare le risorse che non si vogliono mantenere. 

Per eliminare tutte le risorse create in questa esercitazione, eliminare il gruppo di risorse. 

1. Selezionare **Gruppi di risorse** e quindi il gruppo di risorse creato per questa esercitazione.

2. Nel riquadro Gruppo di risorse selezionare **Elimina gruppo di risorse**. Viene richiesto di immettere il nome del gruppo di risorse, quindi è possibile eliminarlo. Vengono rimosse anche tutte le risorse contenute al suo interno.

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni su come [rispondere agli eventi dell'hub IoT usando Griglia di eventi per attivare le azioni](../iot-hub/iot-hub-event-grid.md).
* [Informazioni sull'ordinamento di eventi di connessione e disconnessione dispositivi](../iot-hub/iot-hub-how-to-order-connection-state-events.md)
* Informazioni sulle altre operazioni che è possibile eseguire con [Griglia di eventi](overview.md).

Per un elenco completo dei connettori per App per la logica supportati, vedere [Panoramica dei connettori](/connectors/).
