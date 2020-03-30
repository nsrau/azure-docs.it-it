---
title: Eseguire la migrazione di Archiviazione di Azure Data Lake da Gen1 a Gen2Migrate Azure Data Lake Storage from Gen1 to Gen2
description: Eseguire la migrazione di Archiviazione Lake Lake di Azure da Gen1 a Gen2.Migrate Azure Data Lake Storage from Gen1 to Gen2.
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.date: 03/11/2020
ms.service: storage
ms.reviewer: rukmani-msft
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: fb982324b66c5ac0d2db00eb906ed850827bc72e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79533284"
---
# <a name="migrate-azure-data-lake-storage-from-gen1-to-gen2"></a>Eseguire la migrazione di Archiviazione di Azure Data Lake da Gen1 a Gen2Migrate Azure Data Lake Storage from Gen1 to Gen2

È possibile eseguire la migrazione di dati, carichi di lavoro e applicazioni da Data Lake Storage Gen1 a Data Lake Storage Gen2.

Azure Data Lake Storage Gen2 si basa sull'archiviazione BLOB di Azure e offre un set di funzionalità dedicate all'analisi dei Big Data.Azure Data Lake Storage Gen2 is built on [Azure Blob storage](storage-blobs-introduction.md) and provides a set of capabilities dedicated to Big Data analytics. [Data Lake Storage Gen2](https://azure.microsoft.com/services/storage/data-lake-storage/) combina funzionalità di [Azure Data Lake Storage Gen1,](https://docs.microsoft.com/azure/data-lake-store/index)ad esempio la semantica del file system, la sicurezza e la scalabilità a livello di file e la scalabilità con funzionalità di archiviazione a livelli a basso costo e di disponibilità elevata dall'archiviazione BLOB di [Azure.](storage-blobs-introduction.md)

> [!NOTE]
> Per una lettura più semplice, questo articolo usa il termine *Gen1* per fare riferimento ad Azure Data Lake Storage Gen1 e il termine *Gen2* per fare riferimento ad Azure Data Lake Storage Gen2.

## <a name="recommended-approach"></a>Approccio consigliato

Per eseguire la migrazione a Gen2, è consigliabile l'approccio seguente.

:heavy_check_mark: Passaggio 1: Valutare la prontezza

:heavy_check_mark: Passaggio 2: Preparare la migrazione

:heavy_check_mark: Passaggio 3: Eseguire la migrazione dei carichi di lavoro di dati e applicazioni

:heavy_check_mark: Passo 4: Cutover da Gen1 a Gen2

> [!NOTE]
> Gen1 e Gen2 sono servizi diversi, non è richiesta alcuna esperienza di aggiornamento sul posto, sforzo di migrazione intenzionale richiesto. 

### <a name="step-1-assess-readiness"></a>Passaggio 1: Valutare la prontezzaStep 1: Assess readiness

1. Scopri [l'offerta Data Lake Storage Gen2](https://azure.microsoft.com/services/storage/data-lake-storage/); vantaggi, costi e architettura generale. 

2. [Confrontare le capacità](#gen1-gen2-feature-comparison) di Gen1 con quelle di Gen2. 

3. Esaminare un elenco di [problemi noti](data-lake-storage-known-issues.md) per valutare eventuali lacune nella funzionalità.

4. Gen2 supporta funzionalità di archiviazione BLOB quali [la registrazione diagnostica,](../common/storage-analytics-logging.md) [i livelli](storage-blob-storage-tiers.md)di accesso e i criteri di gestione del ciclo di [vita dell'archiviazione BLOB.](storage-lifecycle-management-concepts.md) Se sei interessante nell'uso di una di queste funzionalità, controlla [il livello di supporto corrente.](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-supported-blob-storage-features)

5. Esaminare lo stato corrente del [supporto dell'ecosistema](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-multi-protocol-access) di Azure per assicurarsi che Gen2 supporti tutti i servizi da cui dipendono le soluzioni.

### <a name="step-2-prepare-to-migrate"></a>Passaggio 2: Preparare la migrazioneStep 2: Prepare to migrate

1. Identificare i set di dati di cui verrà eseguita la migrazione.

   Cogliere questa opportunità per pulire i set di dati non più utilizzati. A meno che non si preveda di eseguire la migrazione di tutti i dati contemporaneamente, si è verificato il momento di identificare gruppi logici di dati di cui è possibile eseguire la migrazione in fasi.
   
2. Determinare l'impatto che una migrazione avrà sull'azienda.

   Ad esempio, valutare se è possibile permettersi qualsiasi tempo di inattività durante la migrazione ha luogo. Queste considerazioni consentono di identificare un modello di migrazione appropriato e di scegliere gli strumenti più appropriati.

3. Creare un piano di migrazione. 

   È consigliabile utilizzare questi modelli di [migrazione.](#migration-patterns) È possibile scegliere uno di questi modelli, combinarli o progettare un modello personalizzato personalizzato.

### <a name="step-3-migrate-data-workloads-and-applications"></a>Passaggio 3: Eseguire la migrazione di dati, carichi di lavoro e applicazioniStep 3: Migrate data, workloads, and applications

Eseguire la migrazione di dati, carichi di lavoro e applicazioni usando il modello preferito. È consigliabile convalidare gli scenari in modo incrementale.

1. [Creare un account di archiviazione](data-lake-storage-quickstart-create-account.md) e abilitare la funzionalità dello spazio dei nomi gerarchico. 

2. Eseguire la migrazione dei dati. 

3. Configurare i servizi nei carichi di lavoro in modo che puntino all'endpoint Gen2.Configure [services in your workloads](data-lake-storage-integrate-with-azure-services.md) to point to your Gen2 endpoint. 
   
4. Aggiornare le applicazioni per l'utilizzo delle API Gen2.Update applications to use Gen2 APIs. Consultate le guide per [.NET](data-lake-storage-directory-file-acl-dotnet.md), [Java](data-lake-storage-directory-file-acl-java.md), [Python,](data-lake-storage-directory-file-acl-python.md) [JavaScript](data-lake-storage-directory-file-acl-javascript.md) e [REST.](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2) 
   
5. Aggiornare gli script per l'utilizzo dei [cmdlet di PowerShell](data-lake-storage-directory-file-acl-powershell.md)di Data Lake Storage Gen2 e dei [comandi dell'interfaccia della riga di comando di Azure.](data-lake-storage-directory-file-acl-cli.md)
   
6. Cercare riferimenti URI che `adl://` contengono la stringa nei file di codice o nei blocchi appunti di Databricks, nei file Apache Hive HQL o in qualsiasi altro file usato come parte dei carichi di lavoro. Sostituire questi riferimenti con [l'URI formattato Gen2](data-lake-storage-introduction-abfs-uri.md) del nuovo account di archiviazione. Ad esempio: l'URI `adl://mydatalakestore.azuredatalakestore.net/mydirectory/myfile` Gen1: potrebbe diventare `abfss://myfilesystem@mydatalakestore.dfs.core.windows.net/mydirectory/myfile`. 

7. Configurare la sicurezza dell'account in modo da includere i ruoli di controllo degli [accessi in base](../common/storage-auth-aad-rbac-portal.md)al ruolo, la sicurezza a livello di [file e cartelle](data-lake-storage-access-control.md)e i firewall e le reti virtuali di Archiviazione [di Azure.](../common/storage-network-security.md)

### <a name="step-4-cutover-from-gen1-to-gen2"></a>Passo 4: Cutover da Gen1 a Gen2

Dopo aver verificato che le applicazioni e i carichi di lavoro sono stabili in Gen2, è possibile iniziare a usare Gen2 per soddisfare gli scenari aziendali. Disattivare tutte le pipeline rimanenti in esecuzione in Gen1 e rimuovere le autorizzazioni per l'account Gen1. 

<a id="gen1-gen2-feature-comparison" />

## <a name="gen1-vs-gen2-capabilities"></a>Funzionalità Gen1 vs Gen2

Questa tabella confronta le funzionalità di Gen1 con quella di Gen2.

|Area |Prima generazione   |Seconda generazione |
|---|---|---|
|Organizzazione dei dati|[Spazio dei nomi gerarchico](data-lake-storage-namespace.md)<br>Supporto di file e cartelle|[Spazio dei nomi gerarchico](data-lake-storage-namespace.md)<br>Supporto per contenitori, file e cartelle |
|Ridondanza geografica| [Lrs](../common/storage-redundancy.md#locally-redundant-storage)| [LRS](../common/storage-redundancy.md#locally-redundant-storage), [RS](../common/storage-redundancy.md#zone-redundant-storage), [GRS](../common/storage-redundancy.md#geo-redundant-storage), [RA-GRS](../common/storage-redundancy.md#read-access-to-data-in-the-secondary-region) |
|Authentication|[Identità gestita da AAD](../../active-directory/managed-identities-azure-resources/overview.md)<br>[Entità servizio](../../active-directory/develop/app-objects-and-service-principals.md)|[Identità gestita da AAD](../../active-directory/managed-identities-azure-resources/overview.md)<br>[Entità servizio](../../active-directory/develop/app-objects-and-service-principals.md)<br>[Chiave di accesso condiviso](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key)|
|Autorizzazione|Gestione - [Controllo degli accessi in base al ruolo](../../role-based-access-control/overview.md)<br>Dati – [ACL](data-lake-storage-access-control.md)|Gestione - [Controllo degli accessi in base al ruolo](../../role-based-access-control/overview.md)<br>Dati - ACL , [controllo degli accessi in base al ruoloData](../../role-based-access-control/overview.md) - [ACLs](data-lake-storage-access-control.md), RBAC |
|Crittografia – Dati inattivi|Lato server: con chiavi [gestite da Microsoft](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) o [dal cliente](../common/encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|Lato server: con chiavi [gestite da Microsoft](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) o [dal cliente](../common/encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Supporto VNET|[Integrazione VNET](../../data-lake-store/data-lake-store-network-security.md)|[Endpoint di servizio](../common/storage-network-security.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json), [Endpoint privati (anteprima pubblica)](../common/storage-private-endpoints.md)|
|Esperienza per gli sviluppatori|[REST](../../data-lake-store/data-lake-store-data-operations-rest-api.md), [.NET](../../data-lake-store/data-lake-store-data-operations-net-sdk.md), [Java](../../data-lake-store/data-lake-store-get-started-java-sdk.md), [Python](../../data-lake-store/data-lake-store-data-operations-python.md), [PowerShell](../../data-lake-store/data-lake-store-get-started-powershell.md), [interfaccia della riga](../../data-lake-store/data-lake-store-get-started-cli-2.0.md) di comando di Azure|[REST](/rest/api/storageservices/data-lake-storage-gen2), [.NET](data-lake-storage-directory-file-acl-dotnet.md), [Java](data-lake-storage-directory-file-acl-java.md), [Python](data-lake-storage-directory-file-acl-python.md), [JavaScript](data-lake-storage-directory-file-acl-javascript.md), [PowerShell](data-lake-storage-directory-file-acl-powershell.md), [interfaccia della riga di](data-lake-storage-directory-file-acl-cli.md) comando di Azure (in anteprima pubblica)|
|Log di diagnostica|Registri classici<br>[Azure Monitor integrato](../../data-lake-store/data-lake-store-diagnostic-logs.md)|[Registri classici](../common/storage-analytics-logging.md) (in anteprima pubblica)<br>Integrazione del monitor di Azure - timeline TBD|
|Ecosistema|[HDInsight (3.6)](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md), [Azure Databricks (3.1 e versioni successive)](https://docs.databricks.com/data/data-sources/azure/azure-datalake.html), [SQL DW](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store), [ADF](../../data-factory/load-azure-data-lake-store.md)|[HDInsight (3.6, 4.0)](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md), [Azure Databricks (5.1 e versioni successive)](https://docs.microsoft.com/azure/databricks/data/data-sources/azure/azure-datalake-gen2), [SQL DW](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md), [ADF](../../data-factory/load-azure-data-lake-storage-gen2.md)|

<a id="migration-patterns" />

## <a name="gen1-to-gen2-patterns"></a>Modelli da Gen1 a Gen2

Scegliere un modello di migrazione e quindi modificare il modello in base alle esigenze.

|||
|---|---|
|**Sollevamento e spostamento**|Il modello più semplice. Ideale se le pipeline di dati possono permettersi tempi di inattività.|
|**Copia incrementale**|Simile a *sollevamento e spostamento*, ma con meno tempi di inattività. Ideale per grandi quantità di dati che richiedono più tempo per la copia.|
|**Doppia conduttura**|Ideale per pipeline che non possono permettersi tempi di inattività.|
|**Sincronizzazione bidirezionale**|Simile alla *doppia pipeline*, ma con un approccio più graduale adatto per pipeline più complesse.|

Diamo un'occhiata più da vicino a ogni modello.
 
### <a name="lift-and-shift-pattern"></a>Modello di sollevamento e spostamento

Questo è lo schema più semplice.

1. Arrestare tutte le scritture a Gen1.

2. Spostare i dati da Gen1 a Gen2. È [consigliabile Azure Data Factory.](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage) Gli ACL copiano con i dati.

3. Operazioni di inserimento dei punti e carichi di lavoro in Gen2.Point ingest operations and workloads to Gen2.

4. Decommissione Gen1.

> [!div class="mx-imgBorder"]
> ![ascensore e spostamento modello](./media/data-lake-storage-migrate-gen1-to-gen2/lift-and-shift.png)

#### <a name="considerations-for-using-the-lift-and-shift-pattern"></a>Considerazioni per l'utilizzo del modello di sollevamento e spostamento

:heavy_check_mark: Cutover da Gen1 a Gen2 per tutti i carichi di lavoro contemporaneamente.

:heavy_check_mark: tempo di inattività previsto durante la migrazione e il periodo di cutover.

:heavy_check_mark: ideale per pipeline che possono permettersi tempi di inattività e tutte le app possono essere aggiornate contemporaneamente.

### <a name="incremental-copy-pattern"></a>Modello di copia incrementale

1. Iniziare a spostare i dati da Gen1 a Gen2. È [consigliabile Azure Data Factory.](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage) Gli ACL copiano con i dati.

2. Copiare in modo incrementale i nuovi dati da Gen1.

3. Dopo aver copiato tutti i dati, arrestare tutte le scritture in Gen1 e indirizzare i carichi di lavoro a Gen2.After all data is copied, stop all writes to Gen1, and point workloads to Gen2.

4. Decommissione Gen1.

> [!div class="mx-imgBorder"]
> ![Modello di copia incrementale](./media/data-lake-storage-migrate-gen1-to-gen2/incremental-copy.png)

#### <a name="considerations-for-using-the-incremental-copy-pattern"></a>Considerazioni sull'utilizzo del modello di copia incrementale:Considerations for using the incremental copy pattern:

:heavy_check_mark: Cutover da Gen1 a Gen2 per tutti i carichi di lavoro contemporaneamente.

:heavy_check_mark: Tempo di inattività previsto solo durante il periodo di cutover.

:heavy_check_mark: ideale per le pipeline in cui tutte le app sono aggiornate contemporaneamente, ma la copia dei dati richiede più tempo.

### <a name="dual-pipeline-pattern"></a>Modello a doppia pipeline

1. Spostare i dati da Gen1 a Gen2. È [consigliabile Azure Data Factory.](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage) Gli ACL copiano con i dati.

2. Invierare nuovi dati sia per Gen1 che per Gen2.

3. Indirizzare i carichi di lavoro a Gen2.Point workloads to Gen2.

4. Arrestare tutte le scritture a Gen1 e quindi rimuovere Gen1.

> [!div class="mx-imgBorder"]
> ![Modello a doppia pipeline](./media/data-lake-storage-migrate-gen1-to-gen2/dual-pipeline.png)

#### <a name="considerations-for-using-the-dual-pipeline-pattern"></a>Considerazioni sull'utilizzo del modello di pipeline duale:Considerations for using the dual pipeline pattern:

:heavy_check_mark: le pipeline Gen1 e Gen2 vengono eseguite side-by-side.

:heavy_check_mark: supporta zero tempi di inattività.

:heavy_check_mark: ideale in situazioni in cui i carichi di lavoro e le applicazioni non possono permettersi tempi di inattività ed è possibile inserire in entrambi gli account di archiviazione.

### <a name="bi-directional-sync-pattern"></a>Modello di sincronizzazione bidirezionale

1. Configurare la replica bidirezionale tra Gen1 e Gen2. Si consiglia [WanDisco](https://docs.wandisco.com/bigdata/wdfusion/adls/). Offre una funzione di riparazione per i dati esistenti.

3. Quando tutti gli spostamenti sono completi, arrestare tutte le scritture in Gen1 e disattivare la replica bidirezionale.

4. Decommissione Gen1.

> [!div class="mx-imgBorder"]
> ![Modello bidirezionale](./media/data-lake-storage-migrate-gen1-to-gen2/bidirectional-sync.png)

#### <a name="considerations-for-using-the-bi-directional-sync-pattern"></a>Considerazioni sull'uso del modello di sincronizzazione bidirezionale:Considerations for using the bi-directional sync pattern:

:heavy_check_mark: ideale per scenari complessi che coinvolgono un numero elevato di pipeline e dipendenze in cui un approccio in più fasi potrebbe avere più senso.  

:heavy_check_mark: sforzo di migrazione è elevato, ma fornisce supporto side-by-side per Gen1 e Gen2.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulle varie parti della configurazione della sicurezza per un account di archiviazione. Vedere Guida alla [sicurezza di Archiviazione di Azure](../common/storage-security-guide.md).
- Ottimizza le prestazioni per il tuo Data Lake Store. Vedere [Ottimizzare Azure Data Lake Storage Gen2 per le prestazioniSee Optimize Azure Data Lake Storage Gen2 for performance](data-lake-storage-performance-tuning-guidance.md)
- Rivedere le procedure consigliate per la gestione dell'archivio dati. Vedere Procedure consigliate per l'uso di [Azure Data Lake Storage Gen2See Best practices for using Azure Data Lake Storage Gen2](data-lake-storage-best-practices.md)

