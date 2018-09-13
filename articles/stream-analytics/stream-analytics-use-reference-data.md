---
title: Usare i dati di riferimento per le ricerche in Analisi di flusso di Azure
description: Questo articolo descrive come usare i dati di riferimento per cercare o correlare dati in una struttura di query di un processo di Analisi di flusso di Azure.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/25/2018
ms.openlocfilehash: 2a6172a4e163d937f5a0a2140831b730bca23c3f
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/05/2018
ms.locfileid: "43696524"
---
# <a name="using-reference-data-for-lookups-in-stream-analytics"></a>Uso dei dati di riferimento per le ricerche in Analisi di flusso
I dati di riferimento, noti anche come tabella di ricerca, sono un set di dati limitato di natura statica o soggetti a lenti cambiamenti, usati per eseguire una ricerca o la correlazione con il flusso di dati. Analisi di flusso di Azure carica i dati di riferimento nella memoria per ottenere un'elaborazione del flusso a bassa latenza. Per usare i dati di riferimento in un processo di Analisi di flusso di Azure, si usa in genere un [JOIN dei dati di riferimento](https://msdn.microsoft.com/library/azure/dn949258.aspx) nella query. Analisi di flusso usa l'archivio BLOB di Azure come livello di archiviazione per i dati di riferimento e Azure Data Factory consente di trasformare e/o copiare nell'archivio BLOB di Azure i dati da usare come dati di riferimento da [qualsiasi numero di archivi dati locali e basati sul cloud](../data-factory/copy-activity-overview.md). I dati di riferimento sono modellati come una sequenza di BLOB (definiti nella configurazione di input) in ordine crescente in base alla data/ora specificata nel nome di BLOB. Supporta **solo** l'aggiunta alla fine della sequenza usando una data/ora **successiva** rispetto a quella specificata dall'ultimo BLOB nella sequenza.

Analisi di flusso supporta i dati di riferimento con **dimensione massima di 300 MB**. È possibile ottenere un limite di dimensione massima per i dati di riferimento di 300 MB solo con query semplici. Man mano che aumenta la complessità della query per includere l'elaborazione con informazioni sullo stato, ad esempio aggregazioni finestra, join temporali e funzioni di analisi temporale, è previsto che la dimensione massima supportata dei dati di riferimento diminuisca. Se Analisi di flusso di Azure non può caricare i dati di riferimento ed eseguire operazioni complesse, il processo esaurisce la memoria e ha esito negativo. In questi casi, la metrica di utilizzo in percentuale dell'unità di streaming raggiungerà il 100%.    

|**Numero di unità di streaming**  |**Dimensione massima approssimativa supportata (in MB)**  |
|---------|---------|
|1   |50   |
|3   |150   |
|6 e oltre   |300   |

L'aumento del numero di unità di streaming di un processo oltre 6 non comporta l'aumento della dimensione massima supportata dei dati di riferimento.

Il supporto per la compressione non è disponibile per i dati di riferimento. 

## <a name="configuring-reference-data"></a>Configurazione dei dati di riferimento
Per configurare i dati di riferimento, è prima di tutto necessario creare un input che sia di tipo **Dati di riferimento**. La tabella seguente illustra ogni proprietà che è necessario fornire durante la creazione di input di dati di riferimento con la relativa descrizione:

|**Nome proprietà**  |**Descrizione**  |
|---------|---------|
|Alias di input   | Nome descrittivo che verrà usato nella query di processo per fare riferimento a questo input.   |
|Account di archiviazione   | Nome dell'account di archiviazione in cui si trovano i BLOB. Se è incluso nella stessa sottoscrizione del processo di Analisi di flusso, può essere selezionato nell'elenco a discesa.   |
|Chiave dell'account di archiviazione   | Chiave privata associata all'account di archiviazione. Viene compilata automaticamente se l'account di archiviazione si trova nella stessa sottoscrizione del processo di analisi di flusso.   |
|Contenitore di archiviazione   | I contenitori forniscono un raggruppamento logico per gli oggetti BLOB archiviati nel servizio BLOB di Microsoft Azure. Quando si carica un oggetto BLOB nel servizio BLOB, è necessario specificare un contenitore per il BLOB.   |
|Modello di percorso   | Percorso usato per individuare i BLOB nel contenitore specificato. All'interno del percorso è possibile scegliere di specificare una o più istanze delle 2 variabili seguenti:<BR>{date}, {time}<BR>Esempio 1: products/{date}/{time}/product-list.csv<BR>Esempio 2: products/{date}/product-list.csv<BR><br> Se il blob non esiste nel percorso specificato, il processo di analisi di flusso attenderà per un periodo illimitato perché il blob diventi disponibile.   |
|Formato data [facoltativo]   | Se è stata usata la variabile {date} nel modello di percorso specificato, è possibile selezionare il formato di data in cui sono organizzati i BLOB nell'elenco a discesa dei formati supportati.<BR>Esempio: AAAA/MM/GG, MM/GG/AAAA e così via   |
|Formato ora [facoltativo]   | Se è stata usata la variabile {time} nel modello di percorso specificato, è possibile selezionare il formato di ora in cui sono organizzati i BLOB nell'elenco a discesa dei formati supportati.<BR>Esempio: HH, HH/mm o HH-mm.  |
|Formato di serializzazione eventi   | Per accertarsi che le query funzionino come previsto, l'analisi di flusso deve conoscere il formato di serializzazione usato per i flussi di dati in entrata. Per i dati di riferimento, i formati dati supportati sono CSV e JSON.  |
|Codifica   | Al momento UTF-8 è l'unico formato di codifica supportato.  |

## <a name="static-reference-data"></a>Dati di riferimento statici
Se non è previsto che i dati di riferimento cambino, viene abilitato il supporto dei dati di riferimento statici specificando un percorso statico nella configurazione di input. Analisi di flusso di Azure preleva il BLOB dal percorso specificato. Non sono necessari i token di sostituzione {date} e {time}. I dati di riferimento sono immutabili in Analisi di flusso. Pertanto, non è consigliabile sovrascrivere un BLOB di dati di riferimento statici.

## <a name="generating-reference-data-on-a-schedule"></a>Generazione di dati di riferimento in una pianificazione
Se i dati di riferimento sono costituiti da un set di dati che cambia lentamente, è possibile abilitare il supporto per l'aggiornamento dei dati di riferimento specificando un modello di percorso nella configurazione di input con i token di sostituzione {date} e {time}. Analisi di flusso seleziona le definizioni dei dati di riferimento aggiornate in base a questo modello di percorso. Ad esempio, un modello `sample/{date}/{time}/products.csv` con formato di data **"AAAA-MM-GG"** e formato di ora **"HH-mm"** indica ad Analisi di flusso di selezionare il BLOB aggiornato `sample/2015-04-16/17-30/products.csv` alle 17.30 del 16 aprile 2015 nel fuso orario UTC.

Analisi di flusso di Azure verifica automaticamente se sono disponibili BLOB di dati di riferimento aggiornati a intervalli di un minuto.

> [!NOTE]
> Attualmente i processi di analisi di flusso cercano l'aggiornamento del BLOB solo quando la data/ora del computer precede quella codificata nel nome del BLOB. Ad esempio, il processo cercherà `sample/2015-04-16/17-30/products.csv` non appena possibile ma non prima delle 17.30 del 16 aprile 2015 nel fuso orario UTC. Il processo non cercherà *mai* un BLOB con data/ora codificata precedente all'ultima individuata.
> 
> ad esempio quando il processo trova il BLOB `sample/2015-04-16/17-30/products.csv` ignora tutti i file con data/ora codificata precedente alle 17.30 del 16 aprile 2015. Di conseguenza, se nello stesso contenitore viene creato un BLOB `sample/2015-04-16/17-25/products.csv` arrivato in ritardo, questo non viene usato dal processo.
> 
> Analogamente, se il file `sample/2015-04-16/17-30/products.csv` viene generato solo alle 22.03 del 16 aprile 2015, ma nel contenitore non è presente alcun BLOB con data/ora precedente, il processo usa questo file a partire dalle 22.03 del 16 aprile 2015 e i dati di riferimento precedenti fino a quel momento.
> 
> Un'eccezione a questo si verifica quando il processo deve elaborare nuovamente dei dati indietro nel tempo o quando il processo viene in primo luogo avviato. All'avvio, il processo cerca il BLOB più recente generato prima dell'ora di inizio del processo specificata. Questa operazione viene eseguita per verificare la presenza di un set di dati di riferimento **non vuoto** all'avvio del processo. Se non viene trovato, il processo restituisce il messaggio di diagnostica seguente: `Initializing input without a valid reference data blob for UTC time <start time>`.
> 
> 

È possibile usare [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) per orchestrare l'attività di creazione dei BLOB aggiornati richiesti da Analisi di flusso per aggiornare le definizioni dei dati di riferimento. Data factory è un servizio di integrazione delle informazioni basato sul cloud che permette di automatizzare lo spostamento e la trasformazione dei dati. Data factory supporta la [connessione a un numero elevato di archivi dati basati su cloud e locali](../data-factory/copy-activity-overview.md) e il semplice trasferimento dei dati in base a una pianificazione regolare specificata dall'utente. Per altre informazioni e per istruzioni dettagliate su come configurare una pipeline di Data factory per generare dati di riferimento per l'analisi di flusso che vengano aggiornati in base a una pianificazione predefinita, consultare questo [esempio di GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ReferenceDataRefreshForASAJobs).

## <a name="tips-on-refreshing-your-reference-data"></a>Suggerimenti sull'aggiornamento dei dati di riferimento
1. Non sovrascrivere i BLOB dei dati di riferimento perché sono immutabili.
2. Il modo consigliato per aggiornare i dati di riferimento è il seguente:
    * Usare {date}/{time} nel modello di percorso
    * Aggiungere un nuovo BLOB usando lo stesso modello di contenitore e percorso definito nell'input del processo
    * Usare un valore di data/ora **maggiore** rispetto a quello specificato dall'ultimo BLOB nella sequenza.
3. I BLOB dei dati di riferimento **non** vengono ordinati in base all'ora dell'"ultima modifica" del BLOB, ma solo in base all'ora e alla data specificate nel nome del BLOB con le sostituzioni di {date} e {time}.
3. Per evitare di dover elencare un numero elevato di BLOB, valutare l'eliminazione dei BLOB molto vecchi per cui non verrà più eseguita l'elaborazione. Si noti che ASA potrebbe doverne rielaborare una piccola quantità in alcuni scenari, ad esempio un riavvio.

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Avvio rapido: creare un processo di Analisi di flusso di Azure tramite il portale di Azure](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
