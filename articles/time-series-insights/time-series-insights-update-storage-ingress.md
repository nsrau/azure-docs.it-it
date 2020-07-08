---
title: Archiviazione e ingresso dei dati nella versione di anteprima - Azure Time Series Insights | Microsoft Docs
description: Informazioni sull'archiviazione e l'ingresso dei dati nell'anteprima di Azure Time Series Insights.
author: lyrana
ms.author: lyhughes
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 04/27/2020
ms.custom: seodec18
ms.openlocfilehash: d3bfb589ec4c152b136e8e1f432864b719c97d58
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85509320"
---
# <a name="data-storage-and-ingress-in-azure-time-series-insights-preview"></a>Archiviazione e ingresso dei dati nella versione di anteprima di Azure Time Series Insights

Questo articolo descrive gli aggiornamenti all'archiviazione e all'ingresso dei dati per l'anteprima di Azure Time Series Insights. Descrive la struttura di archiviazione sottostante, il formato di file e la proprietà ID serie temporale. Vengono descritti anche il processo di ingresso sottostante, le procedure consigliate e le limitazioni dell'anteprima attuale.

## <a name="data-ingress"></a>Ingresso dei dati

L'ambiente di Azure Time Series Insights contiene un *motore di inserimento* per raccogliere, elaborare e archiviare dati di serie temporali.

Quando si [pianifica l'ambiente](time-series-insights-update-plan.md) vanno fatte alcune considerazioni per garantire l'elaborazione di tutti i dati in ingresso, per ottenere una scalabilità elevata dei dati in ingresso e ridurre al minimo la *latenza di inserimento*, ovvero il tempo impiegato da Time Series Insights per leggere ed elaborare i dati dall'origine evento.

I criteri di ingresso dei dati dall'anteprima di Time Series Insights determinano la posizione di origine dei dati e il relativo formato.

### <a name="ingress-policies"></a>Criteri di ingresso

L'*ingresso dei dati* implica la modalità di invio dei dati a un ambiente di anteprima di Azure Time Series Insights.

La configurazione della chiave, la formattazione e le procedure consigliate sono riepilogate di seguito.

#### <a name="event-sources"></a>Origini eventi

L'anteprima di Azure Time Series Insights supporta le origini evento seguenti:

- [Hub IoT Azure](../iot-hub/about-iot-hub.md)
- [Hub eventi di Azure](../event-hubs/event-hubs-about.md)

L'anteprima di Azure Time Series Insights supporta un massimo di due origini evento per ogni istanza. Quando si connette un'origine evento, l'ambiente TSI leggerà tutti gli eventi attualmente archiviati nell'hub IoT o nell'hub eventi, a partire dall'evento meno recente.

> [!IMPORTANT]
>
> * È possibile che si verifichi una latenza iniziale elevata quando si collega un'origine evento all'ambiente di anteprima.
> La latenza dell'origine evento dipende dal numero di eventi attualmente presenti nell'hub IoT o nell'hub eventi.
> * La latenza elevata si ridurrà dopo l'inserimento iniziale dei dati di origine dell'evento. Inviare un ticket di supporto con il portale di Azure se si verifica una latenza elevata.

#### <a name="supported-data-format-and-types"></a>Formato e tipi di dati supportati

Azure Time Series Insights supporta i file JSON con codifica UTF-8 inviati dall'hub IoT o dagli Hub eventi di Azure. 

I tipi di dati supportati sono:

| Tipo di dati | Descrizione |
|---|---|
| **bool** | Tipo di dati con uno dei due stati: `true` o `false`. |
| **dateTime** | Rappresenta un istante di tempo, in genere espresso come data e ora del giorno. Espresso nel [formato](https://www.iso.org/iso-8601-date-and-time-format.html) ISO 8601. |
| **long** | Intero con segno a 64 bit  |
| **double** | Un formato a virgola mobile [IEEE 754](https://ieeexplore.ieee.org/document/8766229) a 64 bit a precisione doppia. |
| **string** | Valori di testo, costituiti da caratteri Unicode.          |

> [!IMPORTANT]
>
> * L'ambiente TSI è fortemente tipizzato. Se i dispositivi o i tag inviano sia dati integrali che non integrali, i valori delle proprietà del dispositivo verranno archiviati in due colonne doppie e lunghe separate e la [funzione COALESCE ()](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax) deve essere utilizzata quando si effettuano chiamate API e si definiscono le espressioni variabili del modello Time Series.

#### <a name="objects-and-arrays"></a>Oggetti e matrici

È possibile inviare tipi complessi quali oggetti e matrici, come parte del payload dell'evento, ma i dati vengono resi flat durante l'archiviazione.

Le informazioni dettagliate che descrivono come definire gli eventi JSON, inviare tipi di dati complessi e come rendere flat gli oggetti annidati sono disponibili in [Come definire i dati JSON per l'ingresso e le query](./time-series-insights-update-how-to-shape-events.md) per facilitare la pianificazione e l'ottimizzazione.

### <a name="ingress-best-practices"></a>Procedure consigliate per l'ingresso dei dati

Si consiglia di impiegare le seguenti procedure consigliate:

* Configurare Azure Time Series Insights e qualsiasi hub IoT o hub eventi nella stessa area per ridurre la potenziale latenza.

* [Pianificare le esigenze di scalabilità](time-series-insights-update-plan.md) calcolando la velocità di inserimento prevista e verificando che rientri tra le velocità supportate elencate di seguito.

* Informazioni su come ottimizzare e definire i dati JSON, nonché le limitazioni attuali in anteprima, leggendo [Come definire i dati JSON per l'ingresso e le query](./time-series-insights-update-how-to-shape-events.md).

* Usare l'inserimento streaming solo per i dati near real-time e recenti. I dati cronologici di streaming non sono supportati.

#### <a name="historical-data-ingestion"></a>Inserimento dei dati cronologici

L'uso della pipeline di streaming per importare i dati cronologici non è attualmente supportato nell'anteprima di Azure Time Series Insights. Se è necessario importare i dati passati nell'ambiente in uso, seguire le linee guida indicate sotto:

* Non trasmettere dati in tempo reale e cronologici in parallelo. L'inserimento di dati non in ordine comporterà un calo delle prestazioni delle query.
* Inserire i dati cronologici rispettando la sequenza temporale per ottenere prestazioni ottimali.
* Rispettare i limiti di velocità effettiva di inserimento indicati sotto.
* Disabilitare l'archivio ad accesso frequente se i dati sono precedenti al periodo di conservazione dell'archivio ad accesso frequente.

### <a name="ingress-scale-and-preview-limitations"></a>Limitazioni di scalabilità e anteprima dell'ingresso

Qui sono descritte le limitazioni in ingresso dell'anteprima di Azure Time Series Insights.

> [!TIP]
> Leggere [Pianificare l'ambiente di anteprima](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-plan#review-preview-limits) per un elenco completo di tutti i limiti dell'anteprima.

#### <a name="per-environment-limitations"></a>Limitazioni per ambiente

In generale, la velocità di ingresso viene visualizzata come il fattore che determina il numero di dispositivi all'interno dell'organizzazione, la frequenza di emissione degli eventi e le dimensioni di ogni evento:

*  **Numero di dispositivi** × **Frequenza di emissione degli eventi** × **Dimensioni di ogni evento**.

Per impostazione predefinita, l'anteprima di Time Series Insights può inserire i dati in ingresso a una velocità che può arrivare **fino a 1 MB al secondo (MBps) per ogni ambiente di Time Series Insights**. [Per ogni partizione dell'hub](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-storage-ingress#hub-partitions-and-per-partition-limits) ci sono altre limitazioni.

> [!TIP]
>
> * È possibile richiedere una velocità di inserimento supportata dall'ambiente fino a 16 MBps.
> * Contattare Microsoft se è necessaria una velocità effettiva maggiore inviando un ticket di supporto tramite il portale di Azure.
 
* **Esempio 1:**

    In Contoso Shipping ci sono 100.000 dispositivi che emettono un evento tre volte al minuto. Le dimensioni di un evento sono pari a 200 byte. Viene usato un hub IoT con quattro partizioni come origine evento di Time Series Insights.

    * La velocità di inserimento per l'ambiente di Time Series Insights sarà: **100.000 dispositivi * 200 byte/evento * (3/60 evento/sec) = 1 MBps**.
    * La velocità di inserimento per partizione sarà 0,25 MBps.
    * La velocità di inserimento di Contoso Shipping è compresa nel limite della scalabilità di anteprima.

* **Esempio 2:**

    In Contoso Fleet Analytics ci sono 60.000 dispositivi che emettono un evento ogni secondo. Usano un hub eventi con un numero di partizioni pari a 4 come origine evento di Time Series Insights. Le dimensioni di un evento sono pari a 200 byte.

    * La velocità di inserimento dell'ambiente sarà: **60.000 dispositivi * 200 byte/evento * 1 evento/sec = 12 MBps**.
    * La velocità per partizione sarà 3 MBps.
    * La velocità di inserimento di Contoso Fleet Analytics supera i limiti dell'ambiente e della partizione. È possibile inviare una richiesta a Time Series Insights tramite il portale di Azure per aumentare la velocità di inserimento dell'ambiente e creare un hub eventi con più partizioni che rientrino nei limiti dell'anteprima.

#### <a name="hub-partitions-and-per-partition-limits"></a>Partizioni dell'hub e limiti per partizione

Quando si pianifica l'ambiente di Time Series Insights, è importante prendere in considerazione la configurazione delle origini eventi che si connetteranno a Time Series Insights. Sia l'hub IoT che l'hub eventi di Azure usano le partizioni per abilitare la scalabilità orizzontale per l'elaborazione degli eventi. 

Una *partizione* è una sequenza ordinata di eventi contenuta in un hub. Il numero di partizioni viene impostato durante la fase di creazione dell'hub e non può essere modificato.

Per le procedure consigliate di partizionamento degli hub eventi, vedere [Quante partizioni sono necessarie?](https://docs.microsoft.com/azure/event-hubs/event-hubs-faq#how-many-partitions-do-i-need)

> [!NOTE]
> Per la maggior parte degli hub IoT usati con Azure Time Series Insights sono necessarie solo quattro partizioni.

Che si stia creando un nuovo hub per l'ambiente di Time Series Insights o che si stia usando un hub esistente, è necessario calcolare la velocità di inserimento per partizione per verificare che rientri nei limiti dell'anteprima. 

L'anteprima di Azure Time Series Insights dispone attualmente di un **limite per partizione generale di 0,5 MBps**.

#### <a name="iot-hub-specific-considerations"></a>Considerazioni specifiche sull'hub IoT

Quando un dispositivo viene creato nell'hub IoT, viene assegnato in modo permanente a una partizione. In questo modo, l'hub IoT è in grado di garantire l'ordine degli eventi, poiché l'assegnazione non cambia mai.

Un'assegnazione della partizione fissa influisce anche sulle istanze di Time Series Insights che inseriscono i dati inviati dall'hub IoT downstream. Quando i messaggi provenienti da più dispositivi vengono inoltrati all'hub con lo stesso ID dispositivo del gateway, possono arrivare nella stessa partizione contemporaneamente e possono superare potenzialmente i limiti di scalabilità per partizione.

**Impatto**:

* Se una sola partizione ha una velocità di inserimento continua che supera il limite dell'anteprima, è possibile che Time Series Insights non sincronizzi tutti i dati di telemetria del dispositivo prima che il periodo di conservazione dei dati dell'hub IoT venga superato. Di conseguenza, i dati inviati possono andare perduti se i limiti di inserimento vengono superati in modo costante.

Per attenuare questa circostanza, è consigliabile attenersi alle procedure consigliate seguenti:

* Calcolare le velocità di inserimento per ambiente e per partizione prima di distribuire la soluzione.
* Verificare che il carico dei dispositivi dell'hub IoT sia bilanciato il più possibile.

> [!IMPORTANT]
> Per gli ambienti che usano l'hub IoT come origine eventi, calcolare la velocità di inserimento usando il numero di dispositivi hub in uso per assicurarsi che la velocità scenda al di sotto del limite di 0,5 MBps per partizione nell'anteprima.
>
> * Anche se più eventi arrivano simultaneamente, il limite dell'anteprima non verrà superato.

  ![Diagramma delle partizioni dell'hub IoT](media/concepts-ingress-overview/iot-hub-partiton-diagram.png)

Per altre informazioni sull'ottimizzazione della velocità effettiva e delle partizioni dell'hub, vedere le risorse seguenti:

* [Scalabilità dell'hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-scaling)
* [Scalabilità dell'hub eventi](https://docs.microsoft.com/azure/event-hubs/event-hubs-scalability#throughput-units)
* [Partizioni dell'hub eventi](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#partitions)

### <a name="data-storage"></a>Archiviazione dei dati

Quando si crea un ambiente SKU *con pagamento in base al consumo* dell'anteprima di Time Series Insights, si creano due risorse di Azure:

* Un ambiente di anteprima di Azure Time Series Insights che può essere configurato per l'archiviazione dei dati ad accesso frequente.
* Un account BLOB di Archiviazione di Azure per utilizzo generico v1 per l'archiviazione di dati ad accesso sporadico.

I dati nell'archivio ad accesso frequente sono disponibili solo tramite le [query di Time Series](./time-series-insights-update-tsq.md) e lo [strumento di esplorazione dell'anteprima di Azure Time Series Insights](./time-series-insights-update-explorer.md). L'archivio ad accesso frequente conterrà i dati recenti nel [periodo di conservazione](./time-series-insights-update-plan.md#the-preview-environment) selezionato durante la creazione dell'ambiente Time Series Insights.

L'anteprima di Time Series Insights salva i dati dell'archivio ad accesso sporadico nell'Archiviazione BLOB di Azure nel [formato di file parquet](#parquet-file-format-and-folder-structure). L'anteprima di Time Series Insights gestisce esclusivamente i dati di archivio ad accesso sporadico, ma consente di leggerli direttamente come file parquet standard.

> [!WARNING]
> In qualità di proprietario dell'account di Archiviazione BLOB di Azure in cui si trovano i dati dell'archivio ad accesso sporadico, l'utente ha completo accesso a tutti i dati dell'account. Questo accesso include le autorizzazioni di scrittura ed eliminazione. Non modificare o eliminare i dati scritti dall'anteprima di Time Series Insights, perché ciò può causare una perdita di dati.

### <a name="data-availability"></a>Disponibilità dei dati

L'anteprima di Azure Time Series Insights suddivide in partizioni e indicizza i dati per ottenere prestazioni ottimali delle query. Dopo essere stati indicizzati, i dati sono disponibili per le query sia dagli archivi con accesso frequente (se abilitati) che dagli archivi con accesso sporadico. La quantità di dati inseriti può influire sulla disponibilità.

> [!IMPORTANT]
> Durante l'anteprima, è possibile che siano necessari fino a 60 secondi prima che i dati diventino disponibili. Se si verifica una latenza significativa superiore a 60 secondi, inviare un ticket di supporto tramite il portale di Azure.

## <a name="azure-storage"></a>Archiviazione di Azure

Questa sezione descrive i dettagli di Archiviazione di Azure rilevanti per l'anteprima di Azure Time Series Insights.

Per una descrizione completa dell'Archiviazione BLOB di Azure, vedere l'[introduzione ai BLOB di archiviazione ](../storage/blobs/storage-blobs-introduction.md).

### <a name="your-storage-account"></a>Account di archiviazione

Quando si crea un ambiente con pagamento in base al consumo di anteprima di Azure Time Series Insights, viene creato un account BLOB di Archiviazione di Azure per utilizzo generico v1 come archivio ad accesso sporadico a lungo termine.  

L'anteprima di Azure Time Series Insights conserva fino a due copie di ogni evento nell'account di Archiviazione di Azure. Una copia archivia gli eventi ordinati in base al momento dell'inserimento, consentendo sempre l'accesso agli eventi in una sequenza temporale ordinata. Nel corso del tempo, l'anteprima di Time Series Insights crea anche una copia ripartizionata dei dati da ottimizzare per query di Time Series Insights performanti.

In fase di anteprima pubblica, i dati vengono archiviati a tempo indeterminato nell'account di Archiviazione di Azure.

#### <a name="writing-and-editing-time-series-insights-blobs"></a>Scrittura e modifica di BLOB di Time Series Insights

Per garantire le prestazioni delle query e la disponibilità dei dati, non modificare o eliminare i BLOB creati dall'anteprima di Time Series Insights.

#### <a name="accessing-time-series-insights-preview-cold-store-data"></a>Accesso ai dati dell'archivio ad accesso sporadico dell'anteprima di Time Series Insights

Oltre ad accedere ai dati dallo [strumento di esplorazione dell'anteprima di Time Series Insights](./time-series-insights-update-explorer.md) e dalle [query di Time Series](./time-series-insights-update-tsq.md), è anche possibile accedere ai dati direttamente dai file parquet archiviati nell'archivio ad accesso sporadico. Ad esempio, è possibile leggere, trasformare e pulire i dati in un notebook di Jupyter, e poi usarli per eseguire il training del modello di Azure Machine Learning nello stesso flusso di lavoro Spark.

Per accedere ai dati direttamente dall'account di Archiviazione di Azure, è necessario l'accesso in lettura all'account usato per archiviare i dati dell'anteprima di Time Series Insights. È quindi possibile leggere i dati selezionati in base all'ora di creazione del file parquet presente nella cartella `PT=Time` descritta di seguito nella sezione dedicata al [formato di file parquet](#parquet-file-format-and-folder-structure).  Per altre informazioni sull'abilitazione dell'accesso in lettura all'account di archiviazione, vedere [Gestire l'accesso alle risorse dell'account di archiviazione](../storage/blobs/storage-manage-access-to-resources.md).

#### <a name="data-deletion"></a>Eliminazione di dati

Non eliminare i file dell'anteprima di Time Series Insights. Gestire i dati correlati solo dall'interno dell'anteprima di Time Series Insights.

### <a name="parquet-file-format-and-folder-structure"></a>Formato di file parquet e struttura della cartelle

Parquet è un formato di file open source a colonne progettato per archiviazioni e prestazioni efficienti. L'anteprima di Time Series Insights usa parquet per abilitare le prestazioni delle query basate su ID di Time Series su larga scala.  

Per altre informazioni sul tipo di file parquet, vedere la [documentazione relativa a parquet](https://parquet.apache.org/documentation/latest/).

L'anteprima di Time Series Insights archivia le copie dei dati come segue:

* La prima copia, quella iniziale, viene partizionata in base all'ora di inserimento e archivia i dati approssimativamente in ordine di arrivo. Questi dati si trovano nella cartella `PT=Time`:

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* La seconda copia ripartizionata viene raggruppata in base agli ID serie temporale e si trova nella cartella `PT=TsId`:

  `V=1/PT=TsId/<TSI_INTERNAL_STRUCTURE>/<TSI_INTERNAL_NAME>.parquet`

Il timestamp nei nomi di BLOB nella `PT=Time` cartella corrisponde all'ora di arrivo dei dati a TSI (non al timestamp degli eventi).

I dati nella cartella `PT=TsId` verranno ottimizzati per la query nel tempo e non sono statici. Durante il partizionamento, gli stessi eventi possono essere presenti in più BLOB. Inoltre, il nome dei BLOB potrebbe cambiare in futuro.

> [!NOTE]
>
> * `<YYYY>` esegue il mapping a una rappresentazione dell'anno a 4 cifre.
> * `<MM>` esegue il mapping a una rappresentazione del mese a 2 cifre.
> * `<YYYYMMDDHHMMSSfff>` esegue il mapping a una rappresentazione timestamp dell'anno a quattro cifre (`YYYY`), del mese a due cifre (`MM`), del giorno a due cifre (`DD`), dell'ora a due cifre (`HH`), dei minuti a due cifre (`MM`), dei secondi a due cifre (`SS`) e dei millisecondi a tre cifre (`fff`).

Gli eventi dell'anteprima di Time Series Insights vengono mappati al contenuto del file parquet come segue:

* Viene eseguito il mapping di ogni evento a una singola riga.
* Ogni riga include la colonna **timestamp** con un timestamp dell'evento. La proprietà timestamp non ha mai un valore Null. L'impostazione predefinita è l'**ora di accodamento dell'evento**, se la proprietà timestamp non viene specificata nell'origine evento. Il timestamp archiviato è sempre in formato UTC.
* Ogni riga include una o più colonne ID serie temporale definite al momento della creazione dell'ambiente Time Series Insights. Il nome della proprietà TSID include il suffisso `_string`.
* Tutte le altre proprietà inviate come dati di telemetria sono mappate alle nomi di colonna che terminano con `_string` (stringa), `_bool` (booleano), `_datetime` (datetime) e `_double` (doppio), a seconda del tipo di proprietà.
* Questo schema di mapping si applica alla prima versione del formato di file, a cui si fa riferimento come **V=1** ed è archiviato nella cartella di base con lo stesso nome. Con l'evolversi di questa funzionalità, lo schema di mapping potrebbe cambiare e il numero presente nel nome di riferimento potrebbe aumentare.

## <a name="next-steps"></a>Passaggi successivi

- Leggere [Come definire i dati JSON per l'ingresso e le query](./time-series-insights-update-how-to-shape-events.md).

- Leggere la nuova [modellazione di dati](./time-series-insights-update-tsm.md).
