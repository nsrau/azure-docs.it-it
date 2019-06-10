---
title: Esempi dell'integrazione del bus di servizio di Azure in Griglia di eventi | Microsoft Docs
description: Questo articolo fornisce esempi della messaggistica del bus di servizio e dell'integrazione in Griglia di eventi.
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: f99766cb-8f4b-4baf-b061-4b1e2ae570e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: spelluru
ms.openlocfilehash: b7dbc7dbc0b670de81a3f4603b0d52bce7559af8
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/31/2019
ms.locfileid: "66428310"
---
# <a name="respond-to-azure-service-bus-events-received-via-azure-event-grid-by-using-azure-functions-and-azure-logic-apps"></a>Rispondere agli eventi del bus di servizio di Azure ricevuti tramite Griglia di eventi di Azure usando Funzioni di Azure e App per la logica di Azure
Questa esercitazione descrive come rispondere agli eventi del bus di servizio di Azure ricevuti tramite Griglia di eventi di Azure usando Funzioni di Azure e App per la logica di Azure. Verranno eseguiti i passaggi seguenti:
 
- Creare una funzione di Azure di test per il debug e la visualizzazione del flusso iniziale di eventi da Griglia di eventi.
- Creare una funzione di Azure per ricevere ed elaborare i messaggi del bus di servizio in base agli eventi di Griglia di eventi.
- Creare un'app per la logica per rispondere agli eventi di Griglia di eventi

Dopo aver creato gli artefatti del bus di servizio, di Griglia di eventi, di Funzioni di Azure e di App per la logica, eseguire le operazioni seguenti: 

1. Inviare messaggi a un argomento del bus di servizio. 
2. Verificare che le sottoscrizioni dell'argomento abbiano ricevuto tali messaggi
3. Verificare che la funzione o l'app che ha sottoscritto l'evento abbia ricevuto l'evento. 

## <a name="create-a-service-bus-namespace"></a>Creare uno spazio dei nomi del bus di servizio
Seguire le istruzioni disponibili nell'esercitazione [Guida introduttiva: Usare il portale di Azure per creare un argomento del bus di servizio e le sottoscrizioni all'argomento](service-bus-quickstart-topics-subscriptions-portal.md) per eseguire le attività seguenti:

- Creare uno spazio dei nomi del bus di servizio **Premium**. 
- Ottenere la stringa di connessione. 
- Creare un argomento del bus di servizio.
- Creare due sottoscrizioni dell'argomento. 

## <a name="prepare-a-sample-application-to-send-messages"></a>Preparare un'applicazione di esempio per inviare messaggi
È possibile inviare un messaggio all'argomento del bus di servizio con qualsiasi metodo. Il codice di esempio alla fine di questa procedura presuppone che si usi Visual Studio 2017.

1. Clonare [il repository azure-service-bus di GitHub](https://github.com/Azure/azure-service-bus/).
2. In Visual Studio andare alla cartella *\samples\DotNet\Microsoft.ServiceBus.Messaging\ServiceBusEventGridIntegration* e aprire il file *SBEventGridIntegration.sln*.
3. Andare al progetto **MessageSender** e quindi selezionare **Program.cs**.
4. Immettere il nome dell'argomento del bus di servizio e la stringa di connessione ottenuta nel passaggio precedente:

    ```CSharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    const string TopicName = "YOUR TOPIC NAME";
    ```
5. Compilare ed eseguire il programma per inviare messaggi di test all'argomento del bus di servizio. 

## <a name="set-up-a-test-function-on-azure"></a>Configurare una funzione di test in Azure 
Prima di affrontare l'intero scenario, configurare almeno una semplice funzione di test utilizzabile per il debug e per osservare gli eventi che vengono trasmessi. Seguire le istruzioni incluse nell'articolo [Creare la prima funzione nel portale di Azure](../azure-functions/functions-create-first-azure-function.md) per eseguire le attività seguenti: 

1. Creare un'app per le funzioni.
2. Creare una funzione attivata tramite HTTP. 

Eseguire la procedura seguente: 


# <a name="azure-functions-v2tabv2"></a>[Funzioni di Azure v2](#tab/v2)

1. Espandere **Funzioni** nella visualizzazione struttura ad albero e selezionare la funzione. Sostituire il codice della funzione con il codice seguente: 

    ```CSharp
    #r "Newtonsoft.Json"
    
    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;
    
    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");
        var content = req.Body;
        string jsonContent = await new StreamReader(content).ReadToEndAsync();
        log.LogInformation($"Received Event with payload: {jsonContent}");
    
        IEnumerable<string> headerValues;
        headerValues = req.Headers.GetCommaSeparatedValues("Aeg-Event-Type");
    
        if (headerValues.Count() != 0)
        {
            var validationHeaderValue = headerValues.FirstOrDefault();
            if(validationHeaderValue == "SubscriptionValidation")
            {
                var events = JsonConvert.DeserializeObject<GridEvent[]>(jsonContent);
                var code = events[0].Data["validationCode"];
                log.LogInformation("Validation code: {code}");
                return (ActionResult) new OkObjectResult(new { validationResponse = code });
            }
        }
    
        return jsonContent == null
            ? new BadRequestObjectResult("Please pass a name on the query string or in the request body")
            : (ActionResult)new OkObjectResult($"Hello, {jsonContent}");
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
2. Selezionare **Salva ed esegui**.

    ![Output dell'app per le funzioni](./media/service-bus-to-event-grid-integration-example/function-run-output.png)
3. Selezionare **Recupera URL della funzione** e prendere nota dell'URL. 

    ![Ottenere l'URL della funzione](./media/service-bus-to-event-grid-integration-example/get-function-url.png)

# <a name="azure-functions-v1tabv1"></a>[Funzioni di Azure v1](#tab/v1)

1. Configurare la funzione in modo che usi la versione **v1**: 
    1. Selezionare l'app per le funzioni nella visualizzazione struttura ad albero e selezionare **Impostazioni dell'app per le funzioni**. 

        ![Impostazioni dell'app per le funzioni]()./media/service-bus-to-event-grid-integration-example/function-app-settings.png)
    2. Selezionare **~1** per **Versione runtime**. 
2. Espandere **Funzioni** nella visualizzazione struttura ad albero e selezionare la funzione. Sostituire il codice della funzione con il codice seguente: 

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
4. Selezionare **Salva ed esegui**.

    ![Output dell'app per le funzioni](./media/service-bus-to-event-grid-integration-example/function-run-output.png)
4. Selezionare **Recupera URL della funzione** e prendere nota dell'URL. 

    ![Ottenere l'URL della funzione](./media/service-bus-to-event-grid-integration-example/get-function-url.png)

---

## <a name="connect-the-function-and-namespace-via-event-grid"></a>Connettere la funzione e lo spazio dei nomi tramite Griglia di eventi
In questa sezione la funzione e lo spazio dei nomi del bus di servizio vengono collegati tra loro tramite il portale di Azure. 

Per creare una sottoscrizione di Griglia di eventi di Azure, seguire questa procedura:

1. Nel portale di Azure passare allo spazio dei nomi e quindi, nel riquadro sinistro, selezionare **Eventi**. Verrà aperta la finestra dello spazio dei nomi, con due sottoscrizioni di Griglia di eventi visualizzate nel riquadro destro. 
    
    ![Bus di servizio - pagina Eventi](./media/service-bus-to-event-grid-integration-example/service-bus-events-page.png)
2. Selezionare **+ Sottoscrizione di eventi** sulla barra degli strumenti. 
3. Nella pagina **Crea sottoscrizione di eventi** seguire questa procedura:
    1. Immettere un **nome** per la sottoscrizione. 
    2. Selezionare **Webhook** per **Tipo di endpoint**. 

        ![Bus di servizio - sottoscrizione di Griglia di eventi](./media/service-bus-to-event-grid-integration-example/event-grid-subscription-page.png)
    3. Scegliere **Seleziona endpoint**, incollare l'URL della funzione e quindi selezionare **Confermare la selezione**. 

        ![Funzione - selezione dell'endpoint](./media/service-bus-to-event-grid-integration-example/function-select-endpoint.png)
    4. Passare alla scheda **Filtri**, immettere il nome della **prima sottoscrizione** dell'argomento del bus di servizio creato in precedenza e quindi selezionare il pulsante **Crea**. 

        ![Filtro della sottoscrizione di eventi](./media/service-bus-to-event-grid-integration-example/event-subscription-filter.png)
4. Verificare che la sottoscrizione di eventi sia presente nell'elenco.

    ![Sottoscrizione di eventi nell'elenco](./media/service-bus-to-event-grid-integration-example/event-subscription-in-list.png)

## <a name="send-messages-to-the-service-bus-topic"></a>Inviare messaggi all'argomento del bus di servizio
1. Eseguire l'applicazione .NET C# che invia messaggi all'argomento del bus di servizio. 

    ![Output dell'app console](./media/service-bus-to-event-grid-integration-example/console-app-output.png)
1. Nella pagina dell'app per le funzioni di Azure, espandere **Funzioni**, espandere la **funzione** e selezionare **Monitoraggio**. 

    ![Funzione Monitoraggio](./media/service-bus-to-event-grid-integration-example/function-monitor.png)

## <a name="receive-messages-by-using-azure-functions"></a>Ricevere messaggi usando Funzioni di Azure
Nella sezione precedente si è osservato un semplice scenario di test e debug e si è verificato che gli eventi vengano trasmessi. 

In questa sezione verrà illustrato come ricevere ed elaborare i messaggi dopo avere ricevuto un evento.

### <a name="publish-a-function-from-visual-studio"></a>Pubblicare una funzione da Visual Studio
1. Nella stessa soluzione di Visual Studio (**SBEventGridIntegration**) aperta selezionare **ReceiveMessagesOnEvent.cs** nel progetto **SBEventGridIntegration**. 
2. Immettere la stringa di connessione del bus di servizio nel codice seguente:

    ```Csharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    ```
3. Scaricare il **profilo di pubblicazione** per la funzione:
    1. Selezionare l'app per le funzioni. 
    2. Selezionare la scheda **Panoramica** se non è già selezionata. 
    3. Selezionare **Recupera profilo di pubblicazione** sulla barra degli strumenti. 

        ![Recupero del profilo di pubblicazione per la funzione](./media/service-bus-to-event-grid-integration-example/function-download-publish-profile.png)
    4. Salvare il file nella cartella del progetto. 
4. In Visual Studio fare clic con il pulsante destro del mouse su **SBEventGridIntegration** e quindi scegliere **Pubblica**. 
5. Selezionare *Avvia** nella pagina **Pubblica**. 
6. Nella pagina **Selezionare una destinazione di pubblicazione** eseguire la procedura seguente e selezionare **Importa profilo**. 

    ![Visual Studio - pulsante Importa profilo](./media/service-bus-to-event-grid-integration-example/visual-studio-import-profile-button.png)
7. Selezionare il **file del profilo di pubblicazione** scaricato in precedenza. 
8. Selezionare **Pubblica** nella pagina **Pubblica**. 

    ![Visual Studio - Pubblica](./media/service-bus-to-event-grid-integration-example/select-publish.png)
9. Verificare che la nuova funzione di Azure **ReceiveMessagesOnEvent** sia visibile. Se necessario, aggiornare la pagina. 

    ![Verificare che la nuova funzione sia stata creata](./media/service-bus-to-event-grid-integration-example/function-receive-messages.png)
10. Ottenere l'URL della nuova funzione e prenderne nota. 

### <a name="event-grid-subscription"></a>Sottoscrizione di Griglia di eventi

1. Eliminare la sottoscrizione di Griglia di eventi esistente:
    1. Nella pagina **Spazio dei nomi del bus di servizio** selezionare **Eventi** nel menu a sinistra. 
    2. Selezionare la sottoscrizione di eventi esistente. 
    3. Nella pagina **Sottoscrizione di eventi** selezionare **Elimina**.
2. Seguire le istruzioni della sezione [Connettere la funzione e lo spazio dei nomi tramite Griglia di eventi](#connect-the-function-and-namespace-via-event-grid) per creare una sottoscrizione di Griglia di eventi usando l'URL della nuova funzione.
3. Seguire le istruzioni nella sezione [Inviare messaggi all'argomento del bus di servizio](#send-messages-to-the-service-bus-topic) per inviare messaggi all'argomento e monitorare la funzione. 

## <a name="receive-messages-by-using-logic-apps"></a>Ricevere messaggi usando App per la logica
Connettere un'app per la logica al bus di servizio di Azure e a Griglia di eventi di Azure seguendo questa procedura:

1. Creare un'app per la logica nel portale di Azure.
    1. Selezionare **+ Crea una risorsa**, selezionare **Integrazione** e quindi **App per la logica**. 
    2. Nella pagina **App per la logica - Crea** immettere un **nome** per l'app per la logica.
    3. Selezionare la **sottoscrizione**di Azure. 
    4. Selezionare **Usa esistente** per **Gruppo di risorse** e selezionare il gruppo di risorse usato per le altre risorse create in precedenza, ad esempio funzione di Azure o spazio dei nomi del bus di servizio. 
    5. Selezionare il **percorso** per l'app per la logica. 
    6. Fare clic su **Crea** per creare l'app per la logica. 
2. Nella pagina **Progettazione app per la logica** selezionare **App per la logica vuota** in **Modelli**. 
3. Nella finestra di progettazione seguire questa procedura:
    1. Cercare **Griglia di eventi**. 
    2. Selezionare **Quando si verifica un evento della risorsa (anteprima) - Griglia di eventi di Azure**. 

        ![Progettazione app per la logica - selezione del trigger di Griglia di eventi](./media/service-bus-to-event-grid-integration-example/logic-apps-event-grid-trigger.png)
4. Selezionare **Accedi**, immettere le credenziali di Azure e selezionare **Consenti accesso**. 
5. Nella pagina **Quando si verifica un evento della risorsa** seguire questa procedura:
    1. Selezionare la sottoscrizione di Azure. 
    2. Per **Tipo di risorsa**, selezionare **Microsoft.ServiceBus.Namespaces**. 
    3. Per **Nome risorsa** selezionare lo spazio dei nomi del bus di servizio. 
    4. Selezionare **Aggiungi nuovo parametro** e quindi **Filtro per suffisso**. 
    5. Per **Filtro per suffisso** immettere il nome della seconda sottoscrizione dell'argomento del bus di servizio. 
        ![Progettazione app per la logica - configurazione dell'evento](./media/service-bus-to-event-grid-integration-example/logic-app-configure-event.png)
6. Selezionare **+ Nuovo passaggio** nella finestra di progettazione e seguire la procedura seguente:
    1. Cercare **Bus di servizio**.
    2. Selezionare **Bus di servizio** nell'elenco. 
    3. Selezionare **Recupera messaggi** nell'elenco **Azioni**. 
    4. Selezionare **Recupera i messaggi dalla sottoscrizione dell'argomento (blocco visualizzazione)** . 

        ![Progettazione app per la logica - recupero dell'azione per i messaggi](./media/service-bus-to-event-grid-integration-example/service-bus-get-messages-step.png)
    5. Immettere un **nome per la connessione**. Ad esempio:  **Recuperare i messaggi della sottoscrizione dell'argomento** e selezionare lo spazio dei nomi del bus di servizio. 

        ![Progettazione app per la logica - selezione dello spazio dei nomi del bus di servizio](./media/service-bus-to-event-grid-integration-example/logic-apps-select-namespace.png) 
    6. Selezionare **RootManageSharedAccessKey**.

        ![Progettazione app per la logica - selezione della chiave di accesso condiviso](./media/service-bus-to-event-grid-integration-example/logic-app-shared-access-key.png) 
    7. Selezionare **Create** (Crea). 
    8. Selezionare l'argomento e la sottoscrizione. 
    
        ![Progettazione app per la logica - selezione dell'argomento e della sottoscrizione del bus di servizio](./media/service-bus-to-event-grid-integration-example/logic-app-select-topic-subscription.png)
7. Selezionare **+ Nuovo passaggio** e seguire la procedura seguente: 
    1. Selezionare **Bus di servizio**.
    2. Selezionare **Completa il messaggio in una sottoscrizione dell'argomento** nell'elenco di azioni. 
    3. Selezionare l'**argomento** del bus di servizio.
    4. Selezionare la seconda **sottoscrizione** dell'argomento.
    5. Per **Token di blocco del messaggio** selezionare **Token di blocco** in **Contenuto dinamico**. 

        ![Progettazione app per la logica - selezione dell'argomento e della sottoscrizione del bus di servizio](./media/service-bus-to-event-grid-integration-example/logic-app-complete-message.png)
8. Per salvare le app per la logica, selezionare **Salva** sulla barra degli strumenti di Progettazione app per la logica. 
9. Seguire le istruzioni nella sezione [Inviare messaggi all'argomento del bus di servizio](#send-messages-to-the-service-bus-topic) per inviare messaggi all'argomento. 
10. Passare alla pagina **Panoramica** dell'app per la logica. Le esecuzioni dell'app per la logica sono visualizzate nella **Cronologia esecuzioni** dei messaggi inviati.

    ![Progettazione app per la logica - esecuzioni dell'app per la logica](./media/service-bus-to-event-grid-integration-example/logic-app-runs.png)

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
