---
title: Eseguire la migrazione di Azure Data Lake Storage da Gen1 a Gen2
description: Eseguire la migrazione di Azure Data Lake Storage da Gen1 a Gen2.
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.date: 03/11/2020
ms.service: storage
ms.reviewer: rukmani-msft
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: e8266e5750a14542e7f115e021daa40b2b0bf8f6
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2020
ms.locfileid: "79130002"
---
# <a name="migrate-azure-data-lake-storage-from-gen1-to-gen2"></a>Eseguire la migrazione di Azure Data Lake Storage da Gen1 a Gen2

È possibile migrare i dati, i carichi di lavoro e le applicazioni da Data Lake Storage Gen1 a Data Lake Storage Gen2.

Azure Data Lake Storage Gen2 è basato sull' [archiviazione BLOB di Azure](storage-blobs-introduction.md) e offre un set di funzionalità dedicate a Big Data Analytics. [Data Lake storage Gen2](https://azure.microsoft.com/services/storage/data-lake-storage/) combina le funzionalità di [Azure Data Lake storage Gen1](https://docs.microsoft.com/azure/data-lake-store/index), ad esempio file System semantica, directory e sicurezza a livello di file e scalabilità con risorse di archiviazione a basso costo, archiviazione a livelli, disponibilità elevata/ripristino di emergenza dall' [archiviazione BLOB di Azure](storage-blobs-introduction.md).

> [!NOTE]
> Per semplificare la lettura, in questo articolo viene usato il termine *Gen1* per fare riferimento a Azure Data Lake storage Gen1 e il termine *Gen2* per fare riferimento a Azure Data Lake storage Gen2.

## <a name="recommended-approach"></a>Approccio consigliato

Per eseguire la migrazione a Gen2, è consigliabile usare l'approccio seguente.

: heavy_check_mark: passaggio 1: valutazione della conformità

: heavy_check_mark: passaggio 2: preparare la migrazione

: heavy_check_mark: passaggio 3: eseguire la migrazione di carichi di lavoro di dati e applicazioni

: heavy_check_mark: passaggio 4: cutover da Gen1 a Gen2

> [!NOTE]
> Gen1 e Gen2 sono servizi diversi, non è disponibile alcuna esperienza di aggiornamento sul posto, è richiesta la migrazione intenzionale. 

### <a name="step-1-assess-readiness"></a>Passaggio 1: valutare la conformità

1. Scopri di più sull' [offerta di data Lake storage Gen2](https://azure.microsoft.com/services/storage/data-lake-storage/); i vantaggi, i costi e l'architettura generale. 

2. [Confrontare le funzionalità](#gen1-gen2-feature-comparison) di Gen1 con quelle di Gen2. 

3. Esaminare un elenco di [problemi noti](data-lake-storage-known-issues.md) per valutare eventuali gap nella funzionalità.

4. Gen2 supporta le funzionalità di archiviazione BLOB, ad esempio la [registrazione diagnostica](../common/storage-analytics-logging.md), i [livelli di accesso](storage-blob-storage-tiers.md)e i criteri di gestione del ciclo di vita dell' [archiviazione BLOB](storage-lifecycle-management-concepts.md). Se si è interessati a usare una di queste funzionalità, esaminare il [livello di supporto corrente](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-multi-protocol-access?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-storage-feature-support).

5. Esaminare lo stato corrente del [supporto ecosistema di Azure](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-multi-protocol-access?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-ecosystem-support) per assicurarsi che Gen2 supporti i servizi da cui dipendono le soluzioni.

### <a name="step-2-prepare-to-migrate"></a>Passaggio 2: preparare la migrazione

1. Identificare i set di dati di cui verrà eseguita la migrazione.

   Cogliere questa opportunità per pulire i set di dati che non vengono più usati. A meno che non si preveda di eseguire la migrazione di tutti i dati contemporaneamente, è necessario identificare i gruppi logici di dati di cui è possibile eseguire la migrazione in fasi.
   
2. Determinare l'effetto della migrazione sulla propria azienda.

   Si consideri, ad esempio, se è possibile permettersi qualsiasi tempo di inattività durante la migrazione. Queste considerazioni possono essere utili per identificare un modello di migrazione adatto e per scegliere gli strumenti più appropriati.

3. Creare un piano di migrazione. 

   Si consiglia di usare questi [modelli di migrazione](#migration-patterns). È possibile scegliere uno di questi modelli, combinarli insieme o progettare un modello personalizzato personalizzato.

### <a name="step-3-migrate-data-workloads-and-applications"></a>Passaggio 3: eseguire la migrazione di dati, carichi di lavoro e applicazioni

Eseguire la migrazione di dati, carichi di lavoro e applicazioni usando il modello che si preferisce. Si consiglia di convalidare gli scenari in modo incrementale.

1. [Creare un account di archiviazione](data-lake-storage-quickstart-create-account.md) e abilitare la funzionalità di spazio dei nomi gerarchico. 

2. Eseguire la migrazione dei dati. 

3. Configurare i [servizi nei carichi di lavoro](data-lake-storage-integrate-with-azure-services.md) in modo che puntino all'endpoint Gen2. 
   
4. Aggiornare le applicazioni per l'uso delle API Gen2. Vedere Guide per [.NET](data-lake-storage-directory-file-acl-dotnet.md), [Java](data-lake-storage-directory-file-acl-java.md), [Python](data-lake-storage-directory-file-acl-python.md), [JavaScript](data-lake-storage-directory-file-acl-javascript.md) e [Rest](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2). 
   
5. Aggiornare gli script per usare Data Lake Storage Gen2 [cmdlet di PowerShell](data-lake-storage-directory-file-acl-powershell.md)e i comandi dell'interfaccia della riga di comando di [Azure](data-lake-storage-directory-file-acl-cli.md).
   
6. Cercare i riferimenti URI che contengono la stringa `adl://` nei file di codice o nei notebook di databricks, Apache Hive file HQL o qualsiasi altro file usato come parte dei carichi di lavoro. Sostituire questi riferimenti con l' [URI formattato Gen2](data-lake-storage-introduction-abfs-uri.md) del nuovo account di archiviazione. Ad esempio: l'URI Gen1: `adl://mydatalakestore.azuredatalakestore.net/mydirectory/myfile` potrebbe diventare `abfss://myfilesystem@mydatalakestore.dfs.core.windows.net/mydirectory/myfile`. 

7. Configurare la sicurezza per l'account in modo da includere [ruoli di controllo degli accessi in base al ruolo](../common/storage-auth-aad-rbac-portal.md), sicurezza a livello di [file e cartelle](data-lake-storage-access-control.md)e [firewall e reti virtuali di archiviazione di Azure](../common/storage-network-security.md).

### <a name="step-4-cutover-from-gen1-to-gen2"></a>Passaggio 4: cutover da Gen1 a Gen2

Quando si è certi che le applicazioni e i carichi di lavoro siano stabili in Gen2, è possibile iniziare a usare Gen2 per soddisfare gli scenari aziendali. Disattivare le pipeline rimanenti in esecuzione su Gen1 e rimuovere le autorizzazioni per l'account Gen1. 

<a id="gen1-gen2-feature-comparison" />

## <a name="gen1-vs-gen2-capabilities"></a>Funzionalità di Gen1 e Gen2

In questa tabella vengono confrontate le funzionalità di Gen1 a quella di Gen2.

|Area |Prima generazione   |Seconda generazione |
|---|---|---|
|Organizzazione dei dati|[Spazio dei nomi gerarchico](data-lake-storage-namespace.md)<br>Supporto di file e cartelle|[Spazio dei nomi gerarchico](data-lake-storage-namespace.md)<br>Supporto di contenitori, file e cartelle |
|Ridondanza geografica| [CON ridondanza locale](../common/storage-redundancy.md#locally-redundant-storage)| [con ridondanza locale](../common/storage-redundancy.md#locally-redundant-storage), [ZRS](../common/storage-redundancy.md#zone-redundant-storage), [GRS](../common/storage-redundancy.md#geo-redundant-storage), [RA-GRS](../common/storage-redundancy.md#read-access-to-data-in-the-secondary-region) |
|Authentication|[Identità gestita di AAD](../../active-directory/managed-identities-azure-resources/overview.md)<br>[Entità servizio](../../active-directory/develop/app-objects-and-service-principals.md)|[Identità gestita di AAD](../../active-directory/managed-identities-azure-resources/overview.md)<br>[Entità servizio](../../active-directory/develop/app-objects-and-service-principals.md)<br>[Chiave di accesso condiviso](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key)|
|Autorizzazione|Gestione- [RBAC](../../role-based-access-control/overview.md)<br>Dati- [ACL](data-lake-storage-access-control.md)|Gestione- [RBAC](../../role-based-access-control/overview.md)<br>[ACL](data-lake-storage-access-control.md)dati, [RBAC](../../role-based-access-control/overview.md) |
|Crittografia: dati inattivi|Lato server: con chiavi [gestite dal servizio](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#microsoft-managed-keys) o gestite dal [cliente](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#customer-managed-keys-with-azure-key-vault)|Lato server: con chiavi [gestite dal servizio](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#microsoft-managed-keys) o gestite dal [cliente](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#customer-managed-keys-with-azure-key-vault)|
|Supporto di VNET|[integrazione rete virtuale](../../data-lake-store/data-lake-store-network-security.md)|[Endpoint di servizio](../common/storage-network-security.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json), [endpoint privati (anteprima pubblica)](../common/storage-private-endpoints.md)|
|Esperienza per gli sviluppatori|[Rest](../../data-lake-store/data-lake-store-data-operations-rest-api.md), [.NET](../../data-lake-store/data-lake-store-data-operations-net-sdk.md), [Java](../../data-lake-store/data-lake-store-get-started-java-sdk.md), [Python](../../data-lake-store/data-lake-store-data-operations-python.md), [PowerShell](../../data-lake-store/data-lake-store-get-started-powershell.md), [interfaccia](../../data-lake-store/data-lake-store-get-started-cli-2.0.md) della riga di comando di Azure|[Rest](https://review.docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2), [.NET](/data-lake-storage-directory-file-acl-dotnet.md), [Java](data-lake-storage-directory-file-acl-java.md), [Python](data-lake-storage-directory-file-acl-python.md), [JavaScript](data-lake-storage-directory-file-acl-javascript.md), [PowerShell](data-lake-storage-directory-file-acl-powershell.md), [interfaccia](data-lake-storage-directory-file-acl-cli.md) della riga di comando di Azure (in anteprima pubblica)|
|Log di diagnostica|Log classici<br>[Monitoraggio di Azure integrato](../../data-lake-store/data-lake-store-diagnostic-logs.md)|[Log classici](../common/storage-analytics-logging.md) (in anteprima pubblica)<br>Integrazione di monitoraggio di Azure-sequenza temporale TBD|
|Ecosistema|[HDInsight (3,6)](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md), [Azure Databricks (3,1 e versioni successive)](https://docs.databricks.com/data/data-sources/azure/azure-datalake.html), [SQL DW](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store), [ADF](../../data-factory/load-azure-data-lake-store.md)|[HDInsight (3,6, 4,0)](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md), [Azure Databricks (5,1 e versioni successive)](https://docs.microsoft.com/azure/databricks/data/data-sources/azure/azure-datalake-gen2), [SQL DW](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md), [ADF](../../data-factory/load-azure-data-lake-storage-gen2.md)|

<a id="migration-patterns" />

## <a name="gen1-to-gen2-patterns"></a>Modelli di Gen1 a Gen2

Scegliere un modello di migrazione, quindi modificare il modello in base alle esigenze.

|||
|---|---|
|**Trasferimento in modalità lift-and-shift**|Modello più semplice. Ideale se le pipeline di dati possono permettersi tempi di inattività.|
|**Copia incrementale**|Simile a *Lift-and-Shift*, ma con minor tempo di inattività. Ideale per grandi quantità di dati che importano più tempo per la copia.|
|**Pipeline doppia**|Ideale per le pipeline che non possono permettersi tempi di inattività.|
|**Sincronizzazione bidirezionale**|Simile alla *pipeline doppia*, ma con un approccio più graduale adatto per pipeline più complesse.|

Diamo uno sguardo più da vicino a ogni modello.
 
### <a name="lift-and-shift-pattern"></a>Modello lift-and-Shift

Questo è il modello più semplice.

1. Arrestare tutte le Scritture in Gen1.

2. Spostare i dati da Gen1 a Gen2. È consigliabile [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage). Gli ACL vengono copiati con i dati.

3. Operazioni di inserimento dei punti e carichi di lavoro in Gen2.

4. Rimuovere le autorizzazioni di Gen1.

![modello lift-and-Shift](./media/data-lake-storage-migrate-gen1-to-gen2/lift-and-shift.png)

#### <a name="considerations-for-using-the-lift-and-shift-pattern"></a>Considerazioni sull'uso del modello lift-and-Shift

: heavy_check_mark: cutover da Gen1 a Gen2 per tutti i carichi di lavoro allo stesso tempo.

: heavy_check_mark: prevedere tempi di inattività durante la migrazione e il periodo di cutover.

: heavy_check_mark: ideale per le pipeline che possono permettersi tempi di inattività e tutte le app possono essere aggiornate contemporaneamente.

### <a name="incremental-copy-pattern"></a>Modello di copia incrementale

1. Iniziare a trasferire i dati da Gen1 a Gen2. È consigliabile [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage). Gli ACL vengono copiati con i dati.

2. Copiare in modo incrementale i nuovi dati da Gen1.

3. Dopo la copia di tutti i dati, arrestare tutte le Scritture in Gen1 e i carichi di lavoro punto in Gen2.

4. Rimuovere le autorizzazioni di Gen1.

![Modello di copia incrementale](./media/data-lake-storage-migrate-gen1-to-gen2/incremental-copy.png)

#### <a name="considerations-for-using-the-incremental-copy-pattern"></a>Considerazioni sull'utilizzo del modello di copia incrementale:

: heavy_check_mark: cutover da Gen1 a Gen2 per tutti i carichi di lavoro allo stesso tempo.

: heavy_check_mark: prevedere tempi di inattività solo durante il periodo cutover.

: heavy_check_mark: ideale per le pipeline in cui tutte le app sono state aggiornate in una sola volta, ma la copia dei dati richiede più tempo.

### <a name="dual-pipeline-pattern"></a>Modello di pipeline doppia

1. Spostare i dati da Gen1 a Gen2. È consigliabile [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage). Gli ACL vengono copiati con i dati.

2. Inserire nuovi dati sia per Gen1 che per Gen2.

3. Carichi di lavoro punti a Gen2.

4. Arrestare tutte le Scritture in Gen1, quindi rimuovere le autorizzazioni di Gen1.

![Modello di pipeline doppia](./media/data-lake-storage-migrate-gen1-to-gen2/dual-pipeline.png)

#### <a name="considerations-for-using-the-dual-pipeline-pattern"></a>Considerazioni sull'uso del modello Dual pipeline:

: heavy_check_mark: le pipeline Gen1 e Gen2 vengono eseguite side-by-side.

: heavy_check_mark: supporta zero tempi di inattività.

: heavy_check_mark: ideale nelle situazioni in cui i carichi di lavoro e le applicazioni non possono permettersi tempi di inattività e possono essere inseriti in entrambi gli account di archiviazione.

### <a name="bi-directional-sync-pattern"></a>Modello di sincronizzazione bidirezionale

1. Configurare la replica bidirezionale tra Gen1 e Gen2. È consigliabile [WanDisco](https://docs.wandisco.com/bigdata/wdfusion/adls/). Offre una funzionalità di ripristino per i dati esistenti.

3. Al termine di tutti gli spostamenti, arrestare tutte le Scritture in Gen1 e disattivare la replica bidirezionale.

4. Rimuovere le autorizzazioni di Gen1.

![Modello bidirezionale](./media/data-lake-storage-migrate-gen1-to-gen2/bidirectional-sync.png)

#### <a name="considerations-for-using-the-bi-directional-sync-pattern"></a>Considerazioni sull'uso del modello di sincronizzazione bidirezionale:

: heavy_check_mark: ideale per scenari complessi che coinvolgono un numero elevato di pipeline e dipendenze in cui un approccio graduale potrebbe essere più appropriato.  

: heavy_check_mark: l'operazione di migrazione è elevata, ma fornisce supporto affiancato per Gen1 e Gen2.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulle varie parti della configurazione della sicurezza per un account di archiviazione. Vedere [Guida alla sicurezza di archiviazione di Azure](../common/storage-security-guide.md).
- Ottimizzare le prestazioni per il Data Lake Store. Vedere [ottimizzare Azure Data Lake storage Gen2 per le prestazioni](data-lake-storage-performance-tuning-guidance.md)
- Esaminare le procedure consigliate per la gestione dei Data Lake Store. Vedere le procedure consigliate [per l'uso di Azure Data Lake storage Gen2](data-lake-storage-best-practices.md)

