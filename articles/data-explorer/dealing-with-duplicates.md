---
title: Gestire i dati duplicati in Esplora dati di Azure
description: Questo argomento si illustra diversi approcci per la gestione con dati duplicati quando si usa Esplora dati di Azure.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/19/2018
ms.openlocfilehash: 8f55b6dfb7b5bc9eda675aca4ed80a66b8a25a7f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60445771"
---
# <a name="handle-duplicate-data-in-azure-data-explorer"></a>Gestire i dati duplicati in Esplora dati di Azure

I dispositivi che inviano dati al cloud mantengono una cache locale dei dati. A seconda delle dimensioni dei dati, la cache locale potrebbe archiviare i dati per giorni o anche mesi. Si vogliono proteggere i database analitici dai dispositivi non funzionanti che inviano di nuovo i dati memorizzati nella cache e causano la duplicazione dei dati nel database analitico. Questo argomento descrive le procedure consigliate per la gestione dei dati duplicati per questi tipi di scenari.

La soluzione migliore per la duplicazione dei dati è evitare la duplicazione. Se possibile, risolvere il problema prima nella pipeline di dati, risparmiando così sui costi associati allo spostamento dati lungo la pipeline di dati ed evitando di sprecare risorse per gestire i dati duplicati inseriti nel sistema. Nelle situazioni in cui il sistema di origine non può essere modificato, esistono tuttavia diversi modi per gestire questo scenario.

## <a name="understand-the-impact-of-duplicate-data"></a>Informazioni sull'impatto dei dati duplicati

Monitorare la percentuale di dati duplicati. Dopo che è stata rilevata la percentuale di dati duplicati, è possibile analizzare l'ambito del problema e l'impatto aziendale e scegliere la soluzione appropriata.

Query di esempio per identificare la percentuale di record duplicati:

```kusto
let _sample = 0.01; // 1% sampling
let _data =
DeviceEventsAll
| where EventDateTime between (datetime('10-01-2018 10:00') .. datetime('10-10-2018 10:00'));
let _totalRecords = toscalar(_data | count);
_data
| where rand()<= _sample
| summarize recordsCount=count() by hash(DeviceId) + hash(EventId) + hash(StationId)  // Use all dimensions that make row unique. Combining hashes can be improved
| summarize duplicateRecords=countif(recordsCount  > 1)
| extend duplicate_percentage = (duplicateRecords / _sample) / _totalRecords  
```

## <a name="solutions-for-handling-duplicate-data"></a>Soluzioni di gestione dei dati duplicati

### <a name="solution-1-dont-remove-duplicate-data"></a>Soluzione n. 1: Non rimuovere i dati duplicati

È importante conoscere i requisiti aziendali e la tolleranza dei dati duplicati. Alcuni set di dati possono gestire una certa percentuale di dati duplicati. Se i dati duplicati non hanno un impatto significativo, è possibile ignorarne la presenza. Il vantaggio di non rimuovere i dati duplicati è che non vengono generati overhead aggiuntivi nel processo di inserimento o nelle prestazioni delle query.

### <a name="solution-2-handle-duplicate-rows-during-query"></a>Soluzione n. 2: Gestire le righe duplicate durante la query

È anche possibile filtrare le righe duplicate nei dati durante la query. La funzione di aggregazione [`arg_max()`](/azure/kusto/query/arg-max-aggfunction) può essere usata per filtrare i record duplicati e restituire l'ultimo record in base al timestamp (o a un'altra colonna). Il vantaggio dell'uso di questo metodo è un inserimento più rapido perché durante la fase di query si verifica la deduplicazione. Tutti i record (inclusi quelli duplicati) sono inoltre disponibili per il controllo e la risoluzione dei problemi. Lo svantaggio di usare la funzione `arg_max` è la maggior durata della query e il carico sulla CPU ogni volta che viene eseguita una query dei dati. A seconda della quantità dei dati sottoposti a query, questa soluzione potrebbe diventare non funzionale o utilizzare troppa memoria e sarà necessario passare ad altre opzioni.

Nell'esempio seguente viene eseguita una query dell'ultimo record inserito per trovare un set di colonne che determinano i record univoci:

```kusto
DeviceEventsAll
| where EventDateTime > ago(90d)
| summarize hint.strategy=shuffle arg_max(EventDateTime, *) by DeviceId, EventId, StationId
```

Questa query può anche essere inserita all'interno di una funzione invece di eseguire direttamente una query sulla tabella:

```kusto
.create function DeviceEventsView
{
DeviceEventsAll
| where EventDateTime > ago(90d)
| summarize arg_max(EventDateTime, *) by DeviceId, EventId, StationId
}
```

### <a name="solution-3-filter-duplicates-during-the-ingestion-process"></a>Soluzione n. 3: Filtrare i duplicati durante il processo di inserimento

Un'altra soluzione consiste nel filtrare i duplicati durante il processo di inserimento. Il sistema ignora i dati duplicati durante l'inserimento nelle tabelle di Kusto. I dati vengono inseriti in una tabella di staging e copiati in un'altra tabella dopo la rimozione delle righe duplicate. Il vantaggio di questa soluzione è che le prestazioni delle query migliorano notevolmente rispetto alla soluzione precedente. Gli svantaggi includono una maggiore quantità di tempo per l'inserimento e costi aggiuntivi per l'archiviazione dei dati.

L'esempio seguente illustra questo metodo:

1. Creare un'altra tabella con lo stesso schema:

    ```kusto
    .create table DeviceEventsUnique (EventDateTime: datetime, DeviceId: int, EventId: int, StationId: int)
    ```

1. Creare una funzione per filtrare i record duplicati tramite anti-join dei nuovi record con quelli inseriti in precedenza.

    ```kusto
    .create function RemoveDuplicateDeviceEvents()
    {
    DeviceEventsAll
    | join hint.strategy=broadcast kind = anti
        (
        DeviceEventsUnique
        | where EventDateTime > ago(7d)   // filter the data for certain time frame
        | limit 1000000   //set some limitations (few million records) to avoid choking-up the system during outage recovery

        ) on DeviceId, EventId, StationId
    }
    ```

    > [!NOTE]
    > I join sono operazioni associate alla CPU e aggiungono un carico ulteriore sul sistema.

1. Impostare [Criteri di aggiornamento](/azure/kusto/management/update-policy) sulla tabella `DeviceEventsUnique`. I criteri di aggiornamento vengono attivati quando vengono aggiunti nuovi dati alla tabella `DeviceEventsAll`. Il motore Kusto eseguirà automaticamente la funzione quando verranno creati nuovi [extent](/azure/kusto/management/extents-overview). L'elaborazione ha come ambito i dati appena creati. Il comando seguente unisce la tabella di origine (`DeviceEventsAll`), la tabella di destinazione (`DeviceEventsUnique`) e la funzione `RemoveDuplicatesDeviceEvents` per creare i criteri di aggiornamento.

    ```kusto
    .alter table DeviceEventsUnique policy update
    @'[{"IsEnabled": true, "Source": "DeviceEventsAll", "Query": "RemoveDuplicateDeviceEvents()", "IsTransactional": true, "PropagateIngestionProperties": true}]'
    ```

    > [!NOTE]
    > I criteri di aggiornamento estendono la durata dell'inserimento perché i dati vengono filtrati durante l'inserimento e quindi vengono inseriti due volte (nella tabella `DeviceEventsAll` e nella tabella `DeviceEventsUnique`).

1. (Facoltativo) Impostare una conservazione dei dati più bassa per la tabella `DeviceEventsAll` per evitare di archiviare copie dei dati. Scegliere il numero di giorni in base al volume dei dati e al periodo di tempo per cui si vogliono conservare i dati per la risoluzione dei problemi. È possibile impostarlo su un periodo di conservazione di `0d` giorni per risparmiare COGS e migliorare le prestazioni, dal momento che i dati non vengono caricati nella risorsa di archiviazione.

    ```kusto
    .alter-merge table DeviceEventsAll policy retention softdelete = 1d
    ```

## <a name="summary"></a>Summary

La duplicazione dei dati può essere gestita in diversi modi. Valutare le opzioni con attenzione, tenendo conto del prezzo e delle prestazioni, per determinare il metodo corretto per la propria azienda.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Scrivere query per Esplora dati di Azure](write-queries.md)
