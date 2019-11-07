---
title: Usare Azure Data Factory per migrare i dati da Amazon S3 ad archiviazione di Azure
description: Usare Azure Data Factory per migrare i dati da Amazon S3 ad archiviazione di Azure.
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
ms.date: 8/04/2019
ms.openlocfilehash: 4d4e0453105dacfbf35624a2a9acb9d5994f4dea
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73675738"
---
# <a name="use-azure-data-factory-to-migrate-data-from-amazon-s3-to-azure-storage"></a>Usare Azure Data Factory per migrare i dati da Amazon S3 ad archiviazione di Azure 

Azure Data Factory offre un meccanismo efficiente, affidabile ed economico per eseguire la migrazione dei dati su larga scala da Amazon S3 all'archiviazione BLOB di Azure o Azure Data Lake Storage Gen2.  Questo articolo fornisce le informazioni seguenti per gli sviluppatori e gli sviluppatori di dati: 

> [!div class="checklist"]
> * Prestazioni 
> * Resilienza della copia
> * Sicurezza di rete
> * Architettura della soluzione di alto livello 
> * Procedure consigliate per l'implementazione  

## <a name="performance"></a>Prestazioni

ADF offre un'architettura senza server che consente il parallelismo a livelli diversi, consentendo agli sviluppatori di compilare pipeline per sfruttare al meglio la larghezza di banda di rete, oltre a IOPS e larghezza di banda di archiviazione per ottimizzare la velocità effettiva di spostamento dei dati per l'ambiente. 

I clienti hanno completato la migrazione di petabyte di dati costituiti da centinaia di milioni di file da Amazon S3 all'archiviazione BLOB di Azure, con una velocità effettiva prolungata di 2 GBps e superiore. 

![prestazioni](media/data-migration-guidance-s3-to-azure-storage/performance.png)

L'immagine precedente illustra come è possibile ottenere velocità di spostamento dei dati eccezionali attraverso diversi livelli di parallelismo:
 
- Una singola attività di copia può sfruttare le risorse di calcolo scalabili: quando si usa Azure Integration Runtime, è possibile specificare [fino a 256 DIUs](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#data-integration-units) per ogni attività di copia in modo senza server. Quando si usa Integration Runtime indipendenti, è possibile scalare manualmente il computer o scalare orizzontalmente in più computer ([fino a 4 nodi](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)) e una singola attività di copia partiziona il set di file in tutti i nodi. 
- Una singola attività di copia legge e scrive nell'archivio dati usando più thread. 
- Il flusso di controllo ADF può avviare più attività di copia in parallelo, ad esempio utilizzando [per ogni ciclo](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity). 

## <a name="resilience"></a>Resilienza

All'interno di una singola esecuzione dell'attività di copia, ADF include un meccanismo di ripetizione dei tentativi incorporato che consente di gestire un determinato livello di errori temporanei negli archivi dati o nella rete sottostante. 

Quando si esegue la copia binaria da S3 a BLOB e da S3 a ADLS Gen2, ADF esegue automaticamente il checkpoint.  Se l'esecuzione di un'attività di copia ha avuto esito negativo o si è verificato un timeout, dopo un nuovo tentativo (assicurarsi di ritentare il conteggio > 1), la copia riprende dall'ultimo punto di errore anziché partire dall'inizio. 

## <a name="network-security"></a>Sicurezza di rete 

Per impostazione predefinita, ADF trasferisce i dati da Amazon S3 all'archiviazione BLOB di Azure o Azure Data Lake Storage Gen2 usando la connessione crittografata su protocollo HTTPS.  HTTPS fornisce la crittografia dei dati in transito e impedisce l'intercettazione e gli attacchi man-in-the-Middle. 

In alternativa, se non si vuole trasferire i dati su Internet pubblico, è possibile ottenere una maggiore sicurezza trasferendo i dati attraverso un collegamento di peering privato tra AWS Direct Connect e Azure Express route.  Per ottenere questo risultato, fare riferimento all'architettura della soluzione riportata di seguito. 

## <a name="solution-architecture"></a>Architettura della soluzione

Migrare i dati su Internet pubblico:

![soluzione-architettura-pubblico-rete](media/data-migration-guidance-s3-to-azure-storage/solution-architecture-public-network.png)

- In questa architettura i dati vengono trasferiti in modo sicuro tramite HTTPS tramite Internet pubblico. 
- Sia l'origine Amazon S3 che l'archivio BLOB di Azure di destinazione o Azure Data Lake Storage Gen2 sono configurati per consentire il traffico da tutti gli indirizzi IP di rete.  Vedere la seconda architettura riportata di seguito per limitare l'accesso di rete a un intervallo di indirizzi IP specifico. 
- È possibile aumentare facilmente la quantità di potenza in modalità senza server per usare completamente la larghezza di banda di rete e di archiviazione, in modo da ottenere la velocità effettiva migliore per l'ambiente. 
- Con questa architettura è possibile ottenere sia la migrazione dello snapshot iniziale che la migrazione dei dati Delta. 

Migrare i dati su un collegamento privato: 

![soluzione-architettura-privato-rete](media/data-migration-guidance-s3-to-azure-storage/solution-architecture-private-network.png)

- In questa architettura, la migrazione dei dati viene eseguita tramite un collegamento peering privato tra AWS Direct Connect e Azure Express route in modo che i dati non attraversino mai la rete Internet pubblica.  Richiede l'uso di AWS VPC e della rete virtuale di Azure. 
- Per ottenere questa architettura, è necessario installare il runtime di integrazione self-hosted di ADF in una macchina virtuale Windows nella rete virtuale di Azure.  È possibile scalare manualmente le VM IR self-hosted o aumentare le prestazioni in più macchine virtuali (fino a 4 nodi) per usare completamente la rete e le operazioni di i/o della larghezza di banda di archiviazione. 
- Se è accettabile trasferire i dati tramite HTTPS, ma si vuole bloccare l'accesso di rete a S3 di origine a un intervallo di indirizzi IP specifico, è possibile adottare una variante di questa architettura rimuovendo AWS VPC e sostituendo il collegamento privato con HTTPS.  È necessario che il runtime di integrazione virtuale e il runtime di integrazione self-hosted di Azure siano disponibili in una macchina virtuale di Azure, in modo che sia possibile disporre di un indirizzo IP instradabile pubblicamente 
- Con questa architettura è possibile ottenere sia la migrazione dei dati dello snapshot iniziale che la migrazione dei dati Delta. 

## <a name="implementation-best-practices"></a>Procedure consigliate per l'implementazione 

### <a name="authentication-and-credential-management"></a>Autenticazione e gestione delle credenziali 

- Per eseguire l'autenticazione nell'account Amazon S3, è necessario usare [la chiave di accesso per l'account IAM](https://docs.microsoft.com/azure/data-factory/connector-amazon-simple-storage-service#linked-service-properties). 
- Sono supportati più tipi di autenticazione per la connessione all'archivio BLOB di Azure.  È consigliabile usare [identità gestite per le risorse di Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity) : basate su un ADF gestito automaticamente in Azure ad, consente di configurare le pipeline senza fornire le credenziali nella definizione del servizio collegato.  In alternativa, è possibile eseguire l'autenticazione nell'archiviazione BLOB di Azure usando un' [entità servizio](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication), una [firma di accesso condiviso](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication)o una [chiave dell'account di archiviazione](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication). 
- Sono inoltre supportati più tipi di autenticazione per la connessione a Azure Data Lake Storage Gen2.  È consigliabile usare [identità gestite per le risorse di Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity) , anche se è possibile usare l' [entità servizio](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication) o la [chiave dell'account di archiviazione](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication) . 
- Quando non si usano identità gestite per le risorse di Azure, [l'archiviazione delle credenziali in Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) è fortemente consigliata per semplificare la gestione e la rotazione centralizzate delle chiavi senza modificare i servizi collegati di ADF.  Questa è anche una delle [procedure consigliate per ci/CD](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd). 

### <a name="initial-snapshot-data-migration"></a>Migrazione iniziale dei dati dello snapshot 

La partizione dei dati è consigliata soprattutto quando si esegue la migrazione di più di 10 TB di dati.  Per partizionare i dati, usare l'impostazione "prefix" per filtrare le cartelle e i file in Amazon S3 in base al nome e quindi ogni processo di copia di ADF può copiare una partizione alla volta.  È possibile eseguire più processi di copia ADF simultaneamente per una migliore velocità effettiva. 

Se uno dei processi di copia ha esito negativo a causa di un problema temporaneo di rete o archivio dati, è possibile rieseguire il processo di copia non riuscito per ricaricare nuovamente la partizione specifica da AWS s3.  Tutti gli altri processi di copia che caricano altre partizioni non saranno interessati. 

### <a name="delta-data-migration"></a>Migrazione dei dati Delta 

Il modo più efficiente per identificare i file nuovi o modificati da AWS S3 consiste nell'usare la convenzione di denominazione partizionata in base al tempo: quando i dati in AWS S3 sono stati partizionati con le informazioni relative all'intervallo di tempo nel nome del file o della cartella (ad esempio,/yyyy/mm/DD/file.csv), la pipeline può identificare facilmente i file e le cartelle da copiare in modo incrementale. 

In alternativa, se i dati in AWS S3 non sono partizionati, ADF è in grado di identificare i file nuovi o modificati in base ai relativi LastModifiedDate.   Il modo in cui funziona è che ADF analizzerà tutti i file di AWS S3 e copierà solo il file nuovo e aggiornato il cui ultimo timestamp modificato è maggiore di un determinato valore.  Tenere presente che se si dispone di un numero elevato di file in S3, l'analisi dei file iniziale potrebbe richiedere molto tempo indipendentemente dal numero di file che corrispondono alla condizione di filtro.  In questo caso è consigliabile partizionare prima i dati, usando la stessa impostazione "prefix" per la migrazione dello snapshot iniziale, in modo che l'analisi dei file possa essere eseguita in parallelo.  

### <a name="for-scenarios-that-require-self-hosted-integration-runtime-on-azure-vm"></a>Per gli scenari che richiedono il runtime di integrazione self-hosted in una macchina virtuale di Azure 

Se si esegue la migrazione dei dati tramite un collegamento privato o si vuole consentire un intervallo di indirizzi IP specifico sul firewall Amazon S3, è necessario installare il runtime di integrazione self-hosted in una macchina virtuale Windows di Azure. 

- La configurazione consigliata per iniziare con per ogni macchina virtuale di Azure è Standard_D32s_v3 con 32 vCPU e 128 GB di memoria.  È possibile monitorare l'utilizzo della CPU e della memoria della VM IR durante la migrazione dei dati per vedere se è necessario aumentare le prestazioni della macchina virtuale per ottenere prestazioni migliori o ridurre la macchina virtuale per ridurre i costi. 
- È anche possibile aumentare la scalabilità orizzontale associando fino a 4 nodi VM con un singolo runtime di integrazione self-hosted.  Un processo di copia singolo in esecuzione su un runtime di integrazione self-hosted suddivide automaticamente il set di file e usa tutti i nodi VM per copiare i file in parallelo.  Per la disponibilità elevata, si consiglia di iniziare con 2 nodi VM per evitare un singolo punto di errore durante la migrazione dei dati. 

### <a name="rate-limiting"></a>Limitazione della frequenza 

Come procedura consigliata, eseguire un POC di prestazioni con un set di dati di esempio rappresentativo, in modo da poter determinare le dimensioni appropriate della partizione. 

Iniziare con una singola partizione e una singola attività di copia con l'impostazione predefinita DIU.  Aumentare gradualmente l'impostazione DIU fino a raggiungere il limite di larghezza di banda della rete o il limite di IOPS/larghezza di banda degli archivi dati oppure è stato raggiunto il numero massimo di 256 DIU consentito per una singola attività di copia. 

Successivamente, aumentare gradualmente il numero di attività di copia simultanee fino a raggiungere i limiti dell'ambiente. 

Quando si verificano errori di limitazione delle richieste segnalati dall'attività di copia ADF, ridurre l'impostazione della concorrenza o di DIU in ADF oppure provare ad aumentare i limiti di larghezza di banda e di IOPS della rete e degli archivi dati.  

### <a name="estimating-price"></a>Prezzo stimato 

> [!NOTE]
> Questo è un esempio di prezzo ipotetico.  I prezzi effettivi variano in base alla velocità effettiva dell'ambiente.

Si consideri la pipeline seguente costruita per la migrazione dei dati da S3 ad archiviazione BLOB di Azure: 

![prezzi-pipeline](media/data-migration-guidance-s3-to-azure-storage/pricing-pipeline.png)

Si supponga quanto segue: 

- Il volume totale dei dati è 2 PB 
- Migrazione dei dati tramite HTTPS usando la prima architettura della soluzione 
- 2 PB è diviso in 1 K partizioni e ogni copia sposta una partizione 
- Ogni copia è configurata con DIU = 256 e ottiene una velocità effettiva di 1 GBps 
- La concorrenza ForEach è impostata su 2 e la velocità effettiva aggregata è di 2 GBps 
- In totale, sono necessarie 292 ore per completare la migrazione 

Ecco il prezzo stimato in base ai presupposti precedenti: 

![prezzi-tabella](media/data-migration-guidance-s3-to-azure-storage/pricing-table.png)

### <a name="additional-references"></a>Riferimenti aggiuntivi 
- [Connettore servizio di archiviazione semplice di Amazon](https://docs.microsoft.com/azure/data-factory/connector-amazon-simple-storage-service)
- [Connettore di Archiviazione BLOB di Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
- [Connettore di Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
- [Guida all'ottimizzazione delle prestazioni dell'attività di copia](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)
- [Creazione e configurazione di Integration Runtime indipendenti](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
- [Disponibilità elevata e scalabilità del runtime di integrazione self-hosted](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)
- [Considerazioni sulla sicurezza dello spostamento dei dati](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)
- [Archiviare le credenziali in Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
- [Copiare il file in modo incrementale in base al nome del file partizionato ora](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-partitioned-file-name-copy-data-tool)
- [Copia i file nuovi e modificati in base a LastModifiedDate](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-lastmodified-copy-data-tool)
- [Pagina dei prezzi di ADF](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="template"></a>Modello

Ecco il [modello](solution-template-migration-s3-azure.md) con cui iniziare a eseguire la migrazione di petabyte di dati costituiti da centinaia di milioni di file da Amazon S3 a Azure Data Lake storage Gen2.

## <a name="next-steps"></a>Passaggi successivi

- [Copia i file da più contenitori con Azure Data Factory](solution-template-copy-files-multiple-containers.md)