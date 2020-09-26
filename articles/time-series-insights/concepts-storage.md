---
title: Panoramica dell'archiviazione-Azure Time Series Insights Gen2 | Microsoft Docs
description: Informazioni sull'archiviazione dei dati in Azure Time Series Insights Gen2.
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 09/15/2020
ms.custom: seodec18
ms.openlocfilehash: d8e3c7258a70902fe362ee73c2f366146484ce54
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91287542"
---
# <a name="data-storage"></a>Archiviazione dati

Questo articolo descrive l'archiviazione dei dati in Azure Time Series Insights Gen2. Vengono illustrate le procedure consigliate, la disponibilità dei dati e le attività a caldo e a freddo.

## <a name="provisioning"></a>Provisioning

Quando si crea un ambiente Azure Time Series Insights Gen2, sono disponibili le opzioni seguenti:

* Archiviazione dati a freddo:
   * Creare una nuova risorsa di archiviazione di Azure nella sottoscrizione e nell'area scelte per l'ambiente.
   * Alleghi un account di archiviazione di Azure preesistente. Questa opzione è disponibile solo distribuendo da un [modello](https://docs.microsoft.com/azure/templates/microsoft.timeseriesinsights/allversions)di Azure Resource Manager e non è visibile nel portale di Azure.
* Archiviazione dati a caldo:
   * Un archivio caldo è facoltativo e può essere abilitato o disabilitato durante o dopo l'esecuzione del provisioning. Se si decide di abilitare l'archivio a caldo in un secondo momento e sono già presenti dati nell'archivio a freddo, esaminare [questa](concepts-storage.md#warm-store-behavior) sezione per comprendere il comportamento previsto. È possibile configurare il periodo di conservazione dei dati dell'archivio di riscaldamento per 7 o 31 giorni. questa operazione può anche essere regolata in base alle esigenze.

Quando un evento viene inserito, viene indicizzato sia nell'archivio a caldo (se abilitato) che nell'archivio a freddo.

[![Panoramica dell'archiviazione](media/concepts-storage/pipeline-to-storage.png)](media/concepts-storage/pipeline-to-storage.png#lightbox)


> [!WARNING]
> In qualità di proprietario dell'account di Archiviazione BLOB di Azure in cui si trovano i dati dell'archivio ad accesso sporadico, l'utente ha completo accesso a tutti i dati dell'account. Questo accesso include le autorizzazioni di scrittura ed eliminazione. Non modificare o eliminare i dati che Azure Time Series Insights Scritture Gen2 perché ciò può causare la perdita di dati.

## <a name="data-availability"></a>Disponibilità dei dati

Azure Time Series Insights Gen2 le partizioni e indicizza i dati per ottenere prestazioni ottimali delle query. I dati diventeranno disponibili per eseguire una query sia dal sistema caldo (se abilitato) che dall'archivio a freddo dopo che è stato indicizzato. La quantità di dati da inserire e la velocità effettiva per partizione possono influire sulla disponibilità. Esaminare le limitazioni relative alla [velocità effettiva](./concepts-streaming-ingress-throughput-limits.md) dell'origine eventi e le [procedure](./concepts-streaming-ingestion-event-sources.md#streaming-ingestion-best-practices) consigliate per ottenere prestazioni ottimali. È anche possibile configurare un [avviso](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-environment-mitigate-latency#monitor-latency-and-throttling-with-alerts) di ritardo per ricevere una notifica nel caso in cui si verifichino problemi di elaborazione dei dati nell'ambiente.

> [!IMPORTANT]
> È possibile che si verifichi un periodo di tempo massimo di 60 secondi prima che i dati diventino disponibili. Se si verifica una latenza significativa superiore a 60 secondi, inviare un ticket di supporto tramite il portale di Azure.

## <a name="warm-store"></a>Negozio caldo

I dati nell'archivio a caldo sono disponibili solo tramite le [API di query della serie temporale](./time-series-insights-update-tsq.md), il [Azure Time Series Insights di gestione](./time-series-insights-update-explorer.md)delle chiavi di gestione delle chiavi o il [connettore Power bi](./how-to-connect-power-bi.md). Le query di archivio warm sono gratuite e non esiste alcuna quota, ma è previsto un [limite di 30](https://docs.microsoft.com/rest/api/time-series-insights/reference-api-limits#query-apis---limits) richieste simultanee.

### <a name="warm-store-behavior"></a>Comportamento dell'archivio caldo 

* Quando questa funzionalità è abilitata, tutti i dati trasmessi nell'ambiente verranno indirizzati all'archivio a caldo, indipendentemente dal timestamp dell'evento. Si noti che la pipeline di inserimento del flusso è compilata per lo streaming quasi in tempo reale e l'inserimento di eventi cronologici [non è supportato](./concepts-streaming-ingestion-event-sources.md#historical-data-ingestion).
* Il periodo di memorizzazione viene calcolato in base al momento in cui l'evento è stato indicizzato nell'archivio a caldo, non al timestamp dell'evento. Ciò significa che i dati non sono più disponibili in warm Store dopo che è trascorso il periodo di memorizzazione, anche se il timestamp dell'evento è per il futuro.
  - Esempio: un evento con previsioni meteorologiche di 10 giorni viene inserito e indicizzato in un contenitore di archiviazione a caldo configurato con un periodo di conservazione di 7 giorni. Dopo 7 giorni di tempo, la stima non è più accessibile nell'archivio a caldo, ma è possibile eseguire query a freddo. 
* Se si Abilita l'archiviazione a caldo in un ambiente esistente in cui sono già stati indicizzati i dati recenti nell'archiviazione a freddo, si noti che l'archivio di riscaldamento non verrà riempito con questi dati.
* Se è stata appena abilitata l'opzione warm Store e si verificano problemi durante la visualizzazione dei dati recenti in Esplora risorse, è possibile disattivare temporaneamente le query di warm Store:

   [![Disabilitare le query a caldo](media/concepts-storage/toggle-warm.png)](media/concepts-storage/toggle-warm.png#lightbox)

## <a name="cold-store"></a>Archivio a freddo

Questa sezione descrive i dettagli di archiviazione di Azure rilevanti per Azure Time Series Insights Gen2.

Per una descrizione completa dell'Archiviazione BLOB di Azure, vedere l'[introduzione ai BLOB di archiviazione ](../storage/blobs/storage-blobs-introduction.md).

### <a name="your-cold-storage-account"></a>L'account di archiviazione a freddo

Azure Time Series Insights Gen2 conserva fino a due copie di ogni evento nell'account di archiviazione di Azure. Una copia archivia gli eventi ordinati in base al momento dell'inserimento, consentendo sempre l'accesso agli eventi in una sequenza temporale ordinata. Nel corso del tempo, Azure Time Series Insights Gen2 crea anche una copia ripartizionata dei dati per ottimizzare le query efficienti.

Tutti i dati vengono archiviati per un periodo illimitato nell'account di archiviazione di Azure.

#### <a name="writing-and-editing-blobs"></a>Scrittura e modifica di BLOB

Per garantire le prestazioni delle query e la disponibilità dei dati, non modificare o eliminare i BLOB creati da Azure Time Series Insights Gen2.

#### <a name="accessing-cold-store-data"></a>Accesso ai dati dell'archivio freddo

Oltre ad accedere ai dati dalle API di query di [Azure Time Series Insights Explorer](./time-series-insights-update-explorer.md) e della [serie temporale](./time-series-insights-update-tsq.md), è anche possibile accedere ai dati direttamente dai file parquet archiviati nell'archivio a freddo. Ad esempio, è possibile leggere, trasformare e pulire i dati in un notebook di Jupyter, e poi usarli per eseguire il training del modello di Azure Machine Learning nello stesso flusso di lavoro Spark.

Per accedere ai dati direttamente dall'account di archiviazione di Azure, è necessario l'accesso in lettura all'account usato per archiviare i dati di Azure Time Series Insights Gen2. È quindi possibile leggere i dati selezionati in base all'ora di creazione del file parquet presente nella cartella `PT=Time` descritta di seguito nella sezione dedicata al [formato di file parquet](#parquet-file-format-and-folder-structure).  Per altre informazioni sull'abilitazione dell'accesso in lettura all'account di archiviazione, vedere [Gestire l'accesso alle risorse dell'account di archiviazione](../storage/blobs/storage-manage-access-to-resources.md).

#### <a name="data-deletion"></a>Eliminazione di dati

Non eliminare i file di Azure Time Series Insights Gen2. Gestire i dati correlati solo dall'interno Azure Time Series Insights Gen2.

### <a name="parquet-file-format-and-folder-structure"></a>Formato di file parquet e struttura della cartelle

Parquet è un formato di file open source a colonne progettato per archiviazioni e prestazioni efficienti. Azure Time Series Insights Gen2 USA parquet per abilitare le prestazioni delle query basate su ID della serie temporale su larga scala.  

Per altre informazioni sul tipo di file parquet, vedere la [documentazione relativa a parquet](https://parquet.apache.org/documentation/latest/).

Azure Time Series Insights Gen2 archivia copie dei dati come segue:

* La prima copia, quella iniziale, viene partizionata in base all'ora di inserimento e archivia i dati approssimativamente in ordine di arrivo. Questi dati si trovano nella cartella `PT=Time`:

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* La seconda copia ripartizionata viene raggruppata in base agli ID serie temporale e si trova nella cartella `PT=TsId`:

  `V=1/PT=TsId/<TSI_INTERNAL_NAME>.parquet`

Il timestamp nei nomi dei BLOB nella `PT=Time` cartella corrisponde all'ora di arrivo dei dati Azure Time Series Insights Gen2 e non al timestamp degli eventi.

I dati nella cartella `PT=TsId` verranno ottimizzati per la query nel tempo e non sono statici. Durante il partizionamento, è possibile che alcuni eventi siano presenti in più BLOB. Non è garantito che la denominazione dei BLOB in questa cartella rimanga invariata.

In generale, se è necessario accedere ai dati direttamente tramite file parquet, utilizzare la `PT=Time` cartella.  Le funzionalità future consentiranno di accedere in modo efficiente alla `PT=TsId` cartella.

> [!NOTE]
>
> * `<YYYY>` esegue il mapping a una rappresentazione dell'anno a 4 cifre.
> * `<MM>` esegue il mapping a una rappresentazione del mese a 2 cifre.
> * `<YYYYMMDDHHMMSSfff>` esegue il mapping a una rappresentazione timestamp dell'anno a quattro cifre (`YYYY`), del mese a due cifre (`MM`), del giorno a due cifre (`DD`), dell'ora a due cifre (`HH`), dei minuti a due cifre (`MM`), dei secondi a due cifre (`SS`) e dei millisecondi a tre cifre (`fff`).

Azure Time Series Insights eventi Gen2 vengono mappati al contenuto del file parquet come indicato di seguito:

* Viene eseguito il mapping di ogni evento a una singola riga.
* Ogni riga include la colonna **timestamp** con un timestamp dell'evento. La proprietà timestamp non ha mai un valore Null. Il valore predefinito è l' **ora di accodamento dell'evento** se la proprietà timestamp non è specificata nell'origine evento. Il timestamp archiviato è sempre in formato UTC.
* Ogni riga include le colonne IDST (Time Series ID) definite quando viene creato l'ambiente Azure Time Series Insights Gen2. Il nome della proprietà TSID include il suffisso `_string`.
* Tutte le altre proprietà inviate come dati di telemetria vengono mappate ai nomi di colonna che terminano con `_bool` (booleano), `_datetime` (timestamp), `_long` (Long), `_double` (Double), `_string` (String) o `dynamic` (Dynamic), a seconda del tipo di proprietà.  Per ulteriori informazioni, vedere informazioni sui [tipi di dati supportati](./concepts-supported-data-types.md).
* Questo schema di mapping si applica alla prima versione del formato di file, a cui si fa riferimento come **V=1** ed è archiviato nella cartella di base con lo stesso nome. Con l'evolversi di questa funzionalità, lo schema di mapping potrebbe cambiare e il numero presente nel nome di riferimento potrebbe aumentare.

## <a name="next-steps"></a>Passaggi successivi

* Vedere la pagina relativa alla [modellazione dei dati](./time-series-insights-update-tsm.md).

* Pianificare l' [ambiente di Azure Time Series Insights Gen2](./time-series-insights-update-plan.md).
