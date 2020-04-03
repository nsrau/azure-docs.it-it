---
title: Archiviazione e ingresso dei dati in Anteprima - Informazioni dettagliate sulle serie temporali di Azure Documenti Microsoft
description: Informazioni sull'archiviazione dei dati e sull'ingresso in Azure Time Series Insights Preview.Learn about data storage and ingress in Azure Time Series Insights Preview.
author: lyrana
ms.author: lyhughes
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/10/2020
ms.custom: seodec18
ms.openlocfilehash: 8987cbe6860422ff92119a9f3b13a0a365e6d1a4
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618329"
---
# <a name="data-storage-and-ingress-in-azure-time-series-insights-preview"></a>Archiviazione e ingresso dei dati nella versione di anteprima di Azure Time Series Insights

Questo articolo descrive gli aggiornamenti all'archiviazione e all'ingresso dei dati per Azure Time Series Insights Preview.This article describes updates to data storage and ingress for Azure Time Series Insights Preview. Descrive la struttura di archiviazione sottostante, il formato di file e la proprietà ID serie temporali. Vengono descritti anche il processo di ingresso sottostante, le procedure consigliate e le limitazioni correnti dell'anteprima.

## <a name="data-ingress"></a>Ingresso dati

L'ambiente Azure Time Series Insights contiene un motore di *inserimento* per raccogliere, elaborare e archiviare i dati delle serie temporali. 

Esistono alcune considerazioni da tenere presenti per garantire che tutti i dati in ingresso vengano elaborati, per ottenere un'elevata scala in ingresso e ridurre al minimo la latenza di *inserimento* (il tempo impiegato da Time Series Insights per leggere ed elaborare i dati dall'origine evento) durante la [pianificazione dell'ambiente.](time-series-insights-update-plan.md)

Time Series Insights I criteri di ingresso dell'anteprima determinano la provenietta dei dati e il formato che i dati devono avere.

### <a name="ingress-policies"></a>Criteri di ingresso

*L'ingresso dei dati* implica il modo in cui i dati vengono inviati a un ambiente Azure Time Series Insights Preview.Data ingress involves how data is sent to an Azure Time Series Insights Preview environment. 

La configurazione, la formattazione e le procedure consigliate sono riepilogate di seguito.

#### <a name="event-sources"></a>Origini eventi

Azure Time Series Insights Preview supporta le origini eventi seguenti:Azure Time Series Insights Preview supports the following event sources:

- [Hub IoT Azure](../iot-hub/about-iot-hub.md)
- [Hub eventi di Azure](../event-hubs/event-hubs-about.md)

Azure Time Series Insights Preview supporta un massimo di due origini eventi per istanza.

> [!IMPORTANT] 
> * È possibile che si verifichi un'elevata latenza iniziale quando si associa un'origine eventi all'ambiente di anteprima. 
> La latenza dell'origine eventi dipende dal numero di eventi attualmente presenti nell'hub IoT o nell'hub eventi.
> * L'elevata latenza diminuirà dopo l'inserimento dei dati dell'origine dell'evento. Inviare un ticket di supporto tramite il portale di Azure se si verifica una latenza elevata in corso.

#### <a name="supported-data-format-and-types"></a>Tipi e formati di dati supportati

Azure Time Series Insights supports UTF-8 encoded JSON sent from Azure IoT Hub or Azure Event Hubs. 

I tipi di dati supportati sono:

| Tipo di dati | Descrizione |
|---|---|
| **bool** | Un tipo di dati con `true` `false`uno dei due stati seguenti: o . |
| **Datetime** | Rappresenta un istante di tempo, in genere espresso come data e ora del giorno. Espresso in formato [ISO 8601.](https://www.iso.org/iso-8601-date-and-time-format.html) |
| **double** | Un punto mobile [IEEE 754 a](https://ieeexplore.ieee.org/document/8766229) 64 bit a precisione doppia. |
| **string** | Valori di testo, costituiti da caratteri Unicode.          |

#### <a name="objects-and-arrays"></a>Oggetti e matrici

È possibile inviare tipi complessi, ad esempio oggetti e matrici come parte del payload dell'evento, ma i dati verranno sottoposti a un processo di appiattimento quando archiviati. 

Informazioni dettagliate che descrivono come modellare gli eventi JSON, inviare tipi complessi e appiattire gli oggetti nidificati sono disponibili in [Come modellare JSON per l'ingresso e](./time-series-insights-update-how-to-shape-events.md) la query per facilitare la pianificazione e l'ottimizzazione.

### <a name="ingress-best-practices"></a>Procedure consigliate per l'ingressoIngress best practices

Si consiglia di utilizzare le seguenti procedure consigliate:

* Configurare Azure Time Series Insights e qualsiasi hub IoT o hub eventi nella stessa area per ridurre la latenza potenziale.

* [Pianificare le esigenze](time-series-insights-update-plan.md) di scalabilità calcolando il tasso di inserimento previsto e verificando che rientri nella tariffa indicata di seguito.

* Comprendere come ottimizzare e modellare i dati JSON, nonché le limitazioni correnti nell'anteprima, leggendo [come modellare JSON per l'ingresso e](./time-series-insights-update-how-to-shape-events.md)la query.

### <a name="ingress-scale-and-preview-limitations"></a>Limitazioni della scala in ingresso e dell'anteprima 

Le limitazioni in ingresso dell'anteprima di Azure Time Series Insights sono descritte di seguito.

> [!TIP]
> Leggere [Pianificare l'ambiente di anteprima](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-plan#review-preview-limits) per un elenco completo di tutti i limiti di anteprima.

#### <a name="per-environment-limitations"></a>Limitazioni per ambiente

In generale, le frequenze in ingresso sono considerate come il fattore del numero di dispositivi presenti nell'organizzazione, della frequenza di emissione degli eventi e delle dimensioni di ogni evento:

*  **Numero di dispositivi** - Frequenza di **emissione dell'evento** - **Dimensione di ogni evento**.

Per impostazione predefinita, l'anteprima di Time Series Insights può ingerire i dati in ingresso con una velocità massima di **1 megabyte al secondo (MBps) per ogni ambiente Time Series Insights.** Esistono limitazioni aggiuntive [per ogni partizione hub.](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-storage-ingress#hub-partitions-and-per-partition-limits)

> [!TIP] 
> * Il supporto ambientale per l'inserimento di velocità fino a 16 MBps può essere fornito su richiesta.
> * Contattarci se è necessaria una velocità effettiva più elevata inviando un ticket di supporto tramite il portale di Azure.Contact us if you require higher throughput by submitting a support ticket through Azure portal.
 
* **Esempio 1:**

    Contoso Shipping dispone di 100.000 dispositivi che generano un evento tre volte al minuto. La dimensione di un evento è di 200 byte. Usano un hub Iot con quattro partizioni come origine eventi Time Series Insights.

    * La frequenza di inserimento per l'ambiente Time Series Insights sarebbe: **100.000 dispositivi, 200 byte/evento ( 3/60 eventi/sec) , 1 MBps**.
    * La velocità di inserimento per partizione sarebbe 0,25 MBps.The ingestion rate per partition would be 0.25 MBps.
    * La tariffa di inserimento di Contoso Shipping rientra nel limite di apertura della scalabilità.

* **Esempio 2:**

    Contoso Fleet Analytics dispone di 60.000 dispositivi che generano un evento ogni secondo. Utilizzano un hub eventi con un numero di partizioni pari a 4 come origine eventi Time Series Insights. La dimensione di un evento è di 200 byte.

    * La frequenza di inserimento nell'ambiente sarebbe: **60.000 dispositivi , 200 byte/evento, 1 evento/sec, 12 MBps**.
    * La velocità per partizione sarebbe di 3 MBps.
    * La frequenza di inserimento di Contoso Fleet Analytics supera i limiti dell'ambiente e delle partizioni. Possono inviare una richiesta a Time Series Insights tramite il portale di Azure per aumentare la frequenza di inserimento per il proprio ambiente e creare un hub eventi con più partizioni entro i limiti di anteprima.

#### <a name="hub-partitions-and-per-partition-limits"></a>Partizioni hub e limiti per partizione

Quando si pianifica l'ambiente Time Series Insights, è importante considerare la configurazione delle origini eventi che si connetterà a Time Series Insights. Sia l'hub IoT di Azure che gli hub eventi utilizzano le partizioni per abilitare la scalabilità orizzontale per l'elaborazione degli eventi. 

Una *partizione* è una sequenza ordinata di eventi contenuti in un hub. Il conteggio delle partizioni viene impostato durante la fase di creazione dell'hub e non può essere modificato. 

Per le procedure consigliate per il partizionamento degli hub eventi, vedere [Quante partizioni sono necessarie?](https://docs.microsoft.com/azure/event-hubs/event-hubs-faq#how-many-partitions-do-i-need)

> [!NOTE]
> La maggior parte degli hub IoT usati con Azure Time Series Insights richiedono solo quattro partizioni.

Sia che tu stia creando un nuovo hub per il tuo ambiente Time Series Insights o ne usi uno esistente, dovrai calcolare la frequenza di inserimento per partizione per determinare se rientra nei limiti di anteprima. 

Azure Time Series Insights Preview ha attualmente un limite generale **per partizione di 0,5 MBps**.

#### <a name="iot-hub-specific-considerations"></a>Considerazioni specifiche sull'hub IoT

Quando un dispositivo viene creato nell'hub IoT, viene assegnato in modo permanente a una partizione. In questo modo, l'hub IoT è in grado di garantire l'ordine degli eventi (poiché l'assegnazione non cambia mai).

Un'assegnazione di partizione fissa influisce anche sulle istanze di Time Series Insights che inviano dati inviati dall'hub IoT verso il basso. Quando i messaggi provenienti da più dispositivi vengono inoltrati all'hub usando lo stesso ID dispositivo gateway, possono arrivare nella stessa partizione contemporaneamente al superamento dei limiti di scalabilità per partizione. 

**Impatto**:

* Se una singola partizione riscontra una frequenza di inserimento costante oltre il limite di anteprima, è possibile che Time Series Insights non sincronizzi tutti i dati di telemetria del dispositivo prima che sia stato superato il periodo di conservazione dei dati dell'hub IoT.If a single partition experiences a sustained rate of ingestion over the Preview limit, it's possible that Time Series Insights will not sync all device telemetry before the IoT Hub data retention period has been exceeded. Di conseguenza, i dati inviati possono andare persi se i limiti di inserimento vengono costantemente superati.

Per attenuare tale circostanza, è consigliabile attenersi alle seguenti procedure consigliate:

* Calcolare le velocità di inserimento per ambiente e per partizione prima di distribuire la soluzione.
* Assicurarsi che i dispositivi IoT Hub siano bilanciati al massimo della misura.

> [!IMPORTANT]
> Per gli ambienti che usano l'hub IoT come origine eventi, calcola la frequenza di inserimento usando il numero di dispositivi hub in uso per assicurarti che la frequenza sia inferiore al limite di 0,5 MBps per partizione nell'anteprima.
> * Anche se più eventi arrivano contemporaneamente, il limite di anteprima non verrà superato.

  ![Diagramma della partizione hub IoT](media/concepts-ingress-overview/iot-hub-partiton-diagram.png)

Per altre informazioni sull'ottimizzazione della velocità effettiva e delle partizioni dell'hub, fare riferimento alle risorse seguenti:Refer to the following resources to learn more about optimizing hub throughput and partitions:

* [Scala hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-scaling)
* [Scala hub eventi](https://docs.microsoft.com/azure/event-hubs/event-hubs-scalability#throughput-units)
* [Partizioni dell'hub eventi](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#partitions)

### <a name="data-storage"></a>Archiviazione dei dati

Quando si crea un ambiente SKU pay-as-you-go (PAYG) Time Series Insights Preview, si creano due risorse di Azure:When you create a Time Series Insights *Preview pay-as-you-go* (SKU environment), you create two Azure resources:

* Un ambiente Azure Time Series Insights Preview che può essere configurato per l'archiviazione dei dati a caldo.
* Un account BLOB V1 generico di Archiviazione di Azure per l'archiviazione dei dati a freddo.

I dati nel punto di archiviazione a caldo sono disponibili solo tramite [query Time Series](./time-series-insights-update-tsq.md) e Azure Time Series Insights Preview [explorer](./time-series-insights-update-explorer.md). L'archivio a caldo conterrà dati recenti entro il periodo di [conservazione](./time-series-insights-update-plan.md#the-preview-environment) selezionato durante la creazione dell'ambiente Time Series Insights.

Time Series Insights Preview salva i dati dell'archivio a freddo nell'archivio BLOB di Azure nel formato di [file Parquet.](#parquet-file-format-and-folder-structure) Time Series Insights Preview gestisce questi dati di cold store esclusivamente, ma è disponibile per la lettura direttamente come file Parquet standard.

> [!WARNING]
> In qualità di proprietario dell'account di archiviazione BLOB di Azure in cui risiedono i dati dell'archivio a freddo, si ha accesso completo a tutti i dati nell'account. Questo accesso include le autorizzazioni di scrittura ed eliminazione. Non modificare o eliminare i dati scritti da Time Series Insights Preview, perché ciò può causare la perdita di dati.

### <a name="data-availability"></a>Disponibilità dei dati

Azure Time Series Insights Preview partiziona e indici Dati di anteprima per ottenere prestazioni ottimali per le query. I dati diventano disponibili per eseguire query sia da warm (se abilitato) che da cold store dopo l'indicizzazione. La quantità di dati che viene ingerita può influire su questa disponibilità.

> [!IMPORTANT]
> Durante l'anteprima, è possibile che si verifichi un periodo fino a 60 secondi prima che i dati diventino disponibili. Se si verifica una latenza significativa oltre i 60 secondi, inviare un ticket di supporto tramite il portale di Azure.If you experience significant latency beyond 60 seconds, please submit a support ticket through the Azure portal.

## <a name="azure-storage"></a>Archiviazione di Azure

Questa sezione descrive i dettagli di Archiviazione di Azure relativi ad Azure Time Series Insights Preview.This section describes Azure Storage details relevant to Azure Time Series Insights Preview.

Per una descrizione completa dell'archiviazione BLOB di Azure, leggere [l'introduzione ai BLOB](../storage/blobs/storage-blobs-introduction.md)di archiviazione.

### <a name="your-storage-account"></a>L'account di archiviazioneYour storage account

Quando si crea un ambiente PAYG di Azure Time Series Insights Preview PAYG, viene creato un account BLOB V1 di Azure Storage generico come cold store a lungo termine.  

Azure Time Series Insights Preview conserva fino a due copie di ogni evento nell'account di Archiviazione di Azure.Azure Time Series Insights Preview retains up to two copies of each event in your Azure Storage account. Una copia archivia gli eventi ordinati in base al tempo di inserimento, consentendo sempre l'accesso agli eventi in una sequenza ordinata nel tempo. Nel corso del tempo, Time Series Insights Preview crea anche una copia ripartizionata dei dati da ottimizzare per eseguire una query Time Series Insights. 

Durante l'anteprima pubblica, i dati vengono archiviati a tempo indeterminato nell'account di archiviazione di Azure.During public Preview, data is stored indefinitely in your Azure Storage account.

#### <a name="writing-and-editing-time-series-insights-blobs"></a>Scrittura e modifica di BLOB di Time Series Insights

Per garantire le prestazioni delle query e la disponibilità dei dati, non modificare o eliminare i BLOB creati da Time Series Insights Preview.To ensure query performance and data availability, don't edit or delete any blobs that Time Series Insights Preview creates.

#### <a name="accessing-time-series-insights-preview-cold-store-data"></a>Accesso ai dati dell'archivio a freddo di Time Series Insights Preview 

Oltre ad accedere ai dati da [Time Series Insights Preview explorer](./time-series-insights-update-explorer.md) e Time Series [Query](./time-series-insights-update-tsq.md), è anche possibile accedere ai dati direttamente dai file Parquet memorizzati nel cold store. Ad esempio, è possibile leggere, trasformare e pulire i dati in un blocco appunti di Jupyter, quindi usarli per eseguire il training del modello di Azure Machine Learning nello stesso flusso di lavoro Spark.For example, you can read, transform, and cleanse data in a Jupyter notebook, then use it to train your Azure Machine Learning model in the same Spark workflow.

Per accedere ai dati direttamente dall'account di archiviazione di Azure, è necessario disporre dell'accesso in lettura all'account usato per archiviare i dati di Time Series Insights Preview.To access data directly from your Azure Storage account, you need read access to the account used to store your Time Series Insights Preview data. È quindi possibile leggere i dati selezionati in base all'ora di creazione del file Parquet che si trova nella `PT=Time` cartella descritta di seguito nella sezione Formato file di [parquet.](#parquet-file-format-and-folder-structure)  Per altre informazioni sull'abilitazione dell'accesso in lettura all'account di archiviazione, vedere [Gestire l'accesso alle risorse dell'account di archiviazione.](../storage/blobs/storage-manage-access-to-resources.md)

#### <a name="data-deletion"></a>Eliminazione di dati

Non eliminare i file di anteprima di Time Series Insights. Gestisci i dati correlati solo dall'anteprima di Time Series Insights.

### <a name="parquet-file-format-and-folder-structure"></a>Formato file di parquet e struttura delle cartelle

Parquet è un formato di file colonnare open source progettato per un'archiviazione e prestazioni efficienti. Time Series Insights Preview utilizza Parquet per abilitare le prestazioni delle query basate su ID Time Series su larga scala.  

Per ulteriori informazioni sul tipo di file Parquet, leggere la documentazione del [parquet.](https://parquet.apache.org/documentation/latest/)

Time Series Insights Preview archivia copie dei dati come segue:

* La prima copia iniziale è partizionata in base al tempo di ingestione e memorizza i dati approssimativamente in ordine di arrivo. Questi dati risiedono nella `PT=Time` cartella:

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* La seconda copia ripartizionata è raggruppata per ID Serie `PT=TsId` temporali e si trova nella cartella:

  `V=1/PT=TsId/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

In entrambi i casi, la proprietà time del file Parquet corrisponde all'ora di creazione del BLOB. I dati `PT=Time` nella cartella vengono mantenuti senza modifiche una volta scritti nel file. I dati `PT=TsId` nella cartella verranno ottimizzati per le query nel tempo e non sono statici.

> [!NOTE]
> * `<YYYY>`esegue il mapping a una rappresentazione dell'anno a quattro cifre.
> * `<MM>`esegue il mapping a una rappresentazione mensile a due cifre.
> * `<YYYYMMDDHHMMSSfff>`esegue il mapping a una rappresentazione`YYYY`di timestamp con`MM`anno a quattro`DD`cifre ( ),`HH`un mese a`MM`due cifre`SS`( ), un giorno`fff`a due cifre ( ), un'ora a due cifre ( ), un secondo a due cifre ( ) e un millisecondo a tre cifre ( ).

Gli eventi di Time Series Insights Preview vengono mappati al contenuto del file Parquet come segue:

* Viene eseguito il mapping di ogni evento a una singola riga.
* Ogni riga include la colonna **timestamp** con un timestamp di evento. La proprietà timestamp non è mai null. L'ora predefinita è **l'ora accodata** se la proprietà timestamp non è specificata nell'origine dell'evento. Il timestamp memorizzato è sempre in formato UTC.
* Ogni riga include le colonne TSID (Time Series ID) definite quando viene creato l'ambiente Time Series Insights. Il nome della proprietà `_string` TSID include il suffisso.
* Tutte le altre proprietà inviate come dati di `_string` telemetria `_bool` vengono mappate a nomi di colonna che terminano con (stringa), (booleano), `_datetime` (datetime) o `_double` (double), a seconda del tipo di proprietà.
* Questo schema di mapping si applica alla prima versione del formato di file, a cui viene fatto riferimento come **V1** e archiviato nella cartella di base con lo stesso nome. Man mano che questa funzionalità si evolve, questo schema di mapping potrebbe cambiare e il nome di riferimento viene incrementato.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sulla forma json per l'ingresso e la query, vedere [Modellare il codice JSON per l'ingresso e](./time-series-insights-update-how-to-shape-events.md)la query.

- Informazioni sulla nuova [modellazione dei dati](./time-series-insights-update-tsm.md).
