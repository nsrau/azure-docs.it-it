---
title: Eseguire la migrazione dei dati da un cluster Hadoop locale ad Archiviazione di AzureMigrate data from an on-premises Hadoop cluster to Azure Storage
description: Informazioni su come usare Azure Data Factory per eseguire la migrazione dei dati dal cluster Hadoop locale ad Archiviazione di Azure.Learn how to use Azure Data Factory to migrate data from on-premises Hadoop cluster to Azure Storage.
services: data-factory
ms.author: yexu
author: dearandyxu
ms.reviewer: ''
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 8/30/2019
ms.openlocfilehash: 63b657e77172282225a9bc890b2f185b0f4d42a1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417125"
---
# <a name="use-azure-data-factory-to-migrate-data-from-an-on-premises-hadoop-cluster-to-azure-storage"></a>Usare Azure Data Factory per eseguire la migrazione dei dati da un cluster Hadoop locale a Archiviazione di AzureUse Azure Data Factory to migrate data from an on-premises Hadoop cluster to Azure Storage 

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory offre un meccanismo performante, affidabile ed economico per la migrazione dei dati su larga scala da HDFS locale all'archiviazione BLOB di Azure o all'archiviazione di Azure Data Lake Storage Gen2.Azure Data Factory provides a performant, robust and cost-effective mechanism for migrating data at scale from on-premises HDFS to Azure Blob storage or Azure Data Lake Storage Gen2. 

Data Factory offers two basic approaches for migrating data from on-premises HDFS to Azure. È possibile selezionare l'approccio in base allo scenario. 

- **Modalità DistCp** di Data Factory (scelta consigliata): in Data Factory è possibile usare [DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) (copia distribuita) per copiare i file così com'è nell'archiviazione BLOB di Azure (inclusa la [copia in fasi)](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy)o Azure Data Lake Store Gen2. Utilizzare Data Factory integrato con DistCp per sfruttare un cluster potente esistente per ottenere la migliore velocità effettiva di copia. È inoltre possibile ottenere i vantaggi di una pianificazione flessibile e di un'esperienza di monitoraggio unificata da Data Factory. A seconda della configurazione di Data Factory, l'attività di copia crea automaticamente un comando DistCp, invia i dati al cluster Hadoop e quindi monitora lo stato della copia. È consigliabile la modalità Data Factory DistCp per la migrazione dei dati da un cluster Hadoop locale ad Azure.We recommend Data Factory DistCp mode for migrating data from an on-premises Hadoop cluster to Azure.
- **Modalità runtime integrazione nativa di Data Factory:** DistCp non è un'opzione in tutti gli scenari. Ad esempio, in un ambiente di reti virtuali di Azure, lo strumento DistCp non supporta il peering privato di Azure ExpressRoute con un endpoint di rete virtuale di Archiviazione di Azure.For example, in an Azure Virtual Networks environment, the DistCp tool doesn't support Azure ExpressRoute private peering with an Azure Storage virtual network endpoint. Inoltre, in alcuni casi, non si desidera utilizzare il cluster Hadoop esistente come motore per la migrazione dei dati in modo da non inserire carichi pesanti nel cluster, che potrebbero influire sulle prestazioni dei processi ETL esistenti. È invece possibile usare la funzionalità nativa del runtime di integrazione di Data Factory come motore che copia i dati da HDFS locale ad Azure.Instead, you can use the native capability of the Data Factory integration runtime as the engine that copies data from on-premises HDFS to Azure.

In questo articolo vengono fornite le informazioni seguenti su entrambi gli approcci:This article provides the following information about both approaches:
> [!div class="checklist"]
> * Prestazioni 
> * Resilienza della copia
> * Sicurezza di rete
> * Architettura della soluzione di alto livello 
> * Procedure consigliate per l'implementazione  

## <a name="performance"></a>Prestazioni

In modalità Data Factory DistCp, la velocità effettiva è la stessa come se si utilizza lo strumento DistCp in modo indipendente. La modalità Data Factory DistCp massimizza la capacità del cluster Hadoop esistente. È possibile utilizzare DistCp per la copia tra cluster o intracluster di grandi dimensioni. 

DistCp utilizza MapReduce per influire sulla distribuzione, la gestione degli errori e il ripristino e la creazione di report. Espande un elenco di file e directory nell'input per il mapping delle attività. Ogni attività copia una partizione di file specificata nell'elenco di origine. È possibile usare Data Factory integrato con DistCp per creare pipeline per utilizzare completamente la larghezza di banda di rete, le operazioni di I/O al secondo di archiviazione e la larghezza di banda per ottimizzare la velocità effettiva di spostamento dei dati per l'ambiente.  

La modalità runtime di integrazione nativa di Data Factory consente anche il parallelismo a diversi livelli. È possibile usare il parallelismo per utilizzare completamente la larghezza di banda di rete, le operazioni di I/O al secondo di archiviazione e la larghezza di banda per ottimizzare la velocità effettiva di spostamento dei dati:You can use parallelism to fully utilize your network bandwidth, storage IOPS, and bandwidth to maximize data movement throughput:

- Una singola attività di copia può sfruttare le risorse di calcolo scalabili. Con un runtime di integrazione self-hosted, è possibile aumentare manualmente il computer o la scalabilità orizzontale su più computer[(fino a quattro nodi](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)). Una singola attività di copia partiziona il proprio set di file in tutti i nodi. 
- Una singola attività di copia legge e scrive nell'archivio dati utilizzando più thread. 
- Il flusso di controllo di Data Factory può avviare più attività di copia in parallelo. Ad esempio, è possibile utilizzare un [ciclo For Each](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity). 

Per ulteriori informazioni, vedere la [guida alle prestazioni dell'attività](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)di copia .

## <a name="resilience"></a>Resilienza

In modalità Data Factory DistCp è possibile utilizzare parametri della `-i`riga di comando `-update`DistCp diversi (ad esempio, ignorare gli errori o , scrivere dati quando le dimensioni del file di origine e del file di destinazione sono diversi) per livelli diversi di resilienza.

Nella modalità runtime di integrazione nativa di Data Factory, in una singola esecuzione dell'attività di copia, Data Factory dispone di un meccanismo di ripetizione dei tentativi incorporato. Può gestire un determinato livello di errori temporanei negli archivi dati o nella rete sottostante. 

Quando si esegue la copia binaria da HDFS locale all'archiviazione BLOB e dall'HDFS locale all'archivio dati gen2, Data Factory esegue automaticamente il checkpoint in larga misura. Se un'esecuzione dell'attività di copia ha esito negativo o scade, in un tentativo successivo (assicurarsi che il numero di tentativi sia > 1), la copia riprende dall'ultimo punto di errore anziché iniziare dall'inizio.

## <a name="network-security"></a>Sicurezza di rete 

Per impostazione predefinita, Data Factory trasferisce i dati da HDFS locale all'archiviazione BLOB o Azure Data Lake Storage Gen2 usando una connessione crittografata tramite il protocollo HTTPS. HTTPS fornisce la crittografia dei dati in transito e previene le intercettazioni e gli attacchi man-in-the-middle. 

In alternativa, se non si desidera che i dati vengano trasferiti tramite Internet pubblico, per una maggiore sicurezza, è possibile trasferire i dati tramite un collegamento di peering privato tramite ExpressRoute.Alternatively, if you don't want data to be transferred over the public internet, for higher security, you can transfer data over a private peering link via ExpressRoute. 

## <a name="solution-architecture"></a>Architettura della soluzione

Questa immagine illustra la migrazione dei dati su Internet pubblico:

![Diagramma che illustra l'architettura della soluzione per la migrazione dei dati su una rete pubblica](media/data-migration-guidance-hdfs-to-azure-storage/solution-architecture-public-network.png)

- In questa architettura, i dati vengono trasferiti in modo sicuro utilizzando HTTPS su Internet pubblico. 
- È consigliabile usare la modalità Data Factory DistCp in un ambiente di rete pubblica. È possibile sfruttare un potente cluster esistente per ottenere la migliore velocità effettiva di copia. È inoltre possibile ottenere i vantaggi di una pianificazione flessibile e di un'esperienza di monitoraggio unificato da Data Factory.
- Per questa architettura, è necessario installare il runtime di integrazione self-hosted di Data Factory in un computer Windows dietro un firewall aziendale per inviare il comando DistCp al cluster Hadoop e monitorare lo stato della copia. Poiché la macchina non è il motore che sposterà i dati (solo a scopo di controllo), la capacità della macchina non influisce sulla velocità effettiva dello spostamento dei dati.
- Sono supportati i parametri esistenti del comando DistCp.

Questa immagine illustra la migrazione dei dati tramite un collegamento privato:This image depicts migrating data over a private link: 

![Diagramma che illustra l'architettura della soluzione per la migrazione dei dati in una rete privata](media/data-migration-guidance-hdfs-to-azure-storage/solution-architecture-private-network.png)

- In this architecture, data is migrated over a private peering link via Azure ExpressRoute. I dati non attraversano mai l'internet pubblico.
- Lo strumento DistCp non supporta il peering privato ExpressRoute con un endpoint di rete virtuale di Archiviazione di Azure.The DistCp tool doesn't support ExpressRoute private peering with an Azure Storage virtual network endpoint. È consigliabile usare la funzionalità nativa di Data Factory tramite il runtime di integrazione per eseguire la migrazione dei dati.
- Per questa architettura, è necessario installare il runtime di integrazione self-hosted di Data Factory in una macchina virtuale Windows nella rete virtuale di Azure.For this architecture, you must install the Data Factory self-hosted integration runtime on a Windows VM in your Azure virtual network. È possibile aumentare manualmente la macchina virtuale o la scalabilità orizzontale a più macchine virtuali per usare completamente le operazioni di I/O al secondo di rete e di archiviazione o la larghezza di banda.
- La configurazione consigliata per cui iniziare per ogni macchina virtuale di Azure (con il runtime di integrazione self-hosted di Data Factory installato) viene Standard_D32s_v3 con una vCPU 32 e 128 GB di memoria. È possibile monitorare l'utilizzo della CPU e della memoria della macchina virtuale durante la migrazione dei dati per verificare se è necessario aumentare la scalabilità della macchina virtuale per migliorare le prestazioni o ridurre la macchina virtuale per ridurre i costi.
- È anche possibile scalare orizzontalmente associando fino a quattro nodi VM a un singolo runtime di integrazione self-hosted. Un singolo processo di copia in esecuzione su un runtime di integrazione self-hosted partiziona automaticamente il set di file e utilizza tutti i nodi VM per copiare i file in parallelo. Per la disponibilità elevata, è consigliabile iniziare con due nodi VM per evitare uno scenario con singolo punto di errore durante la migrazione dei dati.
- Quando si utilizza questa architettura, sono disponibili la migrazione iniziale dei dati dello snapshot e la migrazione dei dati differenziali.

## <a name="implementation-best-practices"></a>Procedure consigliate per l'implementazione

È consigliabile seguire queste procedure consigliate quando si implementa la migrazione dei dati.

### <a name="authentication-and-credential-management"></a>Autenticazione e gestione delle credenziali 

- Per eseguire l'autenticazione a HDFS, è possibile utilizzare [Windows (Kerberos) o Anonimo](https://docs.microsoft.com/azure/data-factory/connector-hdfs#linked-service-properties). 
- Sono supportati più tipi di autenticazione per la connessione all'archiviazione BLOB di Azure.Multiple authentication types are supported for connecting to Azure Blob storage.  È consigliabile usare [le identità gestite per le risorse](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity)di Azure.We recommend using managed identities for Azure resources . Basate su un'identità di Data Factory gestita automaticamente in Azure Active Directory (Azure AD), le identità gestite consentono di configurare le pipeline senza fornire credenziali nella definizione del servizio collegato. In alternativa, è possibile eseguire l'autenticazione all'archiviazione BLOB usando [un'entità servizio,](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication)una firma di [accesso condiviso](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication)o una [chiave dell'account](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication)di archiviazione. 
- Sono supportati più tipi di autenticazione anche per la connessione a Data Lake Storage Gen2.Multiple authentication types are also supported for connecting to Data Lake Storage Gen2.  È consigliabile usare [le identità gestite per le risorse](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity)di Azure, ma è anche possibile usare [un'entità servizio](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication) o una chiave [dell'account](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication)di archiviazione. 
- Quando non si usano le identità gestite per le risorse di Azure, è consigliabile archiviare le credenziali in Archiviazione chiave di Azure per semplificare la gestione e la rotazione centralizzata delle chiavi senza modificare i servizi collegati di Data Factory.When you're not using managed identities for Azure resources, we recommend [storing the credentials in Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) to make it easier to centrally manage and rotate keys without modifying Data Factory linked services. Questa è anche una [best practice per CI/CD.](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd) 

### <a name="initial-snapshot-data-migration"></a>Migrazione iniziale dei dati dello snapshot 

In modalità Data Factory DistCp, è possibile creare un'attività di copia per inviare il comando DistCp e utilizzare parametri diversi per controllare il comportamento iniziale della migrazione dei dati. 

In modalità runtime di integrazione nativa di Data Factory è consigliabile partizione dati, soprattutto quando si esegue la migrazione di più di 10 TB di dati. Per partizionare i dati, utilizzare i nomi delle cartelle in HDFS. Ogni processo di copia di Data Factory può quindi copiare una partizione di cartella alla volta. È possibile eseguire più processi di copia di Data Factory contemporaneamente per migliorare la velocità effettiva.

Se uno dei processi di copia non riesce a causa di problemi temporanei di rete o archivio dati, è possibile eseguire nuovamente il processo di copia non riuscita per ricaricare la partizione specifica da HDFS. Altri processi di copia che caricano altre partizioni non sono interessati.

### <a name="delta-data-migration"></a>Migrazione dei dati Delta 

In modalità Data Factory DistCp è possibile utilizzare `-update`il parametro della riga di comando DistCp , scrivere i dati quando il file di origine e il file di destinazione sono di dimensioni diverse per la migrazione dei dati delta.

Nella modalità di integrazione nativa di Data Factory, il modo più performante per identificare i file nuovi o modificati da HDFS consiste nell'utilizzare una convenzione di denominazione partizionata nel tempo. Quando i dati in HDFS sono stati partizionati nel tempo con informazioni sull'intervallo di tempo nel nome del file o della cartella (ad esempio, */aaaa/mm/gg/file.csv),* la pipeline può facilmente identificare i file e le cartelle da copiare in modo incrementale.

In alternativa, se i dati in HDFS non sono partizionati nel tempo, Data Factory può identificare i file nuovi o modificati usando il valore LastModifiedDate.Alternatively, if your data in HDFS isn't time-partitioned, Data Factory can identify new or changed files by using their **LastModifiedDate** value. Data Factory analizza tutti i file da HDFS e copia solo i file nuovi e aggiornati con un timestamp dell'ultima modifica maggiore di un valore impostato. 

Se si dispone di un numero elevato di file in HDFS, la scansione iniziale dei file potrebbe richiedere molto tempo, indipendentemente dal numero di file che soddisfano la condizione di filtro. In questo scenario, è consigliabile partizionare prima i dati utilizzando la stessa partizione utilizzata per la migrazione iniziale dello snapshot. Quindi, la scansione dei file può avvenire in parallelo.

### <a name="estimate-price"></a>Prezzo stimato 

Si consideri la pipeline seguente per la migrazione dei dati da HDFS all'archiviazione BLOB di Azure:Consider the following pipeline for migrating data from HDFS to Azure Blob storage: 

![Diagramma che mostra la pipeline dei prezzi](media/data-migration-guidance-hdfs-to-azure-storage/pricing-pipeline.png)

Supponiamo che le seguenti informazioni: 

- Il volume totale dei dati è 1 PB.
- La migrazione dei dati viene eseguita utilizzando la modalità runtime di integrazione nativa di Data Factory.You migrate data by using the Data Factory native integration runtime mode.
- 1 PB è diviso in 1.000 partizioni e ogni copia sposta una partizione.
- Ogni attività di copia è configurata con un runtime di integrazione self-hosted associato a quattro computer e che raggiunge una velocità effettiva di 500 MBps.
- La concorrenza ForEach è impostata su 4 e la velocità effettiva aggregata è 2 GBps.ForEach concurrency is set to **4** and aggregate throughput is 2 GBps.
- In totale, sono necessarie 146 ore per completare la migrazione.

Ecco il prezzo stimato in base ai nostri presupposti: 

![Tabella che mostra i calcoli dei prezzi](media/data-migration-guidance-hdfs-to-azure-storage/pricing-table.png)

> [!NOTE]
> Questo è un esempio ipotetico di prezzi. I prezzi effettivi dipendono dalla velocità effettiva nell'ambiente.
> Il prezzo per una macchina virtuale Windows di Azure (con il runtime di integrazione self-hosted installato) non è incluso.

### <a name="additional-references"></a>Riferimenti aggiuntivi

- [Connettore HDFS](https://docs.microsoft.com/azure/data-factory/connector-hdfs)
- [Connettore di archiviazione BLOB di AzureAzure Blob storage connector](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
- [Connettore di Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
- [Guida all'ottimizzazione delle prestazioni dell'attività di copia](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)
- [Creare e configurare un runtime di integrazione self-hosted](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
- [Disponibilità elevata e scalabilità del runtime di integrazione self-hostedSelf-hosted integration runtime high availability and scalability](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)
- [Considerazioni relative alla sicurezza per lo spostamento dei dati](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)
- [Archiviare le credenziali in Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
- [Copiare un file in modo incrementale in base a un nome di file partizionato nel tempo](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-partitioned-file-name-copy-data-tool)
- [Copiare i file nuovi e modificati basati su LastModifiedDate](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-lastmodified-copy-data-tool)
- [Pagina dei prezzi di Data Factory](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="next-steps"></a>Passaggi successivi

- [Copiare file da più contenitori usando Azure Data FactoryCopy files from multiple containers by using Azure Data Factory](solution-template-copy-files-multiple-containers.md)