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
ms.date: 04/27/2020
ms.custom: seodec18
ms.openlocfilehash: e3af10e5e9b56b537fedf0af7ffa7ddb37030c73
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82189182"
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

Azure Time Series Insights anteprima supporta un massimo di due origini evento per ogni istanza. Quando si connette un'origine evento, l'ambiente di STI leggerà tutti gli eventi attualmente archiviati nell'hub eventi o in Internet, a partire dall'evento meno recente.

> [!IMPORTANT]
>
> * È possibile che si verifichi una latenza iniziale elevata quando si connette un'origine evento all'ambiente di anteprima.
> La latenza dell'origine evento dipende dal numero di eventi attualmente presenti nell'hub o nell'hub eventi.
> * Una latenza elevata sarà secondaria dopo l'inserimento iniziale dei dati dell'origine evento. Inviare un ticket di supporto tramite il portale di Azure se si verifica una latenza elevata.

#### <a name="supported-data-format-and-types"></a>Tipi e formati di dati supportati

Azure Time Series Insights supporta JSON con codifica UTF-8 inviati dall'hub degli eventi di Azure o dagli hub eventi di Azure. 

I tipi di dati supportati sono:

| Tipo di dati | Descrizione |
|---|---|
| **bool** | Tipo di dati con uno dei due stati seguenti `true` : `false`o. |
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

Per impostazione predefinita, Time Series Insights anteprima può inserire i dati in ingresso a una velocità di un **massimo di 1 MB al secondo (Mbps) per ogni ambiente Time Series Insights**. Esistono limitazioni aggiuntive [per ogni partizione dell'hub](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-storage-ingress#hub-partitions-and-per-partition-limits).

> [!TIP]
>
> * Il supporto dell'ambiente per l'inserimento di velocità fino a 16 MBps può essere fornito dalla richiesta.
> * Contattaci se è necessaria una velocità effettiva maggiore inviando un ticket di supporto tramite portale di Azure.
 
* **Esempio 1:**

    Contoso Shipping ha 100.000 dispositivi che emettono un evento tre volte al minuto. Le dimensioni di un evento sono pari a 200 byte. Usano un hub Internet delle cose con quattro partizioni come origine evento Time Series Insights.

    * La velocità di inserimento per l'ambiente di Time Series Insights sarà: **100.000 dispositivi * 200 byte/evento * (3/60 evento/sec) = 1 Mbps**.
    * La velocità di inserimento per partizione è 0,25 MBps.
    * Il tasso di inserimento di Contoso Shipping sarà entro il limite di anteprima della scalabilità.

* **Esempio 2:**

    Contoso Fleet Analytics ha 60.000 dispositivi che emettono un evento ogni secondo. Usano un hub eventi con un numero di partizioni pari a 4 come origine evento Time Series Insights. Le dimensioni di un evento sono pari a 200 byte.

    * La velocità di inserimento dell'ambiente è: **60.000 dispositivi * 200 byte/evento * 1 evento/sec = 12 Mbps**.
    * Il tasso per partizione è di 3 MBps.
    * La velocità di inserimento di Contoso Fleet Analytics è relativa ai limiti dell'ambiente e della partizione. Possono inviare una richiesta di Time Series Insights tramite portale di Azure per aumentare la velocità di inserimento per il proprio ambiente e creare un hub eventi con più partizioni entro i limiti di anteprima.

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
>
> * Anche se più eventi arrivano simultaneamente, il limite di anteprima non verrà superato.

  ![Diagramma delle partizioni dell'hub Internet](media/concepts-ingress-overview/iot-hub-partiton-diagram.png)

Per altre informazioni sull'ottimizzazione della velocità effettiva e delle partizioni dell'hub, vedere le risorse seguenti:

* [Scalabilità dell'hub Internet](https://docs.microsoft.com/azure/iot-hub/iot-hub-scaling)
* [Scalabilità dell'hub eventi](https://docs.microsoft.com/azure/event-hubs/event-hubs-scalability#throughput-units)
* [Partizioni dell'hub eventi](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#partitions)

### <a name="data-storage"></a>Archiviazione dei dati

Quando si crea un ambiente con SKU *con pagamento in base* al consumo (PAYG) di anteprima, si creano due risorse Time Series Insights di Azure:

* Ambiente Azure Time Series Insights anteprima che può essere configurato per l'archiviazione dei dati a caldo.
* Un account BLOB di archiviazione di Azure per utilizzo generico V1 per l'archiviazione di dati a freddo.

I dati nell'archivio a caldo sono disponibili solo tramite [Query Time Series](./time-series-insights-update-tsq.md) e [Esplora Azure Time Series Insights Preview](./time-series-insights-update-explorer.md). Il negozio caldo conterrà i dati recenti entro il [periodo di memorizzazione](./time-series-insights-update-plan.md#the-preview-environment) selezionato durante la creazione dell'ambiente Time Series Insights.

Time Series Insights anteprima Salva i dati dell'archivio a freddo nell'archivio BLOB di Azure nel [formato di file parquet](#parquet-file-format-and-folder-structure). Time Series Insights anteprima gestisce i dati di archivio a freddo esclusivamente, ma è disponibile per la lettura diretta come file parquet standard.

> [!WARNING]
> Il proprietario dell'account di archiviazione BLOB di Azure in cui risiedono i dati dell'archivio freddo ha accesso completo a tutti i dati dell'account. Questo accesso include le autorizzazioni di scrittura ed eliminazione. Non modificare o eliminare i dati scritti da Time Series Insights Preview, perché ciò può causare la perdita di dati.

### <a name="data-availability"></a>Disponibilità dei dati

Azure Time Series Insights Visualizza in anteprima le partizioni e indicizza i dati per ottenere prestazioni ottimali delle query. I dati diventeranno disponibili per eseguire una query sia dal sistema caldo (se abilitato) che dall'archivio a freddo dopo che è stato indicizzato. La quantità di dati da inserire può influire sulla disponibilità.

> [!IMPORTANT]
> Durante l'anteprima, è possibile che si verifichi un periodo di tempo massimo di 60 secondi prima che i dati diventino disponibili. Se si verifica una latenza significativa superiore a 60 secondi, inviare un ticket di supporto tramite la portale di Azure.

## <a name="azure-storage"></a>Archiviazione di Azure

Questa sezione descrive i dettagli di archiviazione di Azure rilevanti per Azure Time Series Insights Preview.

Per una descrizione completa dell'archiviazione BLOB di Azure, vedere [Introduzione ai BLOB di archiviazione](../storage/blobs/storage-blobs-introduction.md).

### <a name="your-storage-account"></a>L'account di archiviazione

Quando si crea un ambiente di Azure Time Series Insights anteprima PAYG, viene creato un account BLOB di archiviazione di Azure per utilizzo generico V1 come archivio a lungo termine.  

Azure Time Series Insights anteprima conserva fino a due copie di ogni evento nell'account di archiviazione di Azure. Una copia archivia gli eventi ordinati in base al tempo di inserimento, consentendo sempre l'accesso agli eventi in una sequenza temporale ordinata. Nel corso del tempo, Time Series Insights anteprima crea anche una copia ripartizionata dei dati per ottimizzare l'esecuzione di query Time Series Insights.

Durante l'anteprima pubblica, i dati vengono archiviati per un periodo illimitato nell'account di archiviazione di Azure.

#### <a name="writing-and-editing-time-series-insights-blobs"></a>Scrittura e modifica di BLOB di Time Series Insights

Per garantire le prestazioni delle query e la disponibilità dei dati, non modificare o eliminare i BLOB creati da Time Series Insights anteprima.

#### <a name="accessing-time-series-insights-preview-cold-store-data"></a>Accesso ai dati dell'archivio freddo di Time Series Insights Preview

Oltre ad accedere ai dati dalla query [Time Series Insights Preview Explorer](./time-series-insights-update-explorer.md) e [Time Series](./time-series-insights-update-tsq.md), è anche possibile accedere ai dati direttamente dai file parquet archiviati nell'archivio a freddo. Ad esempio, è possibile leggere, trasformare e pulire i dati in un notebook di Jupyter, quindi usarli per eseguire il training del modello di Azure Machine Learning nello stesso flusso di lavoro Spark.

Per accedere ai dati direttamente dall'account di archiviazione di Azure, è necessario l'accesso in lettura all'account usato per archiviare i dati di Time Series Insights Preview. È quindi possibile leggere i dati selezionati in base all'ora di creazione del file parquet presente nella `PT=Time` cartella descritta di seguito nella sezione [formato file parquet](#parquet-file-format-and-folder-structure) .  Per altre informazioni sull'abilitazione dell'accesso in lettura all'account di archiviazione, vedere [gestire l'accesso alle risorse dell'account di archiviazione](../storage/blobs/storage-manage-access-to-resources.md).

#### <a name="data-deletion"></a>Eliminazione di dati

Non eliminare i file di anteprima Time Series Insights. Gestisci i dati correlati solo dall'interno di Time Series Insights Preview.

### <a name="parquet-file-format-and-folder-structure"></a>Formato di file parquet e struttura di cartelle

Parquet è un formato di file a colonne open source progettato per l'archiviazione e le prestazioni efficienti. Time Series Insights anteprima usa parquet per abilitare le prestazioni delle query basate su ID della serie temporale su larga scala.  

Per ulteriori informazioni sul tipo di file parquet, leggere la [documentazione parquet](https://parquet.apache.org/documentation/latest/).

Time Series Insights anteprima archivia le copie dei dati come segue:

* La prima copia iniziale viene partizionata in base al tempo di inserimento e archivia approssimativamente i dati in ordine di arrivo. Questi dati si trovano nella `PT=Time` cartella:

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* La seconda copia ripartizionata viene raggruppata in base agli ID di serie temporali e si trova `PT=TsId` nella cartella:

  `V=1/PT=TsId/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

In entrambi i casi, la proprietà Time del file parquet corrisponde al tempo di creazione del BLOB. I dati nella `PT=Time` cartella vengono conservati senza modifiche dopo la scrittura nel file. I dati nella `PT=TsId` cartella verranno ottimizzati per la query nel tempo e non sono statici.

> [!NOTE]
>
> * `<YYYY>`esegue il mapping a una rappresentazione dell'anno a quattro cifre.
> * `<MM>`esegue il mapping a una rappresentazione mensile a due cifre.
> * `<YYYYMMDDHHMMSSfff>`esegue il mapping a una rappresentazione timestamp con anno a quattro cifre (`YYYY`), mese a due cifre (`MM`), giorno a due cifre (`DD`), ora a due cifre (`HH`), minuti a due cifre (`MM`), secondi a due cifre (`SS`) e millisecondi a tre cifre (`fff`).

Per gli eventi di anteprima Time Series Insights viene eseguito il mapping al contenuto del file parquet come segue:

* Viene eseguito il mapping di ogni evento a una singola riga.
* Ogni riga include la colonna **timestamp** con un timestamp dell'evento. La proprietà timestamp non è mai null. Il valore predefinito è l' **ora di accodamento dell'evento** se la proprietà timestamp non è specificata nell'origine evento. Il timestamp archiviato è sempre in formato UTC.
* Ogni riga include le colonne IDST (Time Series ID) definite al momento della creazione dell'ambiente Time Series Insights. Il nome della proprietà IDST include `_string` il suffisso.
* Tutte le altre proprietà inviate come dati di telemetria vengono mappate ai nomi `_string` di colonna che terminano con (String), `_bool` (Boolean), `_datetime` (DateTime) o `_double` (Double), a seconda del tipo di proprietà.
* Questo schema di mapping si applica alla prima versione del formato di file, a cui si fa riferimento come **V = 1** e viene archiviato nella cartella di base con lo stesso nome. Con l'evolversi di questa funzionalità, lo schema di mapping potrebbe cambiare e il nome di riferimento viene incrementato.

## <a name="next-steps"></a>Passaggi successivi

- Leggere [le informazioni su come eseguire il formato JSON per l'ingresso e la query](./time-series-insights-update-how-to-shape-events.md).

- Scopri di più sulla nuova [modellazione dei dati](./time-series-insights-update-tsm.md).
