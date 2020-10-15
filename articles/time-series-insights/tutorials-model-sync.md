---
title: Sincronizzazione del modello tra i dispositivi gemelli digitali di Azure e Time Series Insights | Microsoft Docs
description: Procedure consigliate e strumenti usati per tradurre il modello di asset in ADT in modello di asset in Azure TSI
ms.service: time-series-insights
services: time-series-insights
author: msrsrinivasan
ms.author: radhsrin
manager: diviso
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/22/2020
ms.custom: dpalled
ms.openlocfilehash: c3948a5bdfce583384992fb87bf40e9e7251974d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91344010"
---
# <a name="model-synchronization-between-azure-digital-twins-and-time-series-insights-gen2"></a>Sincronizzazione dei modelli tra Gemelli digitali di Azure e Time Series Insights Gen2

Questo articolo illustra le procedure consigliate e gli strumenti usati per tradurre il modello di asset in Azure Digital Twins (ADT) in modello di asset in Azure Time Series Insights (STI).  Questo articolo è la seconda parte di una serie di esercitazioni in due parti che illustra l'integrazione dei dispositivi gemelli digitali di Azure con Azure Time Series Insights. L'integrazione dei dispositivi gemelli digitali di Azure con Time Series Insights consente l'archiviazione e il rilevamento della cronologia delle telemetrie e delle proprietà calcolate dei dispositivi gemelli digitali. Questa serie di esercitazioni è destinata agli sviluppatori che lavorano per integrare Time Series Insights con i dispositivi gemelli digitali di Azure. La parte 1 illustra la  [creazione di una pipeline di dati che introduce i dati della serie temporale effettivi dai dispositivi gemelli digitali di Azure a Time Series Insights](https://docs.microsoft.com/azure/digital-twins/how-to-integrate-time-series-insights) e questa, secondo parte della serie di esercitazioni, spiega la sincronizzazione del modello di asset tra i dispositivi Time Series Insights gemelli digitali di Azure Questa esercitazione illustra le procedure consigliate per la scelta e la definizione della convenzione di denominazione per l'ID della serie temporale (ID TS) e la definizione manuale delle gerarchie nel modello Time Series (TSM).

## <a name="choosing-a-time-series-id"></a>Scelta di un ID di serie temporale

Time Series ID è un identificatore univoco usato per identificare gli asset in Time Series Insights. I dati delle serie temporali (telemetrie dal campo, che sono coppie di valori temporali) vengono rappresentati usando le variabili elencate in IDST. Nei dispositivi gemelli digitali di Azure, le proprietà gemelle e le telemetrie vengono usate per rappresentare rispettivamente lo stato di un gemello e le misurazioni prodotte dal gemello. A partire dalla progettazione corrente di TSM, degli IDST deve essere univoco. L'uso di ID gemelli dei gemelli nei dispositivi gemelli digitali di Azure o in combinazione con il nome della proprietà o della telemetria renderà sempre un ID TS univoco in TSM. In un caso tipico, **_`<Twin ID>`_** sarà il IDST e i nomi di proprietà e telemetria saranno le variabili in TSM. Tuttavia, esistono casi d'uso in cui è preferibile che le gerarchie di asset in Time Series Insights siano bidimensionali utilizzando il formato di chiavi composite, ad esempio **_`<Twin ID>+ <Delimiter of Choice> + <Name of the Property or Telemetry>`_** . Ecco un esempio per illustrare il caso successivo. Si consideri una stanza di una costruzione modellata come un gemello e con ID Room22. La proprietà di impostazione della temperatura deve essere convertita come **_idst Room22_TempSetting_** e la misurazione della temperatura da tradurre in **_Room22_TempMea_** in TSM.

[![Scelta di un ID di serie temporale](media/tutorials-model-sync/choosing-tsid.png)](media/tutorials-model-sync/choosing-tsid.png#lightbox)

## <a name="contextualizing-time-series"></a>Serie temporale contestualizzazione

La contestualizzazione dei dati (per la maggior parte della natura spaziale) in Time Series Insights viene ottenuta tramite gerarchie di asset e lo stesso viene usato per semplificare la navigazione dei dati tramite una visualizzazione albero in Time Series Insights Explorer. I tipi Time Series e le gerarchie vengono definiti tramite il modello Time Series (TSM) in Time Series Insights. I tipi in TSM consentono di definire le variabili, mentre i livelli di gerarchia e i valori dei campi di istanza vengono utilizzati per costruire la visualizzazione albero in Esplora Time Series Insights. Per ulteriori informazioni su TSM, vedere la [documentazione online Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/concepts-model-overview).

Nei dispositivi gemelli digitali di Azure, la connessione tra gli asset viene espressa usando relazioni gemelle. Le relazioni gemelle sono semplicemente un grafico degli asset connessi. Tuttavia, nelle informazioni dettagliate sulle serie temporali, le relazioni tra gli asset sono di natura gerarchica. Ovvero gli asset condividono una relazione di tipo padre-figlio e sono rappresentati utilizzando una struttura ad albero. Per tradurre le informazioni sulle relazioni da dispositivi gemelli digitali di Azure in gerarchie di Time Series Insights, è necessario scegliere relazioni gerarchiche rilevanti da dispositivi gemelli digitali di Azure. I dispositivi gemelli digitali di Azure usano un linguaggio open standard e di modellazione denominato Digital Twin Definition Language (DTDL). Nei modelli DTDL vengono descritti usando una variante di JSON denominata JSON-LD. Vedere la [documentazione di DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) per i dettagli completi sulla specifica.

[![Connessione tra asset](media/tutorials-model-sync/asset-connection.png)](media/tutorials-model-sync/asset-connection.png#lightbox)

## <a name="translating-graph-representation-in-azure-digital-twins-to-tree-structure-in-time-series-insights"></a>Conversione della rappresentazione Graph nei dispositivi gemelli digitali di Azure in una struttura ad albero in Time Series Insights

Le sezioni seguenti dell'esercitazione consentono di acquisire alcuni scenari di base per la conversione manuale della struttura Graph nei dispositivi gemelli digitali di Azure in una struttura ad albero in Time Series Insights.

Sistema di esempio: questa esercitazione usa l'esempio seguente per illustrare i concetti illustrati di seguito. Si tratta di un sistema di gestione semplice e fittizio per la creazione di un piano e due camere. Dispone di tre termostati, uno in ogni stanza e un altro comune al pavimento. Inoltre, dispone anche di un contatore dei flussi idrici che misura il flusso idrico da Room21 a Room22 tramite una connessione tramite pipe tra le chat room. Esaminando la relazione spaziale tra due gemelli, sono presenti entrambi i tipi di relazioni.

1. Relazione gerarchica più comune. Ad esempio, Building40-> Floor01-> floors *-> temperature
1. Relazione circolare non comune. Ad esempio, a partire da Building40, FlowMtr può essere tracciato tramite due percorsi diversi.

   1. Building40-> Floor01-> Room21-> FlowMtr *-flusso >
   1. Building40-> Floor01-> Room22-> FlowMtr *-flusso >  
      Dove "Flow" è la telemetria effettiva che misura il flusso idrico tra Room21 e Room22

> [!Note]
>
> `*` Floors sta per FloorThermoStat e FlowMtr rappresenta il contatore dei flussi e viene in genere scelto come IDST. Temperature e Flow sono i dati di telemetria non elaborati indicati come variabili in Time Series Insights.

Data la limitazione attuale in Time Series Insights che un asset non può essere rappresentato in più rami, nelle sezioni seguenti viene illustrata la modellazione di relazioni gerarchiche e circolari in Time Series Insights.

## <a name="case-1-hierarchical-parent-child-relationship"></a>Caso 1: relazione gerarchica (padre-figlio)

Si tratta del tipo di relazione più comune tra i due gemelli. la creazione di una relazione padre-figlio pura di modellazione è illustrata nella figura seguente. I campi dell'istanza e IDST sono derivati da ID gemelli, come illustrato di seguito. Anche se i campi di istanza possono essere aggiornati manualmente usando Time Series Insights Explorer, la sezione seguente denominata "aggiornamento dei campi di istanza tramite API" illustra l'ascolto delle modifiche del modello nei dispositivi gemelli digitali di Azure e l'aggiornamento dei campi di istanza in Time Series Insights tramite funzioni di Azure.

[![Mapping di ID gemelli](media/tutorials-model-sync/mapping-twin-ids.png)](media/tutorials-model-sync/mapping-twin-ids.png#lightbox)

[![Mapping di ID gemelli 2](media/tutorials-model-sync/mapping-twin-ids2.png)](media/tutorials-model-sync/mapping-twin-ids2.png#lightbox)

## <a name="case-2-circular-relationship"></a>Caso 2: relazione circolare

### <a name="circular-relationship-in-azure-digital-twins-to-single-hierarchy-relationship-in-time-series-insights"></a>Relazione circolare tra i due dispositivi digitali di Azure e la relazione di gerarchia singola in Time Series Insights

Dato che IDST deve essere univoco e può essere rappresentato solo in una gerarchia, questo caso rappresenta _' FlowMtr '_ con i dati di telemetria denominati _' Flow '_ appena sotto il gemello _' Room21'_. In futuro, quando Time Series Insights possibile supportare la rappresentazione di serie temporali in TSM, i dati di telemetria _' Flow '_ verranno rappresentati in _' room 21'_ e _' Room 22'_

La schermata seguente mostra il mapping manuale degli ID gemelli nei dispositivi gemelli digitali di Azure al campo di istanza in TSM e la gerarchia risultante in Time Series Insights.

[![Mapping di ID gemelli nei dispositivi gemelli digitali di Azure](media/tutorials-model-sync/mapping-twin-ids-adt.png)](media/tutorials-model-sync/mapping-twin-ids-adt.png#lightbox)

### <a name="circular-relationship-in-azure-digital-twins-to-multiple-hierarchies-in-time-series-insights-using-duplicates"></a>Relazione circolare tra i dispositivi gemelli digitali di Azure e più gerarchie in Time Series Insights, usando i duplicati

Nella parte 1 dell'esercitazione viene illustrato il modo in cui un programma client (funzione di Azure) consente il trasferimento dei dati di telemetria dall'hub Internet o da altre origini evento ai dispositivi gemelli digitali di Azure. Questo approccio suggerisce l'uso dello stesso programma client per apportare aggiornamenti alle proprietà pertinenti dei gemelli padre. Nell'esempio specificato, durante la lettura dei dati di telemetria di FlowMtr dall'hub Internet e l'aggiornamento della proprietà "Flow" in FlowMtr gemello, il programma può aggiornare anche le proprietà corrispondenti in tutti i possibili gemelli padre dell'origine. In questo esempio, sarebbe "outflowmea" (identificato con la proprietà "outflow" Relationship) della proprietà Room21 e "inflowmea" di Room22.  Lo screenshot seguente illustra l'esperienza utente finale in Time Series Insights Explorer. È necessario tenere presente che i dati sono duplicati in base a questo approccio.

[![Esplora Time Series Insights](media/tutorials-model-sync/tsi-explorer.png)](media/tutorials-model-sync/tsi-explorer.png#lightbox)

Il frammento di codice seguente mostra in che modo l'applicazione client è stata in grado di esplorare la relazione gemella usando le API di dispositivi gemelli di Azure.

> [!Note]
>
> Questo esempio di frammento di codice presuppone che i lettori abbiano familiarità con la [parte 01](https://docs.microsoft.com/azure/digital-twins/tutorial-end-to-end#set-up-the-sample-function-app) dell'esercitazione e la modifica del codice è stata apportata all'interno della funzione "ProcessHubToDTEvents".

```csharp
if (propertyPath.Equals("/Flow"))
{
//Update the flow value property of the flow meter
await AdtUtilities.UpdateTwinProperty(client, twinId, "replace",
propertyPath, "double", propertyValue, log);

//also update the sending end flow
string parentIdOutflow = await AdtUtilities.FindParent(client, twinId,
"outflow", log);
if (parentIdOutflow != null)
await AdtUtilities.UpdateTwinProperty(client, parentIdOutflow, "replace", "outflow", "double", propertyValue, log);
else
log.LogInformation("Unable to find Parent with outflow
relationship for " + twinId );
//and receiving end flow value
string parentIdinflow = await AdtUtilities.FindParent(client, twinId,
"inflow", log);
if (parentIdinflow != null)

await AdtUtilities.UpdateTwinProperty(client, parentIdinflow,
"replace", "inflow", "double", propertyValue, log);
else
log.LogInformation("Unable to find Parent with inflow
relationship for " + twinId);
}
```

## <a name="updating-instance-fields-using-apis"></a>Aggiornamento di campi di istanza mediante le API

Questa sezione dell'esercitazione illustra l'ascolto delle modifiche del modello nei dispositivi gemelli digitali di Azure, ad esempio la creazione, l'eliminazione di gemelli o la modifica delle relazioni tra i due gemelli e l'aggiornamento a livello di codice delle gerarchie e dei campi di istanza con Time Series Insights Questo metodo di aggiornamento Time Series Insights modello viene in genere eseguito tramite funzioni di Azure. Nei dispositivi gemelli digitali di Azure, le notifiche degli eventi, ad esempio l'aggiunta o l'eliminazione di un dispositivo gemello, possono essere indirizzate a servizi downstream come hub eventi che a loro volta possono essere inseriti in funzioni di Azure. Altre informazioni sul routing degli eventi e sul filtro sono illustrate [qui](https://docs.microsoft.com/azure/digital-twins/how-to-manage-routes-portal).  Questa sezione illustra come usare le API del modello di Time Series Insights in funzioni di Azure per aggiornare Time Series Insights modello in risposta all'aggiunta di dispositivi gemelli (un tipo di modifica del modello) in dispositivi gemelli digitali di Azure.

### <a name="receiving-and-identifying-twin-addition-event-notification"></a>Ricezione e identificazione della notifica degli eventi di aggiunta dei due

```csharp
[FunctionName("RouteEventsToTsi")]
public async Task Run([EventGridTrigger]EventGridEvent eventGridEvent)
{
    try
    {
        if (eventGridEvent != null && eventGridEvent.Data != null)
        {
            logger.LogInformation($"EventType: {eventGridEvent.EventType}");
            logger.LogInformation($"EventGridEvent: {JsonConvert.SerializeObject(eventGridEvent)}");

            //Shape event and Send event data to event hub and tsi
            await SendEventToEventHubAsync(eventGridEvent).ConfigureAwait(false);

            //If a new twin was created, update the newly created instance in TSI with info retrieved from ADT
            if (eventGridEvent.EventType == Constants.TwinCreateEventType)
            {
                //retrieve building, floor and room of value twin
                var twinInfo = await RetrieveTwinInfoAsync(eventGridEvent).ConfigureAwait(false);
                //Update Tsi instance with type(sensor type), hierarchy(space hierarchy) and instance fields(twin info retrieved above)
                var instance = await CreateInstanceToSendAsync(twinInfo).ConfigureAwait(false);
                var instanceToUpdate = new List<TimeSeriesInstance>() { instance };
                var response = await tsiClient.TimeSeriesInstances.ExecuteBatchAsync(new InstancesBatchRequest(update: instanceToUpdate)).ConfigureAwait(false);
            }
        }
    }
    catch (Exception ex)
    {
        logger.LogError($"Exception: {ex.Message}");
    }
}
```

### <a name="creating-time-series-insights-client-and-adding-instance-details"></a>Creazione di Time Series Insights client e aggiunta di dettagli dell'istanza

```csharp
private async Task<TimeSeriesInstance> CreateInstanceToSendAsync(Dictionary<string, string> twinInfo)
{
    try
    {
        tsiClient = await GetTSIClientAsync().ConfigureAwait(false);

        var timeSeriesId = new object[] { twinInfo[Constants.DtId] };
        var instances = await tsiClient.TimeSeriesInstances.ExecuteBatchAsync(
            new InstancesBatchRequest(
                get: new InstancesRequestBatchGetOrDelete(
                    new IList<object>[] { timeSeriesId }))).ConfigureAwait(false);
        var instance = instances.Get.First().Instance;

        if (instance != null)
        {
            instance = await AddHierarchyToInstanceAsync(instance).ConfigureAwait(false);
            instance = await AddTypeToInstanceAsync(instance, twinInfo[Constants.TwinType]).ConfigureAwait(false);

            instance.InstanceFields = new Dictionary<string, object>
                            {
                                { "Building", twinInfo[Constants.BuildingName] },
                                { "Floor", twinInfo[Constants.FloorName] },
                                { "Room", twinInfo[Constants.ParentName] }
                            };

            //If value twin is a sensor value twin, add sensor type instance field to diff from spatial value twin
            if (twinInfo[Constants.ParentType] == Constants.Sensor)
            {
                instance.InstanceFields.Add(Constants.SensorType, twinInfo[Constants.TwinType]);
            }
            return instance;
        }
        else
        {
            logger.LogError($"instance with id {twinInfo[Constants.DtId]} not found");
            return new TimeSeriesInstance();
        }
    }
    catch (Exception e)
    {
        logger.LogError(e.Message);
        throw;
    }
}
```

### <a name="applying-hierarchy-info-to-instance"></a>Applicazione delle informazioni della gerarchia all'istanza

```csharp
private async Task<TimeSeriesInstance> AddHierarchyToInstanceAsync(TimeSeriesInstance instance)
{
    if (instance.HierarchyIds == null)
    {
        TimeSeriesHierarchy spacesHierarchy = null;
        try
        {
            var hierarchy = await RunGetHierarchiesAsync(Constants.SpaceHierarchy).ConfigureAwait(false);
            spacesHierarchy = hierarchy.First(h => h.Name.Equals(Constants.SpaceHierarchy));
            instance.HierarchyIds = new List<Guid?>();
            instance.HierarchyIds.Add(spacesHierarchy.Id);
        }
        catch (Exception ex)
        {
            logger.LogWarning($"Hierarchy 'space hierarchy' not found, {ex}");
            throw;
        }
    }
    return instance;
}
```

## <a name="next-steps"></a>Passaggi successivi

Il terzo della serie di esercitazioni consiste nel mostrare come eseguire query sui dati cronologici dai dispositivi gemelli digitali di Azure usando API Time Series Insights. Si tratta di un lavoro in corso e la sezione verrà aggiornata al momento della preparazione. Nel frattempo, i lettori sono invitati a fare riferimento alla [documentazione dell'API di query dei dati Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/concepts-query-overview).
