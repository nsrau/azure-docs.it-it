---
title: Integrazione con Time Series Insights
titleSuffix: Azure Digital Twins
description: Vedere How to set up Event routes from Azure Digital Gemells to Azure Time Series Insights.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 7/14/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 9be44d29ab08a7404bfb14d691bb5bb339284714
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2020
ms.locfileid: "87131600"
---
# <a name="integrate-digital-twins-with-azure-time-series-insights"></a>Integrare i dispositivi gemelli digitali con Azure Time Series Insights

In questo riferimento si apprenderà come integrare i dispositivi gemelli digitali di Azure con [Azure Time Series Insights (TSI)](../time-series-insights/overview-what-is-tsi.md). Questa soluzione consentirà di raccogliere e analizzare i dati cronologici sulla soluzione Internet delle cose. I dispositivi gemelli digitali di Azure sono ideali per inserire i dati in Time Series Insights, perché consentono di correlare più flussi di dati e standardizzare le informazioni prima di inviarle ai Time Series Insights. 

## <a name="solution-architecture"></a>Architettura della soluzione

Verrà collegato Time Series Insights a dispositivi digitali gemelli di Azure tramite il percorso riportato di seguito.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-integrate-time-series-insights/diagram-simple.png" alt-text="Visualizzazione dei servizi di Azure in uno scenario end-to-end, evidenziando Time Series Insights" lightbox="media/how-to-integrate-time-series-insights/diagram-simple.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="prerequisites"></a>Prerequisiti

* È necessaria un'istanza di Azure Digital Twins che è possibile aggiornare le informazioni gemelle più volte per vedere che i dati sono stati rilevati in Time Series Insights. 
    * Se non è presente, seguire l'esercitazione sui dispositivi gemelli digitali di Azure [*: connettere una soluzione end-to-end*](./tutorial-end-to-end.md) per configurare un'istanza di dispositivi gemelli digitali di Azure e un dispositivo dell'intero virtuale per generare modifiche dei dispositivi gemelli.

## <a name="create-a-route-and-filter-to-twin-update-notifications"></a>Creare una route e filtrare le notifiche di aggiornamento del dispositivo gemello

Le istanze dei dispositivi gemelli digitali di Azure possono generare [eventi di aggiornamento gemelli](how-to-interpret-event-data.md) ogni volta che viene aggiornato lo stato di un dispositivo Verrà creata una route che indirizza gli eventi di aggiornamento a un hub eventi per un'ulteriore elaborazione.

Esercitazione sui gemelli digitali di Azure [*: connettere una soluzione end-to-end*](./tutorial-end-to-end.md) illustra uno scenario in cui viene usato un termometro per aggiornare un attributo di temperatura collegato al dispositivo gemello di una stanza. Questo modello usa gli aggiornamenti dei dispositivi gemelli, anziché l'invio di dati di telemetria da un dispositivo Internet delle cose, che offre la flessibilità necessaria per modificare l'origine dati sottostante senza dover aggiornare la logica del Time Series Insights.

1. Creare uno spazio dei nomi dell'hub eventi che riceverà gli eventi dall'istanza di Azure Digital gemelli. È possibile usare le istruzioni dell'interfaccia della riga di comando di Azure riportate di seguito oppure usare la portale di Azure: [*Guida introduttiva: creare un hub eventi usando portale di Azure*](../event-hubs/event-hubs-create.md).

    ```azurecli-interactive
    # Create an Event Hubs namespace. Specify a name for the Event Hubs namespace.
    az eventhubs namespace create --name <Event Hubs namespace> --resource-group <resource group name> -l <region, for example: East US>
    ```

2. Creare un hub eventi.

    ```azurecli-interactive
    # Create an event hub to receive twin change events. Specify a name for the event hub. 
    az eventhubs eventhub create --name <event hub name> --resource-group <resource group name> --namespace-name <Event Hubs namespace>
    ```

3. Creare una [regola di autorizzazione](https://docs.microsoft.com/cli/azure/eventhubs/eventhub/authorization-rule?view=azure-cli-latest#az-eventhubs-eventhub-authorization-rule-create) con le autorizzazioni di invio e ricezione.

    ```azurecli-interactive
    # Create an authorization rule. Specify a name for the rule.
    az eventhubs eventhub authorization-rule create --rights Listen Send --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <twins event hub name> --name <twins auth rule>
    ```

4. Creare un endpoint per collegare l'argomento di griglia di eventi ai dispositivi gemelli digitali di Azure.

    ```azurecli
    az dt endpoint create eventhub --endpoint-name <Event-Hub-endpoint-name> --eventhub-resource-group <resource group name> --eventhub-namespace <Event Hubs namespace> --eventhub <twins event hub name> --eventhub-policy <twins auth rule> -n <your-Azure-Digital-Twins-instance-name>
    ```

5. Creare una route nei dispositivi gemelli digitali di Azure per inviare eventi di aggiornamento dei dispositivi gemelli all'endpoint. Il filtro in questa route consente di passare solo i messaggi di aggiornamento gemelli all'endpoint.

    ```azurecli
    az dt route create -n <your-Azure-Digital-Twins-instance-name> --endpoint-name <Event-Hub-endpoint-name> --route-name <my_route> --filter "type = 'Microsoft.DigitalTwins.Twin.Update'"
    ```

## <a name="create-an-azure-function"></a>Creare una funzione di Azure 

Successivamente, si creerà una funzione attivata da Hub eventi all'interno di una nuova app per le funzioni, l'app per le funzioni dall'esercitazione end-to-end ([*esercitazione: connettere una soluzione end-to-end*](./tutorial-end-to-end.md)). Questa funzione converte gli aggiornamenti dal formato originale come documenti patch JSON in oggetti JSON che contengono solo valori aggiornati e aggiunti dai dispositivi gemelli.

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
            [EventHubTrigger("twins-fx-hub", Connection = "EventHubAppSetting-Twins")]EventData myEventHubMessage, 
            [EventHub("alkarche-tsi-demo-hub", Connection = "EventHubAppSetting-TSI")]IAsyncCollector<string> outputEvents, 
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

## <a name="send-telemetry-to-an-event-hub"></a>Inviare dati di telemetria a un hub eventi

Si creerà ora un secondo hub eventi e si configurerà la funzione per trasmettere il relativo output a tale hub eventi. Questo hub eventi verrà quindi connesso a Time Series Insights.

### <a name="create-an-event-hub"></a>Creare un hub eventi

È possibile usare le istruzioni dell'interfaccia della riga di comando di Azure riportate di seguito oppure usare la portale di Azure: [*Guida introduttiva: creare un hub eventi usando portale di Azure*](../event-hubs/event-hubs-create.md).

1. Preparare lo spazio dei nomi dell'hub eventi e il nome del gruppo di risorse precedente 

2. Creare un hub eventi
    ```azurecli-interactive
    # Create an event hub. Specify a name for the event hub. 
    az eventhubs eventhub create --name <TSI event hub name> --resource-group <resource group name> --namespace-name <Event Hubs namespace>
    ```
3. Creare una [regola di autorizzazione](https://docs.microsoft.com/cli/azure/eventhubs/eventhub/authorization-rule?view=azure-cli-latest#az-eventhubs-eventhub-authorization-rule-create) con le autorizzazioni di invio e ricezione
    ```azurecli-interactive
    # Create an authorization rule. Specify a name for the rule.
    az eventhubs eventhub authorization-rule create --rights Listen Send --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <event hub name> --name <TSI auth rule>
    ```

### <a name="configure-your-function"></a>Configurare la funzione

È necessario impostare una variabile di ambiente nell'app per le funzioni precedente, contenente la stringa di connessione dell'hub eventi.

#### <a name="set-the-time-series-insights-event-hub-connection-string"></a>Impostare la stringa di connessione dell'hub eventi Time Series Insights

1. Ottenere la [stringa di connessione dell'hub eventi](../event-hubs/event-hubs-get-connection-string.md) per le regole di autorizzazione create in precedenza per l'hub Time Series Insights:

    ```azurecli-interactive
    az eventhubs eventhub authorization-rule keys list --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <twins event hub name> --name <twins auth rule>
    ```

2. Nell'app per le funzioni creare un'impostazione dell'app contenente la stringa di connessione:

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EventHubAppSetting-TSI=<your-event-hub-connection-string> -g <your-resource-group> -n <your-App-Service-(function-app)-name>"
    ```

#### <a name="set-the-twins-event-hub-connection-string"></a>Impostare la stringa di connessione dell'hub eventi gemelli

1. Ottenere la [stringa di connessione dell'hub eventi](../event-hubs/event-hubs-get-connection-string.md) per le regole di autorizzazione create in precedenza per l'hub gemelli.

    ```azurecli-interactive
    az eventhubs eventhub authorization-rule keys list --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <TSI event hub name> --name <TSI auth rule>
    ```

2. Nell'app per le funzioni creare un'impostazione dell'app contenente la stringa di connessione:

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EventHubAppSetting-Twins=<your-event-hub-connection-string> -g <your-resource-group> -n <your-App-Service-(function-app)-name>"
    ```

## <a name="create-and-connect-a-time-series-insights-instance"></a>Creazione e connessione di un'istanza di Time Series Insights

Si procederà quindi alla configurazione di un'istanza di Time Series Insights per ricevere i dati dal secondo hub eventi. Per ulteriori informazioni su questo processo [ *, vedere Esercitazione: configurazione di un ambiente Azure Time Series Insights Gen2 PAYG*](../time-series-insights/tutorials-set-up-tsi-environment.md)

1. Nella portale di Azure iniziare a creare una risorsa Time Series Insights. 
    1. Selezionare il piano tariffario **PAYG (anteprima)** .
    2. È necessario scegliere un ID della serie temporale per questo ambiente. L'ID della serie temporale può essere composto da un massimo di tre valori che verranno usati per la ricerca dei dati in Time Series Insights. Per questa esercitazione, è possibile usare **$dtId**. Per altre informazioni sulla selezione di un valore ID, vedere [*procedure consigliate per la scelta di un ID di serie temporale*](https://docs.microsoft.com/azure/time-series-insights/how-to-select-tsid).
    
        :::image type="content" source="media/how-to-integrate-time-series-insights/create-twin-id.png" alt-text="Il portale di creazione UX per un ambiente Time Series Insights. Il piano tariffario PAYG (anteprima) è selezionato e il nome della proprietà ID della serie temporale è $dtId":::

2. Selezionare **Next: origine evento** e selezionare le informazioni di hub eventi riportate sopra. Sarà inoltre necessario creare un nuovo gruppo di consumer di hub eventi.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/event-source-twins.png" alt-text="Il portale di creazione UX per un'origine evento Time Series Insights ambiente. Si sta creando un'origine evento con le informazioni sull'hub eventi riportate sopra. Si sta creando anche un nuovo gruppo di consumer.":::

## <a name="begin-sending-iot-data-to-azure-digital-twins"></a>Iniziare a inviare i dati dell'Internet di Azure ai dispositivi gemelli digitali

Per iniziare a inviare dati a Time Series Insights, è necessario avviare l'aggiornamento delle proprietà dei dispositivi gemelli digitali con la modifica dei valori dei dati. Usare il comando [AZ DT Twin Update](https://docs.microsoft.com/cli/azure/ext/azure-iot/dt/twin?view=azure-cli-latest#ext-azure-iot-az-dt-twin-update) .

Se si usa l'esercitazione end-to-end per semplificare la configurazione dell'ambiente, è possibile iniziare a inviare i dati delle cose simulate eseguendo il `DeviceSimulator` progetto dall'esercitazione sui gemelli digitali di Azure [*: connettere una soluzione end-to-end*](tutorial-end-to-end.md). Le istruzioni sono disponibili nella sezione [*configurare ed eseguire la simulazione*](tutorial-end-to-end.md#configure-and-run-the-simulation) dell'esercitazione.

## <a name="visualize-your-data-in-time-series-insights"></a>Visualizza i dati in Time Series Insights

A questo punto, i dati devono essere propagati nell'istanza di Time Series Insights, pronti per essere analizzati. Per esplorare i dati in arrivo, attenersi alla procedura riportata di seguito.

1. Aprire l'istanza di Time Series Insights nel portale di Azure. Visitare l'URL di Time Series Insights Explorer illustrato nella panoramica.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/view-environment.png" alt-text="Fare clic sull'URL di Time Series Insights Explorer nella scheda Panoramica dell'ambiente Time Series Insights":::

2. Nella finestra di esplorazione si vedranno i tre gemelli visualizzati a sinistra. Fare clic su **thermostat67**, selezionare **patch_value**e fare clic su **Aggiungi**.

    :::image type="content" source="media/how-to-integrate-time-series-insights/add-data.png" alt-text="Fare clic su * * thermostat67 * *, selezionare * * temperature * * e fare clic su * * Aggiungi * *":::

3. Si dovrebbero ora visualizzare le letture della temperatura iniziali dal termostato, come mostrato di seguito. La stessa lettura della temperatura viene aggiornata per room21 e floor1 ed è possibile visualizzare i flussi di dati in parallelo.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/initial-data.png" alt-text="I dati relativi alla temperatura iniziale sono grafici in TSI Explorer. Si tratta di una riga di valori casuali compresa tra 68 e 85":::

4. Se si consente l'esecuzione della simulazione per un periodo di tempo più lungo, la visualizzazione sarà simile alla seguente:
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/day-data.png" alt-text="I dati relativi alla temperatura per ogni gemello sono rappresentati in tre linee parallele di colori diversi.":::

## <a name="next-steps"></a>Passaggi successivi

I dispositivi gemelli digitali vengono archiviati per impostazione predefinita come gerarchia piatta in Time Series Insights, ma possono essere arricchiti con le informazioni sul modello e una gerarchia a più livelli per l'organizzazione. Per ulteriori informazioni su questo processo, vedere: 

* [*Esercitazione: definire e applicare un modello*](../time-series-insights/tutorials-set-up-tsi-environment.md#define-and-apply-a-model) 

È possibile scrivere logica personalizzata per fornire automaticamente queste informazioni usando i dati del modello e del grafo già archiviati nei dispositivi gemelli digitali di Azure. Per altre informazioni sulla gestione, l'aggiornamento e il recupero delle informazioni dal grafico gemello, vedere i riferimenti seguenti:

* [*Procedura: gestire un dispositivo gemello digitale*](./how-to-manage-twin.md)
* [*Procedura: eseguire una query sul grafico gemello*](./how-to-query-graph.md)