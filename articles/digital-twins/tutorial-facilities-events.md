---
title: Acquisire eventi da uno spazio di Gemelli digitali di Azure | Microsoft Docs
description: Informazioni su come ricevere notifiche dagli spazi grazie all'integrazione di Gemelli digitali di Azure con App per la logica seguendo i passaggi descritti in questa esercitazione.
services: digital-twins
author: dsk-2015
ms.service: digital-twins
ms.topic: tutorial
ms.date: 10/15/2018
ms.author: dkshir
ms.openlocfilehash: 91dd16938efbd1e24419352f66e3238646a77e8a
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2018
ms.locfileid: "49323285"
---
# <a name="tutorial-receive-notifications-from-your-azure-digital-twins-spaces-using-logic-apps"></a>Esercitazione: Ricevere notifiche dagli spazi di Gemelli digitali di Azure usando App per la logica

Dopo aver distribuito l'istanza di Gemelli digitali di Azure, avere effettuato il provisioning degli spazi e avere implementato funzioni personalizzate per il monitoraggio di condizioni specifiche, è possibile inviare una notifica di posta elettronica all'amministratore dell'ufficio quando si verificano le condizioni monitorate. 

Nella [prima esercitazione](tutorial-facilities-setup.md) è stato configurato il grafico spaziale di un edificio immaginario, con una stanza contenente sensori relativi a movimento, anidride carbonica e temperatura. Nella [seconda esercitazione](tutorial-facilities-udf.md) è stato effettuato il provisioning del grafico e di una funzione personalizzata detta funzione definita dall'utente per monitorare i valori dei sensori e attivare le notifiche quando la stanza è vuota e la temperatura e i livelli di anidride carbonica rientrano in un intervallo adeguato. Questa esercitazione illustra come integrare queste notifiche con App per la logica di Azure per inviare messaggi di posta elettronica quando è disponibile una stanza con tali condizioni. Un amministratore dell'ufficio può usare queste informazioni per aiutare i dipendenti a prenotare la sala riunioni più produttiva. 

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Integrare eventi con Griglia di eventi
> * Inviare notifiche per gli eventi con l'app per la logica
    
## <a name="prerequisites"></a>Prerequisiti

Questa esercitazione presuppone che siano già state effettuate le attività di [configurazione](tutorial-facilities-setup.md) e [provisioning](tutorial-facilities-udf.md) dell'installazione di Gemelli digitali di Azure. Prima di procedere, assicurarsi di avere:
- Un [account Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Un'istanza di Gemelli digitali in esecuzione.
- Gli [esempi C# di Gemelli digitali](https://github.com/Azure-Samples/digital-twins-samples-csharp) scaricati ed estratti nel computer di lavoro.
- [.NET Core SDK versione 2.1.403 o successiva](https://www.microsoft.com/net/download) nel computer di sviluppo per eseguire l'esempio. Eseguire `dotnet --version` per verificare se è installata la versione corretta. 
- Un account Office 365 per inviare notifiche tramite messaggi di posta elettronica.

## <a name="integrate-events-with-event-grid"></a>Integrare eventi con Griglia di eventi 
In questa sezione si configura [Griglia di eventi](../event-grid/overview.md) per raccogliere gli eventi dell'istanza di Gemelli digitali e reindirizzarli a un [gestore eventi](../event-grid/event-handlers.md) come App per la logica.

### <a name="create-event-grid-topic"></a>Creare un argomento di Griglia di eventi
Gli [argomenti di Griglia di eventi](../event-grid/concepts.md#topics) forniscono un'interfaccia per instradare gli eventi generati dalla funzione definita dall'utente. 

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Nel riquadro di spostamento a sinistra fare clic su **Crea una risorsa**. 

1. Cercare un **argomento di Griglia di eventi** e selezionarlo. Fare clic su **Create**(Crea).

1. In **Nome** immettere un nome per l'argomento di Griglia di eventi e scegliere un valore per **Sottoscrizione**. In **Gruppo di risorse** selezionare il gruppo di risorse usato o creato per l'istanza di Gemelli digitali e quindi selezionare un'opzione in **Località**. Fare clic su **Create**(Crea). 

    ![Creare un argomento di Griglia di eventi](./media/tutorial-facilities-events/create-event-grid-topic.png)

1. Passare all'argomento di Griglia di eventi dal gruppo di risorse, fare clic su **Panoramica** e copiare il valore di **Endpoint argomento** in un file temporaneo. Questo URL sarà necessario nella sezione successiva. 

1. Fare clic su **Chiavi di accesso** e copiare i valori di **Chiave 1** e **Chiave 2** in un file temporaneo. Questi valori saranno necessari per creare l'endpoint nella sezione successiva.

    ![Chiavi di Griglia di eventi](./media/tutorial-facilities-events/event-grid-keys.png)

### <a name="create-an-endpoint-for-the-event-grid-topic"></a>Creare un endpoint per l'argomento di Griglia di eventi

1. Nella finestra di comando assicurarsi di trovarsi nella cartella **_occupancy-quickstart\src_** dell'esempio di Gemelli digitali.

1. Aprire il file **_actions\createEndpoints.yaml_** nell'editor di Visual Studio Code. Assicurarsi che siano presenti i contenuti seguenti:

    ```yaml
    - type: EventGrid
      eventTypes:
      - SensorChange
      - SpaceChange
      - TopologyOperation
      - UdfCustom
      connectionString: Primary_connection_string_for_your_Event_Grid
      secondaryConnectionString: Secondary_connection_string_for_your_Event_Grid
      path: Event_Grid_Topic_Path
    ```

1. Sostituire il segnaposto `Primary_connection_string_for_your_Event_Grid` con il valore di **Chiave 1**. 

1. Sostituire il segnaposto `Secondary_connection_string_for_your_Event_Grid` con il valore di **Chiave 2**.

1. Sostituire il segnaposto `Event_Grid_Topic_Path` con il percorso dell'argomento di Griglia di eventi. Per ottenere questo percorso, rimuovere *https://* e i percorsi delle risorse finali dall'URL di **Endpoint argomento**. Il formato dovrebbe essere simile al seguente: **NomeGrigliaDiEventi.Località.eventgrid.azure.net**. 

    > [!IMPORTANT]
    > Immettere tutti i valori senza virgolette. Verificare che sia presente almeno uno spazio dopo i due punti nel file *YAML*. È anche possibile convalidare i contenuti del file *YAML* usando qualsiasi validator YAML online, come [questo strumento](https://onlineyamltools.com/validate-yaml).

1. Salvare e chiudere il file. Nella finestra di comando eseguire il comando seguente e accedere quando richiesto. 

    ```cmd/sh
    dotnet run CreateEndpoints
    ```

   Questo comando consente di creare l'endpoint per Griglia di eventi. 

   ![Endpoint per Griglia di eventi](./media/tutorial-facilities-events/dotnet-create-endpoints.png)


## <a name="notify-events-with-logic-app"></a>Inviare notifiche per gli eventi con l'app per la logica
Il servizio [App per la logica di Azure](../logic-apps/logic-apps-overview.md) consente di creare attività automatizzate per gli eventi ricevuti da altri servizi. In questa sezione si configurerà il servizio App per la logica per creare notifiche di posta elettronica per gli eventi instradati dai sensori spaziali, con l'aiuto di un [argomento di Griglia di eventi](../event-grid/overview.md).

1. Nel riquadro di spostamento a sinistra del [portale di Azure](https://portal.azure.com) fare clic su **Crea una risorsa**.

1. Cercare una nuova risorsa **App per la logica** e selezionarla. Fare clic su **Create**(Crea).

1. In **Nome** immettere un nome per l'app per la logica e quindi selezionare le opzioni per **Sottoscrizione**, **Gruppo di risorse** e **Località**. Fare clic su **Create**(Crea).

    ![Creare un'app per la logica](./media/tutorial-facilities-events/create-logic-app.png)

1. Aprire l'app per la logica dopo averla distribuita e quindi aprire il riquadro **Progettazione app per la logica**. 

1. Selezionare il trigger **When an Event Grid event occurs** (Quando si verifica un evento di Griglia di eventi). **Accedere** al tenant con l'account Azure, quando richiesto. Quando richiesto, fare clic su **Consenti l'accesso** per consentire l'accesso a Griglia di eventi. Fare clic su **Continue**.

1. Nella finestra **When a resource event occurs (Preview)** (Quando si verifica un evento della risorsa - Anteprima): 
    1. In **Sottoscrizione** selezionare la sottoscrizione usata in precedenza per creare l'istanza di Griglia di eventi.

    1. Selezionare **Microsoft.EventGrid.Topics** per **Tipo di risorsa**.

    1. Selezionare la risorsa di Griglia di eventi nella casella di riepilogo a discesa per **Nome risorsa**.

    ![Creare un'app per la logica](./media/tutorial-facilities-events/logic-app-resource-event.png)

1. Fare clic sul pulsante **Nuovo passaggio**.

1. Nella finestra **Scegliere un'azione**:
    1. Cercare la frase *analizza json* e selezionare l'azione **Analizza JSON**.

    1. Fare clic nel campo **Contenuto** e selezionare **Corpo** nell'elenco **Contenuto dinamico**.

    1. Fare clic su **Usare il payload di esempio per generare lo schema**. Incollare il payload JSON seguente e quindi fare clic su **Fine**.

        ```JSON
        {
        "id": "32162f00-a8f1-4d37-aee2-9312aabba0fd",
        "subject": "UdfCustom",
        "data": {
          "TopologyObjectId": "20efd3a8-34cb-4d96-a502-e02bffdabb14",
          "ResourceType": "Space",
          "Payload": "\"Air quality is poor.\"",
          "CorrelationId": "32162f00-a8f1-4d37-aee2-9312aabba0fd"
        },
        "eventType": "UdfCustom",
        "eventTime": "0001-01-01T00:00:00Z",
        "dataVersion": "1.0",
        "metadataVersion": "1",
        "topic": "/subscriptions/a382ee71-b48e-4382-b6be-eec7540cf271/resourceGroups/HOL/providers/Microsoft.EventGrid/topics/DigitalTwinEventGrid"
        }
        ```
    
    Il payload ha valori fittizi. L'app per la logica usa il payload di esempio per generare uno **schema**.
    
    ![Analisi JSON nell'app per la logica per Griglia di eventi](./media/tutorial-facilities-events/logic-app-parse-json.png)

1. Fare clic sul pulsante **Nuovo passaggio**.

1. Nella finestra **Scegliere un'azione**:
    1. Cercare e selezionare **Condition Control** (Controllo condizione) nell'elenco **Azioni**. 

    1. Fare clic all'interno della prima casella di testo **Scegliere un valore** e selezionare **Tipo di evento** nell'elenco **Contenuto dinamico** della finestra **Analizza JSON**.

    1. Fare clic all'interno della seconda casella di testo **Scegliere un valore** e digitare *UdfCustom*.

    ![Analisi JSON nell'app per la logica per Griglia di eventi](./media/tutorial-facilities-events/logic-app-condition.png)

1. Nella finestra **È true**:
    1. Fare clic su **Aggiungi un'azione** e selezionare *Office 365 Outlook*.

    1. Nell'elenco **Azioni** selezionare **Invia un messaggio di posta elettronica**. Fare clic su **Accedi** e usare le credenziali del proprio account di posta elettronica. Fare clic su **Consenti l'accesso**, quando viene richiesto.

    1. Nella casella **A** immettere l'ID di posta elettronica per la ricezione delle notifiche. In **Oggetto** immettere il testo *Notifica di Gemelli digitali per la qualità scadente dell'aria nello spazio* e quindi selezionare **TopologyObjectId** nell'elenco **Contenuto dinamico** per **Analizza JSON**.

    1. In **Corpo**, nella stessa finestra, immettere un testo simile al seguente: *In una stanza è stata rilevata una qualità scadente dell'aria ed è necessario regolare la temperatura*. Elaborare a piacimento il messaggio usando gli elementi dell'elenco **Contenuto dinamico** come illustrato di seguito.

    ![Invio di un messaggio di posta elettronica tramite l'app per la logica](./media/tutorial-facilities-events/logic-app-send-email.png)

1. Fare clic sul pulsante **Salva** nella parte superiore del riquadro **Progettazione app per la logica**.

1. Assicurarsi di simulare i dati dei sensori passando alla cartella **_device-connectivity_** dell'esempio di Gemelli digitali in una finestra di comando ed eseguendo `dotnet run`.

In pochi minuti, si dovrebbe iniziare a ricevere notifiche di posta elettronica dall'app per la logica. 

   ![Invio di un messaggio di posta elettronica tramite l'app per la logica](./media/tutorial-facilities-events/logic-app-notification.png)

Per smettere di ricevere questi messaggi, passare all'**app per la logica** nel portale e selezionare il riquadro **Panoramica**. Fare clic su **Disabilita**.


## <a name="clean-up-resources"></a>Pulire le risorse

Se si non si vuole esplorare ulteriormente Gemelli digitali di Azure, è possibile eliminare le risorse create in questa esercitazione:

1. Nel [portale di Azure](http://portal.azure.com) fare clic su **Tutte le risorse** nel menu a sinistra, selezionare il gruppo di risorse di Gemelli digitali e quindi fare clic su **Elimina**.
2. Se necessario, è possibile procedere all'eliminazione delle applicazioni di esempio anche nel computer di lavoro. 


## <a name="next-steps"></a>Passaggi successivi

È possibile passare all'esercitazione successiva per informazioni su come visualizzare i dati dei sensori, analizzare le tendenze e individuare le anomalie. 
> [!div class="nextstepaction"]
> [Esercitazione: Visualizzare e analizzare gli eventi dagli spazi di Gemelli digitali di Azure usando Time Series Insights](tutorial-facilities-analyze.md)

Continuare con gli articoli successivi per altre informazioni sui grafici di intelligenza spaziale e sui modelli a oggetti in Gemelli digitali di Azure. 
> [!div class="nextstepaction"]
> [Informazioni sui modelli a oggetti di Gemelli digitali e sul grafico di intelligenza spaziale](concepts-objectmodel-spatialgraph.md)