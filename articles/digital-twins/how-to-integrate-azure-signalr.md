---
title: Integrazione con Servizio Azure SignalR
titleSuffix: Azure Digital Twins
description: Vedere come trasmettere dati di telemetria di Azure Digital gemelli ai client usando Azure SignalR
author: dejimarquis
ms.author: aymarqui
ms.date: 09/02/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 0931a7e344d6546bd62ceb7513c4aa540f5b9638
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/03/2020
ms.locfileid: "89447908"
---
# <a name="integrate-azure-digital-twins-with-azure-signalr-service"></a>Integrare i dispositivi gemelli digitali di Azure con il servizio Azure SignalR

In questo articolo si apprenderà come integrare i dispositivi gemelli digitali di Azure con il [servizio Azure SignalR](../azure-signalr/signalr-overview.md).

La soluzione descritta in questo articolo consentirà di effettuare il push dei dati di telemetria del gemello digitale ai client connessi, ad esempio una singola pagina Web o un'applicazione per dispositivi mobili. Di conseguenza, i client vengono aggiornati con le metriche in tempo reale e lo stato dei dispositivi Internet, senza dover eseguire il polling del server o inviare nuove richieste HTTP per gli aggiornamenti.

## <a name="prerequisites"></a>Prerequisiti

Di seguito sono riportati i prerequisiti che è necessario completare prima di procedere:

* Prima di integrare la soluzione con il servizio Azure SignalR in questo articolo, è necessario completare l'esercitazione sui dispositivi gemelli di Azure Digital [_**: connettere una soluzione end-to-end**_](tutorial-end-to-end.md), perché questa procedura si basa su di essa. Questa esercitazione illustra la configurazione di un'istanza di Azure Digital gemelli che funziona con un dispositivo di Internet delle cose virtuali per attivare gli aggiornamenti dei dispositivi gemelli digitali. Questa procedura consente di connettere tali aggiornamenti a un'app Web di esempio tramite il servizio Azure SignalR.
    - È necessario il nome dell'argomento di **griglia di eventi** creato nell'esercitazione.
* [**Node.js**](https://nodejs.org/) installato nel computer.

È anche possibile accedere al [portale di Azure](https://portal.azure.com/) con l'account Azure.

## <a name="solution-architecture"></a>Architettura della soluzione

Il servizio Azure SignalR verrà collegato ai dispositivi gemelli digitali di Azure tramite il percorso riportato di seguito. Le sezioni A, B e C nel diagramma sono tratte dal diagramma dell'architettura del [prerequisito dell'esercitazione end-to-end](tutorial-end-to-end.md). in questa procedura verrà compilata aggiungendo la sezione D.

:::image type="content" source="media/how-to-integrate-azure-signalr/signalr-integration-topology.png" alt-text="Visualizzazione dei servizi di Azure in uno scenario end-to-end. Viene illustrato il flusso di dati da un dispositivo all'hub Internet, tramite una funzione di Azure (freccia B) a un'istanza di Azure Digital gemelli (sezione A), quindi tramite griglia di eventi a un'altra funzione di Azure per l'elaborazione (freccia C). La sezione D Mostra il flusso di dati dalla stessa griglia di eventi nella freccia C a una funzione di Azure con etichetta "broadcast". ' Broadcast ' comunica con un'altra funzione di Azure con etichetta ' Negotiate ' è Broadcast ' è Negotiate ' comunicano con i dispositivi computer." lightbox="media/how-to-integrate-azure-signalr/signalr-integration-topology.png":::

## <a name="download-the-sample-applications"></a>Scaricare le applicazioni di esempio

Prima di tutto, scaricare le app di esempio richieste. Sono necessari entrambi gli elementi seguenti:
* [**Esempi di dispositivi gemelli digitali di Azure**](https://docs.microsoft.com/samples/azure-samples/digital-twins-samples/digital-twins-samples/): questo esempio contiene un *AdtSampleApp* con due funzioni di Azure per lo spostamento dei dati in un'istanza di Azure Digital Twins. per informazioni dettagliate su questo scenario, vedere [*esercitazione: connettere una soluzione end-to-end*](tutorial-end-to-end.md). Contiene anche un'applicazione di esempio *DeviceSimulator* che simula un dispositivo molto, generando un nuovo valore di temperatura ogni secondo. 
    - Passare al collegamento di esempio e fare clic sul pulsante *Scarica zip* per scaricare una copia dell'esempio nel computer, come _**Azure_Digital_Twins_samples.zip**_. Decomprimere la cartella.
* [**Esempio di app Web di integrazione SignalR**](https://docs.microsoft.com/samples/azure-samples/digitaltwins-signalr-webapp-sample/digital-twins-samples/): si tratta di un'app Web React di esempio che utilizzerà i dati di telemetria di Azure Digital Twins da un servizio Azure SignalR.
    -  Passare al collegamento di esempio e fare clic sul pulsante *Scarica zip* per scaricare una copia dell'esempio nel computer, come _**Azure_Digital_Twins_SignalR_integration_web_app_sample.zip**_. Decomprimere la cartella.

[!INCLUDE [Create instance](../azure-signalr/includes/signalr-quickstart-create-instance.md)]

Lasciare aperta la finestra del browser alla portale di Azure, che verrà usata di nuovo nella sezione successiva.

## <a name="configure-and-run-the-azure-functions-app"></a>Configurare ed eseguire l'app funzioni di Azure

In questa sezione vengono impostate due funzioni di Azure:
* **Negotiate** : funzione trigger http. Usa l'associazione di input *SignalRConnectionInfo* per generare e restituire informazioni di connessione valide.
* **broadcast** : funzione trigger di [griglia di eventi](../event-grid/overview.md) . Riceve i dati di telemetria di Azure Digital gemelli attraverso la griglia di eventi e usa l'associazione di output dell'istanza di *SignalR* creata nel passaggio precedente per trasmettere il messaggio a tutte le applicazioni client connesse.

Per prima cosa, accedere al browser in cui è aperta la portale di Azure e completare i passaggi seguenti per ottenere la **stringa di connessione** per l'istanza di SignalR configurata. Sarà necessario per configurare le funzioni.
1. Verificare che l'istanza del servizio SignalR distribuita in precedenza sia stata creata correttamente. Questa operazione può essere eseguita cercando il nome nella casella di ricerca nella parte superiore del portale. Selezionare l'istanza per aprirla.

1. Selezionare **chiavi** dal menu istanza per visualizzare le stringhe di connessione per l'istanza del servizio SignalR.

1. Selezionare l'icona per copiare la stringa di connessione primaria.

    :::image type="content" source="media/how-to-integrate-azure-signalr/signalr-keys.png" alt-text="Screenshot del portale di Azure che mostra la pagina chiavi per l'istanza di SignalR. L'icona ' copia negli Appunti ' accanto alla stringa di connessione primaria è evidenziata." lightbox="media/how-to-integrate-azure-signalr/signalr-keys.png":::

Successivamente, avviare Visual Studio (o un altro editor di codice di propria scelta) e aprire la soluzione di codice nella cartella *Azure_Digital_Twins_samples > ADTSampleApp* . Eseguire quindi la procedura seguente per creare le funzioni:

1. Creare una nuova classe Sharp C# denominata **SignalRFunctions.cs** nel progetto *SampleFunctionsApp* .

1. Sostituire il contenuto del file di classe con il codice seguente:

    ```C#
    using System;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Http;
    using Microsoft.Azure.EventGrid.Models;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Extensions.Http;
    using Microsoft.Azure.WebJobs.Extensions.EventGrid;
    using Microsoft.Azure.WebJobs.Extensions.SignalRService;
    using Microsoft.Extensions.Logging;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;
    using System.Collections.Generic;
    
    namespace SampleFunctionsApp
    {
        public static class SignalRFunctions
        {
            public static double temperature;
    
            [FunctionName("negotiate")]
            public static SignalRConnectionInfo GetSignalRInfo(
                [HttpTrigger(AuthorizationLevel.Anonymous, "post")] HttpRequest req,
                [SignalRConnectionInfo(HubName = "dttelemetry")] SignalRConnectionInfo connectionInfo)
            {
                return connectionInfo;
            }
    
            [FunctionName("broadcast")]
            public static Task SendMessage(
                [EventGridTrigger] EventGridEvent eventGridEvent,
                [SignalR(HubName = "dttelemetry")] IAsyncCollector<SignalRMessage> signalRMessages,
                ILogger log)
            {
                JObject eventGridData = (JObject)JsonConvert.DeserializeObject(eventGridEvent.Data.ToString());
    
                log.LogInformation($"Event grid message: {eventGridData}");
    
                var patch = (JObject)eventGridData["data"]["patch"][0];
                if (patch["path"].ToString().Contains("/Temperature"))
                {
                    temperature = Math.Round(patch["value"].ToObject<double>(), 2);
                }
    
                var message = new Dictionary<object, object>
                {
                    { "temperatureInFahrenheit", temperature},
                };
        
                return signalRMessages.AddAsync(
                    new SignalRMessage
                    {
                        Target = "newMessage",
                        Arguments = new[] { message }
                    });
            }
        }
    }
    ```

1. Nella finestra console di *Gestione pacchetti* di Visual Studio o in qualsiasi finestra di comando nel computer nella cartella *Azure_Digital_Twins_samples \adtsampleapp\samplefunctionsapp* eseguire il comando seguente per installare il `SignalRService` pacchetto NuGet nel progetto:
    ```cmd
    dotnet add package Microsoft.Azure.WebJobs.Extensions.SignalRService --version 1.2.0
    ```

    Questa operazione dovrebbe risolvere eventuali problemi di dipendenza nella classe.

Successivamente, pubblicare la funzione in Azure usando la procedura descritta nella [sezione *pubblicare l'app* ](tutorial-end-to-end.md#publish-the-app) dell'esercitazione relativa alla *soluzione di connessione end-to-end* . È possibile pubblicarlo nella stessa app del servizio app o delle funzioni usato nell'esercitazione end-to-end prereq o crearne uno nuovo, ma è possibile usare lo stesso per ridurre al minimo la duplicazione. Inoltre, completare la pubblicazione dell'app con i passaggi seguenti:
1. Raccogliere l' **URL dell'endpoint HTTP**della funzione *Negotiate* . A tale scopo, passare alla pagina app per le [funzioni](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Web%2Fsites/kind/functionapp) del portale di Azure e selezionare l'app per le funzioni dall'elenco. Nel menu dell'app selezionare *funzioni* e scegliere la funzione *Negotiate* .

    :::image type="content" source="media/how-to-integrate-azure-signalr/functions-negotiate.png" alt-text="Visualizzazione portale di Azure dell'app per le funzioni, con ' functions ' evidenziato nel menu. L'elenco di funzioni viene visualizzato nella pagina e viene evidenziata anche la funzione ' Negotiate '.":::

    Fare clic su *Ottieni URL funzione* e copiare il valore **fino a _/API_ (non includere l'ultimo _/Negotiate?_)**. Questa operazione verrà usata in un secondo momento.

    :::image type="content" source="media/how-to-integrate-azure-signalr/get-function-url.png" alt-text="Visualizzazione portale di Azure della funzione ' Negotiate '. Il pulsante ' Ottieni URL funzione ' è evidenziato e la parte dell'URL dall'inizio a'/API '":::

1. Infine, aggiungere la **stringa di connessione** di Azure SignalR precedente alle impostazioni dell'app della funzione usando il comando dell'interfaccia della riga di comando di Azure seguente. Il comando può essere eseguito in [Azure cloud Shell](https://shell.azure.com)o localmente se nel [computer è installata](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)l'interfaccia della riga di comando di Azure:
 
    ```azurecli-interactive
    az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "AzureSignalRConnectionString=<your-Azure-SignalR-ConnectionString>"
    ```

    L'output di questo comando Visualizza tutte le impostazioni dell'app configurate per la funzione di Azure. Cercare `AzureSignalRConnectionString` nella parte inferiore dell'elenco per verificare che sia stato aggiunto.

    :::image type="content" source="media/how-to-integrate-azure-signalr/output-app-setting.png" alt-text="Estratto dell'output in una finestra di comando, che mostra una voce di elenco denominata ' AzureSignalRConnectionString '":::

#### <a name="connect-the-function-to-event-grid"></a>Connettere la funzione a Griglia di eventi

Quindi, sottoscrivere la funzione *broadcast* di Azure all' **argomento di griglia di eventi** creato durante l' [*esercitazione: connettere una soluzione end-to-end*](tutorial-end-to-end.md) prereq. Ciò consentirà ai dati di telemetria di passare dal *thermostat67* gemello all'argomento della griglia di eventi alla funzione, che può essere trasmessa a tutti i client.

A tale scopo, creare una sottoscrizione di **griglia di eventi** dall'argomento della griglia di eventi per la funzione *broadcast* di Azure come endpoint.

Nel [portale di Azure](https://portal.azure.com/) passare all'argomento di Griglia di eventi cercandone il nome nella barra di ricerca in alto. Selezionare *+ Sottoscrizione di eventi*.

:::image type="content" source="media/how-to-integrate-azure-signalr/event-subscription-1b.png" alt-text="Portale di Azure: Sottoscrizione di eventi di Griglia di eventi":::

Nella pagina *Crea sottoscrizione di eventi* compilare i campi come indicato di seguito (i campi compilati per impostazione predefinita non sono indicati):
* *DETTAGLI SOTTOSCRIZIONE EVENTO* > **Nome**: assegnare un nome alla sottoscrizione di eventi.
* *DETTAGLI ENDPOINT* > **Tipo di endpoint** : selezionare *Funzione di Azure* nelle opzioni del menu.
* *DETTAGLI ENDPOINT* > **Endpoint**: scegliere il collegamento *Seleziona un endpoint*. Verrà aperta la finestra *Seleziona funzione di Azure*:
    - Compilare la **sottoscrizione**, il **gruppo di risorse**, l'app per le **funzioni** e la **funzione** (*broadcast*). È possibile che alcuni di questi campi vengano prepopolati dopo aver selezionato la sottoscrizione.
    - Premere **Confermare la selezione**.

:::image type="content" source="media/how-to-integrate-azure-signalr/create-event-subscription.png" alt-text="Portale di Azure la visualizzazione della creazione di una sottoscrizione di eventi. I campi precedenti sono compilati e i pulsanti ' conferma selezione ' è Crea ' sono evidenziati.":::

Di nuovo nella pagina *Crea sottoscrizione di eventi* premere **Crea**.

## <a name="configure-and-run-the-web-app"></a>Configurare ed eseguire l'app Web

In questa sezione verrà visualizzato il risultato in azione. In primo luogo, verrà avviata l'app di esempio per il **dispositivo simulato** che invia dati di telemetria tramite l'istanza di Azure Digital gemelli. Si configurerà quindi l' **app Web client di esempio** per la connessione al flusso di Azure SignalR configurato. Successivamente, dovrebbe essere possibile visualizzare i dati che aggiornano l'app Web di esempio in tempo reale.

### <a name="run-the-device-simulator"></a>Eseguire il simulatore del dispositivo

Durante il prerequisito dell'esercitazione end-to-end, è stato [configurato il simulatore di dispositivi per l'](tutorial-end-to-end.md#configure-and-run-the-simulation) invio di dati tramite un hub Internet e l'istanza di Azure Digital gemelli.

A questo punto, è sufficiente avviare il progetto del simulatore, disponibile in *Azure_Digital_Twins_samples > DeviceSimulator > DeviceSimulator. sln*. Se si usa Visual Studio, è possibile aprire il progetto e quindi eseguirlo con questo pulsante sulla barra degli strumenti:

:::image type="content" source="media/how-to-integrate-azure-signalr/start-button-simulator.png" alt-text="Pulsante di avvio di Visual Studio (progetto DeviceSimulator)":::

Verrà aperta una finestra della console che visualizza i messaggi di telemetria simulati sulle temperature. Questi vengono inviati tramite l'istanza di Azure Digital gemelli, in cui vengono quindi prelevati dalle funzioni di Azure e SignalR.

Non è necessario eseguire altre operazioni in questa console, ma lasciarla in esecuzione mentre si completano i passaggi successivi.

### <a name="configure-the-sample-client-web-app"></a>Configurare l'app Web client di esempio

Configurare quindi l'esempio di **app Web di integrazione SignalR** con i passaggi seguenti:
1. Usando Visual Studio o un editor di codice di propria scelta, aprire la cartella _**Azure_Digital_Twins_SignalR_integration_web_app_sample**_ decompressa scaricata nella sezione [*prerequisiti*](#prerequisites) .

1. Aprire il file *src/App.js* e sostituire l'URL in `HubConnectionBuilder` con l'URL dell'endpoint HTTP della funzione **Negotiate** salvata in precedenza:

    ```javascript
        const hubConnection = new HubConnectionBuilder()
            .withUrl('<URL>')
            .build();
    ```
1. Nel *prompt dei comandi* per gli sviluppatori di Visual Studio o in qualsiasi finestra di comando del computer passare alla cartella *Azure_Digital_Twins_SignalR_integration_web_app_sample \src* . Eseguire il comando seguente per installare i pacchetti del nodo dipendenti:

    ```cmd
    npm install
    ```

Impostare quindi le autorizzazioni nell'app per le funzioni nella portale di Azure:
1. Nella pagina app per le [funzioni](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Web%2Fsites/kind/functionapp) di portale di Azure selezionare l'istanza dell'app per le funzioni.
1. Scorrere verso il basso nel menu istanza e selezionare *CORS*. Nella pagina CORS aggiungere `http://localhost:3000` come origine consentita inserendola nella casella vuota. Selezionare la casella *Abilita Access-Control-Allow-Credentials* e quindi fare clic su *Save*.

    :::image type="content" source="media/how-to-integrate-azure-signalr/cors-setting-azure-function.png" alt-text="Impostazione di CORS nella funzione di Azure":::

### <a name="see-the-results"></a>Visualizzare i risultati

Per visualizzare i risultati in azione, avviare l' **esempio di app Web di integrazione SignalR**. Questa operazione può essere eseguita da qualsiasi finestra della console nel percorso *Azure_Digital_Twins_SignalR_integration_web_app_sample \src* , eseguendo questo comando:

```cmd
npm start
```

Verrà aperta una finestra del browser che esegue l'app di esempio, che visualizza un misuratore della temperatura visiva. Quando l'app è in esecuzione, è consigliabile iniziare a visualizzare i valori di telemetria della temperatura del simulatore di dispositivi che si propagano attraverso i dispositivi gemelli digitali di Azure che vengono riflessi dall'app Web in tempo reale.

:::image type="content" source="media/how-to-integrate-azure-signalr/signalr-webapp-output.png" alt-text="Estratto dall'app Web client di esempio che mostra un misuratore della temperatura visiva. La temperatura riflessa è 67,52":::

## <a name="clean-up-resources"></a>Pulire le risorse

Se le risorse create in questo articolo non sono più necessarie, attenersi alla procedura seguente per eliminarle. 

Usando l'interfaccia della riga di comando di Azure Azure Cloud Shell o locale è possibile eliminare tutte le risorse di Azure in un gruppo di risorse con il comando [AZ Group Delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) . Rimuovere anche il gruppo di risorse...
* istanza di Azure Digital Twins (dall'esercitazione end-to-end)
* l'hub e la registrazione del dispositivo hub (dall'esercitazione end-to-end)
* argomento di griglia di eventi e sottoscrizioni associate
* l'app funzioni di Azure, che include tutte e tre le funzioni e le risorse associate, ad esempio l'archiviazione
* istanza di Azure SignalR

> [!IMPORTANT]
> L'eliminazione di un gruppo di risorse è irreversibile. Il gruppo di risorse e tutte le risorse in esso contenute vengono eliminati in modo permanente. Assicurarsi di non eliminare accidentalmente il gruppo di risorse sbagliato o le risorse errate. 

```azurecli-interactive
az group delete --name <your-resource-group>
```

Se si elimina l'istanza dei dispositivi gemelli digitali di Azure, è anche possibile eliminare la registrazione dell'app Azure AD creata nell'esercitazione end-to-end, usando questo comando:

```azurecli
az ad app delete --id <your-application-ID>
```

Eliminare infine le cartelle di esempio del progetto scaricate nel computer locale (*Azure_Digital_Twins_samples.zip* e *Azure_Digital_Twins_SignalR_integration_web_app_sample.zip*).

## <a name="next-steps"></a>Passaggi successivi

Questo articolo illustra come configurare funzioni di Azure con SignalR per trasmettere gli eventi di telemetria di Azure Digital gemelli a un'applicazione client di esempio.

Successivamente, altre informazioni sul servizio Azure SignalR:
* [*Informazioni sul servizio Azure SignalR*](../azure-signalr/signalr-overview.md)

Per altre informazioni, vedere autenticazione del servizio Azure SignalR con funzioni di Azure:
* [*Autenticazione del servizio Azure SignalR*](../azure-signalr/signalr-tutorial-authenticate-azure-functions.md)
