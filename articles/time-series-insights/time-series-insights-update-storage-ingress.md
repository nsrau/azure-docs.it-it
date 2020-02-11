---
title: Archiviazione dati e ingresso in anteprima-Azure Time Series Insights | Microsoft Docs
description: Informazioni sull'archiviazione e l'ingresso dei dati in Azure Time Series Insights Preview.
author: lyrana
ms.author: lyhughes
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/10/2020
ms.custom: seodec18
ms.openlocfilehash: 0c7f2de0a454dceeff1946a93801c20ad81ab0ab
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77122513"
---
# <a name="data-storage-and-ingress-in-azure-time-series-insights-preview"></a>Archiviazione e ingresso dei dati nella versione di anteprima di Azure Time Series Insights

Questo articolo descrive gli aggiornamenti per l'archiviazione dei dati e il traffico in ingresso per Azure Time Series Insights anteprima. Descrive la struttura di archiviazione sottostante, il formato di file e la proprietà ID Time Series. Vengono descritti anche il processo di ingresso sottostante, le procedure consigliate e le limitazioni dell'anteprima corrente.

## <a name="data-ingress"></a>Dati in ingresso

L'ambiente di Azure Time Series Insights contiene un *motore* di inserimento per la raccolta, l'elaborazione e l'archiviazione di dati di serie temporali. 

Ci sono alcune considerazioni da tenere presenti per assicurarsi che tutti i dati in ingresso vengano elaborati, per ottenere scalabilità in ingresso elevata e ridurre al minimo la *latenza* di inserimento (il tempo impiegato da Time Series Insights per leggere ed elaborare i dati dall'origine evento) durante [la pianificazione dell'ambiente](time-series-insights-update-plan.md).

I criteri di ingresso dei dati di Time Series Insights Preview determinano la posizione in cui i dati possono essere originati e il formato dei dati.

### <a name="ingress-policies"></a>Criteri di ingresso

L' *ingresso dei dati* implica la modalità di invio dei dati a un ambiente di Azure Time Series Insights anteprima. 

La configurazione della chiave, la formattazione e le procedure consigliate sono riepilogate di seguito.

#### <a name="event-sources"></a>Origini eventi

Azure Time Series Insights anteprima supporta le origini eventi seguenti:

- [Hub IoT Azure](../iot-hub/about-iot-hub.md)
- [Hub eventi di Azure](../event-hubs/event-hubs-about.md)

Azure Time Series Insights anteprima supporta un massimo di due origini evento per ogni istanza.

> [!IMPORTANT] 
> * È possibile che si verifichi una latenza iniziale elevata quando si connette un'origine evento all'ambiente di anteprima. 
> La latenza dell'origine evento dipende dal numero di eventi attualmente presenti nell'hub o nell'hub eventi.
> * Una latenza elevata sarà secondaria dopo l'inserimento iniziale dei dati dell'origine evento. Inviare un ticket di supporto tramite il portale di Azure se si verifica una latenza elevata.

#### <a name="supported-data-format-and-types"></a>Tipi e formati di dati supportati

Azure Time Series Insights supporta JSON con codifica UTF-8 inviati dall'hub degli eventi di Azure o dagli hub eventi di Azure. 

I tipi di dati supportati sono:

| Tipo di dati | Descrizione |
|---|---|
| **bool** | Tipo di dati con uno dei due stati seguenti: `true` o `false`. |
| **dateTime** | Rappresenta un istante di tempo, in genere espresso come data e ora del giorno. Espressa in formato [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html) . |
| **double** | Un punto A virgola mobile [IEEE 754](https://ieeexplore.ieee.org/document/8766229) a precisione doppia a 64 bit. |
| **string** | Valori di testo, costituiti da caratteri Unicode.          |

#### <a name="objects-and-arrays"></a>Oggetti e matrici

È possibile inviare tipi complessi, ad esempio oggetti e matrici come parte del payload dell'evento, ma i dati vengono sottoposti a un processo di Flat quando vengono archiviati. 

Informazioni dettagliate che descrivono come definire gli eventi JSON, inviare il tipo complesso e la flat degli oggetti annidati sono disponibili nella pagina [relativa alla modalità di modellazione di JSON per l'ingresso e la query](./time-series-insights-update-how-to-shape-events.md) per semplificare la pianificazione e l'ottimizzazione.

### <a name="ingress-best-practices"></a>Procedure consigliate per il traffico in ingresso

Si consiglia di utilizzare le seguenti procedure consigliate:

* Configurare Azure Time Series Insights e qualsiasi hub eventi o hub eventi nella stessa area per ridurre la latenza potenziale.

* [Pianificare le esigenze di scalabilità](time-series-insights-update-plan.md) calcolando la velocità di inserimento prevista e verificando che rientri nella tariffa supportata indicata di seguito.

* Informazioni su come ottimizzare e modellare i dati JSON, nonché le limitazioni correnti in anteprima, leggendo [come definire il formato JSON per l'ingresso e la query](./time-series-insights-update-how-to-shape-events.md).

### <a name="ingress-scale-and-preview-limitations"></a>Limitazioni di scalabilità e anteprima in ingresso 

Di seguito sono descritte le limitazioni in ingresso Azure Time Series Insights Preview.

> [!TIP]
> Per un elenco completo di tutti i limiti di anteprima, vedere [pianificare l'ambiente di anteprima](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-plan#review-preview-limits) .

#### <a name="per-environment-limitations"></a>Limitazioni per ambiente

In generale, le tariffe in ingresso vengono visualizzate come fattore del numero di dispositivi all'interno dell'organizzazione, della frequenza di emissione degli eventi e delle dimensioni di ogni evento:

*  **Numero di dispositivi** × **frequenza di emissione eventi** × **dimensioni di ogni evento**.

Per impostazione predefinita, Time Series Insights anteprima può inserire i dati in ingresso a una velocità di un **massimo di 1 MB al secondo (Mbps) per ogni ambiente Time Series Insights**.

> [!TIP] 
> * Il supporto dell'ambiente per l'inserimento di velocità fino a 16 MBps può essere fornito dalla richiesta.
> * Contattaci se è necessaria una velocità effettiva maggiore inviando un ticket di supporto tramite portale di Azure.
 
* **Esempio 1:**

    Contoso Shipping ha 100.000 dispositivi che emettono un evento tre volte al minuto. Le dimensioni di un evento sono pari a 200 byte. Usano un hub eventi con quattro partizioni come origine evento Time Series Insights.

    * La velocità di inserimento per l'ambiente di Time Series Insights sarà: **100.000 dispositivi * 200 byte/evento * (3/60 evento/sec) = 1 Mbps**.
    * La velocità di inserimento per partizione è 0,25 MBps.
    * Il tasso di inserimento di Contoso Shipping sarà entro il limite di anteprima della scalabilità.

* **Esempio 2:**

    Contoso Fleet Analytics ha 60.000 dispositivi che emettono un evento ogni secondo. Si sta usando un numero di partizioni dell'hub Internet di 4 come Time Series Insights origine evento. Le dimensioni di un evento sono pari a 200 byte.

    * La velocità di inserimento dell'ambiente è: **20.000 dispositivi * 200 byte/evento * 1 evento/sec = 4 Mbps**.
    * Il tasso per partizione è 1 MBps.
    * Contoso Fleet Analytics può inviare una richiesta di Time Series Insights tramite portale di Azure per aumentare la velocità di inserimento per il proprio ambiente.

#### <a name="hub-partitions-and-per-partition-limits"></a>Partizioni dell'hub e limiti per partizione

Quando si pianifica l'ambiente di Time Series Insights, è importante prendere in considerazione la configurazione delle origini eventi a cui ci si connetterà Time Series Insights. Sia l'hub eventi di Azure che hub eventi utilizzano le partizioni per abilitare la scalabilità orizzontale per l'elaborazione degli eventi. 

Una *partizione* è una sequenza ordinata di eventi contenuta in un hub. Il numero di partizioni viene impostato durante la fase di creazione dell'hub e non può essere modificato. 

Per le procedure consigliate per il partizionamento di hub eventi, rivedere [il numero di partizioni necessarie?](https://docs.microsoft.com/azure/event-hubs/event-hubs-faq#how-many-partitions-do-i-need)

> [!NOTE]
> Per la maggior parte degli hub Internet usati con Azure Time Series Insights sono necessarie solo quattro partizioni.

Sia che si stia creando un nuovo hub per l'ambiente Time Series Insights o usandone uno esistente, è necessario calcolare la velocità di inserimento per partizione per determinare se è entro i limiti di anteprima. 

Azure Time Series Insights anteprima dispone attualmente di un **limite generale per partizione di 0,5 Mbps**.

#### <a name="iot-hub-specific-considerations"></a>Considerazioni specifiche sull'hub

Quando un dispositivo viene creato nell'hub Internet, viene assegnato in modo permanente a una partizione. In questo modo, l'hub Internet è in grado di garantire l'ordine degli eventi (poiché l'assegnazione non cambia mai).

Un'assegnazione di partizione fissa ha un effetto anche sulle istanze Time Series Insights che inseriscono dati inviati dall'hub Internet a valle. Quando i messaggi provenienti da più dispositivi vengono inviati all'hub con lo stesso ID dispositivo gateway, possono arrivare nella stessa partizione contemporaneamente al superamento dei limiti di scala per partizione. 

**Effetto**:

* Se una singola partizione sperimenta un tasso di inserimento prolungato nel limite di anteprima, è possibile che Time Series Insights non sincronizza tutti i dati di telemetria del dispositivo prima del superamento del periodo di conservazione dei dati dell'hub. Di conseguenza, i dati inviati possono andare perduti se i limiti di inserimento vengono superati costantemente.

Per attenuare questa circostanza, è consigliabile attenersi alle procedure consigliate seguenti:

* Calcolare i tassi di inserimento per ambiente e per partizione prima di distribuire la soluzione.
* Verificare che il carico dei dispositivi dell'hub Internet sia bilanciato fino alla misura più lontana possibile.

> [!IMPORTANT]
> Per gli ambienti che usano l'hub Internet come origine eventi, calcolare la velocità di inserimento usando il numero di dispositivi hub usati per assicurarsi che la velocità scende al di sotto del limite di 0,5 MBps per partizione nell'anteprima.
> * Anche se più eventi arrivano simultaneamente, il limite di anteprima non verrà superato.

  ![Diagramma delle partizioni dell'hub Internet](media/concepts-ingress-overview/iot-hub-partiton-diagram.png)

Per altre informazioni sull'ottimizzazione della velocità effettiva e delle partizioni dell'hub, vedere le risorse seguenti:

* [Scalabilità dell'hub Internet](https://docs.microsoft.com/azure/iot-hub/iot-hub-scaling)
* [Scalabilità dell'hub eventi](https://docs.microsoft.com/azure/event-hubs/event-hubs-scalability#throughput-units)
* [Partizioni dell'hub eventi](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#partitions)

### <a name="data-storage"></a>Archiviazione dei dati

Quando si crea un ambiente con SKU *con pagamento in base* al consumo (PAYG) di anteprima, si creano due risorse Time Series Insights di Azure:

* Ambiente Azure Time Series Insights anteprima che può essere configurato per l'archiviazione a caldo.
* Un account BLOB di archiviazione di Azure per utilizzo generico V1 per l'archiviazione di dati a freddo.

I dati nell'archivio a caldo sono disponibili solo tramite [Query Time Series](./time-series-insights-update-tsq.md) e [Esplora Azure Time Series Insights Preview](./time-series-insights-update-explorer.md). 

Time Series Insights anteprima Salva i dati dell'archivio a freddo nell'archivio BLOB di Azure nel [formato di file parquet](#parquet-file-format-and-folder-structure). Time Series Insights anteprima gestisce i dati di archivio a freddo esclusivamente, ma è disponibile per la lettura diretta come file parquet standard.

> [!WARNING]
> Il proprietario dell'account di archiviazione BLOB di Azure in cui risiedono i dati dell'archivio freddo ha accesso completo a tutti i dati dell'account. Questo accesso include le autorizzazioni di scrittura ed eliminazione. Non modificare o eliminare i dati scritti da Time Series Insights Preview, perché ciò può causare la perdita di dati.

### <a name="data-availability"></a>Disponibilità dei dati

Azure Time Series Insights Visualizza in anteprima le partizioni e indicizza i dati per ottenere prestazioni ottimali delle query. I dati diventano disponibili per eseguire una query dopo l'indicizzazione. La quantità di dati da inserire può influire sulla disponibilità.

> [!IMPORTANT]
> Durante l'anteprima, è possibile che si verifichi un periodo di tempo massimo di 60 secondi prima che i dati diventino disponibili. Se si verifica una latenza significativa superiore a 60 secondi, inviare un ticket di supporto tramite la portale di Azure.

## <a name="azure-storage"></a>Archiviazione di Azure

Questa sezione descrive i dettagli di archiviazione di Azure rilevanti per Azure Time Series Insights Preview.

Per una descrizione completa dell'archiviazione BLOB di Azure, vedere [Introduzione ai BLOB di archiviazione](../storage/blobs/storage-blobs-introduction.md).

### <a name="your-storage-account"></a>L'account di archiviazione

Quando si crea un ambiente di Azure Time Series Insights anteprima PAYG, viene creato un account BLOB di archiviazione di Azure per utilizzo generico V1 come archivio a lungo termine.  

Azure Time Series Insights anteprima pubblica fino a due copie di ogni evento nell'account di archiviazione di Azure. Per la copia iniziale sono stati ordinati eventi in base al tempo di inserimento. Tale ordine di evento viene **sempre mantenuto** in modo che altri servizi possano accedere agli eventi senza sequenziare problemi. 

> [!NOTE]
> È anche possibile usare Spark, Hadoop e altri strumenti familiari per elaborare i file parquet non elaborati. 

Time Series Insights anteprima esegue anche il partizionamento dei file parquet da ottimizzare per la query Time Series Insights. Viene salvata anche questa copia di dati ripartizionata. 

Durante l'anteprima pubblica, i dati vengono archiviati per un periodo illimitato nell'account di archiviazione di Azure.

#### <a name="writing-and-editing-time-series-insights-blobs"></a>Scrittura e modifica di BLOB di Time Series Insights

Per garantire le prestazioni delle query e la disponibilità dei dati, non modificare o eliminare i BLOB creati da Time Series Insights anteprima.

#### <a name="accessing-and-exporting-data-from-time-series-insights-preview"></a>Accesso ed esportazione dei dati dalla versione di anteprima di Time Series Insights

Potrebbe essere necessario accedere ai dati visualizzati in Esplora Time Series Insights Preview da usare insieme ad altri servizi. Ad esempio, è possibile usare i dati per compilare un report in Power BI o per eseguire il training di un modello di apprendimento automatico usando Azure Machine Learning Studio. In alternativa, è possibile usare i dati per trasformare, visualizzare e modellare nel notebook di Jupyter.

È possibile accedere ai dati in tre modi:

* Dallo strumento di esplorazione dell'anteprima di Time Series Insights È possibile esportare i dati come file CSV dalla finestra di esplorazione. Per altre informazioni, vedere [Time Series Insights anteprima di Esplora risorse](./time-series-insights-update-explorer.md).
* Dall'API Time Series Insights anteprima usando la query Get Events. Per altre informazioni su questa API, vedere [Query Time Series](./time-series-insights-update-tsq.md).
* Direttamente da un account di archiviazione di Azure. È necessario l'accesso in lettura a qualsiasi account usato per accedere ai dati di Time Series Insights Preview. Per altre informazioni, vedere [gestire l'accesso alle risorse dell'account di archiviazione](../storage/blobs/storage-manage-access-to-resources.md).

#### <a name="data-deletion"></a>Eliminazione di dati

Non eliminare i file di anteprima Time Series Insights. Gestisci i dati correlati solo dall'interno di Time Series Insights Preview.

### <a name="parquet-file-format-and-folder-structure"></a>Formato di file parquet e struttura di cartelle

Parquet è un formato di file a colonne open source progettato per l'archiviazione e le prestazioni efficienti. Time Series Insights anteprima usa parquet per questi motivi. I dati vengono partizionati in base all'ID della serie temporale per le prestazioni delle query su larga scala.  

Per ulteriori informazioni sul tipo di file parquet, leggere la [documentazione parquet](https://parquet.apache.org/documentation/latest/).

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
* Ogni riga include le colonne degli ID di serie temporali definite al momento della creazione dell'ambiente Time Series Insights. Il nome della proprietà include il suffisso `_string`.
* Tutte le altre proprietà inviate come dati di telemetria vengono mappate ai nomi di colonna che terminano con `_string` (String), `_bool` (Boolean), `_datetime` (DateTime) o `_double` (Double), a seconda del tipo di proprietà.
* Questo schema di mapping si applica alla prima versione del formato di file, a cui viene fatto riferimento come **V = 1**. Con l'evolversi di questa funzionalità, il nome potrebbe essere incrementato.

## <a name="next-steps"></a>Passaggi successivi

- Leggere [le informazioni su come eseguire il formato JSON per l'ingresso e la query](./time-series-insights-update-how-to-shape-events.md).

- Scopri di più sulla nuova [modellazione dei dati](./time-series-insights-update-tsm.md).
