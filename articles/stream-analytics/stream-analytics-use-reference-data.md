---
title: Usare i dati di riferimento per le ricerche in Analisi di flusso di Azure
description: Questo articolo descrive come usare i dati di riferimento per cercare o correlare dati in una struttura di query di un processo di Analisi di flusso di Azure.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 5/11/2020
ms.openlocfilehash: 8aae9a0ff3ffdbd4f6bc93db5c6f15dcb938080e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84196431"
---
# <a name="using-reference-data-for-lookups-in-stream-analytics"></a>Uso dei dati di riferimento per le ricerche in Analisi di flusso

I dati di riferimento (noti anche come tabella di ricerca) sono un set di dati limitato che è statico o a modifica lenta, usato per eseguire una ricerca o per aumentare i flussi di dati. Ad esempio, in uno scenario IoT, si potrebbero archiviare i metadati relativi ai sensori, che non cambiano spesso, in dati di riferimento e unirli ai flussi di dati IoT in tempo reale. Analisi di flusso di Azure carica i dati di riferimento nella memoria per ottenere un'elaborazione del flusso a bassa latenza. Per usare i dati di riferimento nel processo di analisi di flusso di Azure, in genere si usa un [join dei dati di riferimento](https://docs.microsoft.com/stream-analytics-query/reference-data-join-azure-stream-analytics) nella query. 

## <a name="example"></a>Esempio  
È possibile avere un flusso in tempo reale degli eventi generati quando le automobili passano un casello. Il casello può acquisire la targa in tempo reale e partecipare a un set di dati statico con i dettagli di registrazione per identificare le targhe che sono scadute.  
  
```SQL  
SELECT I1.EntryTime, I1.LicensePlate, I1.TollId, R.RegistrationId  
FROM Input1 I1 TIMESTAMP BY EntryTime  
JOIN Registration R  
ON I1.LicensePlate = R.LicensePlate  
WHERE R.Expired = '1'
```  

Analisi di flusso supporta l'archivio BLOB di Azure e il database SQL di Azure come livello di archiviazione per i dati di riferimento. È anche possibile trasformare e/o copiare i dati di riferimento nell'archiviazione BLOB da Azure Data Factory per usare [qualsiasi numero di archivi dati locali e basati sul cloud](../data-factory/copy-activity-overview.md).

## <a name="azure-blob-storage"></a>Archiviazione BLOB di Azure

I dati di riferimento sono modellati come una sequenza di BLOB (definiti nella configurazione di input) in ordine crescente in base alla data/ora specificata nel nome di BLOB. Supporta **solo** l'aggiunta alla fine della sequenza usando una data/ora **successiva** rispetto a quella specificata dall'ultimo BLOB nella sequenza.

### <a name="configure-blob-reference-data"></a>Configurare i dati di riferimento BLOB

Per configurare i dati di riferimento, è prima di tutto necessario creare un input che sia di tipo **Dati di riferimento**. La tabella seguente illustra ogni proprietà che è necessario fornire durante la creazione di input di dati di riferimento con la relativa descrizione:

|**Nome proprietà**  |**Descrizione**  |
|---------|---------|
|Alias di input   | Nome descrittivo che verrà usato nella query di processo per fare riferimento a questo input.   |
|Account di archiviazione   | Nome dell'account di archiviazione in cui si trovano i BLOB. Se è incluso nella stessa sottoscrizione del processo di Analisi di flusso, può essere selezionato nell'elenco a discesa.   |
|Chiave dell'account di archiviazione   | Chiave privata associata all'account di archiviazione. Viene compilata automaticamente se l'account di archiviazione si trova nella stessa sottoscrizione del processo di analisi di flusso.   |
|Contenitore di archiviazione   | I contenitori forniscono un raggruppamento logico per gli oggetti BLOB archiviati nel servizio BLOB di Microsoft Azure. Quando si carica un oggetto BLOB nel servizio BLOB, è necessario specificare un contenitore per il BLOB.   |
|Modello di percorso   | Si tratta di una proprietà obbligatoria utilizzata per individuare i BLOB all'interno del contenitore specificato. All'interno del percorso è possibile scegliere di specificare una o più istanze delle 2 variabili seguenti:<BR>{date}, {time}<BR>Esempio 1: products/{date}/{time}/product-list.csv<BR>Esempio 2: products/{date}/product-list.csv<BR>Esempio 3: product-list. csv<BR><br> Se il blob non esiste nel percorso specificato, il processo di analisi di flusso attenderà per un periodo illimitato perché il blob diventi disponibile.   |
|Formato data [facoltativo]   | Se è stata usata la variabile {date} nel modello di percorso specificato, è possibile selezionare il formato di data in cui sono organizzati i BLOB nell'elenco a discesa dei formati supportati.<BR>Esempio: AAAA/MM/GG, MM/GG/AAAA e così via   |
|Formato ora [facoltativo]   | Se è stata usata la variabile {time} nel modello di percorso specificato, è possibile selezionare il formato di ora in cui sono organizzati i BLOB nell'elenco a discesa dei formati supportati.<BR>Esempio: HH, HH/mm o HH-mm.  |
|Formato di serializzazione eventi   | Per accertarsi che le query funzionino come previsto, l'analisi di flusso deve conoscere il formato di serializzazione usato per i flussi di dati in entrata. Per i dati di riferimento, i formati dati supportati sono CSV e JSON.  |
|Codifica   | Al momento UTF-8 è l'unico formato di codifica supportato.  |

### <a name="static-reference-data"></a>Dati di riferimento statici

Se non è previsto che i dati di riferimento cambino, viene abilitato il supporto dei dati di riferimento statici specificando un percorso statico nella configurazione di input. Analisi di flusso di Azure preleva il BLOB dal percorso specificato. Non sono necessari i token di sostituzione {date} e {time}. Poiché i dati di riferimento non sono modificabili in analisi di flusso, è sconsigliabile sovrascrivere un BLOB di dati di riferimento statici.

### <a name="generate-reference-data-on-a-schedule"></a>Generare i dati di riferimento in base a una pianificazione

Se i dati di riferimento sono costituiti da un set di dati che cambia lentamente, è possibile abilitare il supporto per l'aggiornamento dei dati di riferimento specificando un modello di percorso nella configurazione di input con i token di sostituzione {date} e {time}. Analisi di flusso seleziona le definizioni dei dati di riferimento aggiornate in base a questo modello di percorso. Ad esempio, un modello di `sample/{date}/{time}/products.csv` con formato di data **"aaaa-mm-gg"** e formato di ora **"hh-mm"** indica ad analisi di flusso di selezionare il BLOB aggiornato `sample/2015-04-16/17-30/products.csv` alle 5:30 del 16 aprile 2015 fuso orario UTC.

Analisi di flusso di Azure verifica automaticamente se sono disponibili BLOB di dati di riferimento aggiornati a intervalli di un minuto. Se un BLOB con timestamp 10:30:00 viene caricato con un breve ritardo (ad esempio, 10:30:30), si noterà un piccolo ritardo nel processo di analisi di flusso che fa riferimento a questo BLOB. Per evitare tali scenari, è consigliabile caricare il BLOB prima del tempo effettivo di destinazione (10:30:00 in questo esempio) per consentire al processo di analisi di flusso il tempo sufficiente per individuarlo e caricarlo in memoria ed eseguire operazioni. 

> [!NOTE]
> Attualmente i processi di analisi di flusso cercano l'aggiornamento del BLOB solo quando la data/ora del computer precede quella codificata nel nome del BLOB. Ad esempio, il processo cercherà `sample/2015-04-16/17-30/products.csv` non appena possibile ma non prima delle 17.30 del 16 aprile 2015 nel fuso orario UTC. Il processo non cercherà *mai* un BLOB con data/ora codificata precedente all'ultima individuata.
> 
> Ad esempio, quando il processo trova il BLOB, `sample/2015-04-16/17-30/products.csv` ignorerà tutti i file con una data codificata precedente alla 5:30 del 16 aprile 2015, quindi, se `sample/2015-04-16/17-25/products.csv` viene creato un BLOB in ritardo nello stesso contenitore, il processo non lo userà.
> 
> Analogamente, se il file `sample/2015-04-16/17-30/products.csv` viene generato solo alle 22.03 del 16 aprile 2015, ma nel contenitore non è presente alcun BLOB con data/ora precedente, il processo usa questo file a partire dalle 22.03 del 16 aprile 2015 e i dati di riferimento precedenti fino a quel momento.
> 
> Un'eccezione a questo si verifica quando il processo deve elaborare nuovamente dei dati indietro nel tempo o quando il processo viene in primo luogo avviato. All'avvio, il processo cerca il BLOB più recente generato prima dell'ora di inizio del processo specificata. Questa operazione viene eseguita per verificare la presenza di un set di dati di riferimento **non vuoto** all'avvio del processo. Se non viene trovato, il processo restituisce il messaggio di diagnostica seguente: `Initializing input without a valid reference data blob for UTC time <start time>`.

È possibile usare [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) per orchestrare l'attività di creazione dei BLOB aggiornati richiesti da Analisi di flusso per aggiornare le definizioni dei dati di riferimento. Data factory è un servizio di integrazione delle informazioni basato sul cloud che permette di automatizzare lo spostamento e la trasformazione dei dati. Data factory supporta la [connessione a un numero elevato di archivi dati basati su cloud e locali](../data-factory/copy-activity-overview.md) e il semplice trasferimento dei dati in base a una pianificazione regolare specificata dall'utente. Per altre informazioni e per istruzioni dettagliate su come configurare una pipeline di Data factory per generare dati di riferimento per l'analisi di flusso che vengano aggiornati in base a una pianificazione predefinita, consultare questo [esempio di GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ReferenceDataRefreshForASAJobs).

### <a name="tips-on-refreshing-blob-reference-data"></a>Suggerimenti sull'aggiornamento dei dati di riferimento BLOB

1. Non sovrascrivere i BLOB dei dati di riferimento perché sono immutabili.
2. Il modo consigliato per aggiornare i dati di riferimento è il seguente:
    * Usare {date}/{time} nel modello di percorso
    * Aggiungere un nuovo BLOB usando lo stesso modello di contenitore e percorso definito nell'input del processo
    * Usare un valore di data/ora **maggiore** rispetto a quello specificato dall'ultimo BLOB nella sequenza.
3. I BLOB dei dati di riferimento **non** vengono ordinati in base all'ora dell'Ultima modifica del BLOB, ma solo in base all'ora e alla data specificate nel nome del BLOB usando le sostituzioni {date} e {Time}.
3. Per evitare di dover elencare un numero elevato di BLOB, valutare l'eliminazione dei BLOB molto vecchi per cui non verrà più eseguita l'elaborazione. Si noti che ASA potrebbe doverne rielaborare una piccola quantità in alcuni scenari, ad esempio un riavvio.

## <a name="azure-sql-database"></a>database SQL di Azure

I dati di riferimento del database SQL di Azure vengono recuperati dal processo di Analisi di flusso archiviati come snapshot in memoria per l'elaborazione. Lo snapshot dei dati di riferimento viene anche archiviato in un contenitore all'interno di un account di archiviazione specificato nelle impostazioni di configurazione. Il contenitore viene creato automaticamente all'avvio del processo. Se il processo viene arrestato o si trova in uno stato di errore, i contenitori creati automaticamente vengono eliminati quando il processo viene riavviato.  

Se i dati di riferimento sono costituiti da un set di dati che cambia lentamente, è necessario aggiornare periodicamente lo snapshot che viene usato nel processo. Analisi di flusso consente di impostare una frequenza di aggiornamento quando si configura la connessione all'input del database SQL di Azure. Il runtime di Analisi di flusso eseguirà query sul database SQL di Azure all'intervallo specificato dalla frequenza di aggiornamento. La frequenza di aggiornamento più veloce supportata è una volta al minuto. Per ogni aggiornamento, Analisi di flusso archivia un nuovo snapshot nell'account di archiviazione specificato.

Analisi di flusso fornisce due opzioni per eseguire query sul database SQL di Azure. Una query snapshot è obbligatoria e deve essere inclusa in ogni processo. Analisi di flusso esegue la query snapshot periodicamente in base all'intervallo di aggiornamento e usa il risultato della query (lo snapshot) come set di dati di riferimento. La query snapshot dovrebbe essere adatta alla maggior parte degli scenari, ma se si verificano problemi di prestazioni con set di dati di grandi dimensioni e frequenze di aggiornamento veloci, è possibile usare l'opzione della query delta. Le query che importano più di 60 secondi per restituire il set di dati di riferimento comporteranno un timeout.

Con l'opzione della query delta, Analisi di flusso esegue la query snapshot all'inizio, per ottenere un set di dati di riferimento di base. In seguito, Analisi di flusso esegue la query delta periodicamente in base all'intervallo di aggiornamento per recuperare le modifiche incrementali. Queste modifiche incrementali vengono continuamente applicate al set di dati di riferimento per mantenerlo aggiornato. Usare una query differenziale può essere utile a ridurre i costi di archiviazione e le operazioni di I/O di rete.

### <a name="configure-sql-database-reference"></a>Configurare il riferimento del database SQL

Per configurare i dati di riferimento del database SQL, è prima di tutto necessario creare un input **Dati di riferimento**. La tabella seguente illustra ogni proprietà che è necessario fornire durante la creazione di input di dati di riferimento con la relativa descrizione. Per altre informazioni, vedere [Usare dati di riferimento da un database SQL per un processo di Analisi di flusso di Azure](sql-reference-data.md).

È possibile usare [istanza gestita SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) come input di dati di riferimento. È necessario [configurare l'endpoint pubblico in SQL istanza gestita](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure) e quindi configurare manualmente le impostazioni seguenti in analisi di flusso di Azure. La macchina virtuale di Azure che esegue SQL Server con un database collegato è supportata anche dalla configurazione manuale delle impostazioni riportate di seguito.

|**Nome proprietà**|**Descrizione**  |
|---------|---------|
|Alias di input|Nome descrittivo che verrà usato nella query di processo per fare riferimento a questo input.|
|Subscription|Scegliere la sottoscrizione|
|Database|Database SQL di Azure che contiene i dati di riferimento. Per Istanza gestita SQL, è necessario specificare la porta 3342. Ad esempio, *sampleserver.public.database.windows.net,3342*|
|Username|Nome utente associato al database SQL di Azure.|
|Password|Password associata al database SQL di Azure.|
|Aggiorna periodicamente|Questa opzione consente di scegliere una frequenza di aggiornamento. Scegliendo "Attivato" è possibile specificare la frequenza di aggiornamento in GG:HH:MM.|
|Query snapshot|Si tratta dell'opzione di query predefinita che recupera i dati di riferimento dal database SQL.|
|Query delta|Per gli scenari avanzati con grandi set di dati e frequenza di aggiornamento veloce, scegliere di aggiungere una query delta.|

## <a name="size-limitation"></a>Limitazione delle dimensioni

Per ottenere prestazioni ottimali, è consigliabile usare set di impostazioni di riferimento inferiori a 300 MB. L'utilizzo dei dati di riferimento maggiori di 300 MB è supportato nei processi con 6 o più unità di streaming. Questa funzionalità è in anteprima e non deve essere usata nell'ambiente di produzione. L'uso di dati di riferimento di grandi dimensioni può influito sulle prestazioni del processo. Man mano che aumenta la complessità della query per includere l'elaborazione con informazioni sullo stato, ad esempio aggregazioni finestra, join temporali e funzioni di analisi temporale, è previsto che la dimensione massima supportata dei dati di riferimento diminuisca. Se Analisi di flusso di Azure non può caricare i dati di riferimento ed eseguire operazioni complesse, il processo esaurisce la memoria e ha esito negativo. In questi casi, la metrica di utilizzo in percentuale dell'unità di streaming raggiungerà il 100%.    

|**Numero di unità di streaming**  |**Dimensioni consigliate**  |
|---------|---------|
|1   |50 MB o inferiore   |
|3   |150 MB o inferiore   |
|6 e oltre   |300 MB o inferiore. L'uso di dati di riferimento maggiori di 300 MB è supportato in anteprima e può influito sulle prestazioni del processo.    |

Il supporto per la compressione non è disponibile per i dati di riferimento.

## <a name="joining-multiple-reference-datasets-in-a-job"></a>Unione di più set di impostazioni di riferimento in un processo
È possibile unire in join un solo input di flusso con un input dei dati di riferimento in un unico passaggio della query. È tuttavia possibile unire più set di impostazioni di riferimento suddividendo la query in più passaggi. Di seguito è illustrato un esempio.

```SQL  
With Step1 as (
    --JOIN input stream with reference data to get 'Desc'
    SELECT streamInput.*, refData1.Desc as Desc
    FROM    streamInput
    JOIN    refData1 ON refData1.key = streamInput.key 
)
--Now Join Step1 with second reference data
SELECT *
INTO    output 
FROM    Step1
JOIN    refData2 ON refData2.Desc = Step1.Desc 
``` 

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Avvio rapido: Creare un processo di Analisi di flusso tramite il portale di Azure](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
