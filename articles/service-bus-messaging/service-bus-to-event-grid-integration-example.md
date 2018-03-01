---
title: Esempi dell'integrazione del bus di servizio di Azure in Griglia di eventi | Microsoft Docs
description: Esempi della messaggistica del bus di servizio e dell'integrazione in Griglia di eventi
services: service-bus-messaging
documentationcenter: .net
author: ChristianWolf42
manager: timlt
editor: 
ms.assetid: f99766cb-8f4b-4baf-b061-4b1e2ae570e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: get-started-article
ms.date: 02/15/2018
ms.author: chwolf
ms.openlocfilehash: 2a4d17673340d145de9a3514f920c74f7eebf6b6
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="azure-service-bus-to-azure-event-grid-examples"></a>Esempi relativi al bus di servizio di Azure e Griglia di eventi di Azure

Questo documento illustra come configurare funzioni di Azure e un'app per la logica per ricevere messaggi in base alla ricezione di un evento da Griglia di eventi. L'esempio presuppone che sia presente un argomento del bus di servizio con due sottoscrizioni e che la sottoscrizione di Griglia di eventi venga creata in modo da inviare eventi per una sola sottoscrizione del bus di servizio. Si inviano quindi messaggi all'argomento del bus di servizio e si verifica che venga generato l'evento per la sottoscrizione del bus di servizio e che la funzione o l'app per la logica riceva i messaggi dalla sottoscrizione del bus di servizio e li completi.

* Per prima cosa assicurarsi che siano presenti tutti i [prerequisiti](#prerequisites).
* Creare una [semplice funzione di Azure di test](#test-function-setup) per il debug e la verifica del flusso iniziale di eventi da Griglia di eventi.  **Questo passaggio dovrà essere completato indipendentemente dall'esecuzione del passaggio 3 o 4.**
* Creare una [funzione di Azure per ricevere ed elaborare i messaggi del bus di servizio](#receive-messages-using-azure-function) in base agli eventi di Griglia di eventi.
* Utilizzare [app per la logica](#receive-messages-using-azure-logic-app).

## <a name="prerequisites"></a>prerequisiti

### <a name="service-bus-namespace"></a>Spazio dei nomi del bus di servizio

Creare uno spazio dei nomi Premium del bus di servizio. Creare un argomento del bus di servizio con due sottoscrizioni.

### <a name="code-to-send-message-to-the-service-bus-topic"></a>Codice per inviare un messaggio all'argomento del bus di servizio

È possibile inviare un messaggio all'argomento del bus di servizio in qualsiasi modo. In alternativa, è possibile usare l'esempio riportato di seguito. Il codice di esempio presuppone che si usi Visual Studio 2017.

Clonare [questo repository GitHub](https://github.com/Azure/azure-service-bus/).

Passare alla cartella seguente:

\samples\DotNet\Microsoft.ServiceBus.Messaging\ServiceBusEventGridIntegration e aprire il file SBEventGridIntegration.sln.

Passare quindi al progetto MessageSender e aprire Program.cs.

![8][]

Immettere il nome dell'argomento e la stringa di connessione ed eseguire l'applicazione console:

```CSharp
const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
const string TopicName = "YOUR TOPIC NAME";
```

## <a name="test-function-setup"></a>Configurazione della funzione di test

Prima di affrontare l'intero scenario end-to-end è opportuno avere almeno una semplice funzione di test utilizzabile per il debug e per verificare quali eventi vengono trasmessi.

Nel portale creare una nuova applicazione per le funzioni di Azure. Per informazioni sui concetti di base di Funzioni di Azure, fare clic su questo [collegamento](https://docs.microsoft.com/en-us/azure/azure-functions/).

Nella funzione appena creata fare clic sul segno più per aggiungere una funzione trigger HTTP:

![2][]

![3][]

Copiare quindi il codice seguente nella funzione:

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

Fare clic su Salva ed esegui.

## <a name="connect-function-and-namespace-via-event-grid"></a>Connettere la funzione e lo spazio dei nomi tramite Griglia di eventi

Il passaggio successivo consiste nel collegare tra loro la funzione e lo spazio dei nomi del bus di servizio. Per questo esempio, usare il portale di Azure. Per informazioni su come eseguire questa operazione con PowerShell o l'interfaccia della riga di comando di Azure, vedere la pagina contenente la [panoramica concettuale](service-bus-to-event-grid-integration-concept.md.

Per creare una nuova sottoscrizione di Griglia di eventi di Azure, passare allo spazio dei nomi nel portale di Azure e selezionare il pannello Griglia di eventi. Fare clic su "+ Sottoscrizione di eventi".

Lo screenshot seguente mostra uno spazio dei nomi che ha già alcune sottoscrizioni di Griglia di eventi.

![20][]

Lo screenshot seguente mostra come creare una sottoscrizione per una funzione di Azure o un webhook senza filtri specifici. Ricordare di aggiungere il filtro pertinente per la sottoscrizione del bus di servizio come "Filtro per suffisso":

![21][]

Inviare un messaggio all'argomento del bus di servizio come indicato nei prerequisiti e verificare che gli eventi vengano trasmessi mediante la funzionalità di monitoraggio della funzione di Azure.

![9][]

### <a name="receive-messages-using-azure-function"></a>Ricevere messaggi usando una funzione di Azure

Nella sezione precedente è stato esaminato un semplice scenario di test e debug e si è verificato che gli eventi vengano trasmessi. In questa parte della documentazione verrà illustrato come ricevere ed elaborare i messaggi alla ricezione di un evento.

Il motivo per aggiungere una funzione di Azure nel modo seguente risiede nel fatto che le funzioni del bus di servizio in Funzioni di Azure non supportano ancora a livello nativo la nuova integrazione in Griglia di eventi.

Nella stessa soluzione di Visual Studio aperta nei prerequisiti selezionare ReceiveMessagesOnEvent.cs. Immettere la stringa di connessione nel codice:

![10][]

```Csharp
const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
```

Passare quindi al portale di Azure e scaricare il profilo di pubblicazione per la funzione di Azure creata in precedenza nel secondo passaggio [ Configurazione della funzione di test](#2-test-function-setup).

![11][]

In Visual Studio fare quindi clic con il pulsante destro del mouse su SBEventGridIntegration e scegliere Pubblica. Usare il profilo di pubblicazione scaricato in precedenza, selezionare Importa profilo e fare clic su Pubblica.

![12][]

Dopo aver pubblicato la nuova funzione di Azure, creare una nuova sottoscrizione di Griglia di eventi di Azure che punti alla nuova funzione di Azure. Assicurarsi di applicare il filtro "Termina con" corretto, corrispondente al nome della sottoscrizione del bus di servizio.

Inviare quindi un messaggio all'argomento del bus di servizio di Azure creato in precedenza e verificare nel log della funzione di Azure nel portale che gli eventi vengano trasmessi e i messaggi ricevuti.

![12-1][]

### <a name="receive-messages-using-azure-logic-app"></a>Ricevere messaggi usando un'app per la logica di Azure

Le istruzioni seguenti illustrano come connettere un'app per la logica di Azure al bus di servizio e Griglia di eventi di Azure.

Per prima cosa, creare una nuova app per la logica nel portale di Azure e selezionare Griglia di eventi come azione di avvio.

![13][]

La visualizzazione iniziale nella finestra di progettazione di app per la logica dovrebbe essere simile allo screenshot seguente. Il valore di "Nome risorsa" dovrà essere sostituito con il nome del proprio spazio dei nomi. Assicurarsi anche di espandere le opzioni avanzate e aggiungere il filtro per suffisso per la propria sottoscrizione:

![14][]

Aggiungere quindi un'azione di ricezione per il bus di servizio per poter ricevere da una sottoscrizione dell'argomento. L'azione finale dovrebbe essere simile allo screenshot seguente.

![15][]

Aggiungere quindi un evento di completamento, che dovrebbe essere come illustrato di seguito.

![16][]

Salvare l'app per la logica e inviare un messaggio all'argomento del bus di servizio come illustrato nei prerequisiti. Osservare quindi l'esecuzione dell'app per la logica. Facendo clic su "Panoramica" e quindi su "Cronologia esecuzioni" è anche possibile ottenere altri dati sull'esecuzione.

![17][]

![18][]

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni su [Griglia di eventi di Azure](https://docs.microsoft.com/azure/event-grid/).
* Altre informazioni su [Funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/).
* Altre informazioni su [App per la logica di Azure](https://docs.microsoft.com/azure/logic-apps/).
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
