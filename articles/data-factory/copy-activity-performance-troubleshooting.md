---
title: Risolvere i problemi relativi alle prestazioni dell'attività di copiaTroubleshoot copy activity
description: Informazioni su come risolvere i problemi relativi alle prestazioni dell'attività di copia in Azure Data Factory.Learn about how to troubleshoot copy activity performance in Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/11/2020
ms.openlocfilehash: 6df1903e828c0c4cafa6589d4a85f4016bed893e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414134"
---
# <a name="troubleshoot-copy-activity-performance"></a>Risolvere i problemi relativi alle prestazioni dell'attività di copiaTroubleshoot copy activity

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo illustra come risolvere i problemi di prestazioni dell'attività di copia in Azure Data Factory.This article outlines how to troubleshoot copy activity performance issue in Azure Data Factory. 

Dopo aver eseguito un'attività di copia, è possibile raccogliere i risultati di esecuzione e le statistiche sulle prestazioni nella visualizzazione [monitoraggio attività di copia.](copy-activity-monitoring.md) Di seguito è riportato un esempio.

![Monitorare i dettagli dell'esecuzione dell'attività di copiaMonitor copy activity run details](./media/copy-activity-overview/monitor-copy-activity-run-details.png)

## <a name="performance-tuning-tips"></a>Suggerimenti per l'ottimizzazione delle prestazioni

In alcuni scenari, quando si esegue un'attività di copia in Data Factory, nella parte superiore verranno visualizzati "Suggerimenti per **l'ottimizzazione delle prestazioni"** nella parte superiore, come illustrato nell'esempio precedente. I suggerimenti indicano il collo di bottiglia identificato da ADF per questa particolare esecuzione della copia, insieme a suggerimenti su come aumentare la velocità effettiva della copia. Provare a apportare la modifica richiesta, quindi eseguire nuovamente la copia.

Come riferimento, attualmente i suggerimenti per l'ottimizzazione delle prestazioni forniscono suggerimenti per i casi seguenti:As a reference, currently the performance tuning tips provide suggestions for the following cases:

| Category              | Suggerimenti per l'ottimizzazione delle prestazioni                                      |
| --------------------- | ------------------------------------------------------------ |
| Specifico dell'archivio dati   | Caricamento dei dati in **Azure Synpase Analytics (in precedenza SQL DW):** suggerire l'utilizzo dell'istruzione PolyBase o COPY se non viene usata. |
| &nbsp;                | Copia dei dati dal/nel database SQL di **Azure:** quando DTU è in condizioni di utilizzo elevato, suggerisci l'aggiornamento a un livello superiore. |
| &nbsp;                | Copia dei dati da/a **Azure Cosmos DB:** quando RU è sottoposto a un utilizzo elevato, suggerire l'aggiornamento a RU di dimensioni maggiori. |
| &nbsp;                | Inserimento di dati da **Amazon Redshift**: suggerire l'utilizzo di UNLOAD se non viene utilizzato. |
| Limitazione dell'archivio datiData store throttling | Se un numero di operazioni di lettura/scrittura viene limitato dall'archivio dati durante la copia, suggerire di controllare e aumentare la frequenza di richieste consentite per l'archivio dati oppure di ridurre il carico di lavoro simultaneo. |
| Tempo di integrazione  | Se si usa un runtime di **integrazione (IR) self-hosted** e l'attività di copia attende a lungo nella coda fino a quando il runtime di integrazione dispone di risorse disponibili per l'esecuzione, suggerire la scalabilità orizzontale o l'aggiornamento del runtime di integrazione. |
| &nbsp;                | Se si usa un runtime di **integrazione** di Azure che si trova in un'area non ottimale con conseguente lettura/scrittura lenta, suggerire di configurare un runtime di integrazione in un'altra area. |
| Tolleranza di errore       | Se si configura la tolleranza di errore e la saltazione di righe incompatibili comporta un rallentamento delle prestazioni, suggerire di garantire che i dati di origine e sink siano compatibili. |
| copia di staging           | Se la copia in fasi è configurata ma non è utile per la coppia di sink di origine, suggerire di rimuoverla. |
| Riprendi                | Quando l'attività di copia viene ripresa dall'ultimo punto di errore, ma si verifica la modifica dell'impostazione DIU dopo l'esecuzione originale, si noti che la nuova impostazione DIU non ha effetto. |

## <a name="understand-copy-activity-execution-details"></a>Comprendere i dettagli di esecuzione dell'attività di copiaUnderstand copy activity execution details

I dettagli di esecuzione e le durate nella parte inferiore della visualizzazione di monitoraggio dell'attività di copia descrivono le fasi chiave dell'attività di copia (vedere l'esempio all'inizio di questo articolo), che è particolarmente utile per la risoluzione delle prestazioni di copia. Il collo di bottiglia dell'esecuzione della copia è quello con la durata più lunga. Fare riferimento alla tabella seguente nella definizione di ogni fase e informazioni su come risolvere i problemi relativi all'attività di [copia in Azure,](#troubleshoot-copy-activity-on-azure-ir) i prodotti di catalogo e risolvere i problemi relativi all'attività di copia nel componente di messaggistica di controllo di messaggistica [automatico self-hosted](#troubleshoot-copy-activity-on-self-hosted-ir) con tali informazioni.

| Fase           | Descrizione                                                  |
| --------------- | ------------------------------------------------------------ |
| Coda           | Tempo trascorso fino all'avvio effettiva dell'attività di copia nel runtime di integrazione. |
| Script di pre-copia | Tempo trascorso tra l'attività di copia a partire dal runtime di accesso e l'attività di copia che termina l'esecuzione dello script di pre-copia nell'archivio dati sink. Applicare quando si configura lo script di pre-copia per i sink di database, ad esempio quando si scrivono dati nel database SQL di Azure eseguire la pulizia prima di copiare nuovi dati. |
| Trasferimento        | Tempo trascorso tra la fine del passaggio precedente e il runtime di accesso guidato che trasferisce tutti i dati dall'origine al sink. I sottopassaggi in "Trasferimento" vengono eseguiti in parallelo.<br><br>- **Tempo per il primo byte:** Il tempo trascorso tra la fine del passaggio precedente e il momento in cui il runtime di proprietà riceve il primo byte dall'archivio dati di origine. Si applica alle origini non basate su file.<br>- **Fonte elenco:** Quantità di tempo dedicato all'enumerazione dei file di origine o delle partizioni di dati. Quest'ultimo si applica quando si configurano le opzioni di partizione per le origini di database, ad esempio quando si copiano dati da database come Oracle/SAP HANA/Teradata/Netezza/etc.<br/>-**Lettura dalla fonte:** Quantità di tempo dedicato al recupero dei dati dall'archivio dati di origine.<br/>- **Scrittura sul lavandino:** Quantità di tempo dedicato alla scrittura dei dati nell'archivio dati sink. |

## <a name="troubleshoot-copy-activity-on-azure-ir"></a>Risolvere i problemi relativi all'attività di copia nel sistema di archiviazione di AzureTroubleshoot copy activity on Azure IR

Seguire i passaggi di [ottimizzazione delle prestazioni](copy-activity-performance.md#performance-tuning-steps) per pianificare ed eseguire test delle prestazioni per lo scenario. 

Quando le prestazioni dell'attività di copia non soddisfano le aspettative, per risolvere i problemi relativi all'attività di copia singola in esecuzione in Azure Integration Runtime, se nella visualizzazione di monitoraggio della copia vengono visualizzati suggerimenti per [l'ottimizzazione](#performance-tuning-tips) delle prestazioni, applicare il suggerimento e riprovare. In caso contrario, comprendere i dettagli di [esecuzione dell'attività](#understand-copy-activity-execution-details)di copia, verificare quale fase ha la durata **più lunga** e applicare le indicazioni riportate di seguito per migliorare le prestazioni di copia:Otherwise, understand copy activity execution details , check which stage has the longest duration, and apply the guidance below to boost copy performance:

- **"Script di pre-copia" ha sperimentato lunga durata:** significa che lo script di pre-copia in esecuzione sul database sink richiede molto tempo per completare. Ottimizzare la logica di script di pre-copia specificata per migliorare le prestazioni. Per ulteriori informazioni sul miglioramento dello script, contattare il team del database.

- **"Trasferimento - Tempo al primo byte" ha sperimentato una lunga durata di lavoro**: significa che la query di origine richiede molto tempo per restituire i dati. Controllare e ottimizzare la query o il server. Se hai bisogno di ulteriore assistenza, contatta il tuo team di data store.

- **"Trasferimento - Origine elenco" ha sperimentato una lunga durata di lavoro**: significa che l'enumerazione dei file di origine o delle partizioni di dati del database di origine è lenta.

  - Quando si copiano dati da un'origine basata su file, se si utilizza il`modifiedDatetimeStart` filtro`modifiedDatetimeEnd`con **caratteri jolly** per il percorso della cartella o il nome del file`wildcardFolderPath` (o `wildcardFileName`), oppure si utilizza il **filtro dell'ora dell'ultima modifica** (o ), si noti che tale filtro comporterebbe l'attività di copia che elenca tutti i file nella cartella specificata sul lato client, quindi si applicherebbe il filtro. Tale enumerazione di file potrebbe diventare il collo di bottiglia, soprattutto quando solo un piccolo set di file ha soddisfatto la regola di filtro.

    - Verificare se è possibile copiare i file in base al percorso o al nome del [file partizionato datetime.](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md) Tale modo non porta onere sulla lista di origine.

    - Verificare se è possibile utilizzare il filtro nativo dell'archivio dati, in particolare "**prefisso**" per Amazon S3 e BLOB di Azure. Il filtro prefisso è un filtro lato server dell'archivio dati e avrebbe prestazioni molto migliori.

    - Prendere in considerazione la possibilità di dividere singoli set di dati di grandi dimensioni in diversi set di dati più piccoli e lasciare che tali processi di copia eseguiti contemporaneamente ognuno gestisca una parte di dati. È possibile eseguire questa operazione con Lookup/GetMetadata , ForEach e Copy. Fare riferimento a [Copiare file da più contenitori](solution-template-copy-files-multiple-containers.md) o Eseguire la migrazione dei dati da Amazon S3 ai modelli di soluzione [ADLS Gen2](solution-template-migration-s3-azure.md) come esempio generale.

  - Verificare se ADF segnala un errore di limitazione nell'origine o se l'archivio dati è in condizioni di utilizzo elevato. In tal caso, ridurre i carichi di lavoro nell'archivio dati o provare a contattare l'amministratore dell'archivio dati per aumentare il limite di limitazione o la risorsa disponibile.

  - Usare il flusso di accesso a ir di Azure nello stesso archivio dati di origine o vicino.

- **"Trasferimento - lettura dalla fonte" ha sperimentato una lunga durata di lavoro**: 

  - Se applicabile, adottare la procedura consigliata per il caricamento dei dati specifico del connettore. Ad esempio, quando si copiano dati da [Amazon Redshift](connector-amazon-redshift.md), configurare l'utilizzo di Redshift UNLOAD.

  - Verificare se ADF segnala un errore di limitazione nell'origine o se l'archivio dati è con un utilizzo elevato. In tal caso, ridurre i carichi di lavoro nell'archivio dati o provare a contattare l'amministratore dell'archivio dati per aumentare il limite di limitazione o la risorsa disponibile.

  - Controllare l'origine della copia e il modello di sink:Check your copy source and sink pattern: 

    - Se il modello di copia supporta più grandi di 4 unità di integrazione dei dati (DIUs), fare riferimento a [questa sezione](copy-activity-performance-features.md#data-integration-units) sui dettagli, in genere è possibile provare ad aumentare le diM per ottenere prestazioni migliori. 

    - In caso contrario, è consigliabile suddividere singoli set di dati di grandi dimensioni in diversi set di dati più piccoli e consentire a tali processi di copia eseguiti contemporaneamente ognuno dei quali gestisce parti di dati. È possibile eseguire questa operazione con Lookup/GetMetadata , ForEach e Copy. Fare riferimento a [Copia file da più contenitori](solution-template-copy-files-multiple-containers.md), Eseguire la migrazione dei dati da Amazon [S3 ad ADLS Gen2](solution-template-migration-s3-azure.md)o [Copia in blocco con modelli](solution-template-bulk-copy-with-control-table.md) di soluzione tabella di controllo come esempio generale.

  - Usare il flusso di accesso a ir di Azure nello stesso archivio dati di origine o vicino.

- **"Trasferimento - scrittura al lavandino" ha sperimentato una lunga durata di lavoro**:

  - Se applicabile, adottare la procedura consigliata per il caricamento dei dati specifico del connettore. Ad esempio, quando si copiano dati in [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md) (in precedenza SQL DW), usare PolyBase o l'istruzione COPY. 

  - Verificare se ADF segnala un errore di limitazione nel sink o se l'archivio dati è con un utilizzo elevato. In tal caso, ridurre i carichi di lavoro nell'archivio dati o provare a contattare l'amministratore dell'archivio dati per aumentare il limite di limitazione o la risorsa disponibile.

  - Controllare l'origine della copia e il modello di sink:Check your copy source and sink pattern: 

    - Se il modello di copia supporta più grandi di 4 unità di integrazione dei dati (DIUs), fare riferimento a [questa sezione](copy-activity-performance-features.md#data-integration-units) sui dettagli, in genere è possibile provare ad aumentare le diM per ottenere prestazioni migliori. 

    - In caso contrario, sintonizzare gradualmente [le copie parallele,](copy-activity-performance-features.md)si noti che troppe copie parallele possono persino danneggiare le prestazioni.

  - Usare il controllo di irrifatto di Azure nello stesso archivio dati di Azure o vicino all'area dell'archivio dati sink.

## <a name="troubleshoot-copy-activity-on-self-hosted-ir"></a>Risolvere i problemi relativi all'attività di copia nel sistema di gestione delle applicazioni self-hostedTroubleshoot copy activity on Self-hosted IR

Seguire i passaggi di [ottimizzazione delle prestazioni](copy-activity-performance.md#performance-tuning-steps) per pianificare ed eseguire test delle prestazioni per lo scenario. 

Se le prestazioni di copia non soddisfano le aspettative, per risolvere i problemi relativi all'attività di copia singola in esecuzione in Azure Integration Runtime, se nella visualizzazione di monitoraggio della copia vengono visualizzati suggerimenti per [l'ottimizzazione](#performance-tuning-tips) delle prestazioni, applicare il suggerimento e riprovare. In caso contrario, comprendere i dettagli di [esecuzione dell'attività](#understand-copy-activity-execution-details)di copia, verificare quale fase ha la durata **più lunga** e applicare le indicazioni riportate di seguito per migliorare le prestazioni di copia:Otherwise, understand copy activity execution details , check which stage has the longest duration, and apply the guidance below to boost copy performance:

- **"Coda" durata:** significa che l'attività di copia attende a lungo nella coda fino a quando il codeR self-hosted non dispone di risorse da eseguire. Controllare la capacità e l'utilizzo dei file IR e [aumentare o ridurre](create-self-hosted-integration-runtime.md#high-availability-and-scalability) la scalabilità in base al carico di lavoro.

- **"Trasferimento - Tempo al primo byte" ha sperimentato una lunga durata di lavoro**: significa che la query di origine richiede molto tempo per restituire i dati. Controllare e ottimizzare la query o il server. Se hai bisogno di ulteriore assistenza, contatta il tuo team di data store.

- **"Trasferimento - Origine elenco" ha sperimentato una lunga durata di lavoro**: significa che l'enumerazione dei file di origine o delle partizioni di dati del database di origine è lenta.

  - Verificare se il computer a raggi Load A-Accesso client ha una bassa latenza di connessione all'archivio dati di origine. Se l'origine è in Azure, è possibile usare [questo strumento](http://www.azurespeed.com/Azure/Latency) per verificare la latenza dal computer a raggi load-appolè all'area di Azure, meno è possibile.

  - Quando si copiano dati da un'origine basata su file, se si utilizza il`modifiedDatetimeStart` filtro`modifiedDatetimeEnd`con **caratteri jolly** per il percorso della cartella o il nome del file`wildcardFolderPath` (o `wildcardFileName`), oppure si utilizza il **filtro dell'ora dell'ultima modifica** (o ), si noti che tale filtro comporterebbe l'attività di copia che elenca tutti i file nella cartella specificata sul lato client, quindi si applicherebbe il filtro. Tale enumerazione di file potrebbe diventare il collo di bottiglia, soprattutto quando solo un piccolo set di file ha soddisfatto la regola di filtro.

    - Verificare se è possibile copiare i file in base al percorso o al nome del [file partizionato datetime.](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md) Tale modo non porta onere sulla lista di origine.

    - Verificare se è possibile utilizzare il filtro nativo dell'archivio dati, in particolare "**prefisso**" per Amazon S3 e BLOB di Azure. Il filtro prefisso è un filtro lato server dell'archivio dati e avrebbe prestazioni molto migliori.

    - Prendere in considerazione la possibilità di dividere singoli set di dati di grandi dimensioni in diversi set di dati più piccoli e lasciare che tali processi di copia eseguiti contemporaneamente ognuno gestisca una parte di dati. È possibile eseguire questa operazione con Lookup/GetMetadata , ForEach e Copy. Fare riferimento a [Copiare file da più contenitori](solution-template-copy-files-multiple-containers.md) o Eseguire la migrazione dei dati da Amazon S3 ai modelli di soluzione [ADLS Gen2](solution-template-migration-s3-azure.md) come esempio generale.

  - Verificare se ADF segnala un errore di limitazione nell'origine o se l'archivio dati è in condizioni di utilizzo elevato. In tal caso, ridurre i carichi di lavoro nell'archivio dati o provare a contattare l'amministratore dell'archivio dati per aumentare il limite di limitazione o la risorsa disponibile.

- **"Trasferimento - lettura dalla fonte" ha sperimentato una lunga durata di lavoro**: 

  - Verificare se il computer a raggi Load A-Accesso client ha una bassa latenza di connessione all'archivio dati di origine. Se l'origine è in Azure, è possibile usare [questo strumento](http://www.azurespeed.com/Azure/Latency) per verificare la latenza dal computer a messaggi di disponibilità automatica alle aree di Azure, meno è possibile.

  - Verificare se il computer a raggi- automatico dispone di larghezza di banda in ingresso sufficiente per leggere e trasferire i dati in modo efficiente. Se l'archivio dati di origine è in Azure, è possibile usare [questo strumento](https://www.azurespeed.com/Azure/Download) per verificare la velocità di download.

  - Controllare la tendenza all'utilizzo della CPU e della memoria del componente di controllo di cpu e del flusso di controllo di accesso self-hosted nel portale di Azure > la pagina di panoramica > data factory. Considerare la possibilità di [aumentare/ridurre il irvalore](create-self-hosted-integration-runtime.md#high-availability-and-scalability) se l'utilizzo della CPU è elevato o se la memoria disponibile è insufficiente.

  - Se applicabile, adottare la procedura consigliata per il caricamento dei dati specifico del connettore. Ad esempio:

    - Durante la copia dei dati da [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [Teradata](connector-teradata.md#teradata-as-source), [SAP HANA](connector-sap-hana.md#sap-hana-as-source), [SAP Table](connector-sap-table.md#sap-table-as-source)e SAP [Open Hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)), abilitare le opzioni di partizione dei dati per copiare i dati in parallelo.

    - Quando si copiano dati da [HDFS,](connector-hdfs.md)configurare l'utilizzo di DistCp.

    - Quando si copiano dati da [Amazon Redshift](connector-amazon-redshift.md), configurare l'utilizzo di Redshift UNLOAD.

  - Verificare se ADF segnala un errore di limitazione nell'origine o se l'archivio dati è con un utilizzo elevato. In tal caso, ridurre i carichi di lavoro nell'archivio dati o provare a contattare l'amministratore dell'archivio dati per aumentare il limite di limitazione o la risorsa disponibile.

  - Controllare l'origine della copia e il modello di sink:Check your copy source and sink pattern: 

    - Se si copiano dati da archivi dati abilitati per le opzioni di partizione, è consigliabile ottimizzare gradualmente [le copie parallele](copy-activity-performance-features.md), si noti che un numero inesime di copie parallele potrebbe persino compromettere le prestazioni.

    - In caso contrario, è consigliabile suddividere singoli set di dati di grandi dimensioni in diversi set di dati più piccoli e consentire a tali processi di copia eseguiti contemporaneamente ognuno dei quali gestisce parti di dati. È possibile eseguire questa operazione con Lookup/GetMetadata , ForEach e Copy. Fare riferimento a [Copia file da più contenitori](solution-template-copy-files-multiple-containers.md), Eseguire la migrazione dei dati da Amazon [S3 ad ADLS Gen2](solution-template-migration-s3-azure.md)o [Copia in blocco con modelli](solution-template-bulk-copy-with-control-table.md) di soluzione tabella di controllo come esempio generale.

- **"Trasferimento - scrittura al lavandino" ha sperimentato una lunga durata di lavoro**:

  - Se applicabile, adottare la procedura consigliata per il caricamento dei dati specifico del connettore. Ad esempio, quando si copiano dati in [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md) (in precedenza SQL DW), usare PolyBase o l'istruzione COPY. 

  - Verificare se il computer a raggi Load A-Accesso remoto dispone di bassa latenza di connessione all'archivio dati sink. Se il sink è in Azure, è possibile usare [questo strumento](http://www.azurespeed.com/Azure/Latency) per verificare la latenza dal computer a raggi Load Arido self-hosted all'area di Azure, meno è.

  - Verificare se il computer a mano self-hosted dispone di larghezza di banda in uscita sufficiente per trasferire e scrivere i dati in modo efficiente. Se l'archivio dati sink è in Azure, è possibile usare [questo strumento](https://www.azurespeed.com/Azure/UploadLargeFile) per controllare la velocità di caricamento.

  - Verificare se la tendenza all'utilizzo della CPU e della memoria del componente di controllo automatico nel portale di Azure > pagina di panoramica > data factory. Considerare la possibilità di [aumentare/ridurre il irvalore](create-self-hosted-integration-runtime.md#high-availability-and-scalability) se l'utilizzo della CPU è elevato o se la memoria disponibile è insufficiente.

  - Verificare se ADF segnala un errore di limitazione nel sink o se l'archivio dati è con un utilizzo elevato. In tal caso, ridurre i carichi di lavoro nell'archivio dati o provare a contattare l'amministratore dell'archivio dati per aumentare il limite di limitazione o la risorsa disponibile.

  - Si consideri di ottimizzare gradualmente [le copie parallele,](copy-activity-performance-features.md)si noti che troppe copie parallele possono persino danneggiare la performance.

## <a name="other-references"></a>Altri riferimenti

Di seguito sono riportati alcuni riferimenti sul monitoraggio e l'ottimizzazione delle prestazioni per alcuni degli archivi dati supportati:

* Archiviazione BLOB di Azure: obiettivi di [scalabilità e prestazioni per l'archiviazione BLOB](../storage/blobs/scalability-targets.md) e l'elenco di controllo prestazioni e [scalabilità per l'archiviazione BLOB.](../storage/blobs/storage-performance-checklist.md)
* Archiviazione tabelle di Azure: obiettivi di [scalabilità e prestazioni per l'archiviazione tabelle](../storage/tables/scalability-targets.md) e l'elenco di controllo [prestazioni e scalabilità per l'archiviazione tabelle.](../storage/tables/storage-performance-checklist.md)
* Database SQL di Azure: è possibile [monitorare le prestazioni](../sql-database/sql-database-single-database-monitor.md) e controllare la percentuale di unità di transazione del database (DTU).
* Azure SQL Data Warehouse: la sua capacità è misurata in unità di data warehouse (DKU). Vedere [Gestire la potenza di calcolo in Azure SQL Data Warehouse (panoramica).](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)
* Azure Cosmos DB: [livelli di prestazioni in Azure Cosmos DB](../cosmos-db/performance-levels.md).
* SQL Server locale: [monitorare e ottimizzare le prestazioni](https://msdn.microsoft.com/library/ms189081.aspx).
* File server locale: [Ottimizzazione delle prestazioni per](https://msdn.microsoft.com/library/dn567661.aspx)i file server .

## <a name="next-steps"></a>Passaggi successivi
Vedere gli altri articoli sull'attività di copia:See the other copy activity articles:

- [Panoramica dell'attività di copia](copy-activity-overview.md)
- [Guida alle prestazioni e alla scalabilità dell'attività di copia](copy-activity-performance.md)
- [Funzionalità di ottimizzazione delle prestazioni dell'attività di copia](copy-activity-performance-features.md)
- [Usare Azure Data Factory per eseguire la migrazione dei dati dal data lake o dal data warehouse ad AzureUse Azure Data Factory to migrate data from your data lake or data warehouse to Azure](data-migration-guidance-overview.md)
- [Eseguire la migrazione dei dati da un archivio dati Amazon S3 ad Archiviazione di Azure](data-migration-guidance-s3-azure-storage.md)
