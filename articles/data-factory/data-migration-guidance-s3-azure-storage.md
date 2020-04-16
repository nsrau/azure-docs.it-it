---
title: Eseguire la migrazione dei dati da un archivio dati Amazon S3 ad Archiviazione di Azure
description: Usare Azure Data Factory per eseguire la migrazione dei dati da Amazon S3 ad Archiviazione di Azure.Use Azure Data Factory to migrate data from Amazon S3 to Azure Storage.
services: data-factory
ms.author: yexu
author: dearandyxu
ms.reviewer: ''
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 8/04/2019
ms.openlocfilehash: 3f40ad7346219b48a38ade38b2a75ddf71940875
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416424"
---
# <a name="use-azure-data-factory-to-migrate-data-from-amazon-s3-to-azure-storage"></a>Usare Azure Data Factory per eseguire la migrazione dei dati da Amazon S3 ad Archiviazione di AzureUse Azure Data Factory to migrate data from Amazon S3 to Azure Storage 

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory offre un meccanismo performante, affidabile ed economico per eseguire la migrazione dei dati su larga scala da Amazon S3 a Archiviazione BLOB di Azure o Azure Data Lake Storage Gen2.  In questo articolo vengono fornite le seguenti informazioni per i data engineer e gli sviluppatori: 

> [!div class="checklist"]
> * Prestazioni 
> * Resilienza della copia
> * Sicurezza di rete
> * Architettura della soluzione di alto livello 
> * Procedure consigliate per l'implementazione  

## <a name="performance"></a>Prestazioni

ADF offre un'architettura senza server che consente il parallelismo a diversi livelli, che consente agli sviluppatori di creare pipeline per utilizzare completamente la larghezza di banda di rete, nonché le operazioni di I/O al secondo e la larghezza di banda di archiviazione per ottimizzare la velocità effettiva di spostamento dei dati per l'ambiente. 

I clienti hanno eseguito correttamente la migrazione di petabyte di dati costituiti da centinaia di milioni di file da Amazon S3 ad Archiviazione BLOB di Azure, con una velocità effettiva sostenuta di 2 GBps e versioni successive. 

![prestazioni](media/data-migration-guidance-s3-to-azure-storage/performance.png)

L'immagine qui sopra illustra come è possibile ottenere grandi velocità di spostamento dei dati attraverso diversi livelli di parallelismo:
 
- Una singola attività di copia può sfruttare le risorse di calcolo scalabili: quando si usa Azure Integration Runtime, è possibile specificare [fino a 256 DIUff](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#data-integration-units) per ogni attività di copia in modo senza server. Quando si utilizza il runtime di integrazione self-hosted, è possibile aumentare manualmente il computer o la scalabilità orizzontale su più computer[(fino a 4 nodi)](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)e una singola attività di copia partiziona il file impostato su tutti i nodi. 
- Una singola attività di copia legge e scrive nell'archivio dati utilizzando più thread. 
- Il flusso di controllo ADF può avviare più attività di copia in parallelo, ad esempio usando [Il ciclo For Each](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity). 

## <a name="resilience"></a>Resilienza

All'interno di una singola attività di copia, ADF dispone di un meccanismo di ripetizione dei tentativi incorporato in modo che possa gestire un determinato livello di errori temporanei negli archivi dati o nella rete sottostante. 

Quando si esegue la copia binaria da S3 a Blob e da S3 ad ADLS Gen2, ADF esegue automaticamente il checkpoint.  Se un'esecuzione dell'attività di copia non è riuscita o si è verificata, in un nuovo tentativo, la copia riprende dall'ultimo punto di errore anziché dall'inizio. 

## <a name="network-security"></a>Sicurezza di rete 

Per impostazione predefinita, ADF trasferisce i dati da Amazon S3 ad Archiviazione BLOB di Azure o Azure Data Lake Storage Gen2 usando la connessione crittografata tramite il protocollo HTTPS.  HTTPS fornisce la crittografia dei dati in transito e previene le intercettazioni e gli attacchi man-in-the-middle. 

In alternativa, se non si desidera che i dati vengano trasferiti tramite Internet pubblico, è possibile ottenere una maggiore sicurezza trasferendo i dati tramite un collegamento di peering privato tra AWS Direct Connect e Azure Express Route.  Fare riferimento all'architettura della soluzione riportata di seguito su come ottenere questo risultato. 

## <a name="solution-architecture"></a>Architettura della soluzione

Eseguire la migrazione dei dati su Internet pubblico:

![soluzione-architettura-rete pubblica-rete](media/data-migration-guidance-s3-to-azure-storage/solution-architecture-public-network.png)

- In questa architettura, i dati vengono trasferiti in modo sicuro utilizzando HTTPS su Internet pubblico. 
- L'origine Amazon S3 e l'archiviazione BLOB di Azure di destinazione o Azure Data Lake Storage Gen2 sono configurate per consentire il traffico da tutti gli indirizzi IP di rete.  Fare riferimento alla seconda architettura riportata di seguito su come limitare l'accesso di rete a un intervallo IP specifico. 
- È possibile aumentare facilmente la quantità di potenza in formato senza server per utilizzare completamente la larghezza di banda di rete e di archiviazione in modo da ottenere la migliore velocità effettiva per l'ambiente. 
- Sia la migrazione iniziale dello snapshot che la migrazione dei dati delta possono essere ottenute utilizzando questa architettura. 

Eseguire la migrazione dei dati tramite collegamento privato:Migrate data over private link: 

![soluzione-architettura-rete privata](media/data-migration-guidance-s3-to-azure-storage/solution-architecture-private-network.png)

- In questa architettura, la migrazione dei dati viene eseguita tramite un collegamento di peering privato tra AWS Direct Connect e Azure Express Route in modo che i dati non attraversino mai Internet pubblico.  Richiede l'uso di AWS VPC e della rete virtuale di Azure. 
- Per ottenere questa architettura, è necessario installare il runtime di integrazione self-hosted ADF in una macchina virtuale Windows all'interno della rete virtuale di Azure.You need to install ADF self-hosted integration runtime on a Windows VM within your Azure virtual network to achieve this architecture.  È possibile aumentare manualmente le macchine virtuali IR self-hosted o scalabilità orizzontale a più macchine virtuali (fino a 4 nodi) per utilizzare completamente la rete e archiviazione IOPS/larghezza di banda. 
- Se è accettabile trasferire i dati su HTTPS ma si desidera bloccare l'accesso di rete alla sorgente S3 a un intervallo IP specifico, è possibile adottare una variante di questa architettura rimuovendo AWS VPC e sostituendo il collegamento privato con HTTPS.  È consigliabile mantenere il sistema di disponibilità di funzionalità virtuale di Azure e il componente di disponibilità automatico in una macchina virtuale di Azure in modo da poter disporre di un IP instradabile pubblicamente statico per scopi di whitelisting. 
- Sia la migrazione iniziale dei dati snapshot che la migrazione dei dati delta possono essere ottenute utilizzando questa architettura. 

## <a name="implementation-best-practices"></a>Procedure consigliate per l'implementazione 

### <a name="authentication-and-credential-management"></a>Autenticazione e gestione delle credenziali 

- Per eseguire l'autenticazione all'account Amazon S3, è necessario utilizzare la chiave di [accesso per l'account IAM.](https://docs.microsoft.com/azure/data-factory/connector-amazon-simple-storage-service#linked-service-properties) 
- Sono supportati più tipi di autenticazione per la connessione ad Archiviazione BLOB di Azure.Multiple authentication types are supported to connect to Azure Blob Storage.  L'uso delle [identità gestite per le risorse](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity) di Azure è altamente consigliato: basato su un'identificazione ADF gestita automaticamente in Azure AD, consente di configurare le pipeline senza fornire credenziali nella definizione del servizio collegato.  In alternativa, è possibile eseguire l'autenticazione all'archiviazione BLOB di Azure usando [l'entità servizio,](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication)la firma di [accesso condiviso](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication)o la chiave dell'account [di archiviazione.](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication) 
- Sono supportati anche più tipi di autenticazione per la connessione ad Azure Data Lake Storage Gen2.Multiple authentication types are also supported to connect to Azure Data Lake Storage Gen2.  L'uso delle [identità gestite per le risorse](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity) di Azure è altamente consigliato, anche se è possibile usare anche [l'entità servizio](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication) o la chiave [dell'account di archiviazione.](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication) 
- Quando non si usano identità gestite per le risorse di Azure, è consigliabile [archiviare le credenziali in Archiviazione chiave](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) di Azure per semplificare la gestione e la rotazione centralizzata delle chiavi senza modificare i servizi collegati ADF.  Questa è anche una delle [best practice per CI/CD](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd). 

### <a name="initial-snapshot-data-migration"></a>Migrazione iniziale dei dati dello snapshot 

La partizione dati è consigliata soprattutto quando si esegue la migrazione di più di 100 TB di dati.  Per partizionare i dati, utilizza l'impostazione 'prefisso' per filtrare le cartelle e i file in Amazon S3 in base al nome, quindi ogni processo di copia ADF può copiare una partizione alla volta.  È possibile eseguire più processi di copia ADF contemporaneamente per una migliore velocità effettiva. 

Se uno dei processi di copia ha esito negativo a causa di problemi temporanei di rete o archivio dati, è possibile eseguire nuovamente il processo di copia non riuscita per ricaricare nuovamente la partizione specifica da AWS S3.  Tutti gli altri processi di copia che caricano altre partizioni non saranno interessati. 

### <a name="delta-data-migration"></a>Migrazione dei dati Delta 

Il modo più performante per identificare i file nuovi o modificati da AWS S3 consiste nell'utilizzare la convenzione di denominazione partizionata nel tempo: quando i dati in AWS S3 sono stati partizionati nel tempo con le informazioni sull'intervallo di tempo nel nome del file o della cartella (ad esempio, /yyyy/mm/gg/file.csv), la pipeline può identificare facilmente i file/cartelle da copiare in modo incrementale. 

In alternativa, se i dati in AWS S3 non sono partizionati nel tempo, ADF può identificare i file nuovi o modificati in base a LastModifiedDate.   Il modo in cui funziona è che ADF esegue la scansione di tutti i file da AWS S3 e copierà solo il file nuovo e aggiornato il cui timestamp dell'ultima modifica è maggiore di un determinato valore.  Tenere presente che se si dispone di un numero elevato di file in S3, la scansione iniziale dei file potrebbe richiedere molto tempo indipendentemente dal numero di file che soddisfano la condizione di filtro.  In questo caso si consiglia di partizionare prima i dati, utilizzando la stessa impostazione 'prefisso' per la migrazione iniziale dello snapshot, in modo che l'analisi dei file possa avvenire in parallelo.  

### <a name="for-scenarios-that-require-self-hosted-integration-runtime-on-azure-vm"></a>Per gli scenari che richiedono il runtime di integrazione self-hosted nella macchina virtuale di AzureFor scenarios that require self-hosted Integration runtime on Azure VM 

Sia che si stia eseguendo la migrazione dei dati tramite collegamento privato o si desideri consentire un intervallo IP specifico nel firewall Amazon S3, è necessario installare il runtime di integrazione self-hosted in Azure Windows VM. 

- La configurazione consigliata per cui iniziare per ogni macchina virtuale di Azure è Standard_D32s_v3 con 32 vCPU e 128 GB di memoria.  È possibile continuare a monitorare l'utilizzo della CPU e della memoria della macchina virtuale a mano reale durante la migrazione dei dati per verificare se è necessario aumentare ulteriormente la macchina virtuale per migliorare le prestazioni o ridurre la macchina virtuale per risparmiare sui costi. 
- È anche possibile scalare orizzontalmente associando fino a 4 nodi VM a un singolo componente di gestione di proprietà self-hosted.  Un singolo processo di copia in esecuzione su un metodo di gestione delle applicazioni self-hosted partiziona automaticamente il set di file e sfrutta tutti i nodi VM per copiare i file in parallelo.  Per la disponibilità elevata, è consigliabile iniziare con 2 nodi VM per evitare un singolo punto di errore durante la migrazione dei dati. 

### <a name="rate-limiting"></a>Limitazione della frequenza 

Come procedura consigliata, eseguire un POC delle prestazioni con un set di dati di esempio rappresentativo, in modo da poter determinare le dimensioni di una partizione appropriata. 

Iniziare con una singola partizione e una singola attività di copia con l'impostazione DIU predefinita.  Aumentare gradualmente l'impostazione DIU fino a raggiungere il limite di larghezza di banda della rete o il limite di IOPS/larghezza di banda degli archivi dati o è stato raggiunto il massimo 256 DIU consentito in una singola attività di copia. 

Successivamente, aumentare gradualmente il numero di attività di copia simultanee fino a raggiungere i limiti dell'ambiente. 

Quando si verificano errori di limitazione segnalati dall'attività di copia di ADF, ridurre l'impostazione di concorrenza o DIU in ADF oppure prendere in considerazione l'aumento dei limiti di larghezza di banda/IOPS della rete e degli archivi dati.  

### <a name="estimating-price"></a>Stima del prezzo 

> [!NOTE]
> Questo è un esempio ipotetico di prezzi.  I prezzi effettivi dipendono dalla velocità effettiva nell'ambiente.

Si consideri la pipeline seguente costruita per la migrazione dei dati da S3 ad Archiviazione BLOB di Azure:Consider the following pipeline constructed for migrating data from S3 to Azure Blob Storage: 

![pricing-pipeline](media/data-migration-guidance-s3-to-azure-storage/pricing-pipeline.png)

Assumiamo quanto segue: 

- Il volume totale dei dati è 2 PB 
- Migrazione dei dati su HTTPS tramite l'architettura della prima soluzioneMigrating data over HTTPS using first solution architecture 
- 2 PB è diviso in 1K partizioni e ogni copia si muove una partizione 
- Ogni copia è configurata con DIU 256 e raggiunge la velocità effettiva di 1 GBps 
- ForEach concurrency is set to 2 and aggregate throughput is 2 GBps 
- In totale, sono necessarie 292 ore per completare la migrazione 

Ecco il prezzo stimato in base alle ipotesi di cui sopra: 

![tabella dei prezzi](media/data-migration-guidance-s3-to-azure-storage/pricing-table.png)

### <a name="additional-references"></a>Riferimenti aggiuntivi 
- [Connettore Amazon Simple Storage Service](https://docs.microsoft.com/azure/data-factory/connector-amazon-simple-storage-service)
- [Connettore di Archiviazione BLOB di Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
- [Connettore di Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
- [Guida all'ottimizzazione delle prestazioni dell'attività di copia](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)
- [Creazione e configurazione del runtime di integrazione self-hosted](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
- [HA e scalabilità di runtime di integrazione self-hosted](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)
- [Considerazioni relative alla sicurezza per lo spostamento dei dati](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)
- [Archiviare le credenziali in Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
- [Copia il file in modo incrementale in base al nome del file partizionato nel tempo](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-partitioned-file-name-copy-data-tool)
- [Copiare i file nuovi e modificati basati su LastModifiedDate](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-lastmodified-copy-data-tool)
- [Pagina dei prezzi DI ADF](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="template"></a>Modello

Ecco il [modello](solution-template-migration-s3-azure.md) per iniziare con la migrazione di petabyte di dati costituiti da centinaia di milioni di file da Amazon S3 ad Azure Data Lake Storage Gen2.

## <a name="next-steps"></a>Passaggi successivi

- [Copiare file da più contenitori con Azure Data Factory](solution-template-copy-files-multiple-containers.md)
