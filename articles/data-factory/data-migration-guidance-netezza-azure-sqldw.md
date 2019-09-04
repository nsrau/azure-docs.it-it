---
title: Usare Azure Data Factory per eseguire la migrazione dei dati dal server Netezza locale ad Azure | Microsoft Docs
description: Usare Azure Data Factory per eseguire la migrazione dei dati dal server Netezza locale ad Azure.
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
ms.date: 9/03/2019
ms.openlocfilehash: 4690fd81247035267861b06c204c6db7a052eba5
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70259561"
---
# <a name="use-azure-data-factory-to-migrate-data-from-on-premises-netezza-server-to-azure"></a>Usare Azure Data Factory per eseguire la migrazione dei dati dal server Netezza locale ad Azure 

Azure Data Factory offre un meccanismo efficiente, affidabile ed economicamente conveniente per la migrazione dei dati su larga scala dal server Netezza locale all'archiviazione di Azure o Azure SQL Data Warehouse. Questo articolo fornisce le informazioni seguenti per gli sviluppatori e gli sviluppatori di dati:

> [!div class="checklist"]
> * Prestazioni 
> * Resilienza della copia
> * Sicurezza di rete
> * Architettura della soluzione di alto livello 
> * Procedure consigliate per l'implementazione  

## <a name="performance"></a>Prestazioni

Azure Data Factory offre un'architettura senza server che consente il parallelismo a livelli diversi, consentendo agli sviluppatori di compilare pipeline per sfruttare al meglio la larghezza di banda di rete e la larghezza di banda del database per ottimizzare la velocità effettiva di spostamento dei dati per i ambiente.

![prestazioni](media/data-migration-guidance-netezza-azure-sqldw/performance.png)

- Una singola attività di copia può sfruttare le risorse di calcolo scalabili: quando si usa Azure Integration Runtime, è possibile specificare [fino a 256 DIUs](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#data-integration-units) per ogni attività di copia in modo senza server. Quando si usa Integration Runtime indipendenti, è possibile scalare manualmente il computer o scalare orizzontalmente in più computer ([fino a 4 nodi](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)) e una singola attività di copia distribuirà la partizione in tutti i nodi. 
- Una singola attività di copia legge e scrive nell'archivio dati usando più thread. 
- Azure Data Factory flusso di controllo può avviare più attività di copia in parallelo, ad esempio utilizzando [per ciascun ciclo](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity). 

È possibile ottenere altri dettagli dalla [Guida alle prestazioni dell'attività di copia](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)

## <a name="resilience"></a>Resilienza

All'interno di una singola esecuzione dell'attività di copia, Azure Data Factory dispone di un meccanismo di ripetizione dei tentativi incorporato che consente di gestire un determinato livello di errori temporanei negli archivi dati o nella rete sottostante.

Azure Data Factory attività di copia offre anche due modi per gestire le righe incompatibili durante la copia dei dati tra gli archivi dati di origine e sink. È possibile interrompere l'attività di copia quando vengono rilevati dati non compatibili o continuare a copiare i dati Rest ignorando le righe di dati incompatibili. Inoltre, è possibile registrare le righe incompatibili nell'archivio BLOB di Azure o Azure Data Lake Store per apprendere la causa dell'errore, correggere i dati nell'origine dati e ripetere l'attività di copia.

## <a name="network-security"></a>Sicurezza di rete 

Per impostazione predefinita, Azure Data Factory trasferisce i dati dal server Netezza locale ad archiviazione di Azure o Azure SQL Data Warehouse usando la connessione crittografata sul protocollo HTTPS. Offre la crittografia dei dati in transito e impedisce l'intercettazione e gli attacchi man-in-the-Middle.

In alternativa, se non si vuole trasferire i dati su Internet pubblico, è possibile ottenere una maggiore sicurezza trasferendo i dati tramite un collegamento di peering privato tramite Azure Express route. Per ottenere questo risultato, fare riferimento all'architettura della soluzione riportata di seguito.

## <a name="solution-architecture"></a>Architettura della soluzione

Migrare i dati su Internet pubblico:

![soluzione-architettura-pubblico-rete](media/data-migration-guidance-netezza-azure-sqldw/solution-architecture-public-network.png)

- In questa architettura i dati vengono trasferiti in modo sicuro tramite HTTPS tramite Internet pubblico.
- Per ottenere questa architettura, è necessario installare Azure Data Factory runtime di integrazione self-hosted in un computer Windows dietro il firewall aziendale. Verificare che il Azure Data Factory runtime di integrazione self-hosted in un computer Windows possa accedere direttamente al server Netezza. È possibile scalare manualmente il computer o aumentare il livello di scalabilità orizzontale in più computer per usare completamente la rete e la larghezza di banda di archiviazione dei dati per copiare i dati.
- Con questa architettura è possibile ottenere sia la migrazione dei dati dello snapshot iniziale che la migrazione dei dati Delta.

Migrare i dati su un collegamento privato: 

![soluzione-architettura-privato-rete](media/data-migration-guidance-netezza-azure-sqldw/solution-architecture-private-network.png)

- In questa architettura, la migrazione dei dati viene eseguita tramite un collegamento peering privato tramite Azure Express route in modo che i dati non attraversino mai la rete Internet pubblica. 
- Per realizzare questa architettura, è necessario installare Azure Data Factory runtime di integrazione self-hosted in una VM Windows all'interno della rete virtuale di Azure. È possibile scalare manualmente le VM o aumentare le prestazioni in più macchine virtuali per usare completamente la rete e la larghezza di banda di archiviazione dei dati per copiare i dati.
- Con questa architettura è possibile ottenere sia la migrazione dei dati dello snapshot iniziale che la migrazione dei dati Delta.

## <a name="implementation-best-practices"></a>Procedure consigliate per l'implementazione 

### <a name="authentication-and-credential-management"></a>Autenticazione e gestione delle credenziali 

- Per eseguire l'autenticazione a Netezza, è possibile usare [l'autenticazione ODBC tramite la stringa di connessione](https://docs.microsoft.com/azure/data-factory/connector-netezza#linked-service-properties). 
- Sono supportati più tipi di autenticazione per la connessione all'archivio BLOB di Azure.  È consigliabile usare [identità gestite per le risorse di Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity) : basate su un Azure Data Factory gestito automaticamente in Azure ad, consente di configurare le pipeline senza fornire le credenziali nella definizione del servizio collegato.  In alternativa, è possibile eseguire l'autenticazione nell'archiviazione BLOB di Azure usando un' [entità servizio](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication), una [firma di accesso condiviso](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication)o una [chiave dell'account di archiviazione](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication). 
- Sono inoltre supportati più tipi di autenticazione per la connessione a Azure Data Lake Storage Gen2.  È consigliabile usare [identità gestite per le risorse di Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity) , anche se è possibile usare l' [entità servizio](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication) o la [chiave dell'account di archiviazione](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication) . 
- Sono inoltre supportati più tipi di autenticazione per la connessione a Azure SQL Data Warehouse. È consigliabile usare [identità gestite per le risorse di Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#managed-identity) , anche se è possibile usare l' [entità servizio](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#service-principal-authentication) o [l'autenticazione SQL](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#sql-authentication) .
- Quando non si usano identità gestite per le risorse di Azure, [l'archiviazione delle credenziali in Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) è fortemente consigliata per semplificare la gestione e la rotazione centralizzate delle chiavi senza modificare Azure Data Factory servizi collegati.  Questa è anche una delle [procedure consigliate per ci/CD](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd). 

### <a name="initial-snapshot-data-migration"></a>Migrazione iniziale dei dati dello snapshot 

Per le tabelle di piccole dimensioni, quando le dimensioni del volume sono inferiori a 100 GB oppure è possibile eseguirne la migrazione in Azure entro 2 ore, è possibile fare in modo che ogni processo di copia carichi i dati per ogni tabella. È possibile eseguire più processi di copia Azure Data Factory per caricare tabelle diverse simultaneamente per una migliore velocità effettiva. 

All'interno di ogni processo di copia, è anche possibile raggiungere un certo livello di parallelismo usando l' [impostazione parallelCopies](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#parallel-copy) con l'opzione partition data per eseguire query parallele e copiare i dati in base alle partizioni. Sono disponibili due opzioni per la partizione di dati con i dettagli seguenti.
- È consigliabile iniziare dalla sezione di dati perché è più efficiente.  Verificare che il numero di parallelism nell'impostazione parallelCopies sia inferiore al numero totale di partizioni di sezioni di dati nella tabella in Netezza server.  
- Se le dimensioni del volume per ogni partizione di sezione dati sono ancora grandi (ad esempio, maggiori di 10 GB), si consiglia di passare alla partizione a intervalli dinamici, in cui si avrà maggiore flessibilità per definire il numero di partizioni e le dimensioni del volume per ogni partizione. per colonna di partizione, limite superiore e limite inferiore.

Per le tabelle di grandi dimensioni, quando le dimensioni del volume sono maggiori di 100 GB oppure non è possibile eseguirne la migrazione in Azure entro 2 ore, è consigliabile partizionare i dati in base alla query personalizzata e quindi fare in modo che ogni processo di copia copi una partizione alla volta. È possibile eseguire più processi di copia Azure Data Factory contemporaneamente per una migliore velocità effettiva. Tenere presente che, affinché ogni destinazione del processo di copia carichi una partizione mediante query personalizzata, è comunque possibile abilitare il parallelismo tramite una sezione di dati o un intervallo dinamico per aumentare la velocità effettiva. 

Se uno dei processi di copia ha esito negativo a causa di un problema temporaneo di rete o archivio dati, è possibile rieseguire il processo di copia non riuscito per ricaricare nuovamente la partizione specifica dalla tabella. Tutti gli altri processi di copia che caricano altre partizioni non saranno interessati.

Quando si caricano i dati in Azure SQL Data Warehouse, è consigliabile abilitare la polibase nel processo di copia con un archivio BLOB di Azure come gestione temporanea.

### <a name="delta-data-migration"></a>Migrazione dei dati Delta 

Per identificare le righe nuove o aggiornate della tabella, è necessario utilizzare una colonna timestamp o una chiave di incremento nello schema, quindi archiviare il valore più recente come limite massimo in una tabella esterna, che può essere utilizzata per filtrare i dati Delta per il caricamento successivo dei dati. 

Diverse tabelle possono utilizzare una colonna di filigrana diversa per identificare le righe nuove o aggiornate. Si consiglia di creare una tabella di controllo esterna in cui ogni riga rappresenta una tabella nel server Netezza con il nome della colonna filigrana e il valore limite massimo specifici. 

### <a name="self-hosted-integration-runtime-configuration-on-azure-vm-or-machine"></a>Configurazione del runtime di integrazione self-hosted nel computer o nella macchina virtuale di Azure

Quando si esegue la migrazione dei dati dal server Netezza in Azure, indipendentemente dal fatto che il server Netezza prometta dietro il firewall aziendale o all'interno di un ambiente VNET, è necessario installare il runtime di integrazione self-hosted nel computer o nella macchina virtuale Windows, che è il motore da spostare dati.

- La configurazione consigliata per iniziare con per ogni computer o macchina virtuale è con 32 vCPU e 128 GB di memoria. È possibile continuare a monitorare l'utilizzo della CPU e della memoria del computer IR durante la migrazione dei dati per verificare se è necessario aumentare ulteriormente il computer per ottenere prestazioni migliori o ridurre il computer per risparmiare sui costi.
- È anche possibile aumentare la scalabilità orizzontale associando fino a 4 nodi con un singolo runtime di integrazione self-hosted. Un processo di copia singolo in esecuzione su un runtime di integrazione self-hosted utilizzerà automaticamente tutti i nodi della macchina virtuale per copiare i dati in parallelo. Per la disponibilità elevata, si consiglia di iniziare con 2 nodi VM per evitare un singolo punto di errore durante la migrazione dei dati.

### <a name="rate-limiting"></a>Limitazione della frequenza

Come procedura consigliata, eseguire un POC di prestazioni con un set di dati di esempio rappresentativo, in modo da poter determinare le dimensioni della partizione appropriate per ogni attività di copia. Si consiglia di fare in modo che ogni partizione venga caricata in Azure entro 2 ore.  

Iniziare con una singola attività di copia con un singolo computer IR self-hosted per copiare una tabella. Aumentare gradualmente l'impostazione di parallelCopies in base al numero di partizioni di sezioni di dati nella tabella e verificare se l'intera tabella può essere caricata in Azure entro 2 ore in base alla velocità effettiva visualizzata dal processo di copia. 

Se non è possibile ottenerla e, contemporaneamente, la capacità del nodo IR self-hosted e dell'archivio dati non sono completamente utilizzati, aumentare gradualmente il numero di attività di copia simultanee fino a raggiungere i limiti della rete o del limite della larghezza di banda degli archivi dati. 

È possibile monitorare l'utilizzo della CPU o della memoria nel computer IR indipendente ed essere pronti per la scalabilità verticale della macchina o per la scalabilità orizzontale in più computer quando viene visualizzata la CPU o la memoria completamente utilizzata. 

Quando si verificano errori di limitazione delle richieste segnalati da Azure Data Factory attività di copia, ridurre l'impostazione della concorrenza o di parallelCopies in Azure Data Factory oppure provare ad aumentare i limiti di larghezza di banda e di IOPS della rete e degli archivi dati. 


### <a name="estimating-price"></a>Prezzo stimato 

Si consideri la pipeline seguente costruita per la migrazione dei dati dal server Netezza locale al data warehouse SQL di Azure:

![prezzi-pipeline](media/data-migration-guidance-netezza-azure-sqldw/pricing-pipeline.png)

Si supponga quanto segue: 

- Il volume totale dei dati è 50 TB. 
- Migrazione dei dati tramite la prima architettura della soluzione (il server Netezza è locale dietro il firewall)
- 50 TB è diviso in 500 partizioni e ogni attività di copia sposta una partizione.
- Ogni attività di copia è configurata con un runtime di integrazione self-hosted su 4 computer e raggiunge una velocità effettiva di 20 MBps. (All'interno dell'attività di copia, parallelCopies è impostato su 4 e ogni thread per caricare i dati dalla tabella raggiunge la velocità effettiva di 5 MBps)
- La concorrenza ForEach è impostata su 3 e la velocità effettiva aggregata è 60 MBps.
- Per completare la migrazione, in totale sono necessarie 243 ore.

Ecco il prezzo stimato in base ai presupposti precedenti: 

![prezzi-tabella](media/data-migration-guidance-netezza-azure-sqldw/pricing-table.png)

> [!NOTE]
> Questo è un esempio di prezzo ipotetico. I prezzi effettivi variano in base alla velocità effettiva dell'ambiente. Il prezzo per il computer Windows (con il runtime di integrazione self-hosted installato) non è incluso. 

### <a name="additional-references"></a>Altri riferimenti 
- [Migrazione dei dati da data warehouse relazionali locali ad Azure tramite Azure Data Factory](https://azure.microsoft.com/mediahandler/files/resourcefiles/data-migration-from-on-premise-relational-data-warehouse-to-azure-data-lake-using-azure-data-factory/Data_migration_from_on-prem_RDW_to_ADLS_using_ADF.pdf)
- [Connettore Netezza](https://docs.microsoft.com/azure/data-factory/connector-netezza)
- [Connettore ODBC](https://docs.microsoft.com/azure/data-factory/connector-odbc)
- [Connettore di Archiviazione BLOB di Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
- [Connettore di Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
- [Connettore Azure SQL Data Warehouse](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse)
- [Guida all'ottimizzazione delle prestazioni dell'attività di copia](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)
- [Creazione e configurazione di Integration Runtime indipendenti](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
- [Disponibilità elevata e scalabilità del runtime di integrazione self-hosted](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)
- [Considerazioni sulla sicurezza dello spostamento dei dati](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)
- [Archiviare le credenziali in Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
- [Copiare i dati in modo incrementale da una tabella](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-portal)
- [Copiare i dati in modo incrementale da più tabelle](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-multiple-tables-portal)
- [Pagina dei prezzi di Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="next-steps"></a>Passaggi successivi

- [Copia i file da più contenitori con Azure Data Factory](solution-template-copy-files-multiple-containers.md)