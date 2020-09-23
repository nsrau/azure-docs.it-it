---
title: Eseguire la migrazione dei dati da un archivio dati Amazon S3 ad Archiviazione di Azure
description: Usare Azure Data Factory per eseguire la migrazione dei dati da un archivio dati Amazon S3 ad Archiviazione di Azure.
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
ms.openlocfilehash: 5de1ef97050f37bb44d87ebae1d95df365952ace
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90984888"
---
# <a name="use-azure-data-factory-to-migrate-data-from-amazon-s3-to-azure-storage"></a>Usare Azure Data Factory per eseguire la migrazione dei dati da un archivio dati Amazon S3 ad Archiviazione di Azure 

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory offre un meccanismo efficiente, affidabile ed economico per eseguire la migrazione dei dati su larga scala da Amazon S3 ad Archiviazione BLOB di Azure o Azure Data Lake Storage Gen2.  In questo articolo vengono fornite le seguenti informazioni per i data engineer e gli sviluppatori: 

> [!div class="checklist"]
> * Prestazioni 
> * Resilienza della copia
> * Sicurezza di rete
> * Architettura di alto livello della soluzione 
> * Procedure consigliate dell'implementazione  

## <a name="performance"></a>Prestazioni

ADF offre un'architettura serverless che consente il parallelismo a livelli diversi, consentendo agli sviluppatori di compilare pipeline per sfruttare al meglio la larghezza di banda di rete, nonché le operazioni di I/O al secondo di archiviazione e la larghezza di banda per ottimizzare la velocità effettiva di spostamento dei dati per l'ambiente. 

I clienti hanno completato la migrazione di petabyte di dati costituiti da centinaia di milioni di file da Amazon S3 ad Archiviazione BLOB di Azure, con una velocità effettiva prolungata di 2 GBps e superiore. 

![Il diagramma mostra diverse partizioni di file in un archivio S3 a W S con le azioni di copia associate all'archivio BLOB di Azure A D L S Gen2.](media/data-migration-guidance-s3-to-azure-storage/performance.png)

L'immagine precedente illustra come è possibile ottenere velocità di spostamento di dati eccezionali mediante diversi livelli di parallelismo:
 
- Una singola attività di copia può sfruttare le risorse di calcolo scalabili: quando si usa Azure Integration Runtime, è possibile specificare [fino a 256 unità di integrazione dati](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#data-integration-units) per ogni attività di copia in modalità serverless; quando si usa il runtime di integrazione self-hosted è possibile aumentare manualmente le prestazioni del computer o aumentare le istanze in più computer ([fino a 4 nodi](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)) e una singola attività di copia eseguirà la partizione del set di file in tutti i nodi. 
- Una singola attività di copia legge da e scrive nell'archivio dati usando più thread. 
- Il flusso di controllo ADF può avviare più attività di copia in parallelo, ad esempio usando il [ciclo For Each](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity). 

## <a name="resilience"></a>Resilienza

All'interno di una singola esecuzione dell'attività di copia, ADF include un meccanismo di ripetizione dei tentativi incorporato che consente di gestire un determinato livello di errori temporanei negli archivi dati o nella rete sottostante. 

Quando si esegue la copia binaria da S3 a BLOB e da S3 ad ADLS Gen2, ADF esegue automaticamente il checkpoint.  Se l'esecuzione di un'attività di copia ha avuto esito negativo o ha raggiunto il timeout, al tentativo successivo la copia riprende dall'ultimo punto di errore anziché ripartire dall'inizio. 

## <a name="network-security"></a>Sicurezza di rete 

Per impostazione predefinita, ADF trasferisce i dati da Amazon S3 ad Archiviazione BLOB di Azure o Azure Data Lake Storage Gen2 usando la connessione crittografata su protocollo HTTPS.  Il protocollo HTTPS offre la crittografia dei dati in transito e impedisce l'intercettazione e gli attacchi man-in-the-middle. 

In alternativa, se non si vuole trasferire i dati mediante Internet pubblico, è possibile ottenere una maggiore sicurezza trasferendo i dati mediante un collegamento di peering privato tra AWS Direct Connect e Azure Express Route.  Per ottenere questo risultato, fare riferimento all'architettura della soluzione riportata di seguito. 

## <a name="solution-architecture"></a>Architettura della soluzione

Migrazione di dati su Internet pubblico:

![Il diagramma mostra la migrazione tramite Internet da parte di H T P da un archivio S3 a W S tramite Azure Integration Runtime in un da D F Azure ad archiviazione di Azure. Il runtime dispone di un canale di controllo con Data Factory.](media/data-migration-guidance-s3-to-azure-storage/solution-architecture-public-network.png)

- In questa architettura i dati vengono trasferiti in modo sicuro tramite HTTPS su rete Internet pubblica. 
- Sia l'origine Amazon S3 sia Archiviazione BLOB di Azure di destinazione o Azure Data Lake Storage Gen2 sono configurati per consentire il traffico da tutti gli indirizzi IP di rete.  Vedere la seconda architettura riportata di seguito sulla modalità di limitazione dell'accesso di rete a un intervallo di indirizzi IP specifico. 
- È possibile aumentare facilmente la quantità di potenza in modalità serverless per usare completamente la larghezza di banda di rete e di archiviazione, in modo da ottenere la velocità effettiva migliore per l'ambiente. 
- Con questa architettura è possibile ottenere sia la migrazione dello snapshot iniziale sia la migrazione dei dati Delta. 

Eseguire la migrazione di dati mediante un collegamento privato: 

![Il diagramma mostra la migrazione tramite una connessione peering privata da un archivio S3 A W S tramite il runtime di integrazione self-hosted in macchine virtuali di Azure agli endpoint del servizio V NET in archiviazione di Azure. Il runtime dispone di un canale di controllo con Data Factory.](media/data-migration-guidance-s3-to-azure-storage/solution-architecture-private-network.png)

- In questa architettura, la migrazione dei dati viene eseguita mediante un collegamento peering privato tra AWS Direct Connect e Azure Express Route in modo che i dati non attraversino mai la rete Internet pubblica.  Richiede l'uso di VPC AWS e della rete virtuale di Azure. 
- Per ottenere questa architettura è necessario installare il runtime di integrazione self-hosted di ADF su una macchina virtuale Windows nella rete virtuale di Azure.  È possibile aumentare manualmente le prestazioni delle macchine virtuali con runtime di integrazione self-hosted o aumentare le istanze in più macchine virtuali (fino a 4 nodi) per usare completamente la rete e le operazioni di I/O al secondo della larghezza di banda di archiviazione. 
- Se è accettabile trasferire i dati tramite HTTPS, ma si vuole bloccare l'accesso di rete a un S3 di origine in un intervallo IP specifico, è possibile adottare una variante di questa architettura rimuovendo VPC AWS e sostituendo il collegamento privato con HTTPS.  Per poter disporre di un IP instradabile pubblicamente a scopo di inserimento nell'elenco elementi consentiti è necessario che il runtime di integrazione virtuale e self-hosted di Azure siano disponibili nella macchina virtuale Azure. 
- Con questa architettura è possibile ottenere sia la migrazione dei dati dello snapshot iniziale sia la migrazione dei dati differenziali. 

## <a name="implementation-best-practices"></a>Procedure consigliate dell'implementazione 

### <a name="authentication-and-credential-management"></a>Gestione dell'autenticazione e delle credenziali 

- Per eseguire l'autenticazione per l'account Amazon S3, è necessario usare la [chiave di accesso per l'account IAM](https://docs.microsoft.com/azure/data-factory/connector-amazon-simple-storage-service#linked-service-properties). 
- Per la connessione ad Archiviazione BLOB di Azure sono supportati più tipi di autenticazione.  L'uso di [identità gestite per le risorse di Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity) è altamente consigliato: basate su un ADF gestito automaticamente in Azure AD, consentono di configurare le pipeline senza fornire le credenziali nella definizione del servizio collegato.  In alternativa, è possibile eseguire l'autenticazione in Archiviazione BLOB di Azure usando l'[entità servizio](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication), la [firma di accesso condiviso](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication) o la [chiave dell'account di archiviazione](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication). 
- Anche per la connessione ad Azure Data Lake Storage Gen2 sono supportati più tipi di autenticazione.  È consigliabile usare [identità gestite per le risorse di Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity), anche se è possibile usare [entità servizio](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication) o [chiave dell'account di archiviazione](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication). 
- Quando non si usano identità gestite per le risorse di Azure, è consigliabile [l'archiviazione delle credenziali in Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) per semplificare la gestione e la rotazione centralizzate delle chiavi senza modificare i servizi collegati di ADF.  Questa è anche una delle [procedure consigliate per CI/CD](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd). 

### <a name="initial-snapshot-data-migration"></a>Migrazione iniziale dei dati dello snapshot 

La partizione dei dati è consigliata soprattutto quando si esegue la migrazione di più di 100 TB di dati.  Per partizionare i dati usare l'impostazione "prefix" per filtrare per nome le cartelle e i file in Amazon S3, quindi ogni processo di copia di ADF può copiare una partizione alla volta.  È possibile eseguire più processi di copia ADF simultaneamente per una migliore velocità effettiva. 

Se uno dei processi di copia ha esito negativo a causa di un problema temporaneo di rete o archivio dati, è possibile rieseguire il processo di copia non riuscito per ricaricare nuovamente la partizione specifica da AWS S3.  Tutti gli altri processi di copia che in quel momento caricano altre partizioni non saranno interessati. 

### <a name="delta-data-migration"></a>Migrazione dei dati Delta 

Il modo più efficiente per identificare i file nuovi o modificati da AWS S3 consiste nell'usare la convenzione di denominazione partizionata in base al tempo: quando i dati in AWS S3 vengono partizionati con le informazioni sull'intervallo di tempo nel nome del file o della cartella (ad esempio, /aaaa/mm/gg/file.csv), la pipeline può identificare facilmente i file o le cartelle da copiare in modo incrementale. 

In alternativa, se i dati in AWS S3 non sono partizionati, ADF è in grado di identificare i file nuovi o modificati in base a LastModifiedDate.   La modalità di funzionamento è che ADF analizzerà tutti i file di AWS S3 e copierà solo il file nuovo e aggiornato il cui ultimo timestamp modificato è maggiore di un determinato valore.  Tenere presente che se si dispone di un numero elevato di file in S3, l'analisi dei file iniziale potrebbe richiedere molto tempo indipendentemente dal numero di file che corrispondono alla condizione di filtro.  In questo caso è consigliabile partizionare prima i dati, usando la stessa impostazione "prefix" usata per la migrazione dello snapshot iniziale, in modo che l'analisi dei file possa essere eseguita in parallelo.  

### <a name="for-scenarios-that-require-self-hosted-integration-runtime-on-azure-vm"></a>Per gli scenari che richiedono il runtime di integrazione self-hosted in una macchina virtuale di Azure 

Se si esegue la migrazione dei dati tramite un collegamento privato o se si desidera consentire un intervallo di indirizzi IP specifico sul firewall Amazon S3, è necessario installare il runtime di integrazione self-hosted in una macchina virtuale Windows di Azure. 

- La configurazione consigliata con cui iniziare per ogni macchina virtuale di Azure è Standard_D32s_v3 con 32 vCPU e 128 GB di memoria.  È possibile monitorare l'uso della CPU e della memoria della VM IR durante la migrazione dei dati per vedere se è necessario aumentare le prestazioni della macchina virtuale per ottenere prestazioni migliori o ridurre le prestazioni della VM per ridurre i costi. 
- È anche possibile aumentare le istanze associando fino a 4 nodi VM con un singolo runtime di integrazione self-hosted.  Un processo di copia singolo in esecuzione su un runtime di integrazione self-hosted esegue automaticamente una partizione del set di file e usa tutti i nodi VM per copiare i file in parallelo.  Per una disponibilità elevata si consiglia di iniziare con 2 nodi VM al fine di evitare un singolo punto di guasto durante la migrazione dei dati. 

### <a name="rate-limiting"></a>Limitazione della frequenza 

Come procedura consigliata eseguire un PoC di prestazioni con un set di dati di esempio rappresentativo, in modo da poter determinare la dimensione appropriata della partizione. 

Iniziare con una singola partizione e una singola attività di copia con l'impostazione DIU predefinita.  Aumentare gradualmente l'impostazione DIU fino a raggiungere il limite di larghezza di banda della rete o il limite di operazioni di I/O al secondo/larghezza di banda degli archivi dati oppure fino a raggiungere il numero massimo di 256 DIU consentito per una singola attività di copia. 

Successivamente, aumentare gradualmente il numero di attività di copia simultanee fino a raggiungere i limiti dell'ambiente. 

Quando si verificano errori di limitazione delle richieste segnalati dall'attività di copia ADF, ridurre l'impostazione della concorrenza o DIU in ADF oppure provare ad aumentare i limiti di larghezza di banda/IOPS della rete e degli archivi dati.  

### <a name="estimating-price"></a>Stima del prezzo 

> [!NOTE]
> Si tratta di un esempio di prezzo ipotetico.  I prezzi effettivi variano in base alla velocità effettiva dell'ambiente.

Si consideri la pipeline seguente costruita per la migrazione dei dati da S3 ad Archiviazione BLOB di Azure: 

![Il diagramma mostra una pipeline per la migrazione dei dati, con il flusso di trigger manuale alla ricerca, il flusso a ForEach, il passaggio a una sottopipeline per ogni partizione che contiene il flusso di copia alla stored procedure. Al di fuori della pipeline, la stored procedure passa ad Azure SQL D B, che passa alla ricerca e a W S S3 Flows da copiare, che scorre nell'archivio BLOB.](media/data-migration-guidance-s3-to-azure-storage/pricing-pipeline.png)

Si supponga quanto segue: 

- Volume di dati totale pari a 2 PB 
- Migrazione dei dati tramite HTTPS usando la prima architettura della soluzione 
- 2 PB suddiviso in 1 K di partizioni e ogni copia sposta una partizione 
- Ogni copia è configurata con DIU = 256 e ottiene una velocità effettiva di 1 GBps 
- La concorrenza ForEach è impostata su 2 e la velocità effettiva aggregata è di 2 GBps 
- In totale, sono necessarie 292 ore per completare la migrazione 

Ecco il prezzo stimato in base ai presupposti precedenti: 

![Lo screenshot di una tabella mostra un prezzo stimato.](media/data-migration-guidance-s3-to-azure-storage/pricing-table.png)

### <a name="additional-references"></a>Riferimenti aggiuntivi 
- [Copiare dati da Amazon Simple Storage Service usando Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-amazon-simple-storage-service)
- [Connettore di Archiviazione BLOB di Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
- [Connettore di Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
- [Guida alle prestazioni delle attività di copia e all'ottimizzazione](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)
- [Creare e configurare un runtime di integrazione self-hosted](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
- [Disponibilità elevata e scalabilità del runtime di integrazione self-hosted](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)
- [Considerazioni relative alla sicurezza per lo spostamento dei dati](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)
- [Archiviare le credenziali in Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
- [Copiare file in modo incrementale in base al nome del file partizionato in base all'ora](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-partitioned-file-name-copy-data-tool)
- [Copiare i file nuovi e modificati in base a LastModifiedDate](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-lastmodified-copy-data-tool)
- [Pagina dei prezzi di ADF](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="template"></a>Modello

Ecco il [modello](solution-template-migration-s3-azure.md) per iniziare a eseguire la migrazione di petabyte di dati costituiti da centinaia di milioni di file da Amazon S3 ad Azure Data Lake Storage Gen2.

## <a name="next-steps"></a>Passaggi successivi

- [Copiare file da più contenitori con Azure Data Factory](solution-template-copy-files-multiple-containers.md)
