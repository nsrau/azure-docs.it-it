---
title: Eseguire la migrazione dei dati da un server Netezza locale ad AzureMigrate data from an on-premises Netezza server to Azure
description: Usare Azure Data Factory per eseguire la migrazione dei dati da un server Netezza locale ad Azure.Use Azure Data Factory to migrate data from an on-premises Netezza server to Azure.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 9/03/2019
ms.openlocfilehash: 80c9929f37b4890387a7625f04db6ce3e37f0cdd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74922125"
---
# <a name="use-azure-data-factory-to-migrate-data-from-an-on-premises-netezza-server-to-azure"></a>Usare Azure Data Factory per eseguire la migrazione dei dati da un server Netezza locale ad AzureUse Azure Data Factory to migrate data from an on-premises Netezza server to Azure 

Azure Data Factory offre un meccanismo performante, affidabile ed economico per eseguire la migrazione dei dati su larga scala da un server Netezza locale all'account di archiviazione di Azure o al database del data warehouse SQL di Azure.Azure Data Factory provides a performant, robust and cost-effective mechanism to migrate data at scale from an on-premises Netezza server to your Azure storage account or Azure SQL Data Warehouse database. 

In questo articolo vengono fornite le seguenti informazioni per i data engineer e gli sviluppatori:

> [!div class="checklist"]
> * Prestazioni 
> * Resilienza della copia
> * Sicurezza di rete
> * Architettura della soluzione di alto livello 
> * Procedure consigliate per l'implementazione  

## <a name="performance"></a>Prestazioni

Azure Data Factory offre un'architettura senza server che consente il parallelismo a vari livelli. Se sei uno sviluppatore, questo significa che è possibile creare pipeline per usare completamente la larghezza di banda di rete e del database per ottimizzare la velocità effettiva di spostamento dei dati per l'ambiente.

![Diagramma delle prestazioni](media/data-migration-guidance-netezza-azure-sqldw/performance.png)

Il diagramma precedente può essere interpretato come segue:

- Una singola attività di copia può sfruttare le risorse di calcolo scalabili. Quando si usa Azure Integration Runtime, è possibile specificare [fino a 256 DIUs](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#data-integration-units) per ogni attività di copia in modo senza server. Con un runtime di integrazione self-hosted (IR self-hosted), è possibile aumentare manualmente il computer o la scalabilità orizzontale a più computer[(fino a quattro nodi](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)) e una singola attività di copia distribuisce la partizione in tutti i nodi. 

- Una singola attività di copia legge e scrive nell'archivio dati utilizzando più thread. 

- Il flusso di controllo di Azure Data Factory può avviare più attività di copia in parallelo. Ad esempio, è possibile avviarli utilizzando un [ciclo For Each](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity). 

Per ulteriori informazioni, consultate Guida alle [prestazioni e alla scalabilità dell'attività di copia.](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)

## <a name="resilience"></a>Resilienza

All'interno di una singola attività di copia, Azure Data Factory dispone di un meccanismo di ripetizione dei tentativi incorporato, che consente di gestire un determinato livello di errori temporanei negli archivi dati o nella rete sottostante.

Con l'attività di copia di Azure Data Factory, quando si copiano dati tra archivi dati di origine e sink, sono disponibili due modi per gestire le righe incompatibili. È possibile interrompere e non riuscire l'attività di copia o continuare a copiare il resto dei dati ignorando le righe di dati incompatibili. Inoltre, per conoscere la causa dell'errore, è possibile registrare le righe incompatibili nell'archiviazione BLOB di Azure o nell'archivio di Azure Data Lake Store, correggere i dati nell'origine dati e ripetere l'attività di copia.

## <a name="network-security"></a>Sicurezza di rete 

Per impostazione predefinita, Azure Data Factory trasferisce i dati dal server Netezza locale a un account di archiviazione di Azure o a un database di Azure SQL Data Warehouse usando una connessione crittografata tramite HTTPS (Hypertext Transfer Protocol Secure). HTTPS fornisce la crittografia dei dati in transito e previene le intercettazioni e gli attacchi man-in-the-middle.

In alternativa, se non si desidera che i dati vengano trasferiti tramite Internet pubblico, è possibile ottenere una maggiore sicurezza trasferendo i dati tramite un collegamento di peering privato tramite Azure Express Route.Alternatively, if you don't want data to be transferred over the public internet, you can help help achieve higher security by transfering data over a private peering link via Azure Express Route. 

Nella sezione successiva viene illustrato come ottenere una maggiore sicurezza.

## <a name="solution-architecture"></a>Architettura della soluzione

In questa sezione vengono illustrati due modi per eseguire la migrazione dei dati.

### <a name="migrate-data-over-the-public-internet"></a>Eseguire la migrazione dei dati su Internet pubblicoMigrate data over the public internet

![Eseguire la migrazione dei dati su Internet pubblicoMigrate data over the public internet](media/data-migration-guidance-netezza-azure-sqldw/solution-architecture-public-network.png)

Il diagramma precedente può essere interpretato come segue:

- In questa architettura, i dati vengono trasferiti in modo sicuro utilizzando HTTPS su Internet pubblico.

- Per ottenere questa architettura, è necessario installare il runtime di integrazione di Azure Data Factory (self-hosted) in un computer Windows dietro un firewall aziendale. Assicurarsi che questo runtime di integrazione possa accedere direttamente al server Netezza. Per utilizzare completamente la larghezza di banda della rete e dell'archivio dati per copiare i dati, è possibile aumentare manualmente il computer o la scalabilità orizzontale su più computer.

- Utilizzando questa architettura, è possibile eseguire la migrazione sia dei dati dello snapshot iniziale che dei dati delta.

### <a name="migrate-data-over-a-private-network"></a>Eseguire la migrazione dei dati su una rete privataMigrate data over a private network 

![Eseguire la migrazione dei dati su una rete privataMigrate data over a private network](media/data-migration-guidance-netezza-azure-sqldw/solution-architecture-private-network.png)

Il diagramma precedente può essere interpretato come segue:

- In questa architettura si esegue la migrazione dei dati tramite un collegamento di peering privato tramite Azure Express Route e i dati non vengono mai attraversati su Internet pubblico. 

- Per ottenere questa architettura, è necessario installare il runtime di integrazione di Azure Data Factory (self-hosted) in una macchina virtuale (VM) Windows all'interno della rete virtuale di Azure.To achieve this architecture, you need to install the Azure Data Factory integration runtime (self-hosted) on a Windows virtual machine (VM) within your Azure virtual network. Per usare completamente la larghezza di banda della rete e dell'archivio dati per copiare i dati, è possibile aumentare manualmente la macchina virtuale o la scalabilità orizzontale in più macchine virtuali.

- Utilizzando questa architettura, è possibile eseguire la migrazione sia dei dati dello snapshot iniziale che dei dati delta.

## <a name="implement-best-practices"></a>Implementare le procedure consigliate 

### <a name="manage-authentication-and-credentials"></a>Gestire l'autenticazione e le credenziali 

- Per eseguire l'autenticazione a Netezza, è possibile utilizzare [l'autenticazione ODBC tramite](https://docs.microsoft.com/azure/data-factory/connector-netezza#linked-service-properties)la stringa di connessione . 

- Per eseguire l'autenticazione nell'archiviazione BLOB di Azure:To authenticate to Azure Blob storage: 

   - È consigliabile usare [le identità gestite per le risorse](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity)di Azure.We recommend using managed identities for Azure resources . Basate su un'identità di Azure Data Factory gestita automaticamente in Azure Active Directory (Azure AD), le identità gestite consentono di configurare le pipeline senza dover fornire le credenziali nella definizione del servizio collegato.  

   - In alternativa, è possibile eseguire l'autenticazione all'archiviazione BLOB di Azure usando [l'entità servizio,](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication)una firma di [accesso condiviso](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication)o una [chiave dell'account](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication)di archiviazione. 

- To authenticate to Azure Data Lake Storage Gen2: 

   - È consigliabile usare [le identità gestite per le risorse](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity)di Azure.We recommend using managed identities for Azure resources .
   
   - È anche possibile usare [l'entità servizio](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication) o una [chiave dell'account](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication)di archiviazione. 

- To authenticate to Azure SQL Data Warehouse:

   - È consigliabile usare [le identità gestite per le risorse](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#managed-identity)di Azure.We recommend using managed identities for Azure resources .
   
   - È inoltre possibile utilizzare [l'entità servizio](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#service-principal-authentication) o [l'autenticazione SQL](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#sql-authentication).

- Quando non si usano le identità gestite per le risorse di Azure, è consigliabile [archiviare le credenziali nell'insieme](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) di credenziali delle chiavi di Azure per semplificare la gestione e la rotazione centralizzata delle chiavi senza dover modificare i servizi collegati di Azure Data Factory.When you're not using managed identities for Azure resources, we recommend storing the credentials in Azure Key Vault to make it easier to centrally manage and rotate keys without having to modify Azure Data Factory linked services. Questa è anche una delle [best practice per CI/CD](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd). 

### <a name="migrate-initial-snapshot-data"></a>Eseguire la migrazione dei dati snapshot inizialiMigrate initial snapshot data 

Per tabelle di piccole dimensioni, ovvero tabelle con un volume inferiore a 100 GB o di cui è possibile eseguire la migrazione in Azure entro due ore, è possibile rendere ogni copia dei dati di caricamento del processo per tabella. Per una maggiore velocità effettiva, è possibile eseguire più processi di copia di Azure Data Factory per caricare contemporaneamente tabelle separate. 

All'interno di ogni processo di copia, per eseguire query parallele e copiare dati per partizioni, è anche possibile raggiungere un certo livello di parallelismo utilizzando [ `parallelCopies` l'impostazione](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#parallel-copy) della proprietà con una delle seguenti opzioni di partizione dei dati:

- Per ottenere una maggiore efficienza, ti invitiamo a partire da una sezione di dati.  Assicurarsi che il `parallelCopies` valore nell'impostazione sia inferiore al numero totale di partizioni di sezione dati nella tabella sul server Netezza.  

- Se il volume di ogni partizione di sezione dati è ancora grande (ad esempio, 10 GB o superiore), ti consigliamo di passare a una partizione di intervallo dinamico. Questa opzione offre una maggiore flessibilità per definire il numero di partizioni e il volume di ogni partizione in base alla colonna della partizione, al limite superiore e al limite inferiore.

Per le tabelle di dimensioni maggiori, ovvero le tabelle con un volume pari o superiore a 100 GB o che *non possono* essere migrate in Azure entro due ore, è consigliabile partizionare i dati in base a una query personalizzata e quindi creare ogni copia copy-job di copia una partizione alla volta. Per una migliore velocità effettiva, è possibile eseguire contemporaneamente più processi di copia di Azure Data Factory.For better throughput, you can run multiple Azure Data Factory copy jobs concurrently. Per ogni destinazione del processo di copia del caricamento di una partizione tramite query personalizzata, è possibile aumentare la velocità effettiva abilitando il parallelismo tramite la sezione di dati o l'intervallo dinamico. 

Se un processo di copia non riesce a causa di un problema temporaneo di rete o dell'archivio dati, è possibile eseguire nuovamente il processo di copia non riuscita per ricaricare la partizione specifica dalla tabella. Altri processi di copia che caricano altre partizioni non sono interessati.

Quando si caricano dati in un database di Azure SQL Data Warehouse, è consigliabile abilitare PolyBase all'interno del processo di copia con l'archiviazione BLOB di Azure come gestione temporanea.

### <a name="migrate-delta-data"></a>Eseguire la migrazione dei dati deltaMigrate delta data 

Per identificare le righe nuove o aggiornate della tabella, utilizzare una colonna timestamp o una chiave di incremento all'interno dello schema. È quindi possibile archiviare il valore più recente come filigrana alta in una tabella esterna e quindi utilizzarlo per filtrare i dati delta al successivo caricamento dei dati. 

Ogni tabella può utilizzare una colonna watermark diversa per identificare le righe nuove o aggiornate. Si consiglia di creare una tabella di controllo esterna. Nella tabella, ogni riga rappresenta una tabella sul server Netezza con il nome di colonna filigrana specifico e il valore massimo della filigrana. 

### <a name="configure-a-self-hosted-integration-runtime"></a>Configurare un runtime di integrazione self-hostedConfigure a self-hosted integration runtime

Se si esegue la migrazione dei dati dal server Netezza ad Azure, indipendentemente dal fatto che il server sia in locale dietro il firewall aziendale o all'interno di un ambiente di rete virtuale, è necessario installare un metodo di integrazione continua in una macchina o in una macchina virtuale Windows, ovvero il motore utilizzato per spostare i dati. Durante l'installazione del componente di distribuzione autonomo, è consigliabile utilizzare l'approccio seguente:

- Per ogni computer Windows o macchina virtuale, iniziare con una configurazione di 32 vCPU e 128 GB di memoria. È possibile continuare a monitorare l'utilizzo della CPU e della memoria del computer a oggetti a ricerca e riprova durante la migrazione dei dati per verificare se è necessario aumentare ulteriormente la scalabilità del computer per migliorare le prestazioni o ridurre il computer per risparmiare sui costi.

- È inoltre possibile eseguire la scalabilità orizzontale associando fino a quattro nodi a un singolo componente di accesso a motore self-hosted. Un singolo processo di copia in esecuzione su un assembly di disponibilità automatico che esegue un componente di gestione delle macchine virtuali applica automaticamente tutti i nodi della macchina virtuale per copiare i dati in parallelo. Per la disponibilità elevata, iniziare con quattro nodi VM per evitare un singolo punto di errore durante la migrazione dei dati.

### <a name="limit-your-partitions"></a>Limitare le partizioni

Come procedura consigliata, eseguire una prova di concetto (POC) delle prestazioni con un set di dati di esempio rappresentativo, in modo da poter determinare una dimensione di partizione appropriata per ogni attività di copia. Si consiglia di caricare ogni partizione in Azure entro due ore.  

Per copiare una tabella, iniziare con una singola attività di copia con un singolo computer a sapieri autohosted. Aumentare `parallelCopies` gradualmente l'impostazione in base al numero di partizioni di sezione dati nella tabella. Verificare se l'intera tabella può essere caricata in Azure entro due ore, in base alla velocità effettiva risultante dal processo di copia. 

Se non può essere caricato in Azure entro due ore e la capacità del nodo A/IR self-hosted e dell'archivio dati non viene completamente utilizzata, aumentare gradualmente il numero di attività di copia simultanee fino a raggiungere il limite della rete o il limite di larghezza di banda dei dati Negozi. 

Continuare a monitorare l'utilizzo della CPU e della memoria sul computer a distribuzione automatica ed essere pronti per la scalabilità verticale del computer o la scalabilità orizzontale su più computer quando si nota che la CPU e la memoria sono completamente utilizzate. 

Quando si verificano errori di limitazione, come riportato dall'attività di copia di Azure Data Factory, ridurre la concorrenza o `parallelCopies` l'impostazione in Azure Data Factory oppure è consigliabile aumentare i limiti di larghezza di banda o operazioni di I/O al secondo (IOPS) della rete e degli archivi dati. 


### <a name="estimate-your-pricing"></a>Stimare i prezzi 

Si consideri la pipeline seguente, che viene costruita per eseguire la migrazione dei dati dal server Netezza locale a un database di Azure SQL Data Warehouse:Consider the following pipeline, which is constructed to migrate data from the on-premises Netezza server to an Azure SQL Data Warehouse database:

![La pipeline dei prezzi](media/data-migration-guidance-netezza-azure-sqldw/pricing-pipeline.png)

Supponiamo che le seguenti affermazioni siano vere: 

- Il volume totale dei dati è 50 terabyte (TB). 

- Stiamo eseguendo la migrazione dei dati utilizzando l'architettura della prima soluzione (il server Netezza è locale, dietro il firewall).

- Il volume da 50 TB è suddiviso in 500 partizioni e ogni attività di copia sposta una partizione.

- Ogni attività di copia è configurata con un componente di gestione delle operazioni di gestione delle operazioni autonome su quattro computer e raggiunge una velocità effettiva di 20 megabyte al secondo (MBps). (All'interno `parallelCopies` dell'attività di copia, è impostato su 4 e ogni thread per caricare i dati dalla tabella raggiunge una velocità effettiva 5-MBps.)

- La concorrenza ForEach è impostata su 3 e la velocità effettiva aggregata è 60 MBps.The ForEach concurrency is set to 3 and the aggregate throughput is 60 MBps.

- In totale, sono necessarie 243 ore per completare la migrazione.

Sulla base dei presupposti precedenti, ecco il prezzo stimato: 

![La tabella dei prezzi](media/data-migration-guidance-netezza-azure-sqldw/pricing-table.png)

> [!NOTE]
> Il prezzo indicato nella tabella precedente è ipotetico. I prezzi effettivi dipendono dalla velocità effettiva nell'ambiente. Il prezzo per la macchina Windows (con il codice a- automatico auto-ospitato installato) non è incluso. 

### <a name="additional-references"></a>Riferimenti aggiuntivi

Per altre informazioni, vedere gli articoli e le guide seguenti:For more information, see the following articles and guides:

- [Eseguire la migrazione dei dati da un database data warehouse relazionale locale ad Azure tramite Azure Data FactoryMigrate data from an on-premises relational Data Warehouse database to Azure by using Azure Data Factory](https://azure.microsoft.com/resources/data-migration-from-on-premise-relational-data-warehouse-to-azure-data-lake-using-azure-data-factory/)
- [Connettore Netezza](https://docs.microsoft.com/azure/data-factory/connector-netezza)
- [Connettore ODBC](https://docs.microsoft.com/azure/data-factory/connector-odbc)
- [Connettore di archiviazione BLOB di AzureAzure Blob storage connector](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
- [Connettore di Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
- [Connettore sql Data Warehouse di AzureAzure SQL Data Warehouse connector](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse)
- [Guida all'ottimizzazione delle prestazioni dell'attività di copia](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)
- [Creare e configurare un runtime di integrazione self-hosted](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
- [HA e scalabilità di runtime di integrazione self-hosted](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)
- [Considerazioni relative alla sicurezza per lo spostamento dei dati](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)
- [Archiviare le credenziali nell'insieme di credenziali delle chiavi di AzureStore credentials in Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
- [Copiare i dati in modo incrementale da una tabella](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-portal)
- [Copiare i dati in modo incrementale da più tabelle](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-multiple-tables-portal)
- [Pagina dei prezzi di Azure Data FactoryAzure Data Factory pricing page](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="next-steps"></a>Passaggi successivi

- [Copiare file da più contenitori usando Azure Data FactoryCopy files from multiple containers by using Azure Data Factory](solution-template-copy-files-multiple-containers.md)
