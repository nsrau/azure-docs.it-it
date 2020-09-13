---
title: Panoramica dell'archiviazione-Azure Time Series Insights Gen2 | Microsoft Docs
description: Informazioni sull'archiviazione dei dati in Azure Time Series Insights Gen2.
author: esung22
ms.author: elsung
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 08/31/2020
ms.custom: seodec18
ms.openlocfilehash: c05de0462dde2b09e0e01919dfc691a85df153fa
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2020
ms.locfileid: "89483270"
---
# <a name="data-storage"></a>Archiviazione dati

Quando si crea un ambiente Azure Time Series Insights Gen2, si creano due risorse di Azure:

* Un ambiente Azure Time Series Insights Gen2 che può essere configurato per l'archiviazione dei dati a caldo.
* Un account di archiviazione di Azure per l'archiviazione dei dati a freddo.

I dati nell'archivio a caldo sono disponibili solo tramite le [API di query di serie temporali](./time-series-insights-update-tsq.md) e [Esplora Azure Time Series Insights](./time-series-insights-update-explorer.md). Il negozio caldo conterrà i dati recenti entro il [periodo di memorizzazione](./time-series-insights-update-plan.md#the-preview-environment) selezionato durante la creazione dell'ambiente di Azure Time Series Insights Gen2.

Azure Time Series Insights Gen2 Salva i dati dell'archivio a freddo nell'archivio BLOB di Azure nel [formato di file parquet](#parquet-file-format-and-folder-structure). Azure Time Series Insights Gen2 gestisce esclusivamente i dati di archivio a freddo, ma è possibile leggerli direttamente come file parquet standard.

> [!WARNING]
> In qualità di proprietario dell'account di Archiviazione BLOB di Azure in cui si trovano i dati dell'archivio ad accesso sporadico, l'utente ha completo accesso a tutti i dati dell'account. Questo accesso include le autorizzazioni di scrittura ed eliminazione. Non modificare o eliminare i dati che Azure Time Series Insights Scritture Gen2 perché ciò può causare la perdita di dati.

## <a name="data-availability"></a>Disponibilità dei dati

Azure Time Series Insights Gen2 le partizioni e indicizza i dati per ottenere prestazioni ottimali delle query. I dati diventeranno disponibili per eseguire una query sia dal sistema caldo (se abilitato) che dall'archivio a freddo dopo che è stato indicizzato. La quantità di dati inseriti può influire sulla disponibilità.

> [!IMPORTANT]
> È possibile che si verifichi un periodo di tempo massimo di 60 secondi prima che i dati diventino disponibili. Se si verifica una latenza significativa superiore a 60 secondi, inviare un ticket di supporto tramite il portale di Azure.

## <a name="azure-storage"></a>Archiviazione di Azure

Questa sezione descrive i dettagli di archiviazione di Azure rilevanti per Azure Time Series Insights Gen2.

Per una descrizione completa dell'Archiviazione BLOB di Azure, vedere l'[introduzione ai BLOB di archiviazione ](../storage/blobs/storage-blobs-introduction.md).

### <a name="your-storage-account"></a>Account di archiviazione

Quando si crea un ambiente di Azure Time Series Insights Gen2, viene creato un account di archiviazione di Azure come archivio a lungo termine a freddo.  

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
