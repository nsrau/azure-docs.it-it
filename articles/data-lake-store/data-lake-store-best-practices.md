---
title: Procedure consigliate per l'uso di Azure Data Lake Store | Microsoft Docs
description: Informazioni sulle procedure consigliate per l'inserimento dati, la sicurezza dei dati e le prestazioni in relazione all'uso di Azure Data Lake Store
services: data-lake-store
documentationcenter: ''
author: sachinsbigdata
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/02/2018
ms.author: sachins
ms.openlocfilehash: c394142ba40fc580bdcec11430dcae2816fa9760
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="best-practices-for-using-azure-data-lake-store"></a>Procedure consigliate per l'uso di Azure Data Lake Store
Questo articolo illustra le procedure consigliate e alcune considerazioni per l'uso di Azure Data Lake Store. L'articolo fornisce informazioni su sicurezza, prestazioni, resilienza e monitoraggio per Data Lake Store. Prima di Data Lake Store, l'uso di Big Data in servizi come Azure HDInsight era un'operazione complessa. Era necessario partizionare i dati tra più account di archiviazione BLOB, per ottenere spazio di archiviazione di petabyte e prestazioni ottimali su tale scala. Data Lake Store rimuove la maggior parte dei limiti assoluti relativi a dimensioni e prestazioni. Ci sono tuttavia alcune considerazioni illustrate in questo articolo che aiutano a ottenere prestazioni ottimali con Data Lake Store. 

## <a name="security-considerations"></a>Considerazioni relative alla sicurezza

Azure Data Lake Store offre controlli di accesso POSIX e controlli dettagliati per utenti, gruppi ed entità servizio di Azure Active Directory (Azure AD). Questi controlli di accesso possono essere impostati su file e cartelle esistenti. I controlli di accesso possono essere usati anche per creare valori predefiniti che possono essere applicati a nuovi file o cartelle. Quando vengono impostate le autorizzazioni sulle cartelle esistenti e sugli oggetti figlio, è necessario propagarle in modo ricorsivo a ogni oggetto. Se il numero di file è elevato, la propagazione delle autorizzazioni può richiedere molto tempo. Il tempo necessario può variare tra i 30 e i 50 oggetti elaborati al secondo. Pianificare quindi in modo appropriato la struttura di cartelle e i gruppi di utenti. In caso contrario, possono verificarsi problemi e ritardi imprevisti quando si lavora con i dati. 

Si supponga di avere una cartella con 100.000 oggetti figlio. Presupponendo il limite inferiore di 30 oggetti elaborati al secondo, per aggiornare l'autorizzazione per l'intera cartella può essere necessaria un'ora. Altre informazioni sugli elenchi di controllo di accesso di Data Lake Store sono disponibili in [Controllo di accesso in Azure Data Lake Store](data-lake-store-access-control.md). Per migliorare le prestazioni per l'assegnazione ricorsiva di elenchi di controllo di accesso, è possibile usare lo strumento da riga di comando di Azure Data Lake. Lo strumento crea più thread e logica di navigazione ricorsiva per applicare rapidamente gli elenchi di controllo di accesso a milioni di file. Lo strumento è disponibile per Linux e Windows e la [documentazione](https://github.com/Azure/data-lake-adlstool) e i [download](http://aka.ms/adlstool-download) per questo strumento sono disponibili in GitHub.

### <a name="use-security-groups-versus-individual-users"></a>Confronto tra l'uso di gruppi di sicurezza e di singoli utenti 

Quando si usano Big Data in Data Lake Store, si usa probabilmente un'entità servizio per consentire ai servizi, ad esempio Azure HDInsight, di usare i dati. In alcuni casi, tuttavia, singoli utenti potrebbero dover accedere ai dati. In questi casi, è necessario usare i gruppi di sicurezza di Azure Active Directory invece di assegnare singoli utenti a file e cartelle. Una volta assegnate le autorizzazioni a un gruppo di sicurezza, per l'aggiunta o la rimozione di utenti dal gruppo non sono necessari aggiornamenti a Data Lake Store. 

### <a name="security-for-groups"></a>Sicurezza per i gruppi 

Come spiegato, quando gli utenti devono accedere a Data Lake Store, è preferibile usare gruppi di sicurezza di Azure Active Directory. Alcuni gruppi consigliati per iniziare sono **ReadOnlyUsers**, **WriteAccessUsers** e **FullAccessUsers** per la radice dell'account e altri separati per le principali sottocartelle. Se si prevede di aggiungere, in seguito, altri gruppi di utenti che ancora non sono stati identificati, prendere in considerazione la creazione di gruppi di sicurezza fittizi che hanno accesso a determinate cartelle. L'uso di gruppi di sicurezza garantisce che in un secondo momento non saranno necessari tempi di elaborazione lunghi per l'assegnazione di nuove autorizzazioni a migliaia di file. 

### <a name="security-for-service-principals"></a>Sicurezza per le entità servizio 

Le entità servizio di Azure Active Directory vengono in genere usate dai servizi come Azure HDInsight per accedere ai dati in Data Lake Store. A seconda dei requisiti di accesso tra più carichi di lavoro, potrebbero esserci alcune considerazioni per garantire la sicurezza all'interno e all'esterno dell'organizzazione. Per molti clienti, potrebbe essere appropriata una singola entità servizio di Azure Active Directory, con autorizzazioni complete nella radice di Data Lake Store. Per altri clienti potrebbero essere necessari più cluster con entità servizio diverse, con un cluster con accesso completo ai dati e un altro cluster con accesso di sola lettura. Come nel caso dei gruppi di sicurezza, prendere in considerazione la creazione di un'entità servizio per ogni scenario previsto (lettura, scrittura, completo) dopo la creazione di un account Data Lake Store. 

### <a name="enable-the-data-lake-store-firewall-with-azure-service-access"></a>Abilitare il firewall di Data Lake Store con l'accesso ai servizi di Azure 

Data Lake Store consente di attivare un firewall e limitare l'accesso solo ai servizi di Azure. Questo scenario è consigliato per ridurre il vettore di attacco per le intrusioni esterne. Il firewall può essere abilitato per l'account Data Lake Store nel portale di Azure tramite le opzioni **Firewall** > **Abilita firewall (ATTIVA)** > **Consenti l'accesso a Servizi di Azure**.  

![Impostazioni del firewall in Data Lake Store](./media/data-lake-store-best-practices/data-lake-store-firewall-setting.png "Impostazioni del firewall in Data Lake Store")

Una volta abilitato il firewall, solo i servizi di Azure come HDInsight, Data Factory, SQL Data Warehouse e così via hanno accesso a Data Lake Store. A causa del processo Network Address Translation interno usato da Azure, il firewall di Data Lake Store non supporta la limitazione di servizi specifici in base all'IP ed è pensato solo per limitare gli endpoint esterni ad Azure, ad esempio in locale. 

## <a name="performance-and-scale-considerations"></a>Considerazioni su prestazioni e scalabilità

Una delle funzionalità più potenti di Data Lake Store è la capacità di rimuovere i limiti assoluti relativi alla velocità effettiva dei dati. La rimozione dei limiti consente ai clienti di aumentare le dimensioni dei dati e i requisiti di prestazioni associati senza necessità di partizionare i dati. Una delle considerazioni più importanti per ottimizzare le prestazioni di Data Lake Store riguarda il fatto che le prestazioni ottimali si possono ottenere con il parallelismo.

### <a name="improve-throughput-with-parallelism"></a>Migliorare la velocità effettiva con il parallelismo 

Considerare di assegnare 8-12 thread per core per un velocità effettiva di lettura/scrittura ottimale. Ciò è legato al blocco delle operazioni di lettura e scrittura in un singolo thread. Un numero maggiore di thread può consentire una concorrenza più elevata nella macchina virtuale. Per garantire l'integrità dei livelli e la possibilità di aumentare il parallelismo, assicurarsi di monitorare l'utilizzo della CPU della macchina virtuale.   

### <a name="avoid-small-file-sizes"></a>Evitare file di piccole dimensioni

Il controllo e le autorizzazioni POSIX in Data Lake Store comportano un sovraccarico che diventa evidente quando si usano numerosi file di piccole dimensioni. È consigliabile raggruppare in batch i dati creando file di dimensioni più grandi invece di scrivere migliaia o milioni di piccoli file in Data Lake Store. Evitando i file di piccole dimensioni è possibile ottenere maggiori vantaggi, ad esempio:

* Riduzione dei controlli di autenticazione su più file
* Riduzione delle connessioni aperte ai file
* Maggiore velocità di copia e replica
* Minor numero di file da elaborare quando si aggiornano le autorizzazioni POSIX di Data Lake Store 

A seconda dei servizi e dei carichi di lavoro che usano i dati, un intervallo appropriato da considerare per le dimensioni dei file è tra i 256 MB e 1 GB, idealmente senza scendere sotto i 100 MB o salire sopra i 2 GB. Se le dimensioni dei file non possono essere raggruppate in batch in Data Lake Store, è possibile usare un processo di compattazione separato che combina questi file in file più grandi. Per altre informazioni e indicazioni sulle dimensioni dei file e su come organizzare i dati in Data Lake Store, vedere [Strutturare il set di dati](data-lake-store-performance-tuning-guidance.md#structure-your-data-set). 

### <a name="large-file-sizes-and-potential-performance-impact"></a>File di dimensioni elevate e potenziale impatto sulle prestazioni 

Anche se Data Lake Store supporta file di grandi dimensioni, fino a petabyte, per ottenere prestazioni ottimali e a seconda del processo di lettura dei dati, potrebbe non essere consigliabile superare in media i 2 GB. Quando si usa, ad esempio **Distcp** per copiare i dati tra posizioni o account di archiviazione diversi, i file rappresentano il livello di granularità più fine usato per determinare le attività di mapping. Se quindi si copiano 10 file di 1 TB ciascuno, vengono allocati almeno 10 mapper. Inoltre, se ci sono numerosi file con mapper assegnati, inizialmente i mapper funzionano in parallelo per spostare i file di grandi dimensioni. Tuttavia, quando il processo inizia a ridursi, rimangono allocati solo pochi mapper e si potrebbe rimanere bloccati a causa di un singolo mapper assegnato a un file di grandi dimensioni. Microsoft ha apportato miglioramenti a Distcp per risolvere questo problema nelle versioni future di Hadoop.  

Un altro esempio da considerare è l'uso di Azure Data Lake Analytics con Data Lake Store. A seconda dell'elaborazione eseguita dall'estrattore, per alcuni file che non possono essere divisi (ad esempio i file XML e JSON) si potrebbe riscontrare un peggioramento delle prestazioni in caso di dimensioni superiori a 2 GB. Nei casi in cui i file possono essere divisi da un estrattore (ad esempio i file CSV), è preferibile usare file di grandi dimensioni.

### <a name="capacity-plan-for-your-workload"></a>Pianificazione della capacità per il carico di lavoro 

Azure Data Lake Store rimuove le limitazioni assolute di I/O previste per gli account di archiviazione BLOB. Tuttavia, ci sono comunque alcuni limiti flessibili da considerare. Le limitazioni predefinite ingresso e in uscita soddisfano le esigenze della maggior parte degli scenari. Se per il carico di lavoro è necessario aumentare i limiti, rivolgersi al supporto tecnico Microsoft. Esaminare inoltre i limiti durante la fase di verifica, in modo che le limitazioni di I/O non vengano raggiunte nell'ambiente di produzione. In tal caso, potrebbe essere necessario attendere un aumento manuale da parte del team di tecnici Microsoft. In caso di applicazione delle limitazioni di I/O, Azure Data Lake Store restituisce un codice di errore 429 e idealmente deve essere eseguito un nuovo tentativo con criteri di backoff esponenziali appropriati. 

### <a name="optimize-writes-with-the-data-lake-store-driver-buffer"></a>Ottimizzare le operazioni di scrittura con il buffer di driver di Data Lake Store 

Per ottimizzare le prestazioni e ridurre il numero di operazioni di I/O al secondo durante la scrittura in Data Lake Store da Hadoop, eseguire operazioni di scrittura il più vicino possibile alle dimensioni del buffer di driver di Data Lake Store. Provare a non superare le dimensioni del buffer prima dello scaricamento, ad esempio in caso di streaming tramite Apache Storm o di carichi di lavoro di streaming Spark. Quando si scrivono i dati in Data Lake Store da HDInsight/Hadoop, è importante sapere che Data Lake Store ha un driver con un buffer di 4 MB. Come molti driver del file system, questo buffer può essere scaricato manualmente prima di raggiungere le dimensioni di 4 MB. In caso contrario, viene scaricato immediatamente in un archivio se l'operazione di scrittura successiva supera le dimensioni massime del buffer. Quando possibile, evitare un sovraccarico o un sottocarico significativo del buffer quando si sincronizzano o si scaricano i criteri in base al numero o all'intervallo di tempo.

## <a name="resiliency-considerations"></a>Considerazioni sulla resilienza 

Quando si progetta un sistema con Data Lake Store o un altro servizio cloud, è necessario considerare i requisiti di disponibilità e come rispondere a potenziali interruzioni del servizio. Un problema può riguardare un'istanza specifica o anche un'intera area, quindi è importante essere preparati con un piano per entrambi i casi. A seconda dei contratti di servizio relativi all'**obiettivo del tempo di ripristino** e all'**obiettivo del punto di ripristino** per il carico di lavoro, è possibile scegliere una strategia più o meno aggressiva per la disponibilità elevata e il ripristino di emergenza.

### <a name="high-availability-and-disaster-recovery"></a>Disponibilità elevata e ripristino di emergenza 

La disponibilità elevata e il ripristino di emergenza possono talvolta essere combinati, anche se ognuno prevede una strategia leggermente diversa, in particolare quando si tratta di dati. Data Lake Store esegue la replica 3x in background, per garantire protezione in caso di errori hardware localizzati. Tuttavia, poiché la replica tra aree non viene eseguita per impostazione predefinita, è necessario gestirla manualmente. Quando si prepara un piano per la disponibilità elevata, in caso di interruzione del servizio il carico di lavoro deve poter accedere ai dati più recenti nel più breve tempo possibile, passando a un'istanza replicata separatamente in locale o in una nuova area.  

In una strategia di ripristino di emergenza, per prepararsi per l'improbabile caso di un errore irreversibile di un'area, è anche importante replicare i dati in un'area diversa. Questi dati possono inizialmente corrispondere ai dati per la disponibilità elevata replicati. Tuttavia, è anche necessario considerare i requisiti per i casi limite, ad esempio il danneggiamento dei dati, per cui potrebbe essere utile creare snapshot periodici a cui eseguire il fallback. A seconda dell'importanza e delle dimensioni dei dati, prendere in considerazione snapshot delta incrementali in periodi di 1, 6 e 24 ore nell'archivio locale e/o secondario, in base alla tolleranza di rischio. 

Per garantire la resilienza dei dati con Data Lake Store, è consigliabile abilitare la replica geografica dei dati in un'area separata con una frequenza in grado di soddisfare i requisiti di disponibilità elevata e ripristino di emergenza, idealmente ogni ora. Questa frequenza di replica consente di ridurre al minimo i movimenti di grandi quantità di dati che possono avere esigenze di velocità effettiva concorrenti con il sistema principale e garantisce un obiettivo del punto di ripristino migliore. Considerare inoltre i modi in cui l'applicazione che usa Data Lake Store può eseguire il failover automatico nell'account secondario tramite il monitoraggio di trigger o della durata dei tentativi non riusciti o almeno inviare una notifica agli amministratori per l'intervento manuale. Tenere presente che è necessario trovare il giusto compromesso tra il failover e l'attesa che un servizio torni online. Se la replica dei dati non è stata completata, un failover potrebbe causare una potenziale perdita, incoerenza o unione complessa dei dati. 

Di seguito sono illustrate le tre principali opzioni consigliate per l'orchestrazione della replica tra gli account Data Lake Store e le principali differenze tra di esse.


|  |Distcp  |Data factory di Azure  |AdlCopy  |
|---------|---------|---------|---------|
|**Limiti di scalabilità**     | Limiti definiti dai nodi di lavoro        | Limiti definiti dal numero massimo di unità di spostamento dei dati cloud        | Limiti definiti dalle unità di analisi        |
|**Supporto per la copia delta**     |   Sì      | No          | No          |
|**Orchestrazione predefinita**     |  No (usare i processi CRON o Oozie Airflow)       | Sì        | No (usare Automazione di Azure o Utilità di pianificazione di Windows)         |
|**File system supportati**     | ADL, HDFS, WASB, S3, GS, CFS        |Numerosi, vedere [Connettori](../data-factory/connector-azure-blob-storage.md).         | Da ADL ad ADL, da WASB ad ADL (solo nella stessa area)        |
|**Supporto del sistema operativo**     |Qualsiasi sistema operativo che esegue Hadoop         | N/D          | Windows 10         |
   

### <a name="use-distcp-for-data-movement-between-two-locations"></a>Usare Distcp per lo spostamento dei dati tra due posizioni 

Distcp, abbreviazione di distributed copy (copia distribuita) è uno strumento da riga di comando Linux fornito con Hadoop che consente lo spostamento di dati distribuiti tra due posizioni. Le due posizioni possono essere Data Lake Store, HDFS, WASB o S3. Questo strumento usa i processi MapReduce in un cluster Hadoop (ad esempio, HDInsight) per la scalabilità orizzontale in tutti i nodi. Distcp è considerato il modo più rapido per spostare i Big Data senza appliance di compressione di rete speciali. Distcp fornisce anche un'opzione per aggiornare solo i delta tra due posizioni e gestisce la ripetizione automatica dei tentativi e la scalabilità dinamica delle risorse di calcolo. Questo approccio è estremamente efficiente per la replica di oggetti come tabelle Hive/Spark che possono avere molti file di grandi dimensioni in una singola directory, quando si vuole eseguire la copia solo dei dati modificati. Per questi motivi, Distcp è lo strumento più consigliato per la copia di dati tra archivi di Big Data. 

I processi di copia possono essere attivati dai flussi di lavoro di Apache Oozie usando trigger di dati o frequenza, oltre che da processi CRON Linux. Per i processi di replica intensivi, è consigliabile attivare un cluster Hadoop HDInsight separato, che può essere ottimizzato e ridimensionato in modo specifico per i processi di copia. In questo modo, i processi di copia non interferiranno con i processi critici. Se la replica è in esecuzione in base a una frequenza sufficientemente ampia, è anche possibile disattivare il cluster tra ogni processo. In caso di failover in un'area secondaria, assicurarsi che venga attivato anche un altro cluster nell'area secondaria, per eseguire la replica dei nuovi dati di nuovo nell'account Data Lake Store primario, quando torna disponibile. Per esempi di uso di Distcp, vedere [Usare Distcp per copiare dati tra i BLOB di archiviazione di Azure e Data Lake Store](data-lake-store-copy-data-wasb-distcp.md).

### <a name="use-azure-data-factory-to-schedule-copy-jobs"></a>Usare Azure Data Factory per pianificare i processi di copia 

È anche possibile usare Azure Data Factory per pianificare i processi di copia tramite un'**attività di copia**, oltre che impostare una frequenza tramite la **copia guidata**. Tenere presente che Azure Data Factory prevede un limite di unità di spostamento dei dati cloud (DMU) e applica limiti relativi a velocità effettiva e calcolo per i carichi di lavoro con dati di grandi dimensioni. Inoltre, Azure Data Factory attualmente non consente gli aggiornamenti delta tra account Data Lake Store, quindi le cartelle come le tabelle Hive richiedono una copia completa per la replica. Per altre informazioni sulla copia con Data Factory, vedere [Guida alle prestazioni dell'attività di copia e all'ottimizzazione](../data-factory/v1/data-factory-copy-activity-performance.md). 

### <a name="adlcopy"></a>AdlCopy

AdlCopy è uno strumento da riga di comando di Windows che consente di copiare i dati tra due account Data Lake Store solo all'interno della stessa area. Lo strumento AdlCopy offre un'opzione autonoma oppure la possibilità di usare un account Azure Data Lake Analytics per eseguire il processo di copia. Anche se questo strumento è stato originariamente creato per le copie on demand e non per attività di replica ad alta affidabilità, offre un'altra opzione per l'esecuzione di copie distribuite tra account Data Lake Store nella stessa area. Per una maggiore affidabilità, è consigliabile usare l'opzione Premium di Data Lake Analytics per i carichi di lavoro di produzione. La versione autonoma può restituire risposte di risorse non disponibili e offre funzionalità di monitoraggio e scalabilità limitate. 

Come Distcp, AdlCopy richiede l'orchestrazione tramite una soluzione come Automazione di Azure o Utilità di pianificazione di Windows. Come per Data Factory, AdlCopy non supporta la copia solo dei file aggiornati, ma esegue una nuova copia sovrascrivendo i file esistenti. Per altre informazioni e per esempi relativi all'uso di AdlCopy, vedere [Copiare i dati da BLOB di Archiviazione di Azure a Data Lake Store](data-lake-store-copy-data-azure-storage-blob.md).

## <a name="monitoring-considerations"></a>Considerazioni sul monitoraggio 

Data Lake Store fornisce funzionalità di controllo e log di diagnostica dettagliati. Data Lake Store fornisce alcune metriche di base nel portale di Azure, nelle sezioni Account Data Lake Store e Monitoraggio di Azure. La disponibilità di Data Lake Store è visualizzata nel portale di Azure. Questa metrica viene tuttavia aggiornata ogni sette minuti e non è possibile eseguire query tramite un'API esposta pubblicamente. Per ottenere la disponibilità più aggiornata di un account Data Lake Store, è necessario eseguire test sintetici per convalidare la disponibilità. Per l'aggiornamento di altre metriche, come quelle relative a utilizzo totale dello spazio di archiviazione, richieste di lettura/scrittura e ingresso/uscita, possono essere necessarie fino a 24 ore. Le metriche più aggiornate devono quindi essere calcolate manualmente tramite gli strumenti da riga di comando di Hadoop o aggregando le informazioni di log. Il modo più rapido per ottenere i dati più recenti relativi all'utilizzo dello spazio di archiviazione consiste nell'eseguire questo comando HDFS da un nodo del cluster Hadoop (ad esempio, il nodo head):   

    hdfs dfs -du -s -h adl://<adls_account_name>.azuredatalakestore.net:443/

### <a name="export-data-lake-store-diagnostics"></a>Esportare i dati di diagnostica di Data Lake Store 

Uno dei modi più rapidi per accedere a log in cui è possibile eseguire ricerche da Data Lake Store è quello di abilitare il log shipping in **Operations Management Suite (OMS)** nel pannello **Diagnostica** per l'account Data Lake Store. Ciò fornisce accesso immediato ai log in ingresso, con filtri temporali e di contenuti, oltre che opzioni di avviso (posta elettronica/webhook) attivate a intervalli di 15 minuti. Per informazioni, vedere [Accesso ai log di diagnostica per Azure Data Lake Store](data-lake-store-diagnostic-logs.md). 

Per avvisi più in tempo reale e per un maggiore controllo sulla destinazione dei log, è possibile esportare i log in Hub eventi di Azure, dove il contenuto può essere analizzato singolarmente o per un intervallo di tempo, per inviare notifiche in tempo reale a una coda. Un'applicazione distinta come un'[app per la logica](../connectors/connectors-create-api-azure-event-hubs.md) può quindi utilizzare gli avvisi e comunicarli al canale appropriato, oltre che inviare le metriche a strumenti di monitoraggio come NewRelic, Datadog o AppDynamics. In alternativa, se si usa uno strumento di terze parti, ad esempio ElasticSearch, è possibile esportare i log in Archiviazione BLOB di Azure e usare il [plug-in Azure Logstash](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob) per utilizzare i dati in stack Elasticsearch, Kibana e Logstash (ELK).

### <a name="turn-on-debug-level-logging-in-hdinsight"></a>Attivare la registrazione a livello di debug in HDInsight 

Se il log shipping di Data Lake Store non è attivato, Azure HDInsight consente anche di attivare la [registrazione sul lato client per Data Lake Store](data-lake-store-performance-tuning-mapreduce.md) tramite log4j. È necessario impostare la proprietà seguente in **Ambari** > **YARN** > **Config (Configurazione)** > **Advanced yarn-log4j configurations (Configurazioni yarn-log4j avanzate)**: 

    log4j.logger.com.microsoft.azure.datalake.store=DEBUG 

Dopo che la proprietà è stata impostata e i nodi sono stati riavviati, i dati di diagnostica di Data Lake Store vengono scritti nei log YARN nei nodi (/tmp/<user>/yarn.log) ed è possibile monitorare i dettagli importanti, come gli errori o le limitazioni (codice errore HTTP 429). Le stesse informazioni possono essere monitorate anche in OMS o in qualunque strumento che consenta il log shipping nel pannello [Diagnostica](data-lake-store-diagnostic-logs.md) dell'account Data Lake Store. È consigliabile attivare almeno la registrazione sul lato client oppure usare l'opzione di log shipping con Data Lake Store per ottenere visibilità operativa e semplificare il debug.

### <a name="run-synthetic-transactions"></a>Eseguire transazioni sintetiche 

Attualmente, la metrica di disponibilità del servizio per Data Lake Store nel portale di Azure prevede una finestra di aggiornamento di 7 minuti. Non è inoltre possibile eseguire query usando un'API esposta pubblicamente. È quindi consigliabile creare un'applicazione di base che esegue transazioni sintetiche in Data Lake Store, in grado di fornire disponibilità immediata. È ad esempio possibile creare un processo Web, un'app per la logica o un'app per le funzioni di Azure per eseguire un'operazione di lettura, creazione e aggiornamento in Data Lake Store e inviare i risultati alla soluzione di monitoraggio. Le operazioni possono essere eseguite in una cartella temporanea e quindi eliminate dopo i test, che è possibile eseguire ogni 30-60 secondi, a seconda dei requisiti.

## <a name="directory-layout-considerations"></a>Considerazioni sul layout di directory

Quando si trasmettono i dati in un Data Lake, è importante pianificare in anticipo la struttura dei dati in modo da ottimizzare la sicurezza, il partizionamento e l'elaborazione. Molti dei consigli seguenti sono applicabili sia ad Azure Data Lake Store che ad Archiviazione BLOB o a HDFS (Hadoop Distributed File System). Ogni carico di lavoro ha requisiti diversi relativi all'utilizzo dei dati, ma di seguito sono illustrati alcuni layout comuni da prendere in considerazione per scenari batch e IoT.

### <a name="iot-structure"></a>Struttura IoT 

Nei carichi di lavoro IoT può esserci una notevole quantità di dati trasmessa in un archivio dati che si estende tra numerosi prodotti, dispositivi, organizzazioni e clienti. È importante pianificare in anticipo il layout di directory per l'organizzazione, la sicurezza e l'elaborazione efficiente dei dati per i consumer downstream. Un modello generale da prendere in considerazione può essere il layout seguente: 

    {Region}/{SubjectMatter(s)}/{yyyy}/{mm}/{dd}/{hh}/ 

I dati di telemetria per gli atterraggi relativi a un motore di un aereo nel Regno Unito potrebbero ad esempio essere simili alla struttura seguente: 

    UK/Planes/BA1293/Engine1/2017/08/11/12/ 

C'è un motivo importante per inserire la data alla fine della struttura di cartelle. Se si vuole bloccare alcuni domini o aree per utenti o gruppi specifici, è possibile farlo facilmente con le autorizzazioni POSIX. In caso contrario, se fosse necessario limitare, per un determinato gruppo di sicurezza, la visualizzazione solo ai dati per il Regno Unito o per alcuni aerei, con la struttura di data all'inizio sarebbe necessaria un'autorizzazione separata per le numerose cartelle nella cartella relativa a ogni ora. La struttura di data all'inizio comporta inoltre un aumento esponenziale del numero di cartelle con il passare del tempo.

### <a name="batch-jobs-structure"></a>Struttura dei processi batch 

Da un punto di vista generale, un approccio comune nell'elaborazione batch consiste nel trasmette i dati a una cartella "in ingresso". Una volta che i dati sono stati elaborati, è quindi possibile inserire i nuovi dati in una cartella "in uscita" per l'uso da parte dei processi downstream. Questa struttura di directory viene talvolta usata per i processi che richiedono l'elaborazione sui singoli file e potrebbero non richiedere l'elaborazione parallela elevata (Massively Parallel Processing, MPP) su set di dati di grandi dimensioni. Analogamente alla struttura IoT consigliata in precedenza, una buona struttura di directory ha cartelle di livello padre per elementi come le aree e gli ambiti (ad esempio organizzazione, prodotto/produttore). Questa struttura consente di proteggere i dati nell'organizzazione e di gestirli meglio nei carichi di lavoro. Considerare inoltre data e ora nella struttura per consentire una migliore organizzazione, ricerche con filtri, sicurezza e automazione nell'elaborazione. Il livello di granularità per la struttura di data è determinato dall'intervallo con cui i dati vengono caricati o elaborati, ad esempio ogni ora, ogni giorno o anche ogni mese. 

Talvolta l'elaborazione di file ha esito negativo a causa del danneggiamento dei dati o di formati imprevisti. In questi casi, può essere utile la presenza di una cartella **/bad** nella struttura di directory, dove spostare i file per un'ulteriore analisi. Il processo batch può anche gestire la creazione di report o le notifiche per i file nella cartella *bad*, per gli interventi manuali. Si consideri la struttura di modelli seguente: 

    {Region}/{SubjectMatter(s)}/In/{yyyy}/{mm}/{dd}/{hh}/ 
    {Region}/{SubjectMatter(s)}/Out/{yyyy}/{mm}/{dd}/{hh}/ 
    {Region}/{SubjectMatter(s)}/Bad/{yyyy}/{mm}/{dd}/{hh}/ 

Una società di marketing riceve ad esempio estratti giornalieri di dati degli aggiornamenti dei clienti dai committenti in America del Nord, che prima e dopo l'elaborazione possono essere come il frammento seguente: 

    NA/Extracts/ACMEPaperCo/In/2017/08/14/updates_08142017.csv 
    NA/Extracts/ACMEPaperCo/Out/2017/08/14/processed_updates_08142017.csv 
 
Nel caso comune di dati batch elaborati direttamente in database, come Hive o i database SQL tradizionali, non è necessaria una cartella **/in** o **/out** perché l'output viene già inserito in una cartella separata per la tabella Hive o un database esterno. Gli estratti giornalieri dei clienti vengono ad esempio inviati nelle rispettive cartelle e l'orchestrazione tramite una soluzione come Azure Data Factory, Apache Oozie o Apache Airflow attiva un processo Hive o Spark giornaliero per elaborare e scrivere i dati in una tabella Hive.

## <a name="next-steps"></a>Passaggi successivi     

* [Panoramica di Archivio Data Lake di Azure](data-lake-store-overview.md) 
* [Controllo di accesso in Azure Data Lake Store](data-lake-store-access-control.md) 
* [Sicurezza in Azure Data Lake Store](data-lake-store-security-overview.md)
* [Ottimizzazione delle prestazioni di Azure Data Lake Store](data-lake-store-performance-tuning-guidance.md)
* [Linee guida per l'ottimizzazione delle prestazioni di Spark in HDInsight e di Azure Data Lake Store](data-lake-store-performance-tuning-spark.md)
* [Linee guida per l'ottimizzazione delle prestazioni di Hive in HDInsight e di Azure Data Lake Store](data-lake-store-performance-tuning-hive.md)
* [Orchestrazione di dati con Azure Data Factory per Archivio Azure Data Lake](https://mix.office.com/watch/1oa7le7t2u4ka)
* [Creare cluster HDInsight con Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md) 
