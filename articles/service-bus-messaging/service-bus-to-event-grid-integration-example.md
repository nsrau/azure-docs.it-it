---
title: Esempi dell'integrazione del bus di servizio di Azure in Griglia di eventi | Microsoft Docs
description: Questo articolo fornisce esempi della messaggistica del bus di servizio e dell'integrazione in Griglia di eventi.
services: service-bus-messaging
documentationcenter: .net
author: ChristianWolf42
manager: timlt
editor: ''
ms.assetid: f99766cb-8f4b-4baf-b061-4b1e2ae570e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: get-started-article
ms.date: 02/15/2018
ms.author: chwolf
ms.openlocfilehash: fd30a8eb5149647a24ff04e099bf5c3e187459ef
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="azure-service-bus-to-azure-event-grid-integration-examples"></a>Esempi dell'integrazione del bus di servizio di Azure in Griglia di eventi di Azure

Questo articolo illustra come configurare una funzione di Azure e un'app per la logica, che ricevono entrambe messaggi in base alla ricezione di un evento da Griglia di eventi di Azure. A questo si eseguiranno le attività seguenti:
 
* Creare una semplice [funzione di Azure di test](#test-function-setup) per il debug e la visualizzazione del flusso iniziale di eventi da Griglia di eventi. Eseguire questo passaggio indipendentemente dal fatto che si eseguano gli altri.
* Creare una [funzione di Azure per ricevere ed elaborare i messaggi del bus di servizio](#receive-messages-using-azure-function) in base agli eventi di Griglia di eventi.
* Utilizzare la [funzionalità App per la logica del Servizio app di Azure](#receive-messages-using-azure-logic-app).

Nell'esempio creato si presuppone che l'argomento del bus di servizio abbia due sottoscrizioni. Nell'esempio si presuppone anche che la sottoscrizione di Griglia di eventi sia stata creata per inviare gli eventi solo per una sottoscrizione del bus di servizio. 

Nell'esempio si inviano messaggi all'argomento del bus di servizio e quindi si verifica che l'evento sia stato generato per questa sottoscrizione del bus di servizio. La funzione o l'app per la logica riceve i messaggi dalla sottoscrizione del bus di servizio e quindi li completa.

## <a name="prerequisites"></a>prerequisiti
Prima di iniziare, verificare di avere completato i passaggi nelle due sezioni successive.

### <a name="create-a-service-bus-namespace"></a>Creare uno spazio dei nomi del bus di servizio

Creare uno spazio dei nomi Premium del bus di servizio e un argomento del bus di servizio con due sottoscrizioni.

### <a name="send-a-message-to-the-service-bus-topic"></a>Inviare un messaggio all'argomento del bus di servizio

È possibile inviare un messaggio all'argomento del bus di servizio con qualsiasi metodo. Il codice di esempio alla fine di questa procedura presuppone che si usi Visual Studio 2017.

1. Clonare [il repository azure-service-bus di GitHub](https://github.com/Azure/azure-service-bus/).

2. In Visual Studio andare alla cartella *\samples\DotNet\Microsoft.ServiceBus.Messaging\ServiceBusEventGridIntegration* e aprire il file *SBEventGridIntegration.sln*.

3. Andare al progetto **MessageSender** e quindi selezionare **Program.cs**.

   ![8][]

4. Immettere il nome dell'argomento e la stringa di connessione e quindi eseguire il codice dell'applicazione console seguente:

    ```CSharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    const string TopicName = "YOUR TOPIC NAME";
    ```

## <a name="set-up-a-test-function"></a>Configurare una funzione di test

Prima di affrontare l'intero scenario, configurare almeno una semplice funzione di test utilizzabile per il debug e per osservare quali eventi vengono trasmessi.

1. Nel portale di Azure creare una nuova applicazione per Funzioni di Azure. Per i concetti di base di Funzioni di Azure, vedere [Documentazione di Funzioni di Azure](https://docs.microsoft.com/en-us/azure/azure-functions/).

2. Nella funzione appena creata selezionare il segno più (+) per aggiungere una funzione trigger HTTP:

    ![2][]
    
    Verrà aperta la finestra **Iniziare rapidamente con una funzione preconfezionata**.

    ![3][]

3. Selezionare il pulsante **Webhook e API**, selezionare **CSharp** e quindi selezionare **Creare questa funzione**.
 
4. Nella funzione incollare il codice seguente:

    ```CSharp
    #r "Newtonsoft.Json"
    using System.Net;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;
    
    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        log.Info("C# HTTP trigger function processed a request.");
        // parse query parameter
        var content = req.Content;
    
        string jsonContent = await content.ReadAsStringAsync(); 
        log.Info($"Received Event with payload: {jsonContent}");
    
    IEnumerable<string> headerValues;
    if (req.Headers.TryGetValues("Aeg-Event-Type", out headerValues))
    {
    var validationHeaderValue = headerValues.FirstOrDefault();
    if(validationHeaderValue == "SubscriptionValidation")
    {
    var events = JsonConvert.DeserializeObject<GridEvent[]>(jsonContent);
         var code = events[0].Data["validationCode"];
         return req.CreateResponse(HttpStatusCode.OK,
         new { validationResponse = code });
    }
    }
    
        return jsonContent == null
        ? req.CreateResponse(HttpStatusCode.BadRequest, "Pass a name on the query string or in the request body")
        : req.CreateResponse(HttpStatusCode.OK, "Hello " + jsonContent);
    }
    
    public class GridEvent
    {
        public string Id { get; set; }
        public string EventType { get; set; }
        public string Subject { get; set; }
        public DateTime EventTime { get; set; }
        public Dictionary<string, string> Data { get; set; }
        public string Topic { get; set; }
    }
    ```

5. Selezionare **Salva ed esegui**.

## <a name="connect-the-function-and-namespace-via-event-grid"></a>Connettere la funzione e lo spazio dei nomi tramite Griglia di eventi

In questa sezione si collegano tra loro la funzione e lo spazio dei nomi del bus di servizio. Per questo esempio, usare il portale di Azure. Per informazioni su come usare PowerShell o l'interfaccia della riga di comando di Azure per eseguire questa procedura, vedere [Panoramica dell'integrazione del bus di servizio di Azure in Griglia di eventi di Azure](service-bus-to-event-grid-integration-concept.md).

Per creare una nuova sottoscrizione di Griglia di eventi di Azure, seguire questa procedura:
1. Nel portale di Azure andare allo spazio dei nomi e quindi nel riquadro sinistro selezionare **Griglia eventi**.  
    Verrà aperta la finestra dello spazio dei nomi, con due sottoscrizioni di Griglia di eventi visualizzate nel riquadro destro.

    ![20][]

2. Selezionare **Sottoscrizione di eventi**.  
    Verrà aperta la finestra **Sottoscrizione di eventi**. L'immagine seguente mostra un modulo per la sottoscrizione di una funzione di Azure o di un webhook senza applicare filtri.

    ![21][]

3. Completare il modulo come illustrato e ricordare di immettere il filtro pertinente nella casella **Filtro per suffisso**.

4. Selezionare **Create**.

5. Inviare un messaggio all'argomento del bus di servizio, come indicato nella sezione "Prerequisiti" e quindi verificare che gli eventi vengano trasmessi tramite la funzionalità Monitoraggio di Funzioni di Azure.

Il passaggio successivo consiste nel collegare tra loro la funzione e lo spazio dei nomi del bus di servizio. Per questo esempio, usare il portale di Azure. Per informazioni su come usare PowerShell o l'interfaccia della riga di comando di Azure per eseguire questo passaggio, vedere [Panoramica dell'integrazione del bus di servizio di Azure in Griglia di eventi di Azure](service-bus-to-event-grid-integration-concept.md).

![9][]

### <a name="receive-messages-by-using-azure-functions"></a>Ricevere messaggi usando Funzioni di Azure

Nella sezione precedente si è osservato un semplice scenario di test e debug e si è verificato che gli eventi vengano trasmessi. 

In questa sezione verrà illustrato come ricevere ed elaborare i messaggi dopo avere ricevuto un evento.

Si aggiungerà una funzione di Azure, come illustrato nell'esempio seguente, perché le funzioni del bus di servizio in Funzioni di Azure non supportano ancora a livello nativo la nuova integrazione in Griglia di eventi.

1. Nella stessa soluzione di Visual Studio aperta nei prerequisiti selezionare **ReceiveMessagesOnEvent.cs**. 

    ![10][]

2. Immettere la stringa di connessione nel codice seguente:

    ```Csharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    ```

3. Nel portale di Azure scaricare il profilo di pubblicazione per la funzione di Azure creata nella sezione "Configurare una funzione di test".

    ![11][]

4. In Visual Studio fare clic con il pulsante destro del mouse su **SBEventGridIntegration** e quindi scegliere **Pubblica**. 

5. Nel riquadro **Pubblica** per il profilo di pubblicazione scaricato in precedenza selezionare **Importa profilo** e quindi **Pubblica**.

    ![12][]

6. Dopo aver pubblicato la nuova funzione di Azure, creare una nuova sottoscrizione di Griglia di eventi di Azure che punti alla nuova funzione di Azure.  
    Nella casella **Termina con** assicurarsi di applicare il filtro corretto, corrispondente al nome della sottoscrizione del bus di servizio.

7. Inviare un messaggio all'argomento del bus di servizio di Azure creato in precedenza e quindi monitorare il log di Funzioni di Azure nel portale di Azure per assicurarsi che gli eventi vengano trasmessi e i messaggi ricevuti.

    ![12-1][]

### <a name="receive-messages-by-using-logic-apps"></a>Ricevere messaggi usando App per la logica

Connettere un'app per la logica con il bus di servizio di Azure e Griglia di eventi di Azure seguendo questa procedura:

1. Creare una nuova app per la logica nel portale di Azure e selezionare **Griglia di eventi** come azione di avvio.

    ![13][]

    Verrà aperta la finestra di progettazione di App per la logica.

    ![14][]

2. Aggiungere le informazioni seguendo questa procedura:

    a. Nella casella **Nome risorsa** immettere il nome dello spazio dei nomi. 

    b. In **Opzioni avanzate** immettere il filtro per la sottoscrizione nella casella **Filtro per suffisso**.

3. Aggiungere un'azione di ricezione per il bus di servizio per poter ricevere i messaggi da una sottoscrizione dell'argomento.  
    L'azione finale è indicata nell'immagine seguente:

    ![15][]

4. Aggiungere un evento completo, come illustrato nell'immagine seguente:

    ![16][]

5. Salvare l'app per la logica e inviare un messaggio all'argomento del bus di servizio come illustrato nella sezione "Prerequisiti".  
    Osservare l'esecuzione dell'app per la logica. Per visualizzare altri dati per l'esecuzione, selezionare **Panoramica** e quindi visualizzare i dati in **Cronologia esecuzioni**.

    ![17][]

    ![18][]

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni su [Griglia di eventi di Azure](https://docs.microsoft.com/azure/event-grid/).
* Altre informazioni su [Funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/).
* Altre informazioni sulla [funzionalità App per la logica del Servizio app di Azure](https://docs.microsoft.com/azure/logic-apps/).
* Altre informazioni sul [bus di servizio di Azure](https://docs.microsoft.com/azure/service-bus/).


[2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid2.png
[3]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid3.png
[7]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid7.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[10]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid10.png
[11]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid11.png
[12]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12.png
[12-1]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-1.png
[12-2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-2.png
[13]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid13.png
[14]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid14.png
[15]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid15.png
[16]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid16.png
[17]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid17.png
[18]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid18.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[21]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png
