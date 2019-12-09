---
title: "Esercitazione: Usare eventi dell'hub IoT per attivare App per la logica di Azure"
description: Questa esercitazione mostra come usare il servizio di routing di eventi di Griglia di eventi di Azure per creare processi automatizzati ed eseguire azioni di App per la logica di Azure basate sugli eventi dell'hub IoT.
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: tutorial
ms.date: 11/21/2019
ms.author: robinsh
ms.openlocfilehash: 334b7b2c59b328e8eff3c7c2b9c3ed46bffc3442
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74706442"
---
# <a name="tutorial-send-email-notifications-about-azure-iot-hub-events-using-event-grid-and-logic-apps"></a>Esercitazione: Esercitazione: Inviare notifiche di posta elettronica sugli eventi dell'hub IoT di Azure usando Griglia di eventi e App per la logica

Griglia di eventi di Azure consente di rispondere agli eventi dell'hub IoT attivando azioni nelle applicazioni aziendali downstream.

Questo articolo illustra una configurazione di esempio che usa l'hub IoT e Griglia di eventi. Al termine, si ottiene un'app per la logica di Azure configurata per l'invio di un messaggio di posta elettronica di notifica ogni volta che viene aggiunto un dispositivo all'hub IoT. 

## <a name="prerequisites"></a>Prerequisiti

* Un account di un provider di posta elettronica supportato da App per la logica di Azure, ad esempio Office 365 Outlook, Outlook.com o Gmail. Questo account di posta elettronica viene usato per inviare le notifiche degli eventi. Per un elenco completo dei connettori per App per la logica supportati, vedere [Panoramica dei connettori](https://docs.microsoft.com/connectors/).
* Un account Azure attivo. Se non si ha un account, è possibile [crearne uno gratuito](https://azure.microsoft.com/pricing/free-trial/).
* Un hub IoT in Azure. Se l'hub non è stato ancora creato, vedere [Introduzione all'hub IoT](../iot-hub/iot-hub-csharp-csharp-getstarted.md) per la procedura dettagliata. 

## <a name="create-a-logic-app"></a>Creare un'app per la logica

Per prima cosa, creare un'app per la logica e aggiungere un trigger di Griglia di eventi che monitori il gruppo di risorse per la macchina virtuale. 

### <a name="create-a-logic-app-resource"></a>Creare una risorsa di App per la logica

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Crea una risorsa**, quindi digitare "app per la logica" nella casella di ricerca e premere INVIO. Selezionare **App per la logica** nei risultati.

   ![Creare l'app per la logica](./media/publish-iot-hub-events-to-logic-apps/select-logic-app.png)

1. Nella schermata successiva selezionare **Crea**. 

1. Assegnare all'app per la logica un nome che sia univoco nella sottoscrizione e quindi selezionare la stessa sottoscrizione, lo stesso gruppo di risorse e la stessa posizione dell'hub IoT. 

   ![Campi per la creazione di un'app per la logica](./media/publish-iot-hub-events-to-logic-apps/create-logic-app-fields.png)

1. Selezionare **Create** (Crea).

1. Dopo aver creato la risorsa, passare all'app per la logica. A questo scopo, selezionare **Gruppi di risorse** e quindi il gruppo di risorse creato per questa esercitazione. Individuare quindi l'app per la logica nell'elenco di risorse e selezionarla. 

1. Nella finestra di progettazione di App per la logica, scorrere in basso fino a visualizzare **Modelli**. Scegliere **App per la logica vuota** per creare un'app per la logica completamente nuova.

### <a name="select-a-trigger"></a>Selezionare un trigger

Un trigger è un evento specifico che avvia l'app per la logica. Per questa esercitazione, il trigger che attiva il flusso di lavoro riceve una richiesta tramite HTTP.  

1. Nella barra di ricerca dei trigger e dei connettori digitare **HTTP**.

1. Selezionare **Richiesta - Alla ricezione di una richiesta HTTP** come trigger. 

   ![Selezionare il trigger di richiesta HTTP](./media/publish-iot-hub-events-to-logic-apps/http-request-trigger.png)

1. Selezionare **Usare il payload di esempio per generare lo schema**. 

   ![Selezionare il trigger di richiesta HTTP](./media/publish-iot-hub-events-to-logic-apps/sample-payload.png)

1. Incollare l'esempio di codice JSON seguente nella casella di testo e quindi selezionare **Fine**:

   ```json
   [{
     "id": "56afc886-767b-d359-d59e-0da7877166b2",
     "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
     "subject": "devices/LogicAppTestDevice",
     "eventType": "Microsoft.Devices.DeviceCreated",
     "eventTime": "2018-01-02T19:17:44.4383997Z",
     "data": {
       "twin": {
         "deviceId": "LogicAppTestDevice",
         "etag": "AAAAAAAAAAE=",
         "deviceEtag": "null",
         "status": "enabled",
         "statusUpdateTime": "0001-01-01T00:00:00",
         "connectionState": "Disconnected",
         "lastActivityTime": "0001-01-01T00:00:00",
         "cloudToDeviceMessageCount": 0,
         "authenticationType": "sas",
         "x509Thumbprint": {
           "primaryThumbprint": null,
           "secondaryThumbprint": null
         },
         "version": 2,
         "properties": {
           "desired": {
             "$metadata": {
               "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
             },
             "$version": 1
           },
           "reported": {
             "$metadata": {
               "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
             },
             "$version": 1
           }
         }
       },
       "hubName": "egtesthub1",
       "deviceId": "LogicAppTestDevice"
     },
     "dataVersion": "1",
     "metadataVersion": "1"
   }]
   ```

1. È possibile che venga inviata una notifica popup con la dicitura **Ricordare di includere nella richiesta un'intestazione Content-Type impostata su application/json**. Questo suggerimento può essere tranquillamente ignorato ed è possibile passare alla sezione successiva. 

### <a name="create-an-action"></a>Creare un'azione

Le azioni sono i passaggi eseguiti dopo che il trigger ha avviato il flusso di lavoro dell'app per la logica. Per questa esercitazione, l'azione è costituita dall'invio di una notifica dal provider di posta elettronica. 

1. Selezionare **Nuovo passaggio**. Viene visualizzata la finestra **Scegliere un'azione**.

1. Cercare **Posta elettronica**.

1. Selezionare il connettore corrispondente al provider di posta elettronica in uso. Questa esercitazione usa **Office 365 Outlook**. I passaggi per altri provider di posta elettronica sono del tutto simili. 

   ![Selezionare il connettore del provider di posta elettronica](./media/publish-iot-hub-events-to-logic-apps/o365-outlook.png)

1. Selezionare l'azione **Invia un messaggio di posta elettronica**. 

1. Se richiesto, accedere all'account di posta elettronica. 

1. Creare il modello di messaggio di posta elettronica. 

   * **A**: immettere l'indirizzo di posta elettronica per ricevere i messaggi di notifica. Per questa esercitazione, usare un account di posta elettronica a cui è possibile accedere per il test. 

   * **Soggetto**: inserire il testo dell'oggetto. Facendo clic nella casella di testo Oggetto, è possibile selezionare il contenuto dinamico da includere. Ad esempio, questa esercitazione usa `IoT Hub alert: {event Type}`. Se il contenuto dinamico non viene visualizzato, selezionare il collegamento ipertestuale **Aggiungi contenuto dinamico**, che consente di attivare e disattivare questa opzione.

   * **Corpo**: scrivere il testo del messaggio di posta elettronica. Selezionare le proprietà JSON tramite lo strumento di selezione per includere il contenuto dinamico in base ai dati degli eventi. Se il contenuto dinamico non viene visualizzato, selezionare il collegamento ipertestuale **Aggiungi contenuto dinamico** sotto la casella di testo **Corpo**. Se i campi desiderati non vengono visualizzati, fare clic su *altro* nella schermata Contenuto dinamico per includere i campi dell'azione precedente.

   Il modello di messaggio di posta elettronica creato sarà simile all'esempio seguente:

   ![Immettere le informazioni del messaggio di posta elettronica](./media/publish-iot-hub-events-to-logic-apps/email-content.png)

1. Salvare l'app per la logica. 

### <a name="copy-the-http-url"></a>Copiare l'URL HTTP

Prima di uscire da Progettazione app per la logica, copiare l'URL su cui è in ascolto l'app per la logica per un trigger. Questo URL viene usato per configurare Griglia di eventi. 

1. Espandere la casella di configurazione del trigger **Alla ricezione di una richiesta HTTP** facendo clic sull'opzione. 

1. Copiare il valore di **URL POST HTTP** facendo clic sul pulsante di copia che si trova accanto. 

   ![Copiare l'URL POST HTTP](./media/publish-iot-hub-events-to-logic-apps/copy-url.png)

1. Salvare l'URL per poterlo usare nella sezione successiva. 

## <a name="configure-subscription-for-iot-hub-events"></a>Configurare la sottoscrizione degli eventi dell'hub IoT

In questa sezione viene configurato l'hub IoT per la pubblicazione degli eventi che si verificano. 

1. Nel portale di Azure passare all'hub IoT. A questo scopo, selezionare **Gruppi di risorse**, quindi selezionare il gruppo di risorse per questa esercitazione e infine l'hub IoT nell'elenco di risorse.

2. Selezionare **Eventi**.

   ![Aprire i dettagli di Griglia di eventi](./media/publish-iot-hub-events-to-logic-apps/event-grid.png)

3. Selezionare **Sottoscrizione di eventi**. 

   ![Creare una nuova sottoscrizione di eventi](./media/publish-iot-hub-events-to-logic-apps/event-subscription.png)

4. Creare la sottoscrizione di eventi con i valori seguenti: 

   * **Dettagli sottoscrizione evento**: fornire un nome descrittivo e selezionare **Schema Griglia di eventi**.

   * **Tipi di eventi**: in **Filtra per tipi di evento** deselezionare tutte le opzioni ad eccezione di **Il dispositivo è stato creato**.

       ![Tipi di eventi della sottoscrizione](./media/publish-iot-hub-events-to-logic-apps/subscription-event-types.png)

   * **Dettagli endpoint**: come Tipo di endpoint selezionare **Webhook**, fare clic su *Selezione endpoint*, incollare l'URL copiato dall'app per la logica e confermare la selezione.

     ![URL di Selezione endpoint](./media/publish-iot-hub-events-to-logic-apps/endpoint-webhook.png)

   Al termine, verrà visualizzato un riquadro simile all'esempio seguente: 

    ![Modulo sottoscrizione di eventi di esempio](./media/publish-iot-hub-events-to-logic-apps/subscription-form.png)

5. È possibile salvare la sottoscrizione di eventi e ricevere notifiche per ogni dispositivo creato nell'hub IoT. Per questa esercitazione, tuttavia, si vogliono usare i campi facoltativi per filtrare dispositivi specifici. Selezionare **Filtri** nella parte superiore del riquadro.

6. Selezionare **Aggiungi nuovo filtro**. Compilare i campi con questi valori:

   * **Chiave**: Selezionare `Subject`.

   * **Operatore**: Selezionare `String begins with`.

   * **Value**:  immettere `devices/Building1_` per filtrare gli eventi del dispositivo nell'edificio 1.
  
   Aggiungere un altro filtro con questi valori:

   * **Chiave**: Selezionare `Subject`.

   * **Operatore**: Selezionare `String ends with`.

   * **Value**: immettere `_Temperature` per filtrare gli eventi del dispositivo correlati alla temperatura.

   La scheda **Filtri** della sottoscrizione di eventi avrà sarà simile all'immagine seguente:

   ![Aggiunta di filtri a una sottoscrizione di eventi](./media/publish-iot-hub-events-to-logic-apps/event-subscription-filters.png)

7. Selezionare **Crea** per salvare la sottoscrizione di eventi.

## <a name="create-a-new-device"></a>Creare un nuovo dispositivo

Testare l'app per la logica creando un nuovo dispositivo per l'attivazione di un messaggio di posta elettronica di notifica degli eventi. 

1. Nell'hub IoT selezionare **IoT Devices** (Dispositivi IoT). 

2. Selezionare **Nuovo**.

3. In **ID dispositivo** immettere `Building1_Floor1_Room1_Light`.

4. Selezionare **Salva**. 

5. È possibile aggiungere più dispositivi con ID diversi per testare i filtri della sottoscrizione di eventi. Provare con questi esempi: 

   * Building1_Floor1_Room1_Light
   * Building1_Floor2_Room2_Temperature
   * Building2_Floor1_Room1_Temperature
   * Building2_Floor1_Room1_Light

   Se sono stati aggiunti i quatto esempi, l'elenco di dispositivi IoT sarà simile all'immagine seguente:

   ![Elenco di dispositivi dell'hub IoT](./media/publish-iot-hub-events-to-logic-apps/iot-hub-device-list.png)

6. Dopo aver aggiunto alcuni dispositivi all'hub IoT, controllare la posta elettronica per vedere quali sono quelli che hanno attivato l'app per la logica. 

## <a name="use-the-azure-cli"></a>Utilizzare l’interfaccia della riga di comando di Azure

Anziché tramite il portale di Azure, è possibile eseguire le procedure relative all'hub IoT usando l'interfaccia della riga di comando di Azure. Per informazioni dettagliate, vedere le pagine relative all'interfaccia della riga di comando di Azure per la [creazione di una sottoscrizione di eventi](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription) e la [creazione di un dispositivo IoT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity).

## <a name="clean-up-resources"></a>Pulire le risorse

Questa esercitazione ha usato risorse che generano addebiti sulla sottoscrizione di Azure. Al termine dell'esercitazione e dopo aver testato i risultati ottenuti, disabilitare o eliminare le risorse che non si vogliono mantenere. 

Per eliminare tutte le risorse create in questa esercitazione, eliminare il gruppo di risorse. 

1. Selezionare **Gruppi di risorse** e quindi il gruppo di risorse creato per questa esercitazione.

2. Nel riquadro Gruppo di risorse selezionare **Elimina gruppo di risorse**. Viene richiesto di immettere il nome del gruppo di risorse, quindi è possibile eliminarlo. Vengono rimosse anche tutte le risorse contenute al suo interno.

Se non si vogliono rimuovere tutte le risorse, è possibile gestirle una alla volta. 

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

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni su come [rispondere agli eventi dell'hub IoT usando Griglia di eventi per attivare le azioni](../iot-hub/iot-hub-event-grid.md).
* [Informazioni sull'ordinamento di eventi di connessione e disconnessione dispositivi](../iot-hub/iot-hub-how-to-order-connection-state-events.md)
* Informazioni sulle altre operazioni che è possibile eseguire con [Griglia di eventi](overview.md).