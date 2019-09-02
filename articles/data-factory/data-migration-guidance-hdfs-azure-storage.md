---
title: Usare Azure Data Factory per eseguire la migrazione dei dati dal cluster Hadoop locale ad archiviazione di Azure | Microsoft Docs
description: Usare Azure Data Factory per eseguire la migrazione dei dati dal cluster Hadoop locale al servizio di archiviazione di Azure.
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
ms.openlocfilehash: 1b26b22fa9cdf9f6671702ceb9640aa39a6ecf17
ms.sourcegitcommit: 8fea78b4521921af36e240c8a92f16159294e10a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2019
ms.locfileid: "70211606"
---
# <a name="use-azure-data-factory-to-migrate-data-from-on-premises-hadoop-cluster-to-azure-storage"></a>Usare Azure Data Factory per eseguire la migrazione dei dati dal cluster Hadoop locale ad archiviazione di Azure 

Azure Data Factory offre un meccanismo efficiente, affidabile ed economicamente conveniente per la migrazione dei dati su larga scala dalla HDFS locale all'archivio BLOB di Azure o alla Azure Data Lake Storage Gen2. Fondamentalmente, Azure Data Factory contiene due approcci per eseguire la migrazione dei dati da HDFS locale ad Azure. È possibile selezionare ognuno di essi in base allo scenario. 

- Modalità DistCp di ADF. ADF supporta l'uso di [DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) per copiare i file così come sono nel BLOB di Azure (inclusa la [copia](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy)di staging) o Azure Data Lake Store, nel qual caso è possibile sfruttare appieno la potenza del cluster anziché eseguire il runtime di integrazione self-hosted di ADF. Fornirà una migliore velocità effettiva di copia, specialmente quando il cluster è molto potente. In base alla configurazione in Azure Data Factory, l'attività di copia crea automaticamente un comando DistCp, invia il cluster Hadoop e monitora lo stato della copia. Grazie all'uso di ADF integrato con DistCp, il cliente non può solo sfruttare il potente cluster esistente per ottenere la migliore velocità effettiva di copia, ma anche sfruttare i vantaggi offerti dalla pianificazione flessibile e dall'esperienza di monitoraggio unificato di ADF. Per impostazione predefinita, la modalità DistCp di ADF è il modo consigliato per eseguire la migrazione dei dati dal cluster Hadoop locale ad Azure.
- Modalità IR nativa di ADF. In alcuni scenari, DistCp non può funzionare per i casi (ad esempio, nell'ambiente VNET, lo strumento DistCp non supporta il peering privato Express Route e l'endpoint VNet di archiviazione di Azure). Inoltre, a volte non si vuole usare il cluster Hadoop esistente come motore per eseguire la migrazione dei dati, in quanto in questo modo verranno portati carichi pesanti nel cluster, che potrebbero influisca sulle prestazioni dei processi ETL esistenti. In tal caso, è possibile usare la funzionalità nativa di ADF, dove ADF Integration Runtime (IR) può essere il motore per copiare i dati da HDFS locale ad Azure.

Questo articolo fornisce le informazioni seguenti di entrambi gli approcci per gli sviluppatori e gli sviluppatori di dati:
> [!div class="checklist"]
> * Prestazioni 
> * Resilienza della copia
> * Sicurezza di rete
> * Architettura della soluzione di alto livello 
> * Procedure consigliate per l'implementazione  

## <a name="performance"></a>Prestazioni

In modalità DistCp di ADF, la velocità effettiva è identica a quella usata dallo strumento DistCp in modo indipendente, che sfrutta la capacità del cluster Hadoop esistente. DistCp (copia distribuita) è uno strumento usato per la copia tra cluster di grandi dimensioni. USA MapReduce per influire sulla distribuzione, la gestione degli errori e il ripristino e la creazione di report. Espande un elenco di file e directory in input per eseguire il mapping delle attività, ognuna delle quali copia una partizione dei file specificati nell'elenco di origine. Grazie all'uso di ADF integrato con DistCp, è possibile compilare pipeline per usare completamente la larghezza di banda di rete, oltre a IOPS e larghezza di banda di archiviazione per ottimizzare la velocità effettiva di spostamento dei dati per l'ambiente.  

In modalità IR nativa di ADF, consente anche il parallelismo a livelli diversi, che può usare completamente la larghezza di banda di rete, oltre a IOPS e larghezza di banda di archiviazione per ottimizzare la velocità effettiva di spostamento dei dati scalando manualmente il computer IR self-hosted o aumentando il livello di scalabilità orizzontale Runtime di integrazione self-hosted di più computer.

- Una singola attività di copia può sfruttare le risorse di calcolo scalabili. Con il runtime di integrazione self-hosted è possibile scalare manualmente il computer o scalare orizzontalmente in più computer ([fino a 4 nodi](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)) e una singola attività di copia partiziona il set di file in tutti i nodi. 
- Una singola attività di copia legge e scrive nell'archivio dati usando più thread. 
- Il flusso di controllo ADF può avviare più attività di copia in parallelo, ad esempio utilizzando [per ogni ciclo](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity). 

È possibile ottenere altri dettagli dalla [Guida alle prestazioni dell'attività di copia](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)

## <a name="resilience"></a>Resilienza

In modalità DistCp di ADF è possibile utilizzare parametri della riga di comando DistCp diversi, ad esempio-i, ignorare gli errori, aggiornare, scrivere dati quando il file di origine e il file di destinazione presentano dimensioni diverse per ottenere livelli di resilienza diversi.

In modalità IR nativa di ADF, all'interno di una singola esecuzione dell'attività di copia, ADF include un meccanismo di ripetizione dei tentativi incorporato che consente di gestire un determinato livello di errori temporanei negli archivi dati o nella rete sottostante. Quando si esegue la copia binaria da un HDFS locale a un BLOB e da un HDFS locale a un ADLS Gen2, ADF esegue automaticamente il checkpoint in un ambito molto elevato. Se l'esecuzione di un'attività di copia ha avuto esito negativo o si è verificato un timeout, dopo un nuovo tentativo (assicurarsi di ritentare il conteggio > 1), la copia riprende dall'ultimo punto di errore anziché partire dall'inizio.

## <a name="network-security"></a>Sicurezza di rete 

Per impostazione predefinita, ADF trasferisce i dati da HDFS locali all'archivio BLOB di Azure o Azure Data Lake Storage Gen2 usando la connessione crittografata sul protocollo HTTPS.  HTTPS fornisce la crittografia dei dati in transito e impedisce l'intercettazione e gli attacchi man-in-the-Middle. 

In alternativa, se non si vuole trasferire i dati su Internet pubblico, è possibile ottenere una maggiore sicurezza trasferendo i dati tramite un collegamento di peering privato tramite Azure Express route. Per ottenere questo risultato, fare riferimento all'architettura della soluzione riportata di seguito.

## <a name="solution-architecture"></a>Architettura della soluzione

Migrare i dati su Internet pubblico:

![soluzione-architettura-pubblico-rete](media/data-migration-guidance-hdfs-to-azure-storage/solution-architecture-public-network.png)

- In questa architettura i dati vengono trasferiti in modo sicuro tramite HTTPS tramite Internet pubblico. 
- La modalità DistCp di ADF è consigliata per l'uso in un ambiente di rete pubblico. In questo modo, non solo è possibile sfruttare il cluster potente esistente per ottenere la migliore velocità effettiva di copia, ma anche sfruttare i vantaggi della pianificazione flessibile e dell'esperienza di monitoraggio unificato di ADF.
- È necessario installare il runtime di integrazione self-hosted ADF in un computer Windows dietro il firewall aziendale per l'invio del comando DistCp al cluster Hadoop e il monitoraggio dello stato della copia. Dato che questo computer non sarà il motore per lo spostamento dei dati (solo a scopo di controllo), la capacità del computer non ha alcun effetto sulla velocità effettiva di spostamento dei dati.
- Sono supportati i parametri esistenti dal comando DistCp.


Migrare i dati su un collegamento privato: 

![soluzione-architettura-privato-rete](media/data-migration-guidance-hdfs-to-azure-storage/solution-architecture-private-network.png)

- In questa architettura, la migrazione dei dati viene eseguita tramite un collegamento peering privato tramite Azure Express route in modo che i dati non attraversino mai la rete Internet pubblica.
- Lo strumento DistCp non supporta il peering privato Express Route e l'endpoint VNet di archiviazione di Azure, pertanto è consigliabile usare la funzionalità nativa di ADF con Integration Runtime per eseguire la migrazione dei dati.
- Per ottenere questa architettura, è necessario installare il runtime di integrazione self-hosted di ADF in una macchina virtuale Windows nella rete virtuale di Azure. È possibile scalare manualmente la macchina virtuale o aumentare le prestazioni in più macchine virtuali per usare completamente la rete e le operazioni di i/o della larghezza di banda di archiviazione.
- La configurazione consigliata per iniziare con per ogni macchina virtuale di Azure (con il runtime di integrazione self-hosted ADF installato) è Standard_D32s_v3 con 32 vCPU e 128 GB di memoria. È possibile continuare a monitorare l'utilizzo della CPU e della memoria della VM durante la migrazione dei dati per verificare se è necessario aumentare ulteriormente la VM per ottenere prestazioni migliori o ridurre la macchina virtuale per ridurre i costi.
- È anche possibile aumentare la scalabilità orizzontale associando fino a 4 nodi VM con un singolo runtime di integrazione self-hosted. Un processo di copia singolo in esecuzione su un runtime di integrazione self-hosted suddivide automaticamente il set di file e usa tutti i nodi VM per copiare i file in parallelo. Per la disponibilità elevata, si consiglia di iniziare con due nodi VM per evitare un singolo punto di errore durante la migrazione dei dati.
- Con questa architettura è possibile ottenere sia la migrazione dei dati dello snapshot iniziale che la migrazione dei dati Delta.


## <a name="implementation-best-practices"></a>Procedure consigliate per l'implementazione 

### <a name="authentication-and-credential-management"></a>Autenticazione e gestione delle credenziali 

- Per eseguire l'autenticazione a HDFS, è possibile usare [Windows (Kerberos) o Anonymous](https://docs.microsoft.com/azure/data-factory/connector-hdfs#linked-service-properties). 
- Sono supportati più tipi di autenticazione per la connessione all'archivio BLOB di Azure.  È consigliabile usare [identità gestite per le risorse di Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity) : basate su un ADF gestito automaticamente in Azure ad, consente di configurare le pipeline senza fornire le credenziali nella definizione del servizio collegato.  In alternativa, è possibile eseguire l'autenticazione nell'archiviazione BLOB di Azure usando un' [entità servizio](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication), una [firma di accesso condiviso](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication)o una [chiave dell'account di archiviazione](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication). 
- Sono inoltre supportati più tipi di autenticazione per la connessione a Azure Data Lake Storage Gen2.  È consigliabile usare [identità gestite per le risorse di Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity) , anche se è possibile usare l' [entità servizio](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication) o la [chiave dell'account di archiviazione](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication) . 
- Quando non si usano identità gestite per le risorse di Azure, [l'archiviazione delle credenziali in Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) è fortemente consigliata per semplificare la gestione e la rotazione centralizzate delle chiavi senza modificare i servizi collegati di ADF.  Questa è anche una delle [procedure consigliate per ci/CD](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd). 

### <a name="initial-snapshot-data-migration"></a>Migrazione iniziale dei dati dello snapshot 

In modalità DistCp di ADF è possibile creare un'attività di copia per inviare il comando DistCp con parametri diversi per controllare il comportamento iniziale della migrazione dei dati. 

Nella modalità IR nativa di ADF, la partizione dei dati è consigliata in particolare quando si esegue la migrazione di più di 10 TB di dati. Per partizionare i dati, sfruttare i nomi delle cartelle in HDFS e quindi ogni processo di copia ADF può copiare una partizione di cartella alla volta. È possibile eseguire più processi di copia ADF simultaneamente per una migliore velocità effettiva.
Se uno dei processi di copia ha esito negativo a causa di un problema temporaneo di rete o archivio dati, è possibile rieseguire il processo di copia non riuscito per ricaricare nuovamente la partizione specifica da HDFS. Tutti gli altri processi di copia che caricano altre partizioni non saranno interessati.

### <a name="delta-data-migration"></a>Migrazione dei dati Delta 

In modalità DistCp di ADF, è possibile usare i parametri della riga di comando di DistCp "-Update, Write data when source file and Destination file-size" per ottenere la migrazione dei dati Delta.

In modalità IR nativa di ADF, il modo più efficiente per identificare i file nuovi o modificati da HDFS consiste nell'usare la convenzione di denominazione partizionata in base al tempo: quando i dati in HDFS sono stati partizionati con le informazioni relative all'intervallo di tempo nel nome del file o della cartella (ad esempio,/yyyy/mm/dd/ file. csv, quindi la pipeline può identificare facilmente i file e le cartelle da copiare in modo incrementale.
In alternativa, se i dati in HDFS non sono partizionati in base al tempo, ADF è in grado di identificare i file nuovi o modificati dai rispettivi LastModifiedDate. Il modo in cui funziona è che ADF analizzerà tutti i file da HDFS e copierà solo il file nuovo e aggiornato il cui ultimo timestamp modificato è maggiore di un determinato valore. Tenere presente che se si dispone di un numero elevato di file in HDFS, l'analisi dei file iniziale potrebbe richiedere molto tempo indipendentemente dal numero di file che corrispondono alla condizione di filtro. In questo caso è consigliabile partizionare prima i dati, usando la stessa partizione per la migrazione snapshot iniziale, in modo che l'analisi dei file possa essere eseguita in parallelo.

### <a name="estimating-price"></a>Prezzo stimato 

Si consideri la pipeline seguente costruita per la migrazione dei dati da HDFS all'archivio BLOB di Azure: 

![prezzi-pipeline](media/data-migration-guidance-hdfs-to-azure-storage/pricing-pipeline.png)

Si supponga quanto segue: 

- Il volume totale dei dati è di 1 PB
- Migrazione dei dati tramite la seconda architettura della soluzione (modalità IR nativa di ADF)
- 1 PB è diviso in 1000 partizioni e ogni copia sposta una partizione
- Ogni attività di copia è configurata con un runtime di integrazione self-hosted associato a 4 computer e raggiunge la velocità effettiva di 500 MBps.
- La concorrenza ForEach è impostata su 4 e la velocità effettiva aggregata è di 2 GBps
- Per completare la migrazione, in totale sono necessarie 146 ore.


Ecco il prezzo stimato in base ai presupposti precedenti: 

![prezzi-tabella](media/data-migration-guidance-hdfs-to-azure-storage/pricing-table.png)

> [!NOTE]
> Questo è un esempio di prezzo ipotetico.  I prezzi effettivi variano in base alla velocità effettiva dell'ambiente.
> Il prezzo per la VM Windows di Azure (con il runtime di integrazione self-hosted installato) non è incluso.

### <a name="additional-references"></a>Altri riferimenti 
- [Connettore HDFS](https://docs.microsoft.com/azure/data-factory/connector-hdfs)
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

## <a name="next-steps"></a>Passaggi successivi

- [Copia i file da più contenitori con Azure Data Factory](solution-template-copy-files-multiple-containers.md)