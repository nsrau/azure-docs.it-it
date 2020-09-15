---
title: Integrazione con Azure Time Series Insights
titleSuffix: Azure Digital Twins
description: Vedere How to set up Event routes from Azure Digital Gemells to Azure Time Series Insights.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 7/14/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: c6c5c9b00ec3309638a7c5618e5995c8c5f07b11
ms.sourcegitcommit: 6e1124fc25c3ddb3053b482b0ed33900f46464b3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2020
ms.locfileid: "90564371"
---
# <a name="integrate-azure-digital-twins-with-azure-time-series-insights"></a>Integrare i dispositivi gemelli digitali di Azure con Azure Time Series Insights

In questo articolo si apprenderà come integrare i dispositivi gemelli digitali di Azure con [Azure Time Series Insights (TSI)](../time-series-insights/overview-what-is-tsi.md).

La soluzione descritta in questo articolo consente di raccogliere e analizzare i dati cronologici sulla soluzione Internet delle cose. I dispositivi gemelli digitali di Azure sono ideali per inserire i dati in Time Series Insights, perché consentono di correlare più flussi di dati e standardizzare le informazioni prima di inviarle ai Time Series Insights. 

## <a name="prerequisites"></a>Prerequisiti

Prima di poter configurare una relazione con Time Series Insights, è necessario avere un'istanza di **Azure Digital gemelli**. Questa istanza deve essere configurata con la possibilità di aggiornare le informazioni sui dispositivi gemelli digitali in base ai dati, in quanto è necessario aggiornare le informazioni gemelle alcune volte per vedere che i dati sono stati rilevati in Time Series Insights. 

Se questa operazione non è già stata configurata, è possibile crearla seguendo l'esercitazione sui dispositivi gemelli di Azure Digital [*: connettere una soluzione end-to-end*](./tutorial-end-to-end.md). Questa esercitazione illustra la configurazione di un'istanza di Azure Digital gemelli che funziona con un dispositivo di Internet delle cose virtuali per attivare gli aggiornamenti dei dispositivi gemelli digitali.

## <a name="solution-architecture"></a>Architettura della soluzione

Si collegherà Time Series Insights ai dispositivi gemelli digitali di Azure tramite il percorso riportato di seguito.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-integrate-time-series-insights/diagram-simple.png" alt-text="Visualizzazione dei servizi di Azure in uno scenario end-to-end, evidenziando Time Series Insights" lightbox="media/how-to-integrate-time-series-insights/diagram-simple.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="create-a-route-and-filter-to-twin-update-notifications"></a>Creare una route e filtrare le notifiche di aggiornamento del dispositivo gemello

Le istanze dei dispositivi gemelli digitali di Azure possono generare [eventi di aggiornamento gemelli](how-to-interpret-event-data.md) ogni volta che viene aggiornato lo stato di un dispositivo In questa sezione verrà creata una [**Route di eventi**](concepts-route-events.md) di Azure Digital gemelli che indirizza gli eventi di aggiornamento a [Hub eventi](../event-hubs/event-hubs-about.md) di Azure per l'ulteriore elaborazione.

Esercitazione sui gemelli digitali di Azure [*: connettere una soluzione end-to-end*](./tutorial-end-to-end.md) illustra uno scenario in cui viene usato un termometro per aggiornare un attributo di temperatura in un dispositivo gemello digitale che rappresenta una stanza. Questo modello si basa sugli aggiornamenti dei dispositivi gemelli anziché sull'invio di dati di telemetria da un dispositivo Internet delle cose, che offre la flessibilità necessaria per modificare l'origine dati sottostante senza dover aggiornare la logica del Time Series Insights.

1. Prima di tutto, creare uno spazio dei nomi dell'hub eventi, che riceverà gli eventi dall'istanza di Azure Digital gemelli. È possibile usare le istruzioni dell'interfaccia della riga di comando di Azure riportate di seguito oppure usare la portale di Azure: [*Guida introduttiva: creare un hub eventi usando portale di Azure*](../event-hubs/event-hubs-create.md).

    ```azurecli
    # Create an Event Hubs namespace. Specify a name for the Event Hubs namespace.
    az eventhubs namespace create --name <name for your Event Hubs namespace> --resource-group <resource group name> -l <region, for example: East US>
    ```

2. Creare un hub eventi nello spazio dei nomi.

    ```azurecli
    # Create an event hub to receive twin change events. Specify a name for the event hub. 
    az eventhubs eventhub create --name <name for your Twins event hub> --resource-group <resource group name> --namespace-name <Event Hubs namespace from above>
    ```

3. Creare una [regola di autorizzazione](https://docs.microsoft.com/cli/azure/eventhubs/eventhub/authorization-rule?view=azure-cli-latest#az-eventhubs-eventhub-authorization-rule-create) con le autorizzazioni di invio e ricezione.

    ```azurecli
    # Create an authorization rule. Specify a name for the rule.
    az eventhubs eventhub authorization-rule create --rights Listen Send --resource-group <resource group name> --namespace-name <Event Hubs namespace from above> --eventhub-name <Twins event hub name from above> --name <name for your Twins auth rule>
    ```

4. Creare un [endpoint](concepts-route-events.md#create-an-endpoint) di Azure Digital Twins che collega l'argomento di griglia di eventi all'istanza di Azure Digital gemelli.

    ```azurecli
    az dt endpoint create eventhub --endpoint-name <name for your Event Hubs endpoint> --eventhub-resource-group <resource group name> --eventhub-namespace <Event Hubs namespace from above> --eventhub <Twins event hub name from above> --eventhub-policy <Twins auth rule from above> -n <your Azure Digital Twins instance name>
    ```

5. Creare una [Route](concepts-route-events.md#create-an-event-route) nei dispositivi gemelli digitali di Azure per inviare eventi di aggiornamento dei dispositivi gemelli all'endpoint. Il filtro in questa route consente di passare solo i messaggi di aggiornamento gemelli all'endpoint.

    >[!NOTE]
    >Esiste attualmente un **problema noto** in Cloud Shell che interessa questi gruppi di comandi: `az dt route`, `az dt model`, `az dt twin`.
    >
    >Per risolverlo, eseguire `az login` in Cloud Shell prima di eseguire il comando oppure usare l'[interfaccia della riga di comando locale](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) anziché Cloud Shell. Per informazioni dettagliate, vedere [*Risoluzione dei problemi: problemi noti in Gemelli digitali di Azure*](troubleshoot-known-issues.md#400-client-error-bad-request-in-cloud-shell).

    ```azurecli
    az dt route create -n <your Azure Digital Twins instance name> --endpoint-name <Event Hub endpoint from above> --route-name <name for your route> --filter "type = 'Microsoft.DigitalTwins.Twin.Update'"
    ```

Prima di procedere, prendere nota dello *spazio dei nomi* e del *gruppo di risorse*dell'hub eventi, perché verranno usati di nuovo per creare un altro hub eventi più avanti in questo articolo.

## <a name="create-an-azure-function"></a>Creare una funzione di Azure 

Successivamente, verrà creata una funzione attivata da Hub eventi all'interno di un'app per le funzioni. È possibile usare l'app per le funzioni creata nell'esercitazione end-to-end ([*esercitazione: connettere una soluzione end-to-end*](./tutorial-end-to-end.md)) o personalizzata. 

Questa funzione convertirà gli eventi di aggiornamento dei dispositivi gemelli dal formato originale come documenti patch JSON in oggetti JSON, che contengono solo valori aggiornati e aggiunti dai dispositivi gemelli.

Per altre informazioni sull'uso di hub eventi con funzioni di Azure, vedere [*trigger di hub eventi di Azure per funzioni di Azure*](../azure-functions/functions-bindings-event-hubs-trigger.md).

All'interno dell'app per le funzioni pubblicata, sostituire il codice della funzione con il codice seguente.

```C#
using Microsoft.Azure.EventHubs;
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using System.Threading.Tasks;
using System.Text;
using System.Collections.Generic;

namespace SampleFunctionsApp
{
    public static class ProcessDTUpdatetoTSI
    { 
        [FunctionName("ProcessDTUpdatetoTSI")]
        public static async Task Run(
            [EventHubTrigger("twins-event-hub", Connection = "EventHubAppSetting-Twins")]EventData myEventHubMessage, 
            [EventHub("tsi-event-hub", Connection = "EventHubAppSetting-TSI")]IAsyncCollector<string> outputEvents, 
            ILogger log)
        {
            JObject message = (JObject)JsonConvert.DeserializeObject(Encoding.UTF8.GetString(myEventHubMessage.Body));
            log.LogInformation("Reading event:" + message.ToString());

            // Read values that are replaced or added
            Dictionary<string, object> tsiUpdate = new Dictionary<string, object>();
            foreach (var operation in message["patch"]) {
                if (operation["op"].ToString() == "replace" || operation["op"].ToString() == "add")
                    //Convert from JSON patch path to a flattened property for TSI
                    //Example input: /Front/Temperature
                    //        output: Front.Temperature
                    string path = operation["path"].ToString().Substring(1);                    
                    path = path.Replace("/", ".");                    
                    tsiUpdate.Add(path, operation["value"]);
            }
            //Send an update if updates exist
            if (tsiUpdate.Count>0){
                tsiUpdate.Add("$dtId", myEventHubMessage.Properties["cloudEvents:subject"]);
                await outputEvents.AddAsync(JsonConvert.SerializeObject(tsiUpdate));
            }
        }
    }
}
```

Da qui, la funzione invierà gli oggetti JSON creati a un secondo hub eventi, a cui ci si connetterà Time Series Insights.

Successivamente, si imposteranno anche alcune variabili di ambiente che verranno usate da questa funzione per connettersi ai propri hub eventi.

## <a name="send-telemetry-to-an-event-hub"></a>Inviare dati di telemetria a un hub eventi

A questo punto si creerà un secondo hub eventi e si configurerà la funzione per trasmettere il relativo output a tale hub eventi. Questo hub eventi verrà quindi connesso a Time Series Insights.

### <a name="create-an-event-hub"></a>Creare un hub eventi

Per creare il secondo hub eventi, è possibile usare le istruzioni dell'interfaccia della riga di comando di Azure riportate di seguito oppure usare la portale di Azure: [*Guida introduttiva: creare un hub eventi usando portale di Azure*](../event-hubs/event-hubs-create.md).

1. Preparare *lo spazio dei nomi di hub eventi* e il nome del *gruppo di risorse* precedente in questo articolo

2. Creare un nuovo hub eventi
    ```azurecli
    # Create an event hub. Specify a name for the event hub. 
    az eventhubs eventhub create --name <name for your TSI event hub> --resource-group <resource group name from earlier> --namespace-name <Event Hubs namespace from earlier>
    ```
3. Creare una [regola di autorizzazione](https://docs.microsoft.com/cli/azure/eventhubs/eventhub/authorization-rule?view=azure-cli-latest#az-eventhubs-eventhub-authorization-rule-create) con le autorizzazioni di invio e ricezione
    ```azurecli
    # Create an authorization rule. Specify a name for the rule.
    az eventhubs eventhub authorization-rule create --rights Listen Send --resource-group <resource group name> --namespace-name <Event Hubs namespace from earlier> --eventhub-name <TSI event hub name from above> --name <name for your TSI auth rule>
    ```

## <a name="configure-your-function"></a>Configurare la funzione

A questo punto, è necessario impostare le variabili di ambiente nell'app per le funzioni precedente, contenenti le stringhe di connessione per gli hub eventi creati.

### <a name="set-the-twins-event-hub-connection-string"></a>Impostare la stringa di connessione dell'hub eventi gemelli

1. Ottenere la [stringa di connessione dell'hub eventi](../event-hubs/event-hubs-get-connection-string.md)gemelli, usando le regole di autorizzazione create in precedenza per l'hub gemelli.

    ```azurecli
    az eventhubs eventhub authorization-rule keys list --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <Twins event hub name from earlier> --name <Twins auth rule from earlier>
    ```

2. Usare la stringa di connessione che si ottiene come risultato per creare un'impostazione dell'app nell'app per le funzioni che contiene la stringa di connessione:

    ```azurecli
    az functionapp config appsettings set --settings "EventHubAppSetting-Twins=<Twins event hub connection string> -g <resource group> -n <your App Service (function app) name>"
    ```

### <a name="set-the-time-series-insights-event-hub-connection-string"></a>Impostare la stringa di connessione dell'hub eventi Time Series Insights

1. Ottenere la [stringa di connessione dell'hub eventi](../event-hubs/event-hubs-get-connection-string.md)di TSI, usando le regole di autorizzazione create in precedenza per l'hub Time Series Insights:

    ```azurecli
    az eventhubs eventhub authorization-rule keys list --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <TSI event hub name> --name <TSI auth rule>
    ```

2. Nell'app per le funzioni creare un'impostazione dell'app contenente la stringa di connessione:

    ```azurecli
    az functionapp config appsettings set --settings "EventHubAppSetting-TSI=<TSI event hub connection string> -g <resource group> -n <your App Service (function app) name>"
    ```

## <a name="create-and-connect-a-time-series-insights-instance"></a>Creazione e connessione di un'istanza di Time Series Insights

Si procederà quindi alla configurazione di un'istanza di Time Series Insights per ricevere i dati dal secondo hub eventi. Seguire questa procedura e per altre informazioni su questo processo, vedere [*esercitazione: configurare un ambiente Azure Time Series Insights PAYG Gen2*](../time-series-insights/tutorials-set-up-tsi-environment.md).

1. Nella portale di Azure iniziare a creare una risorsa Time Series Insights. 
    1. Selezionare il piano tariffario **PAYG (anteprima)** .
    2. È necessario scegliere un ID della **serie temporale** per questo ambiente. L'ID della serie temporale può essere composto da un massimo di tre valori che verranno usati per la ricerca dei dati in Time Series Insights. Per questa esercitazione, è possibile usare **$dtId**. Per altre informazioni sulla selezione di un valore ID, vedere [*procedure consigliate per la scelta di un ID di serie temporale*](https://docs.microsoft.com/azure/time-series-insights/how-to-select-tsid).
    
        :::image type="content" source="media/how-to-integrate-time-series-insights/create-twin-id.png" alt-text="Il portale di creazione UX per un ambiente Time Series Insights. Il piano tariffario PAYG (anteprima) è selezionato e il nome della proprietà ID della serie temporale è $dtId":::

2. Selezionare **Next: origine evento** e selezionare le informazioni di hub eventi riportate sopra. Sarà inoltre necessario creare un nuovo gruppo di consumer di hub eventi.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/event-source-twins.png" alt-text="Il portale di creazione UX per un'origine evento Time Series Insights ambiente. Si sta creando un'origine evento con le informazioni sull'hub eventi riportate sopra. Si sta creando anche un nuovo gruppo di consumer.":::

## <a name="begin-sending-iot-data-to-azure-digital-twins"></a>Iniziare a inviare i dati dell'Internet di Azure ai dispositivi gemelli digitali

Per iniziare a inviare dati a Time Series Insights, è necessario avviare l'aggiornamento delle proprietà dei dispositivi gemelli digitali nei dispositivi gemelli digitali di Azure con valori di dati modificabili. Usare il comando [AZ DT Twin Update](https://docs.microsoft.com/cli/azure/ext/azure-iot/dt/twin?view=azure-cli-latest#ext-azure-iot-az-dt-twin-update) .

[!INCLUDE [digital-twins-known-issue-cloud-shell](../../includes/digital-twins-known-issue-cloud-shell.md)]

Se si usa l'esercitazione end-to-end ([*esercitazione: connettere una soluzione end-to-end*](tutorial-end-to-end.md)) per facilitare la configurazione dell'ambiente, è possibile iniziare a inviare i dati delle cose simulate eseguendo il progetto *DeviceSimulator* dall'esempio. Le istruzioni sono disponibili nella sezione [*configurare ed eseguire la simulazione*](tutorial-end-to-end.md#configure-and-run-the-simulation) dell'esercitazione.

## <a name="visualize-your-data-in-time-series-insights"></a>Visualizza i dati in Time Series Insights

A questo punto, i dati devono essere propagati nell'istanza di Time Series Insights, pronti per essere analizzati. Per esplorare i dati in arrivo, attenersi alla procedura riportata di seguito.

1. Aprire l'istanza di Time Series Insights nell' [portale di Azure](https://portal.azure.com) (è possibile cercare il nome dell'istanza nella barra di ricerca del portale). Visitare l' *URL di Time Series Insights Explorer* visualizzato nella panoramica dell'istanza.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/view-environment.png" alt-text="Selezionare l'URL di Time Series Insights Explorer nella scheda Panoramica dell'ambiente Time Series Insights":::

2. Nella finestra di esplorazione si vedranno i tre gemelli di Azure Digital gemelli mostrati a sinistra. Selezionare _**thermostat67**_, selezionare **temperatura**e quindi fare clic su **Aggiungi**.

    :::image type="content" source="media/how-to-integrate-time-series-insights/add-data.png" alt-text="Selezionare * * thermostat67 * *, selezionare * * temperature * * e quindi premere * * Aggiungi * *":::

3. Si dovrebbero ora visualizzare le letture della temperatura iniziali dal termostato, come mostrato di seguito. La stessa lettura della temperatura viene aggiornata per *room21* e *floor1*ed è possibile visualizzare i flussi di dati in parallelo.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/initial-data.png" alt-text="I dati relativi alla temperatura iniziale sono grafici in TSI Explorer. Si tratta di una riga di valori casuali compresa tra 68 e 85":::

4. Se si consente l'esecuzione della simulazione per un periodo di tempo più lungo, la visualizzazione sarà simile alla seguente:
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/day-data.png" alt-text="I dati relativi alla temperatura per ogni gemello sono rappresentati in tre linee parallele di colori diversi.":::

## <a name="next-steps"></a>Passaggi successivi

I dispositivi gemelli digitali vengono archiviati per impostazione predefinita come gerarchia piatta in Time Series Insights, ma possono essere arricchiti con le informazioni sul modello e una gerarchia a più livelli per l'organizzazione. Per ulteriori informazioni su questo processo, vedere: 

* [*Esercitazione: definire e applicare un modello*](../time-series-insights/tutorials-set-up-tsi-environment.md#define-and-apply-a-model) 

È possibile scrivere logica personalizzata per fornire automaticamente queste informazioni usando i dati del modello e del grafo già archiviati nei dispositivi gemelli digitali di Azure. Per altre informazioni sulla gestione, l'aggiornamento e il recupero delle informazioni dal grafico gemello, vedere i riferimenti seguenti:

* [*Procedura: gestire un dispositivo gemello digitale*](./how-to-manage-twin.md)
* [*Procedura: eseguire una query sul grafico gemello*](./how-to-query-graph.md)