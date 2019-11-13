---
title: Archiviazione dati e ingresso in anteprima-Azure Time Series Insights | Microsoft Docs
description: Informazioni sull'archiviazione e l'ingresso dei dati in Azure Time Series Insights Preview.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: seodec18
ms.openlocfilehash: 62ee248c06d2b26b935f72b3bb73cf708f949c72
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014716"
---
# <a name="data-storage-and-ingress-in-azure-time-series-insights-preview"></a>Archiviazione e ingresso dei dati nella versione di anteprima di Azure Time Series Insights

Questo articolo descrive gli aggiornamenti per l'archiviazione dei dati e il traffico in ingresso per Azure Time Series Insights anteprima. Esamina la struttura di archiviazione sottostante, il formato dei file e la proprietà Time Series ID. Viene inoltre illustrato il processo di ingresso sottostante, le procedure consigliate e le limitazioni dell'anteprima corrente.

## <a name="data-ingress"></a>Dati in ingresso

L'ambiente di Azure Time Series Insights contiene un motore di inserimento per la raccolta, l'elaborazione e l'archiviazione di dati di serie temporali. Quando si pianifica l'ambiente, è necessario tenere in considerazione alcune considerazioni per garantire che tutti i dati in ingresso vengano elaborati e per ottenere scalabilità in ingresso elevata e ridurre al minimo la latenza di inserimento (il tempo impiegato da TSI per leggere ed elaborare i dati dall'evento origine). In Time Series Insights anteprima i criteri di ingresso dei dati determinano la posizione in cui i dati possono essere originati e il formato dei dati.

### <a name="ingress-policies"></a>Criteri di ingresso

Time Series Insights anteprima supporta le origini eventi seguenti:

- [Hub IoT Azure](../iot-hub/about-iot-hub.md)
- [Hub eventi di Azure](../event-hubs/event-hubs-about.md)

Time Series Insights anteprima supporta un massimo di due origini evento per ogni istanza.
  
Azure Time Series Insights supporta il formato JSON inviato tramite l'hub Azure o hub eventi di Azure.

> [!WARNING] 
> Quando si aggiunge una nuova origine evento all'ambiente di Time Series Insights anteprima, a seconda del numero di eventi attualmente presenti nell'hub o nell'hub eventi, è possibile che si verifichi una latenza di inserimento iniziale elevata. Quando i dati vengono inseriti, è necessario aspettarsi che questa latenza elevata sia secondaria, ma se l'esperienza indica altrimenti contattaci inviando un ticket di supporto tramite la portale di Azure.

## <a name="ingress-best-practices"></a>Procedure consigliate per il traffico in ingresso

Si consiglia di utilizzare le seguenti procedure consigliate:

* Configurare Time Series Insights e un hub o hub eventi nella stessa area. In questo modo si riduce la latenza di inserimento causata dalla rete.
* Pianificare le esigenze di scalabilità calcolando la velocità di inserimento prevista e verificando che rientri nella tariffa supportata indicata di seguito
* Informazioni su come ottimizzare e modellare i dati JSON, nonché le limitazioni correnti in anteprima, leggendo [come definire il formato JSON per l'ingresso e la query](./time-series-insights-update-how-to-shape-events.md).

### <a name="ingress-scale-and-limitations-in-preview"></a>Scalabilità in ingresso e limitazioni in anteprima

Per impostazione predefinita, Time Series Insights anteprima supporta una scala di ingresso iniziale di un massimo di 1 megabyte al secondo (MB/s) per ogni ambiente. Se necessario, è disponibile fino a 16 MB/s, se necessario, contattaci inviando un ticket di supporto nel portale di Azure se necessario. Inoltre, è previsto un limite per partizione di 0,5 MB/s. Questa operazione ha implicazioni per i clienti che usano l'hub Internet in modo specifico, data l'affinità tra un dispositivo dell'hub Internet e una partizione. Negli scenari in cui un dispositivo gateway invia messaggi all'hub usando il proprio ID dispositivo e la stringa di connessione, sussiste il rischio di raggiungere il limite di 0,5 MB/s, dato che i messaggi arriveranno in una singola partizione, anche se il payload dell'evento specifica Servizi terminal diversi ID. In generale, la velocità in ingresso viene considerata come un fattore del numero di dispositivi nell'organizzazione, della frequenza di emissione degli eventi e delle dimensioni di un evento. Quando si calcola la velocità di inserimento, gli utenti dell'hub Internet devono usare il numero di connessioni Hub in uso, anziché i dispositivi totali nell'organizzazione. Il supporto per una scalabilità di livello avanzato è in fase di sviluppo. Questa documentazione verrà aggiornata per riflettere tali miglioramenti. 

> [!WARNING]
> Per gli ambienti che usano l'hub Internet come origine eventi, calcolare la velocità di inserimento usando il numero di dispositivi hub in uso.

Per ulteriori informazioni sulle unità di velocità effettiva e sulle partizioni, vedere i collegamenti seguenti:

* [Scalabilità dell'hub Internet](https://docs.microsoft.com/azure/iot-hub/iot-hub-scaling)
* [Scalabilità dell'hub eventi](https://docs.microsoft.com/azure/event-hubs/event-hubs-scalability#throughput-units)
* [Partizioni dell'hub eventi](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#partitions)

### <a name="data-storage"></a>Archiviazione dei dati

Quando si crea un ambiente con SKU con pagamento in base al consumo Time Series Insights anteprima, si creano due risorse di Azure:

* Un ambiente Time Series Insights anteprima che può includere facoltativamente funzionalità di archiviazione A caldo.
* Un account BLOB di archiviazione di Azure per utilizzo generico V1 per l'archiviazione di dati a freddo.

I dati nell'archivio a caldo sono disponibili solo tramite [Query Time Series](./time-series-insights-update-tsq.md) e [Esplora Azure Time Series Insights Preview](./time-series-insights-update-explorer.md). 

Time Series Insights anteprima Salva i dati dell'archivio a freddo nell'archivio BLOB di Azure nel [formato di file parquet](#parquet-file-format-and-folder-structure). Time Series Insights anteprima gestisce i dati di archivio a freddo esclusivamente, ma è disponibile per la lettura diretta come file parquet standard.

> [!WARNING]
> Il proprietario dell'account di archiviazione BLOB di Azure in cui risiedono i dati dell'archivio freddo ha accesso completo a tutti i dati dell'account. Questo accesso include le autorizzazioni di scrittura ed eliminazione. Non modificare o eliminare i dati scritti da Time Series Insights Preview, perché ciò può causare la perdita di dati.

### <a name="data-availability"></a>Disponibilità dei dati

Time Series Insights Visualizza in anteprima le partizioni e indicizza i dati per ottenere prestazioni ottimali delle query. I dati diventano disponibili per eseguire una query dopo l'indicizzazione. La quantità di dati da inserire può influire sulla disponibilità.

> [!IMPORTANT]
> La prossima versione di disponibilità generale (GA) di Time Series Insights renderà i dati disponibili in 60 secondi dopo la lettura dall'origine evento. Durante l'anteprima è possibile che si verifichi un periodo di tempo più lungo prima che i dati diventino disponibili. Se si verifica una latenza significativa superiore a 60 secondi, inviare un ticket di supporto tramite la portale di Azure.

## <a name="azure-storage"></a>Archiviazione di Azure

Questa sezione descrive i dettagli di archiviazione di Azure rilevanti per Azure Time Series Insights Preview.

Per una descrizione completa dell'archiviazione BLOB di Azure, vedere [Introduzione ai BLOB di archiviazione](../storage/blobs/storage-blobs-introduction.md).

### <a name="your-storage-account"></a>L'account di archiviazione

Quando si crea un ambiente con pagamento in base al consumo di Time Series Insights Preview, viene creato un account BLOB di archiviazione di Azure per utilizzo generico V1 come archivio a lungo termine.  

Time Series Insights anteprima pubblica fino a due copie di ogni evento nell'account di archiviazione di Azure. La copia iniziale ha eventi ordinati in base al tempo di inserimento e viene sempre mantenuta, quindi è possibile usare altri servizi per accedervi. È possibile usare Spark, Hadoop e altri strumenti noti per elaborare i file parquet non elaborati. 

Time Series Insights anteprima consente di ripartizionare i file parquet per ottimizzare la query Time Series Insights. Viene salvata anche questa copia di dati ripartizionata.

Durante l'anteprima pubblica, i dati vengono archiviati per un periodo illimitato nell'account di archiviazione di Azure.

### <a name="writing-and-editing-time-series-insights-blobs"></a>Scrittura e modifica di BLOB di Time Series Insights

Per garantire le prestazioni delle query e la disponibilità dei dati, non modificare o eliminare i BLOB creati da Time Series Insights anteprima.

### <a name="accessing-and-exporting-data-from-time-series-insights-preview"></a>Accesso ed esportazione dei dati dalla versione di anteprima di Time Series Insights

Potrebbe essere necessario accedere ai dati visualizzati in Esplora Time Series Insights Preview da usare insieme ad altri servizi. Ad esempio, è possibile usare i dati per compilare un report in Power BI o per eseguire il training di un modello di apprendimento automatico usando Azure Machine Learning Studio. In alternativa, è possibile usare i dati per trasformare, visualizzare e modellare nel notebook di Jupyter.

È possibile accedere ai dati in tre modi:

* Dallo strumento di esplorazione dell'anteprima di Time Series Insights È possibile esportare i dati come file CSV dalla finestra di esplorazione. Per altre informazioni, vedere [Strumento di esplorazione dell'anteprima di Time Series Insights](./time-series-insights-update-explorer.md).
* Dall'API di Time Series Insights Preview. È possibile raggiungere l'endpoint API in `/getRecorded`. Per altre informazioni su questa API, vedere [Query Time Series](./time-series-insights-update-tsq.md).
* Direttamente da un account di archiviazione di Azure È necessario l'accesso in lettura a qualsiasi account usato per accedere ai dati di Time Series Insights Preview. Per altre informazioni, vedere [Gestire l'accesso alle risorse degli account di archiviazione di Azure](../storage/blobs/storage-manage-access-to-resources.md).

### <a name="data-deletion"></a>Eliminazione di dati

Non eliminare i file di anteprima Time Series Insights. È consigliabile gestire i dati correlati solo in Time Series Insights anteprima.

## <a name="parquet-file-format-and-folder-structure"></a>Formato di file parquet e struttura di cartelle

Parquet è un formato di file a colonne open source progettato per l'archiviazione e le prestazioni efficienti. Time Series Insights anteprima usa parquet per questi motivi. I dati vengono partizionati in base all'ID della serie temporale per le prestazioni delle query su larga scala.  

Per ulteriori informazioni sul tipo di file parquet, vedere la [documentazione parquet](https://parquet.apache.org/documentation/latest/).

Time Series Insights anteprima archivia le copie dei dati come segue:

* La prima copia iniziale viene partizionata in base al tempo di inserimento e archivia approssimativamente i dati in ordine di arrivo. I dati si trovano nella cartella `PT=Time`:

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* La seconda copia ripartizionata viene partizionata in base a un raggruppamento di ID di serie temporali e si trova nella cartella `PT=TsId`:

  `V=1/PT=TsId/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

In entrambi i casi, i valori temporali corrispondono al tempo di creazione del BLOB. I dati nella cartella `PT=Time` vengono conservati. I dati nella cartella `PT=TsId` verranno ottimizzati per la query nel tempo e non rimarranno statici.

> [!NOTE]
> * `<YYYY>` viene eseguito il mapping a una rappresentazione dell'anno a quattro cifre.
> * `<MM>` viene eseguito il mapping a una rappresentazione mensile a due cifre.
> * `<YYYYMMDDHHMMSSfff>` esegue il mapping a una rappresentazione timestamp con anno a quattro cifre (`YYYY`), mese a due cifre (`MM`), giorno a due cifre (`DD`), ora a due cifre (`HH`), minuto a due cifre (`MM`), secondo (`SS`) a due cifre e millisecondi a tre cifre (`fff`).

Per gli eventi di anteprima Time Series Insights viene eseguito il mapping al contenuto del file parquet come segue:

* Viene eseguito il mapping di ogni evento a una singola riga.
* Ogni riga include la colonna **timestamp** con un timestamp dell'evento. La proprietà timestamp non è mai null. Se la proprietà timestamp non è specificata nell'origine evento, il valore predefinito è l' **ora di accodamento degli eventi** . Il timestamp è sempre in formato UTC.
* Ogni riga include la colonna ID della serie temporale definita quando viene creato l'ambiente Time Series Insights. Il nome della proprietà include il suffisso `_string`.
* Tutte le altre proprietà inviate come dati di telemetria vengono mappate ai nomi di colonna che terminano con `_string` (String), `_bool` (Boolean), `_datetime` (DateTime) o `_double` (Double), a seconda del tipo di proprietà.
* Questo schema di mapping si applica alla prima versione del formato di file, a cui viene fatto riferimento come **V = 1**. Con l'evolversi di questa funzionalità, il nome potrebbe essere incrementato.

## <a name="next-steps"></a>Passaggi successivi

- Leggere [Azure Time Series Insights archiviazione in anteprima e in ingresso](./time-series-insights-update-storage-ingress.md).

- Scopri di più sulla nuova [modellazione dei dati](./time-series-insights-update-tsm.md).
