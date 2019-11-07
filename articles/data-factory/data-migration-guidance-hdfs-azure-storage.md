---
title: Usare Azure Data Factory per migrare i dati da un cluster Hadoop locale ad archiviazione di Azure
description: Informazioni su come usare Azure Data Factory per eseguire la migrazione dei dati dal cluster Hadoop locale al servizio di archiviazione di Azure.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 8/30/2019
ms.openlocfilehash: b952be49bf5bc00b338aa04ed51e9dc451b5c4f9
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73675826"
---
# <a name="use-azure-data-factory-to-migrate-data-from-an-on-premises-hadoop-cluster-to-azure-storage"></a>Usare Azure Data Factory per migrare i dati da un cluster Hadoop locale ad archiviazione di Azure 

Azure Data Factory offre un meccanismo efficiente, affidabile ed economicamente conveniente per la migrazione dei dati su larga scala dalla HDFS locale all'archivio BLOB di Azure o alla Azure Data Lake Storage Gen2. 

Data Factory offre due approcci di base per la migrazione dei dati da HDFS locale ad Azure. È possibile selezionare l'approccio in base allo scenario. 

- **Data Factory modalità DistCp** (scelta consigliata): in data factory è possibile usare [DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) (copia distribuita) per copiare i file così come sono nell'archiviazione BLOB di Azure (inclusa la [copia](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy)di staging) o Azure Data Lake Store Gen2. USA Data Factory integrato con DistCp per sfruttare i vantaggi di un cluster potente esistente per ottenere la migliore velocità effettiva di copia. Si ottiene anche il vantaggio di una pianificazione flessibile e un'esperienza di monitoraggio unificata da Data Factory. A seconda della configurazione di Data Factory, l'attività di copia crea automaticamente un comando DistCp, invia i dati al cluster Hadoop e quindi monitora lo stato della copia. È consigliabile Data Factory modalità DistCp per la migrazione dei dati da un cluster Hadoop locale ad Azure.
- **Data Factory modalità di runtime di integrazione nativa**: DistCp non è un'opzione in tutti gli scenari. Ad esempio, in un ambiente di reti virtuali di Azure, lo strumento DistCp non supporta il peering privato di Azure ExpressRoute con un endpoint di rete virtuale di archiviazione di Azure. Inoltre, in alcuni casi, non si vuole usare il cluster Hadoop esistente come motore per la migrazione dei dati, in modo da non inserire carichi elevati nel cluster, che potrebbero influire sulle prestazioni dei processi ETL esistenti. È invece possibile usare la funzionalità nativa del runtime di integrazione Data Factory come motore che copia i dati da HDFS locale ad Azure.

Questo articolo fornisce le informazioni seguenti su entrambi gli approcci:
> [!div class="checklist"]
> * Prestazioni 
> * Resilienza della copia
> * Sicurezza di rete
> * Architettura della soluzione di alto livello 
> * Procedure consigliate per l'implementazione  

## <a name="performance"></a>Prestazioni

In Data Factory modalità DistCp, la velocità effettiva è identica a quella usata dallo strumento DistCp in modo indipendente. Data Factory modalità DistCp ottimizza la capacità del cluster Hadoop esistente. È possibile usare DistCp per la copia tra cluster o intra-cluster di grandi dimensioni. 

DistCp utilizza MapReduce per influire sulla distribuzione, la gestione degli errori e il ripristino e la creazione di report. Espande un elenco di file e directory in input per il mapping delle attività. Ogni attività copia una partizione di file specificata nell'elenco di origine. È possibile usare Data Factory integrato con DistCp per creare pipeline per sfruttare al meglio la larghezza di banda di rete, i IOPS di archiviazione e la larghezza di banda per ottimizzare la velocità effettiva di spostamento dei dati per l'ambiente.  

Data Factory modalità runtime di integrazione nativa consente anche il parallelismo a livelli diversi. Puoi usare il parallelismo per sfruttare al meglio la larghezza di banda di rete, i IOPS di archiviazione e la larghezza di banda per ottimizzare la velocità effettiva di spostamento dei dati:

- Una singola attività di copia può sfruttare le risorse di calcolo scalabili. Con un runtime di integrazione self-hosted è possibile scalare manualmente il computer o aumentare la scalabilità orizzontale in più computer ([fino a quattro nodi](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)). Una singola attività di copia esegue il partizionamento del set di file in tutti i nodi. 
- Una singola attività di copia legge e scrive nell'archivio dati usando più thread. 
- Data Factory flusso di controllo può avviare più attività di copia in parallelo. Ad esempio, è possibile usare un [ciclo for each](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity). 

Per ulteriori informazioni, vedere la [Guida alle prestazioni dell'attività di copia](https://docs.microsoft.com/azure/data-factory/copy-activity-performance).

## <a name="resilience"></a>Resilienza

In Data Factory modalità DistCp è possibile utilizzare parametri della riga di comando DistCp diversi, ad esempio `-i`, ignorare gli errori o `-update`, scrivere dati quando il file di origine e il file di destinazione presentano dimensioni diverse) per diversi livelli di resilienza.

In Data Factory modalità runtime di integrazione nativa, in una singola esecuzione dell'attività di copia, Data Factory dispone di un meccanismo di ripetizione dei tentativi incorporato. Può gestire un certo livello di errori temporanei negli archivi dati o nella rete sottostante. 

Quando si esegue la copia binaria dalla HDFS locale all'archiviazione BLOB e da HDFS locale a Data Lake Store Gen2, Data Factory esegue automaticamente il checkpoint in un ambito esteso. Se l'esecuzione di un'attività di copia ha esito negativo o scade, in un tentativo successivo (assicurarsi che il numero di tentativi sia > 1), la copia riprende dall'ultimo punto di errore anziché partire dall'inizio.

## <a name="network-security"></a>Sicurezza di rete 

Per impostazione predefinita, Data Factory trasferisce i dati dalla HDFS locale all'archiviazione BLOB o Azure Data Lake Storage Gen2 tramite una connessione crittografata sul protocollo HTTPS. HTTPS fornisce la crittografia dei dati in transito e impedisce l'intercettazione e gli attacchi man-in-the-Middle. 

In alternativa, se non si vuole trasferire i dati attraverso la rete Internet pubblica, per una maggiore sicurezza è possibile trasferire i dati tramite un collegamento di peering privato tramite ExpressRoute. 

## <a name="solution-architecture"></a>Architettura della soluzione

Questa immagine illustra la migrazione dei dati sulla rete Internet pubblica:

![Diagramma che illustra l'architettura della soluzione per la migrazione dei dati in una rete pubblica](media/data-migration-guidance-hdfs-to-azure-storage/solution-architecture-public-network.png)

- In questa architettura i dati vengono trasferiti in modo sicuro tramite HTTPS tramite la rete Internet pubblica. 
- Si consiglia di usare Data Factory modalità DistCp in un ambiente di rete pubblico. È possibile trarre vantaggio da un potente cluster esistente per ottenere la migliore velocità effettiva di copia. Si ottiene anche il vantaggio di una pianificazione flessibile e un'esperienza di monitoraggio unificata da Data Factory.
- Per questa architettura, è necessario installare il Data Factory runtime di integrazione self-hosted in un computer Windows dietro a un firewall aziendale per inviare il comando DistCp al cluster Hadoop e per monitorare lo stato della copia. Poiché il computer non è il motore che sposterà i dati (solo a scopo di controllo), la capacità del computer non influisce sulla velocità effettiva dello spostamento dei dati.
- Sono supportati i parametri esistenti dal comando DistCp.

Questa immagine illustra la migrazione dei dati tramite un collegamento privato: 

![Diagramma che illustra l'architettura della soluzione per la migrazione dei dati in una rete privata](media/data-migration-guidance-hdfs-to-azure-storage/solution-architecture-private-network.png)

- In questa architettura viene eseguita la migrazione dei dati tramite un collegamento di peering privato tramite Azure ExpressRoute. I dati non passano mai attraverso la rete Internet pubblica.
- Lo strumento DistCp non supporta il peering privato di ExpressRoute con un endpoint di rete virtuale di archiviazione di Azure. Per eseguire la migrazione dei dati, è consigliabile usare la funzionalità nativa di Data Factory tramite il runtime di integrazione.
- Per questa architettura, è necessario installare il Data Factory runtime di integrazione self-hosted in una VM Windows nella rete virtuale di Azure. È possibile scalare manualmente la macchina virtuale o aumentare le prestazioni in più macchine virtuali per usare completamente la rete e gli IOPS di archiviazione o la larghezza di banda.
- La configurazione consigliata per iniziare con per ogni macchina virtuale di Azure (con il Data Factory runtime di integrazione self-hosted installato) è Standard_D32s_v3 con 32 vCPU e 128 GB di memoria. È possibile monitorare l'utilizzo della CPU e della memoria della VM durante la migrazione dei dati per verificare se è necessario aumentare le prestazioni della macchina virtuale per ottenere prestazioni migliori o per ridurre la macchina virtuale per ridurre i costi.
- È anche possibile aumentare la scalabilità orizzontale associando fino a quattro nodi VM con un singolo runtime di integrazione self-hosted. Un processo di copia singolo in esecuzione su un runtime di integrazione self-hosted suddivide automaticamente il set di file e usa tutti i nodi VM per copiare i file in parallelo. Per la disponibilità elevata, è consigliabile iniziare con due nodi VM per evitare uno scenario a singolo punto di errore durante la migrazione dei dati.
- Quando si usa questa architettura, la migrazione iniziale dei dati dello snapshot e la migrazione dei dati Delta sono disponibili.

## <a name="implementation-best-practices"></a>Procedure consigliate per l'implementazione

Quando si implementa la migrazione dei dati, è consigliabile seguire queste procedure consigliate.

### <a name="authentication-and-credential-management"></a>Autenticazione e gestione delle credenziali 

- Per eseguire l'autenticazione a HDFS, è possibile usare [Windows (Kerberos) o Anonymous](https://docs.microsoft.com/azure/data-factory/connector-hdfs#linked-service-properties). 
- Sono supportati più tipi di autenticazione per la connessione all'archivio BLOB di Azure.  Si consiglia vivamente [di usare identità gestite per le risorse di Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity). Basati su un'identità Data Factory gestita automaticamente in Azure Active Directory (Azure AD), le identità gestite consentono di configurare pipeline senza fornire credenziali nella definizione del servizio collegato. In alternativa, è possibile eseguire l'autenticazione nell'archiviazione BLOB usando un' [entità servizio](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication), una [firma di accesso condiviso](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication)o una [chiave dell'account di archiviazione](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication). 
- Sono supportati anche più tipi di autenticazione per la connessione a Data Lake Storage Gen2.  Si consiglia vivamente [di usare identità gestite per le risorse di Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity), ma è anche possibile usare un' [entità servizio](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication) o una [chiave dell'account di archiviazione](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication). 
- Quando non si usano identità gestite per le risorse di Azure, è consigliabile [archiviare le credenziali in Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) per semplificare la gestione e la rotazione centralizzate delle chiavi senza modificare data factory servizi collegati. Questa è anche una [procedura consigliata per ci/CD](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd). 

### <a name="initial-snapshot-data-migration"></a>Migrazione iniziale dei dati dello snapshot 

In Data Factory modalità DistCp è possibile creare un'attività di copia per inviare il comando DistCp e utilizzare parametri diversi per controllare il comportamento di migrazione dei dati iniziale. 

In Data Factory modalità runtime di integrazione nativa, si consiglia la partizione dei dati, specialmente quando si esegue la migrazione di più di 10 TB di dati. Per partizionare i dati, usare i nomi di cartella in HDFS. Ogni processo di copia di Data Factory può quindi copiare una partizione di cartella alla volta. È possibile eseguire più processi di copia Data Factory contemporaneamente per una migliore velocità effettiva.

Se uno dei processi di copia ha esito negativo a causa di problemi temporanei dell'archivio dati o della rete, è possibile rieseguire il processo di copia non riuscito per ricaricare la partizione specifica da HDFS. Non sono interessati altri processi di copia che caricano altre partizioni.

### <a name="delta-data-migration"></a>Migrazione dei dati Delta 

In Data Factory modalità DistCp è possibile utilizzare il parametro della riga di comando DistCp `-update`, scrivere dati quando il file di origine e il file di destinazione presentano dimensioni diverse per la migrazione dei dati Delta.

In Data Factory modalità di integrazione nativa, il modo più efficiente per identificare i file nuovi o modificati da HDFS consiste nell'usare una convenzione di denominazione partizionata in base al tempo. Quando i dati in HDFS sono stati partizionati in tempo con le informazioni relative all'intervallo di tempo nel nome del file o della cartella (ad esempio, */yyyy/mm/DD/file.csv*), la pipeline può identificare facilmente i file e le cartelle da copiare in modo incrementale.

In alternativa, se i dati in HDFS non sono partizionati in base al tempo, Data Factory possibile identificare i file nuovi o modificati usando il valore **LastModifiedDate** . Data Factory analizza tutti i file da HDFS e copia solo i file nuovi e aggiornati con un timestamp dell'Ultima modifica maggiore di un valore impostato. 

Se si dispone di un numero elevato di file in HDFS, l'analisi dei file iniziale potrebbe richiedere molto tempo, indipendentemente dal numero di file che corrispondono alla condizione di filtro. In questo scenario è consigliabile partizionare prima i dati utilizzando la stessa partizione utilizzata per la migrazione snapshot iniziale. L'analisi dei file può quindi essere eseguita in parallelo.

### <a name="estimate-price"></a>Prezzo stimato 

Si consideri la pipeline seguente per la migrazione dei dati da HDFS all'archivio BLOB di Azure: 

![Diagramma che mostra la pipeline dei prezzi](media/data-migration-guidance-hdfs-to-azure-storage/pricing-pipeline.png)

Si supponga le seguenti informazioni: 

- Il volume totale dei dati è di 1 PB.
- Per eseguire la migrazione dei dati, è possibile usare la modalità runtime di integrazione Data Factory nativa.
- 1 PB è diviso in 1.000 partizioni e ogni copia sposta una partizione.
- Ogni attività di copia è configurata con un runtime di integrazione self-hosted associato a quattro computer e che raggiunge la velocità effettiva di 500 MBps.
- La concorrenza ForEach è impostata su **4** e la velocità effettiva aggregata è di 2 Gbps.
- Per completare la migrazione, in totale sono necessarie 146 ore.

Ecco il prezzo stimato in base ai presupposti seguenti: 

![Tabella che mostra i calcoli dei prezzi](media/data-migration-guidance-hdfs-to-azure-storage/pricing-table.png)

> [!NOTE]
> Questo è un esempio di prezzo ipotetico. I prezzi effettivi variano in base alla velocità effettiva dell'ambiente.
> Il prezzo di una VM Windows di Azure (con il runtime di integrazione self-hosted installato) non è incluso.

### <a name="additional-references"></a>Riferimenti aggiuntivi

- [Connettore HDFS](https://docs.microsoft.com/azure/data-factory/connector-hdfs)
- [Connettore di archiviazione BLOB di Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
- [Connettore di Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
- [Guida all'ottimizzazione delle prestazioni dell'attività di copia](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)
- [Creare e configurare un runtime di integrazione self-hosted](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
- [Disponibilità elevata e scalabilità del runtime di integrazione self-hosted](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)
- [Considerazioni sulla sicurezza dello spostamento dei dati](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)
- [Archiviare le credenziali in Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
- [Copiare un file in modo incrementale in base a un nome file con partizionamento temporale](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-partitioned-file-name-copy-data-tool)
- [Copia i file nuovi e modificati in base a LastModifiedDate](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-lastmodified-copy-data-tool)
- [Pagina dei prezzi di Data Factory](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="next-steps"></a>Passaggi successivi

- [Copiare i file da più contenitori usando Azure Data Factory](solution-template-copy-files-multiple-containers.md)