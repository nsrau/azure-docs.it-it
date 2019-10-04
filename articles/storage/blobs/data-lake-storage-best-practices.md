---
title: Procedure consigliate per l'uso di Azure Data Lake Storage Gen2 | Microsoft Docs
description: Informazioni sulle procedure consigliate per l'inserimento dati, la sicurezza dei dati e le prestazioni in relazione all'uso di Azure Data Lake Storage Gen2, noto in precedenza come Azure Data Lake Store
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.reviewer: sachins
ms.openlocfilehash: 1f1db1c347709ed7c8587ed8b5523a231e373999
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/23/2019
ms.locfileid: "69991880"
---
# <a name="best-practices-for-using-azure-data-lake-storage-gen2"></a>Procedure consigliate per l'uso di Azure Data Lake Storage Gen2

Questo articolo illustra le procedure consigliate e alcune considerazioni sul funzionamento di Azure Data Lake Storage Gen2. L'articolo fornisce informazioni su sicurezza, prestazioni, resilienza e monitoraggio per Data Lake Storage Gen2. Prima di Data Lake Storage Gen2, l'uso di Big Data in servizi come Azure HDInsight era un'operazione complessa. Era necessario partizionare i dati tra più account di archiviazione BLOB, per ottenere spazio di archiviazione di petabyte e prestazioni ottimali su tale scala. Data Lake Storage Gen2 elimina la maggior parte dei limiti assoluti relativi a dimensioni e prestazioni. Tuttavia, in questo articolo sono comunque illustrate alcune considerazioni che aiutano a ottenere prestazioni ottimali con Data Lake Storage Gen2.

## <a name="security-considerations"></a>Considerazioni relative alla sicurezza

Azure Data Lake Storage Gen2 offre controlli di accesso POSIX per utenti, gruppi ed entità servizio di Azure Active Directory (Azure AD). Questi controlli di accesso possono essere impostati su file e directory esistenti. I controlli di accesso possono essere usati anche per creare autorizzazioni predefinite che possono essere applicate automaticamente a nuovi file o directory. Altri dettagli sugli elenchi di controllo di accesso di Data Lake Storage Gen2 sono disponibili in [Controllo di accesso di Azure Data Lake Storage Gen2](storage-data-lake-storage-access-control.md).

### <a name="use-security-groups-versus-individual-users"></a>Confronto tra l'uso di gruppi di sicurezza e di singoli utenti

Quando si lavora con Big Data Data Lake Storage Gen2, è probabile che venga usata un'entità servizio per consentire ai servizi, ad esempio Azure HDInsight, di funzionare con i dati. In alcuni casi, tuttavia, singoli utenti potrebbero dover accedere ai dati. In questi casi è assolutamente consigliabile usare i [gruppi di sicurezza](../common/storage-auth-aad.md) di Azure Active Directory invece di assegnare singoli utenti a file e directory.

Una volta assegnate le autorizzazioni a un gruppo di sicurezza, per aggiungere o eliminare utenti dal gruppo non sono necessari aggiornamenti a Data Lake Storage Gen2. Ciò consente inoltre di non superare il numero massimo di voci di controllo di accesso per ogni elenco di controllo di accesso. Questo numero è attualmente 32, compresi i quattro elenchi di controllo di accesso in stile POSIX che sono sempre associati a ogni file e directory: l'utente proprietario, il gruppo proprietario, la maschera e altro. Ogni directory può avere due tipi di elenco di controllo di accesso (ACL), l'ACL di accesso e l'ACL predefinito, per un totale di 64 voci di controllo di accesso. Per altre informazioni sugli elenchi di controllo di accesso, vedere [Access control in Azure Data Lake Storage Gen2](data-lake-storage-access-control.md) (Controllo di accesso in Azure Data Lake Storage Gen2).

### <a name="security-for-groups"></a>Sicurezza per i gruppi

Quando gli utenti devono accedere ai dati in un account di archiviazione con spazio dei nomi gerarchico abilitato, è consigliabile usare i gruppi di sicurezza di Azure Active Directory. Alcuni gruppi consigliati per iniziare possono essere **ReadOnlyUsers**, **WriteAccessUsers**e **FullAccessUsers** per la radice del contenitore e persino separare quelli per le sottodirectory principali. Se si prevede di aggiungere, in seguito, altri gruppi di utenti che ancora non sono stati identificati, prendere in considerazione la creazione di gruppi di sicurezza fittizi che hanno accesso a determinate cartelle. L'uso di gruppi di sicurezza consente di evitare tempi di elaborazione lunghi per l'assegnazione di nuove autorizzazioni a migliaia di file.

### <a name="security-for-service-principals"></a>Sicurezza per le entità servizio

Le entità servizio di Azure Active Directory vengono in genere usate dai servizi come Azure Databricks per accedere ai dati in Data Lake Storage Gen2. Per molti clienti, una singola entità servizio Azure Active Directory può essere adeguata e può disporre di autorizzazioni complete alla radice del contenitore di Data Lake Storage Gen2. Per altri clienti potrebbero essere necessari più cluster con entità servizio diverse, con un cluster con accesso completo ai dati e un altro cluster con accesso di sola lettura. 

### <a name="enable-the-data-lake-storage-gen2-firewall-with-azure-service-access"></a>Abilitare il firewall di Data Lake Storage Gen2 con l'accesso ai servizi di Azure

Data Lake Storage Gen2 consente di attivare un firewall e limitare l'accesso solo ai servizi di Azure. Questo scenario è consigliato per limitare il vettore di attacchi esterni. Il firewall può essere abilitato per l'account di archiviazione nel portale di Azure tramite le opzioni **Firewall** > **Abilita firewall (ATTIVA)**  > **Consenti l'accesso a Servizi di Azure**.

Per accedere all'account di archiviazione da Azure Databricks, distribuire Azure Databricks alla rete virtuale e quindi aggiungere la rete virtuale al firewall. Vedere [configurare i firewall e le reti virtuali di archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-network-security).

## <a name="resiliency-considerations"></a>Considerazioni sulla resilienza

Quando si progetta un sistema con Data Lake Storage Gen2 o con qualsiasi servizio cloud, è necessario considerare i requisiti di disponibilità e come rispondere a potenziali interruzioni del servizio. Un problema può riguardare un'istanza specifica o anche un'intera area, quindi è importante essere preparati con un piano per entrambi i casi. A seconda dei contratti di servizio relativi all'obiettivo del tempo di ripristino e all'obiettivo del punto di ripristino per il carico di lavoro, è possibile scegliere una strategia più o meno aggressiva per la disponibilità elevata e il ripristino di emergenza.

### <a name="high-availability-and-disaster-recovery"></a>Disponibilità elevata e ripristino di emergenza

La disponibilità elevata e il ripristino di emergenza possono talvolta essere combinati, anche se ognuno prevede una strategia leggermente diversa, in particolare quando si tratta di dati. Data Lake Storage Gen2 esegue la replica 3 volte in background per garantire protezione in caso di errori hardware localizzati. Inoltre, altre opzioni di replica, ad esempio ZRS o GZRS (anteprima), migliorano la disponibilità elevata, mentre GRS & RA-GRS migliora il ripristino di emergenza. Quando si prepara un piano per la disponibilità elevata, in caso di interruzione del servizio il carico di lavoro deve poter accedere ai dati più recenti nel più breve tempo possibile, passando a un'istanza replicata separatamente in locale o in una nuova area.

In una strategia di ripristino di emergenza, per prepararsi all'improbabile eventualità di un errore irreversibile di un'area è anche importante replicare i dati in un'area diversa con l'archiviazione con ridondanza geografica e l'archiviazione con ridondanza geografica e accesso in lettura. È anche necessario considerare i requisiti per i casi limite, ad esempio il danneggiamento dei dati, per cui potrebbe essere utile creare snapshot periodici a cui eseguire il fallback. A seconda dell'importanza e delle dimensioni dei dati, prendere in considerazione snapshot delta in sequenza in periodi di 1, 6 e 24 ore, in base alla tolleranza di rischio.

Per la resilienza dei dati con Data Lake Storage Gen2, è consigliabile la replica geografica dei dati tramite un'archiviazione con ridondanza geografica o un'archiviazione con ridondanza geografica e accesso in lettura che soddisfi i requisiti di disponibilità elevata e ripristino di emergenza. Considerare anche i modi in cui l'applicazione che usa Data Lake Storage Gen2 può eseguire il failover automatico nell'area secondaria tramite il monitoraggio dei trigger o della durata dei tentativi non riusciti o almeno inviare una notifica agli amministratori per l'intervento manuale. Tenere presente che è necessario trovare il giusto compromesso tra il failover e l'attesa che un servizio torni online.

### <a name="use-distcp-for-data-movement-between-two-locations"></a>Usare Distcp per lo spostamento dei dati tra due posizioni

Distcp, abbreviazione di distributed copy (copia distribuita) è uno strumento da riga di comando Linux fornito con Hadoop che consente lo spostamento di dati distribuiti tra due posizioni. Le due posizioni possono essere Data Lake Storage Gen2, Hadoop Distributed File System o S3. Questo strumento usa i processi MapReduce in un cluster Hadoop (ad esempio, HDInsight) per la scalabilità orizzontale in tutti i nodi. Distcp è considerato il modo più rapido per spostare i Big Data senza appliance di compressione di rete speciali. Distcp fornisce anche un'opzione per aggiornare solo i delta tra due posizioni e gestisce la ripetizione automatica dei tentativi e la scalabilità dinamica delle risorse di calcolo. Questo approccio è estremamente efficiente per la replica di oggetti come tabelle Hive/Spark che possono avere molti file di grandi dimensioni in una singola directory, quando si vuole eseguire la copia solo dei dati modificati. Per questi motivi, Distcp è lo strumento più consigliato per la copia di dati tra archivi di Big Data.

I processi di copia possono essere attivati dai flussi di lavoro di Apache Oozie usando trigger di dati o frequenza, oltre che da processi CRON Linux. Per i processi di replica intensivi, è consigliabile attivare un cluster Hadoop HDInsight separato, che può essere ottimizzato e ridimensionato in modo specifico per i processi di copia. In questo modo, i processi di copia non interferiranno con i processi critici. Se la replica è in esecuzione in base a una frequenza sufficientemente ampia, è anche possibile disattivare il cluster tra ogni processo. In caso di failover in un'area secondaria, assicurarsi che venga attivato anche un altro cluster nell'area secondaria, per eseguire la replica dei nuovi dati di nuovo nell'account Data Lake Storage Gen2 primario, quando è nuovamente disponibile. Per esempi di uso di Distcp, vedere [Usare Distcp per copiare dati tra i BLOB del servizio di archiviazione di Azure e Data Lake Storage Gen2](../blobs/data-lake-storage-use-distcp.md).

### <a name="use-azure-data-factory-to-schedule-copy-jobs"></a>Usare Azure Data Factory per pianificare i processi di copia

È anche possibile usare Azure Data Factory per pianificare i processi di copia tramite un'attività di copia, oltre a poter impostare una frequenza tramite la copia guidata. Tenere presente che Azure Data Factory prevede un limite di unità di spostamento dei dati cloud (DMU) e applica limiti relativi a velocità effettiva e calcolo per i carichi di lavoro con dati di grandi dimensioni. Azure Data Factory attualmente non consente gli aggiornamenti delta tra account Data Lake Storage Gen2, quindi directory quali le tabelle Hive richiedono una copia completa per la replica. Per altre informazioni sulla copia con Data Factory, vedere l'[articolo su Data Factory](../../data-factory/load-azure-data-lake-storage-gen2.md).

## <a name="monitoring-considerations"></a>Considerazioni sul monitoraggio

Data Lake Storage Gen2 fornisce metriche nel portale di Azure nella sezione dell'account Data Lake Storage Gen2 e in Monitoraggio di Azure. La disponibilità di Data Lake Storage Gen2 è visualizzata nel portale di Azure. Per ottenere la disponibilità aggiornata di un account Data Lake Storage Gen2, è necessario eseguire test sintetici per convalidare la disponibilità. Altre metriche come utilizzo totale dell'archiviazione, richieste di lettura/scrittura e ingresso/uscita sono disponibili per le applicazioni di monitoraggio e possono anche generare avvisi quando vengono superate le soglie, ad esempio la latenza media o il numero di errori al minuto.

## <a name="directory-layout-considerations"></a>Considerazioni sul layout di directory

Quando si trasmettono i dati in un Data Lake, è importante pianificare in anticipo la struttura dei dati in modo da ottimizzare la sicurezza, il partizionamento e l'elaborazione. Molte delle raccomandazioni seguenti sono applicabili a tutti i carichi di lavoro di Big Data. Ogni carico di lavoro ha requisiti diversi relativi all'utilizzo dei dati, ma di seguito sono illustrati alcuni layout comuni da prendere in considerazione per scenari batch e IoT.

### <a name="iot-structure"></a>Struttura IoT

Nei carichi di lavoro IoT può esserci una notevole quantità di dati trasmessa in un archivio dati che si estende tra numerosi prodotti, dispositivi, organizzazioni e clienti. È importante pianificare in anticipo il layout di directory per l'organizzazione, la sicurezza e l'elaborazione efficiente dei dati per i consumer downstream. Un modello generale da prendere in considerazione può essere il layout seguente:

    {Region}/{SubjectMatter(s)}/{yyyy}/{mm}/{dd}/{hh}/

I dati di telemetria per gli atterraggi relativi a un motore di un aereo nel Regno Unito potrebbero ad esempio essere simili alla struttura seguente:

    UK/Planes/BA1293/Engine1/2017/08/11/12/

C'è un motivo importante per inserire la data alla fine della struttura di directory. Se si vuole bloccare alcuni domini o aree per utenti o gruppi specifici, è possibile farlo facilmente con le autorizzazioni POSIX. In caso contrario, se fosse necessario limitare, per un determinato gruppo di sicurezza, la visualizzazione solo ai dati per il Regno Unito o per alcuni aerei, con la struttura di data all'inizio sarebbe necessaria un'autorizzazione separata per le numerose directory nella directory relativa a ogni ora. La struttura di data all'inizio comporta inoltre un aumento esponenziale del numero di directory con il passare del tempo.

### <a name="batch-jobs-structure"></a>Struttura dei processi batch

Da un punto di vista generale, un approccio comune nell'elaborazione batch consiste nel trasmette i dati a una directory "in ingresso". Dopo l'elaborazione dei dati, è quindi possibile inserire i nuovi dati in una directory "in uscita" per l'uso da parte dei processi downstream. Questa struttura di directory viene talvolta usata per i processi che richiedono l'elaborazione sui singoli file e potrebbero non richiedere l'elaborazione parallela elevata (Massively Parallel Processing, MPP) su set di dati di grandi dimensioni. Analogamente alla struttura IoT consigliata in precedenza, una buona struttura di directory ha directory di livello padre per elementi come le aree e gli ambiti (ad esempio organizzazione, prodotto/produttore). Questa struttura consente di proteggere i dati nell'organizzazione e di gestirli meglio nei carichi di lavoro. Considerare inoltre data e ora nella struttura per consentire una migliore organizzazione, ricerche con filtri, sicurezza e automazione nell'elaborazione. Il livello di granularità per la struttura di data è determinato dall'intervallo con cui i dati vengono caricati o elaborati, ad esempio ogni ora, ogni giorno o anche ogni mese.

Talvolta l'elaborazione di file ha esito negativo a causa del danneggiamento dei dati o di formati imprevisti. In questi casi, può essere utile la presenza di una cartella **/bad** nella struttura di directory, dove spostare i file per un'ulteriore analisi. Il processo batch può anche gestire la creazione di report o le notifiche per i file nella cartella *bad*, per gli interventi manuali. Si consideri la struttura di modelli seguente:

    {Region}/{SubjectMatter(s)}/In/{yyyy}/{mm}/{dd}/{hh}/
    {Region}/{SubjectMatter(s)}/Out/{yyyy}/{mm}/{dd}/{hh}/
    {Region}/{SubjectMatter(s)}/Bad/{yyyy}/{mm}/{dd}/{hh}/

Una società di marketing riceve ad esempio estratti giornalieri di dati degli aggiornamenti dei clienti dai committenti in America del Nord, che prima e dopo l'elaborazione possono essere come il frammento seguente:

    NA/Extracts/ACMEPaperCo/In/2017/08/14/updates_08142017.csv
    NA/Extracts/ACMEPaperCo/Out/2017/08/14/processed_updates_08142017.csv

Nel caso comune di dati batch elaborati direttamente in database, come Hive o i database SQL tradizionali, non è necessaria una cartella **/in** o **/out** perché l'output viene già inserito in una cartella separata per la tabella Hive o un database esterno. Gli estratti giornalieri dei clienti vengono ad esempio inviati nelle rispettive cartelle e l'orchestrazione tramite una soluzione come Azure Data Factory, Apache Oozie o Apache Airflow attiva un processo Hive o Spark giornaliero per elaborare e scrivere i dati in una tabella Hive.
