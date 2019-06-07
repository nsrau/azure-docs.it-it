---
title: Archiviazione e ingresso dei dati nella versione di anteprima di Azure Time Series Insights | Microsoft Docs
description: Informazioni sull'archiviazione e l'ingresso dei dati nella versione di anteprima di Azure Time Series Insights.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 05/20/2019
ms.custom: seodec18
ms.openlocfilehash: 33ca86fc2d13fb7f6e29c43e9a7c1d2dc6ef4169
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/07/2019
ms.locfileid: "66755228"
---
# <a name="data-storage-and-ingress-in-azure-time-series-insights-preview"></a>Archiviazione e ingresso dei dati nella versione di anteprima di Azure Time Series Insights

Questo articolo descrive le modifiche all'archiviazione e all'ingresso dei dati nella versione di anteprima di Azure Time Series Insights. Esamina la struttura di archiviazione sottostante, il formato dei file e la proprietà Time Series ID. L'articolo illustra anche il processo di ingresso dei dati sottostante, la velocità effettiva e le limitazioni.

## <a name="data-storage"></a>Archiviazione dei dati

Quando si crea un ambiente SKU con pagamento in base al consumo della versione di anteprima di Time Series Insights, si creano due risorse:

* Un ambiente Time Series Insights.
* Un account V1 per utilizzo generico di Archiviazione di Azure in cui verranno archiviati i dati.

L'anteprima di Insights serie ora Usa l'archiviazione Blob di Azure con il tipo di file Parquet. Time Series Insights gestisce tutte le operazioni sui dati, tra cui la creazione di BLOB, l'indicizzazione e il partizionamento di dati nell'account di archiviazione di Azure. Creare questi BLOB usando un account di archiviazione di Azure.

Come altri BLOB di Archiviazione di Azure, i BLOB creati da Time Series Insights consentono operazioni di lettura e scrittura per supportare svariati scenari di integrazione.

> [!TIP]
> Le prestazioni di Time Series Insights possono essere compromesse se si eseguono operazioni di lettura o scrittura sui BLOB con eccessiva frequenza.

Per una panoramica dell'archiviazione BLOB di Azure, vedere [Introduzione all'archiviazione BLOB di Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction).

Per altre informazioni sul tipo di file Parquet, vedere [Tipi di file supportati in Archiviazione di Azure](https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs#parquet-format).

## <a name="parquet-file-format"></a>Formato di file Parquet

Parquet è un formato di file di dati orientato alla colonna che assicura:

* Interoperabilità
* Efficienza dello spazio
* Efficienza delle query

È stato scelto il formato Parquet perché offre schemi efficienti di compressione e codifica dei dati, con prestazioni elevate per gestire dati complessi in blocco.

Per altre informazioni su questo formato di file, vedere la [documentazione su Parquet](https://parquet.apache.org/documentation/latest/).

### <a name="event-structure-in-parquet"></a>Struttura di eventi in Parquet

Time Series Insights crea e archivia copie di BLOB nei due formati seguenti:

1. La prima copia iniziale viene partizionata in base all'ora di arrivo:

    * `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`
    * Ora di creazione del BLOB per i BLOB partizionati in base all'ora di arrivo.

1. La seconda copia ripartizionata viene partizionata in base a un raggruppamento dinamico di ID serie temporale:

    * `V=1/PT=TsId/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`
    * Timestamp dell'evento minimo in un BLOB per i BLOB partizionati in base agli ID serie temporali.

> [!NOTE]
> * `<YYYY>` esegue il mapping a una rappresentazione dell'anno a 4 cifre.
> * `<MM>` esegue il mapping a una rappresentazione del mese a 2 cifre.
> * `<YYYYMMDDHHMMSSfff>` esegue il mapping a una rappresentazione del timestamp con anno a 4 cifre (`YYYY`), mese a 2 cifre (`MM`), giorno a 2 cifre (`DD`), ora a 2 cifre (`HH`), minuti a 2 cifre (`MM`), secondo a 2 cifre (`SS`) e millisecondo a 3 cifre (`fff`).

Gli eventi di Time Series Insights vengono mappati al contenuto del file Parquet nel modo indicato di seguito:

* Viene eseguito il mapping di ogni evento a una singola riga.
* È presente una colonna **Timestamp** predefinite con un timestamp di evento. La proprietà Timestamp non è mai un valore Null. L'impostazione predefinita è l'**ora di accodamento dell'origine evento**, se la proprietà Timestamp non viene specificata nell'origine evento. Il timestamp è in formato UTC. 
* Tutte le altre proprietà mappate alle colonne terminano con `_string` (stringa), `_bool` (booleano), `_datetime` (datetime) e `_double` (double), a seconda del tipo di proprietà.
* Si fa riferimento a questo schema di mapping per la prima versione del formato file come **V=1**. Man mano che questa funzionalità cambia, il nome sarà incrementato a **V=2**, **V=3** e così via.

## <a name="partitions"></a>Partitions

Ogni ambiente di versione di anteprima di tempo Series Insights è necessario un **ID serie tempo** proprietà e una **Timestamp** proprietà che identificano in modo. Time Series ID funge da partizione logica per i dati e fornisce all'ambiente di anteprima di Time Series Insights un limite naturale per la distribuzione dei dati tra le partizioni fisiche. La gestione delle partizioni fisiche viene eseguita dalla versione di anteprima di Time Series Insights in un account di archiviazione di Azure.

Time Series Insights usa il partizionamento dinamico per ottimizzare le prestazioni a livello di archiviazione e query eliminando e ricreando le partizioni. L'algoritmo di partizionamento dinamico della versione di anteprima di Azure Time Series Insights tenta di impedire a una singola partizione fisica di disporre di dati per più partizioni logiche distinte. In altre parole, l'algoritmo di partizionamento mantiene tutti i dati specifici di un singolo ID serie temporale presente esclusivamente nei file Parquet, senza interlacciamento con altri ID serie temporale. L'algoritmo di partizionamento dinamico cerca anche di preservare l'ordine originale degli eventi all'interno di un singolo ID serie temporale.

Nella fase iniziale, al momento dell'ingresso, i dati vengono partizionati da Timestamp in modo che una singola partizione logica all'interno di un determinato intervallo temporale possa essere distribuita in più partizioni fisiche. Una singola partizione fisica potrebbe anche contenere numerose o tutte le partizioni logiche. A causa delle limitazioni delle dimensioni dei BLOB, anche con un partizionamento ottimale una singola partizione logica può occupare più partizioni fisiche.

> [!NOTE]
> Per impostazione predefinita, il valore Timestamp è l'*ora accodamento* del messaggio nell'origine evento configurata.

Se si caricano dati cronologici o messaggi in batch, assegnare il valore che si vuole archiviare con i dati nella proprietà Timestamp che esegue il mapping al valore Timestamp appropriato. La proprietà Timestamp fa distinzione fra maiuscole e minuscole. Per altre informazioni, vedere [Modello Time Series](./time-series-insights-update-tsm.md).

### <a name="physical-partitions"></a>Partizioni fisiche

Una partizione fisica è un BLOB in blocchi archiviato nell'account di archiviazione. Le dimensioni effettive dei BLOB possono variare poiché dipendono dalla frequenza di push. Tuttavia, in genere la dimensione prevista dovrebbe essere compresa tra 20 MB e 50 MB. Questa previsione ha portato il team di Time Series Insights a scegliere 20 MB come la dimensione per ottimizzare le prestazioni delle query. Questa dimensione può cambiare nel tempo, a seconda delle dimensioni del file e della velocità di ingresso dei dati.

> [!NOTE]
> * I BLOB hanno una dimensione di 20 MB.
> * I BLOB di Azure vengono talvolta ripartizionati per migliorare le prestazioni, eliminandolo e ricreandoli.
> * Inoltre, gli stessi dati di Time Series Insights possono essere presenti in due o più BLOB.

### <a name="logical-partitions"></a>Partizioni logiche

Una partizione logica è una partizione all'interno di una partizione fisica in cui sono archiviati tutti i dati associati al valore di una singola chiave di partizione. La versione di anteprima di tempo Series Insights in modo logico le partizioni ogni blob in base a due proprietà:

* **Time Series ID**: la chiave di partizione per tutti i dati di Time Series Insights all'interno del flusso di eventi e del modello.
* **Timestamp**: l'ora basata sull'ingresso iniziale.

La versione di anteprima di tempo Series Insights offre query ad alte prestazioni basate su queste due proprietà. Le due proprietà forniscono anche il metodo più efficace per la distribuzione rapida dei dati di Time Series Insights.

È importante selezionare un valore Time Series ID appropriato perché questa proprietà non è modificabile. Per altre informazioni, vedere [Scegliere gli ID di serie temporali](./time-series-insights-update-how-to-id.md).

## <a name="azure-storage"></a>Archiviazione di Azure

### <a name="your-storage-account"></a>L'account di archiviazione

Quando si crea un ambiente Time Series Insights con pagamento in base al consumo, si creano due risorse: un ambiente Time Series Insights e un account V1 per utilizzo generico di Archiviazione Azure in cui verranno archiviati i dati. La scelta è stata quella di rendere come predefinita la risorsa v1 per utilizzo generico di Archiviazione di Azure per la sua interoperabilità, il prezzo e le prestazioni. 

Time Series Insights pubblica fino a due copie di ogni evento nell'account di archiviazione di Azure. La copia iniziale viene mantenuta sempre in modo che si può effettuare rapidamente query, con altri servizi. È possibile usare facilmente Spark, Hadoop e altri strumenti comuni tra gli ID serie temporale su file Parquet non elaborati, perché questi motori supportano il filtraggio di base dei nomi file. Il raggruppamento di BLOB per anno e mese è un modo utile per elencare i BLOB all'interno di un intervallo di tempo specifico per un processo personalizzato. 

Inoltre, Time Series Insights rieseguirà il partizionamento dei file Parquet per assicurarne l'ottimizzazione per le API di Time Series Insights. Viene salvato anche il file partizionato più recente.

Durante l'anteprima pubblica, i dati vengono archiviati in modo indefinito nell'account di archiviazione di Azure.

### <a name="writing-and-editing-time-series-insights-blobs"></a>Scrittura e modifica di BLOB di Time Series Insights

Per garantire le prestazioni delle query e la disponibilità dei dati, non modificare o eliminare i BLOB creati da Time Series Insights.

### <a name="accessing-and-exporting-data-from-time-series-insights-preview"></a>Accesso ed esportazione dei dati dalla versione di anteprima di Time Series Insights

È possibile accedere ai dati archiviati nello strumento di esplorazione dell'anteprima di Time Series Insights da usare in combinazione con altri servizi. Ad esempio, è possibile usare i dati per creare report in Power BI, per eseguire l'apprendimento automatico usando Azure Machine Learning Studio o per l'utilizzo in un'applicazione notebook con i notebook Jupyter.

È possibile accedere ai dati in tre modi:

* Da Esplora Series Insights un'anteprima: è possibile esportare i dati come file CSV da Esplora Series Insights un'anteprima. Per altre informazioni, vedere [Strumento di esplorazione dell'anteprima di Time Series Insights](./time-series-insights-update-explorer.md).
* Da Time Series Insights le API di anteprima: l'endpoint dell'API, è possibile contattarlo al `/getRecorded`. Per altre informazioni su questa API, vedere [Query Time Series](./time-series-insights-update-tsq.md).
* Direttamente da un account di archiviazione di Azure (sotto).

#### <a name="from-an-azure-storage-account"></a>Da un account di archiviazione di Azure

* È necessario l'accesso in lettura a qualsiasi account usato per accedere ai dati di Time Series Insights. Per altre informazioni, vedere [Gestire l'accesso alle risorse degli account di archiviazione di Azure](https://docs.microsoft.com/azure/storage/blobs/storage-manage-access-to-resources).
* Per altre informazioni sui metodi diretti per leggere i dati dall'archiviazione BLOB di Azure, vedere [Spostamento dei dati da e verso Archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-moving-data?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
* Per esportare i dati da un account di archiviazione di Azure:
    * Prima di tutto assicurarsi che l'account soddisfi i requisiti necessari per l'esportazione di dati. Per altre informazioni, vedere [Requisiti di sistema di importazione/esportazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-import-export-requirements).
    * Per altre informazioni su altri modi per esportare i dati dall'account di archiviazione di Azure, vedere [Importare ed esportare dati dai BLOB](https://docs.microsoft.com/azure/storage/common/storage-import-export-data-from-blobs).

### <a name="data-deletion"></a>Eliminazione di dati

Non eliminare i BLOB. Non solo sono utili per il controllo e la gestione di un record dei dati, la versione di anteprima di tempo Series Insights gestisce i metadati dei blob all'interno di ogni blob.

## <a name="time-series-insights-data-ingress"></a>Inserimento dei dati di ora Series Insights

### <a name="ingress-policies"></a>Criteri in ingresso

La versione di anteprima di tempo Series Insights supporta le stesse origini eventi e tipi di file che Time Series Insights supporta attualmente.

Alcune origini evento supportate sono:

- Hub IoT Azure
- Hub eventi di Azure
  
  > [!NOTE]
  > Le istanze di Hub eventi di Azure supportano Kafka.

Alcuni tipi di file supportati sono:

* JSON: Per altre informazioni sulle altre forme JSON supportate che è possibile gestire, vedere [Forme JSON supportate](./time-series-insights-send-events.md#json).

### <a name="data-availability"></a>Disponibilità dei dati

La versione di anteprima di tempo Series Insights indicizza i dati tramite una strategia di ottimizzazione delle dimensioni di blob. I dati diventano disponibili per la query dopo l'indicizzazione, che si basa sulla quantità di dati in arrivo e sulla velocità.

> [!IMPORTANT]
> * La versione di disponibilità generale di Time Series Insights renderà disponibili i dati entro 60 secondi dal riscontro di un'origine evento. 
> * Durante l'anteprima calcolare un tempo più lungo prima che i dati vengano resi disponibili.
> * Se la latenza è significativa, contattare il supporto tecnico.

### <a name="scale"></a>Scalabilità

La versione di anteprima di tempo Series Insights supporta una scalabilità un ingresso iniziale di fino a 1 Byte Mega al secondo (Mbps) per ogni ambiente. Il supporto per una scalabilità di livello avanzato è in fase di sviluppo. Per riflettere questi miglioramenti, è previsto un aggiornamento della documentazione.

## <a name="next-steps"></a>Passaggi successivi

- Leggere [Archiviazione e ingresso nell'anteprima di Azure Time Series Insights](./time-series-insights-update-storage-ingress.md).

- Leggere la nuova [modellazione di dati](./time-series-insights-update-tsm.md).

<!-- Images -->
[1]: media/v2-update-storage-ingress/storage-architecture.png
[2]: media/v2-update-storage-ingress/parquet-files.png
[3]: media/v2-update-storage-ingress/blob-storage.png
