---
title: Integrazione con Mappe di Azure
titleSuffix: Azure Digital Twins
description: Vedere come creare una funzione di Azure in grado di usare il grafo gemello e le notifiche dei dispositivi gemelli digitali di Azure per aggiornare una mappa interna di Azure maps.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 6/3/2020
ms.topic: how-to
ms.service: digital-twins
ms.reviewer: baanders
ms.openlocfilehash: 20a376d303f90727063f288e239e89ede2a1113c
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89298216"
---
# <a name="use-azure-digital-twins-to-update-an-azure-maps-indoor-map"></a>Usare i dispositivi gemelli digitali di Azure per aggiornare una mappa interna di mappe di Azure

Questo articolo illustra i passaggi necessari per usare i dati di Azure Digital gemelli per aggiornare le informazioni visualizzate in una *mappa interna* usando le [mappe di Azure](../azure-maps/about-azure-maps.md). I dispositivi gemelli digitali di Azure archiviano un grafico delle relazioni tra i dispositivi e indirizzano i dati di telemetria a endpoint diversi, rendendolo il servizio perfetto per l'aggiornamento delle sovrapposizioni informative nelle mappe.

Questa procedura comprende:

1. Configurazione dell'istanza di Azure Digital gemelli per l'invio di eventi di aggiornamento gemelli a una funzione in [funzioni di Azure](../azure-functions/functions-overview.md).
2. Creazione di una funzione di Azure per aggiornare un stato di funzionalità delle mappe interne di Azure maps.
3. Come archiviare l'ID di Maps e l'ID degli Stati delle funzionalità nel grafico dei gemelli digitali di Azure.

### <a name="prerequisites"></a>Prerequisiti

* Seguire l'esercitazione sui dispositivi gemelli digitali di Azure [*: connettere una soluzione end-to-end*](./tutorial-end-to-end.md).
    * Si estenderà questo dispositivo gemello con un endpoint e una route aggiuntivi. Si aggiungerà anche un'altra funzione all'app per le funzioni da questa esercitazione. 
* Seguire l'esercitazione sulle mappe di Azure [*: usare Azure Maps Creator per creare mappe interne*](../azure-maps/tutorial-creator-indoor-maps.md) per creare una mappa di Azure Maps indoor con uno *stato di funzionalità*.
    * Le [funzionalità statesets](../azure-maps/creator-indoor-maps.md#feature-statesets) sono raccolte di proprietà dinamiche (Stati) assegnate alle funzionalità del set di dati, ad esempio sale o apparecchiature. Nell'esercitazione sulle mappe di Azure precedente, lo stato della funzionalità archivia lo stato della chat in una mappa.
    * Sono necessari l'ID *degli Stati* della funzionalità e l' *ID sottoscrizione*di Azure maps.

### <a name="topology"></a>Topologia

L'immagine seguente illustra il punto in cui gli elementi di integrazione di Maps interni in questa esercitazione rientrano in uno scenario di Azure Digital Twins più ampio e end-to-end.

:::image type="content" source="media/how-to-integrate-maps/maps-integration-topology.png" alt-text="Visualizzazione dei servizi di Azure in uno scenario end-to-end, in cui è evidenziata l'integrazione delle mappe interne" lightbox="media/how-to-integrate-maps/maps-integration-topology.png":::

## <a name="create-a-function-to-update-a-map-when-twins-update"></a>Creare una funzione per aggiornare una mappa quando si aggiornano i dispositivi gemelli

In primo luogo, verrà creata una route nei dispositivi gemelli digitali di Azure per inoltrare tutti gli eventi di aggiornamento gemelli a un argomento di griglia di eventi. Si userà quindi una funzione di Azure per leggere i messaggi di aggiornamento e aggiornare uno stato di funzionalità in Maps di Azure. 

## <a name="create-a-route-and-filter-to-twin-update-notifications"></a>Creare una route e filtrare le notifiche di aggiornamento del dispositivo gemello

Le istanze dei dispositivi gemelli digitali di Azure possono generare eventi di aggiornamento gemelli ogni volta che viene aggiornato lo stato di un dispositivo Esercitazione sui dispositivi gemelli digitali di Azure [*: connettere una soluzione end-to-end*](./tutorial-end-to-end.md) collegata in precedenza illustra uno scenario in cui un termometro viene usato per aggiornare un attributo di temperatura collegato al dispositivo gemello di una stanza. Si estenderà la soluzione sottoscrivendo le notifiche di aggiornamento per i dispositivi gemelli e usando tali informazioni per aggiornare le mappe.

Questo modello legge direttamente dalla stanza gemella, anziché il dispositivo Internet delle cose, che offre la flessibilità necessaria per modificare l'origine dati sottostante per la temperatura senza dover aggiornare la logica di mapping. Ad esempio, è possibile aggiungere più termometrie o impostare questa stanza per condividere un termometro con un'altra stanza, senza dover aggiornare la logica della mappa.

1. Creare un argomento di griglia di eventi, che riceverà gli eventi dall'istanza di Azure Digital gemelli.
    ```azurecli
    az eventgrid topic create -g <your-resource-group-name> --name <your-topic-name> -l <region>
    ```

2. Creare un endpoint per collegare l'argomento di griglia di eventi ai dispositivi gemelli digitali di Azure.
    ```azurecli
    az dt endpoint create eventgrid --endpoint-name <Event-Grid-endpoint-name> --eventgrid-resource-group <Event-Grid-resource-group-name> --eventgrid-topic <your-Event-Grid-topic-name> -n <your-Azure-Digital-Twins-instance-name>
    ```

3. Creare una route nei dispositivi gemelli digitali di Azure per inviare eventi di aggiornamento dei dispositivi gemelli all'endpoint.

    >[!NOTE]
    >Esiste attualmente un **problema noto** in Cloud Shell che interessa questi gruppi di comandi: `az dt route`, `az dt model`, `az dt twin`.
    >
    >Per risolverlo, eseguire `az login` in Cloud Shell prima di eseguire il comando oppure usare l'[interfaccia della riga di comando locale](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) anziché Cloud Shell. Per informazioni dettagliate, vedere [*Risoluzione dei problemi: problemi noti in Gemelli digitali di Azure*](troubleshoot-known-issues.md#400-client-error-bad-request-in-cloud-shell).

    ```azurecli
    az dt route create -n <your-Azure-Digital-Twins-instance-name> --endpoint-name <Event-Grid-endpoint-name> --route-name <my_route> --filter "type = 'Microsoft.DigitalTwins.Twin.Update'"
    ```

## <a name="create-an-azure-function-to-update-maps"></a>Creare una funzione di Azure per aggiornare le mappe

Verrà creata una funzione attivata da griglia di eventi all'interno dell'app per le funzioni dall'esercitazione end-to-end ([*esercitazione: connettere una soluzione end-to-end*](./tutorial-end-to-end.md)). Questa funzione decomprimerà tali notifiche e invierà aggiornamenti a un insieme di Stati della funzionalità mappe di Azure per aggiornare la temperatura di una stanza. 

Vedere il documento seguente per informazioni di riferimento: [*trigger di griglia di eventi di Azure per funzioni di Azure*](https://docs.microsoft.com/azure/azure-functions/functions-bindings-event-grid-trigger).

Sostituire il codice della funzione con il codice seguente. Escluderà solo gli aggiornamenti agli spazi gemelli, leggerà la temperatura aggiornata e invierà le informazioni a Maps di Azure.

```C#
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using System;
using System.Threading.Tasks;
using System.Net.Http;

namespace SampleFunctionsApp
{
    public static class ProcessDTUpdatetoMaps
    {   //Read maps credentials from application settings on function startup
        private static string statesetID = Environment.GetEnvironmentVariable("statesetID");
        private static string subscriptionKey = Environment.GetEnvironmentVariable("subscription-key");
        private static HttpClient httpClient = new HttpClient();

        [FunctionName("ProcessDTUpdatetoMaps")]
        public static async Task Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
        {
            JObject message = (JObject)JsonConvert.DeserializeObject(eventGridEvent.Data.ToString());
            log.LogInformation("Reading event from twinID:" + eventGridEvent.Subject.ToString() + ": " +
                eventGridEvent.EventType.ToString() + ": " + message["data"]);

            //Parse updates to "space" twins
            if (message["data"]["modelId"].ToString() == "dtmi:contosocom:DigitalTwins:Space;1")
            {   //Set the ID of the room to be updated in your map. 
                //Replace this line with your logic for retrieving featureID. 
                string featureID = "UNIT103";

                //Iterate through the properties that have changed
                foreach (var operation in message["data"]["patch"])
                {
                    if (operation["op"].ToString() == "replace" && operation["path"].ToString() == "/Temperature")
                    {   //Update the maps feature stateset
                        var postcontent = new JObject(new JProperty("States", new JArray(
                            new JObject(new JProperty("keyName", "temperature"),
                                 new JProperty("value", operation["value"].ToString()),
                                 new JProperty("eventTimestamp", DateTime.Now.ToString("s"))))));

                        var response = await httpClient.PostAsync(
                            $"https://atlas.microsoft.com/featureState/state?api-version=1.0&statesetID={statesetID}&featureID={featureID}&subscription-key={subscriptionKey}",
                            new StringContent(postcontent.ToString()));

                        log.LogInformation(await response.Content.ReadAsStringAsync());
                    }
                }
            }
        }
    }
}
```

È necessario impostare due variabili di ambiente nell'app per le funzioni. Una è la [chiave di sottoscrizione primaria di Azure Maps](../azure-maps/quick-demo-map-app.md#get-the-primary-key-for-your-account)e l'altra è l' [ID degli Stati di Azure Maps](../azure-maps/tutorial-creator-indoor-maps.md#create-a-feature-stateset).

```azurecli-interactive
az functionapp config appsettings set --settings "subscription-key=<your-Azure-Maps-primary-subscription-key> -g <your-resource-group> -n <your-App-Service-(function-app)-name>"
az functionapp config appsettings set --settings "statesetID=<your-Azure-Maps-stateset-ID> -g <your-resource-group> -n <your-App-Service-(function-app)-name>
```

### <a name="view-live-updates-on-your-map"></a>Visualizza gli aggiornamenti dinamici sulla mappa

Per visualizzare la temperatura di aggiornamento Live, attenersi alla procedura seguente:

1. Iniziare a inviare i dati delle cose simulate eseguendo il progetto **DeviceSimulator** dall'esercitazione sui dispositivi gemelli digitali di Azure [*: connettere una soluzione end-to-end*](tutorial-end-to-end.md). Le istruzioni per questa operazione sono disponibili nella sezione [*configurare ed eseguire la simulazione*](././tutorial-end-to-end.md#configure-and-run-the-simulation) .
2. Usare [il modulo **Azure Maps indoor** ](../azure-maps/how-to-use-indoor-module.md) per eseguire il rendering delle mappe interne create in Azure Maps Creator.
    1. Copiare il codice HTML dall' [*esempio: usare la sezione del modulo Maps*](../azure-maps/how-to-use-indoor-module.md#example-use-the-indoor-maps-module) indoor dell'esercitazione sulle mappe interne [*: usare il modulo mappe interne di Azure Maps*](../azure-maps/how-to-use-indoor-module.md) in un file locale.
    1. Sostituire *tilesetId* e *STATESETID* nel file HTML locale con i valori.
    1. Aprire il file nel browser.

Entrambi gli esempi inviano la temperatura in un intervallo compatibile, quindi dovrebbe essere visualizzato il colore della stanza 121 aggiornamento sulla mappa ogni 30 secondi.

:::image type="content" source="media/how-to-integrate-maps/maps-temperature-update.png" alt-text="Mappa di Office che mostra la stanza 121 arancione colorato":::

## <a name="store-your-maps-information-in-azure-digital-twins"></a>Archiviare le informazioni sulle mappe nei dispositivi gemelli digitali di Azure

Ora che si dispone di una soluzione hardcoded per aggiornare le informazioni sulle mappe, è possibile usare il grafo di Azure Digital gemelli per archiviare tutte le informazioni necessarie per aggiornare la mappa interna. Questo include l'ID degli Stati, l'ID sottoscrizione e l'ID della funzionalità rispettivamente di ogni mappa e località. 

Una soluzione per questo esempio specifico comporta l'aggiornamento di ogni spazio di primo livello in modo che includa un ID degli Stati e mappe dell'attributo ID sottoscrizione e l'aggiornamento di ogni stanza per avere un ID funzionalità. È necessario impostare questi valori una sola volta durante l'inizializzazione del grafo gemello, quindi eseguire una query su tali valori per ogni evento di aggiornamento dei dispositivi gemelli.

A seconda della configurazione della topologia, sarà possibile archiviare questi tre attributi a livelli diversi correlati alla granularità della mappa.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla gestione, l'aggiornamento e il recupero delle informazioni dal grafico gemello, vedere i riferimenti seguenti:

* [*Procedura: gestire i dispositivi gemelli digitali*](./how-to-manage-twin.md)
* [*Procedura: eseguire una query sul grafico gemello*](./how-to-query-graph.md)