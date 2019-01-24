---
title: Aggiornare le soluzioni di analisi dei Big Data da Azure Data Lake Storage Gen1 ad Azure Data Lake Storage Gen2 (anteprima)
description: Aggiornare la soluzione per usare Azure Data Lake Storage Gen2 (anteprima)
services: storage
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.date: 12/19/2018
ms.service: storage
ms.component: data-lake-storage-gen2
ms.openlocfilehash: dd8f33cd64728c1a66e5de05734b05ac35a9836b
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2019
ms.locfileid: "54200174"
---
# <a name="upgrade-your-big-data-analytics-solutions-from-azure-data-lake-storage-gen1-to-azure-data-lake-storage-gen2-preview"></a>Aggiornare le soluzioni di analisi dei Big Data da Azure Data Lake Storage Gen1 ad Azure Data Lake Storage Gen2 (anteprima)

Se si usa Azure Data Lake Storage Gen1 nelle soluzioni di analisi dei Big Data, questa guida consente di aggiornare tali soluzioni per usare Azure Data Lake Storage Gen2 (anteprima). È possibile usare questo documento per valutare le dipendenze di questa soluzione da Data Lake Storage Gen1. Questa guida illustra anche come pianificare ed eseguire l'aggiornamento.

Verranno illustrate le attività seguenti:

:heavy_check_mark: Valutare la conformità all'aggiornamento

:heavy_check_mark: Pianificare un aggiornamento

:heavy_check_mark: Eseguire l'aggiornamento

## <a name="assess-your-upgrade-readiness"></a>Valutare la conformità all'aggiornamento

L'obiettivo è fare in modo che tutte le funzionalità presenti in Data Lake Storage Gen1 siano disponibili in Data Lake Storage Gen2. Il modo in cui tali funzionalità sono esposte, ad esempio nell'SDK, nell'interfaccia della riga di comando e così via, potrebbe essere diverso in Data Lake Storage Gen1 e in Data Lake Storage Gen2. Le applicazioni e i servizi che usano Data Lake Storage Gen1 devono poter usare in modo simile Data Lake Storage Gen2. Infine, alcune delle funzionalità non saranno disponibili fin da subito in Data Lake Storage Gen2. Non appena saranno disponibili, verrà comunicato in questo documento.

Queste sezioni successive consentiranno di decidere il modo migliore per eseguire l'aggiornamento a Data Lake Storage Gen2 e quando ha più senso farlo.

### <a name="data-lake-storage-gen1-solution-components"></a>Componenti delle soluzioni Data Lake Storage Gen1

Molto probabilmente, quando si usa Data Lake Storage Gen1 nelle soluzioni o nelle pipeline di analisi, si usano molte tecnologie aggiuntive per ottenere la totale funzionalità end-to-end. Questo [articolo](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-data-scenarios) descrive diversi componenti del flusso di dati, inclusi inserimento, elaborazione e utilizzo dei dati.

Esistono anche componenti trasversali per effettuare il provisioning, la gestione e il monitoraggio di questi componenti. Ognuno dei componenti opera con Data Lake Storage Gen1 usando l'interfaccia più adatta. Quando si pianifica l'aggiornamento della soluzione a Data Lake Storage Gen2, è necessario conoscere le interfacce usate. Sarà necessario aggiornare sia le interfacce di gestione che le interfacce di dati perché ogni interfaccia ha requisiti diversi.

![Componenti delle soluzioni Data Lake Storage](./media/data-lake-storage-upgrade/solution-components.png)

La **figura 1** sopra mostra i componenti delle funzionalità presenti nella maggior parte delle soluzioni di analisi.

La **figura 2** mostra un esempio di come tali componenti verranno implementati usando tecnologie specifiche.

La funzionalità Archiviazione nella **figura 1** viene fornita da Data Lake Storage Gen1 (**figura 2**). Si noti come i diversi componenti del flusso di dati interagiscono con Data Lake Storage Gen1 usando le API REST o Java SDK. Si noti anche come i componenti delle funzionalità trasversali interagiscono con Data Lake Storage Gen1. Il componente Provisioning usa i modelli di Azure Resource Manager, mentre il componente Monitoraggio, che usa Log Analytics, utilizza i dati operativi provenienti da Data Lake Storage Gen1.

Per aggiornare una soluzione dall'uso di Data Lake Storage Gen1 a quello di Data Lake Storage Gen2, sarà necessario copiare i dati e i metadati ed eseguire un hook dei flussi di dati. Tutti i componenti dovranno quindi poter usare Data Lake Storage Gen2.

Le sezioni seguenti forniscono informazioni per poter prendere decisioni migliori:

:heavy_check_mark: Funzionalità delle piattaforme

:heavy_check_mark: Interfacce di programmazione

:heavy_check_mark: Ecosistema di Azure

:heavy_check_mark: Ecosistema di partner

:heavy_check_mark: Informazioni operative

In ogni sezione sarà possibile determinare gli elementi indispensabili per l'aggiornamento. Dopo essersi assicurati che le funzionalità siano disponibili o che siano in atto soluzioni alternative ragionevoli, passare alla sezione [Pianificazione di un aggiornamento](#planning-for-an-upgrade) di questa guida.

### <a name="platform-capabilities"></a>Funzionalità delle piattaforme

Questa sezione descrive le funzionalità della piattaforma Data Lake Storage Gen1 attualmente disponibili in Data Lake Storage Gen2.

| | Data Lake Storage Gen1 | Data Lake Storage Gen2 - Obiettivo | Data Lake Storage Gen2 - Stato di disponibilità  |
|-----------------------|-----------------|----------------------|----------------------------------|
| Organizzazione dei dati| Supporto di dati archiviati come cartelle e file | Supporto di dati archiviati come oggetti/BLOB, oltre che come cartelle e file - [Collegamento](https://docs.microsoft.com/azure/storage/data-lake-storage/namespace) | Supporto di dati archiviati come cartelle e file: *ora disponibile*, supporto di dati archiviati come oggetti/BLOB: *non ancora disponibile* |
| Spazio dei nomi| Gerarchica | Gerarchica |  *Ora disponibile*  |
| API  | API REST su HTTPS | API REST su HTTP/HTTPS| *Ora disponibile* |
| API lato server| [API REST compatibile con WebHDFS](https://msdn.microsoft.com/library/azure/mt693424.aspx) | API REST per il servizio BLOB di Azure, [API REST per Data Lake Storage Gen2](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2) | API REST per Data Lake Storage Gen2: *ora disponibile*, API REST per il servizio BLOB: *non ancora disponibile*       |
| Client del file system Hadoop | Sì ([Azure Data Lake Storage](https://hadoop.apache.org/docs/current/hadoop-azure-datalake/index.html)) | Sì ([ABFS](https://jira.apache.org/jira/browse/HADOOP-15407))  | *Ora disponibile*  |  | [Chiave condivisa](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key), [Identità di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-authentication-scenarios), [Firme di accesso condiviso](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) | *Ora disponibile*  |
| Operazioni sui dati: autorizzazione  | Elenchi di controllo di accesso (ACL) POSIX a livello di file e cartella basati sulle identità di Azure Active Directory  | Elenchi di controllo di accesso (ACL) POSIX a livello di file e cartella basati sulle identità di Azure Active Directory, [chiave condivisa](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key) per l'autorizzazione a livello di account, [controllo degli accessi in base al ruolo](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac) per accedere ai contenitori | *Ora disponibile* |
| Operazioni sui dati: log  | Yes | Richieste occasionali di log per una durata specifica tramite ticket di supporto, Integrazione di Monitoraggio di Azure | Richieste occasionali di log per una durata specifica tramite ticket di supporto: *ora disponibile*, Integrazione di Monitoraggio di Azure: *non ancora disponibile* |
| Crittografia dei dati inattivi | Trasparente, lato server con chiavi gestite dal servizio e con chiavi gestite dal cliente in Azure KeyVault | Trasparente, lato server con chiavi gestite dal servizio e con chiavi gestite dal cliente in Azure KeyVault | Chiavi gestite dal servizio: *ora disponibile*, chiavi gestite dal cliente: *ora disponibile*  |
| Supporto della Rete virtuale di Microsoft Azure (VNet)  | [Uso di integrazione Rete virtuale di Microsoft Azure (anteprima)](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-network-security)  | [Uso di endpoint di servizio per l'Archiviazione di Microsoft Azure](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | *Ora disponibile* | Operazioni di gestione, come la creazione di account | [Controllo degli accessi in base al ruolo](https://docs.microsoft.com/azure/role-based-access-control/overview) (RBAC) fornito da Azure per la gestione degli account. | [Controllo degli accessi in base al ruolo](https://docs.microsoft.com/azure/role-based-access-control/overview) (RBAC) fornito da Azure per la gestione degli account. | *Ora disponibile*| SDK per sviluppatori | .NET, Java, Python, Node.js  | .NET, Java, Python, Node.js, C++, Ruby, PHP, Go, Android, iOS| *Non ancora disponibile* | Prestazioni ottimizzate per carichi di lavoro di analisi parallela. IOPS e velocità effettiva elevata. | Prestazioni ottimizzate per carichi di lavoro di analisi parallela. IOPS e velocità effettiva elevata. | *Ora disponibile* |
| Limiti di dimensioni | Nessun limite di dimensioni per l'account, i file o il numero di file | Nessun limite di dimensioni per l'account o il numero di file. Dimensioni del file limitate a 5 TB. | *Ora disponibile*|
| Ridondanza geografica| Archiviazione con ridondanza locale | Archiviazione con ridondanza locale, archiviazione con ridondanza della zona, archiviazione con ridondanza geografica, archiviazione con ridondanza geografica e accesso in lettura. Per altre informazioni, vedere [qui](https://docs.microsoft.com/azure/storage/common/storage-redundancy)| *Ora disponibile* |
| Disponibilità internazionale | Vedere [qui](https://azure.microsoft.com/regions/) | Tutte le [aree di Azure](https://azure.microsoft.com/global-infrastructure/regions/)                                                                                                                                                                                                                                                                                                                                       | *Ora disponibile*                                                                                                                           |
| Prezzo                                       | Vedere i [prezzi](https://azure.microsoft.com/pricing/details/data-lake-store/)                                                                            | Vedere i [prezzi](https://azure.microsoft.com/pricing/details/storage/data-lake/)                                                                                                                                                                                                                                                                                                                                         |                                                                                                                                           |
| Contratto di servizio relativo alla disponibilità                            | [Vedere il Contratto di servizio](https://azure.microsoft.com/support/legal/sla/data-lake-store/v1_0/)                                                                   | [Vedere il Contratto di servizio](https://azure.microsoft.com/support/legal/sla/storage/v1_3/)                                                                                                                                                                                                                                                                                                                                                | *Non ancora disponibile*                                                                                                                           |
| Gestione dati                             | Scadenza file                                                                                                                                        | Criteri del ciclo di vita                                                                                                                                                                                                                                                                                                                                                                                                          | *Non ancora disponibile*                                                                                                                       |

### <a name="programming-interfaces"></a>Interfacce di programmazione

Questa tabella descrive i set di API disponibili per le applicazioni personalizzate. Per maggior chiarezza, questi set di API sono stati separati in 2 tipi: API di gestione e API del file system.

Le API di gestione consentono di gestire gli account, mentre le API del file system consentono di eseguire operazioni sui file e sulle cartelle.

|  Set di API                           |  Data Lake Storage Gen1                                                                                                                                                                                                                                                                                                   | Disponibilità per Data Lake Storage Gen2 - Con autenticazione con chiave condivisa | Disponibilità per Data Lake Storage Gen2 - Con autenticazione OAuth                                                                                                  |
|----------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| .NET SDK - Gestione                  | [Collegamento](https://docs.microsoft.com/dotnet/api/overview/azure/datalakestore/management?view=azure-dotnet)                                                                                                                                                                                                                 | *Non supportato*                                                      | *Ora disponibile -* [Collegamento](https://docs.microsoft.com/rest/api/storageservices/operations-on-the-account--blob-service-)                                    |
| .NET SDK - File system                  | [Collegamento](https://docs.microsoft.com/dotnet/api/overview/azure/datalakestore/client?view=azure-dotnet)                                                                                                                                                                                                                     | *Non ancora disponibile*                                                | *Non ancora disponibile*                                                                                                                                             |
| Java SDK - Gestione                  | [Collegamento](https://docs.microsoft.com/java/api/overview/azure/datalakestore/management)                                                                                                                                                                                                                                     | *Non supportato*                                                      | *Ora disponibile -* [Collegamento](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob?view=azure-java-stable)                                     |
| Java SDK - File system                  | [Collegamento](https://docs.microsoft.com/java/api/overview/azure/datalakestore/client)                                                                                                                                                                                                                                         | *Non ancora disponibile*                                                | *Non ancora disponibile*                                                                                                                                             |
| Node.js - Gestione                   | [Collegamento](https://www.npmjs.com/package/azure-arm-datalake-store)                                                                                                                                                                                                                                                                | Non supportate                                                      | *Ora disponibile -* [Collegamento](http://azure.github.io/azure-storage-node/)                                                                                            |
| Node.js - File system                   | [Collegamento](https://www.npmjs.com/package/azure-arm-datalake-store)                                                                                                                                                                                                                                                                | *Non ancora disponibile*                                                | *Non ancora disponibile*                                                                                                                                             |
| Python - Gestione                    | [Collegamento](https://docs.microsoft.com/python/api/overview/azure/datalakestore/management?view=azure-python)                                                                                                                                                                                                                 | *Non supportato*                                                      | *Ora disponibile -* [Collegamento](https://docs.microsoft.com/python/api/overview/azure/storage/management?view=azure-python)                                       |
| Python - File system                    | [Collegamento](http://azure-datalake-store.readthedocs.io/en/latest/)                                                                                                                                                                                                                                                                 | *Non ancora disponibile*                                                | *Non ancora disponibile*                                                                                                                                             |
| API REST - gestione                  | [Collegamento](https://docs.microsoft.com/rest/api/datalakestore/accounts)                                                                                                                                                                                                                                                      | *Non supportato*                                                      | *Ora disponibile -*                                                                                                                                               |
| API REST - filesystem                  | [Collegamento](https://docs.microsoft.com/rest/api/datalakestore/webhdfs-filesystem-apis)                                                                                                                                                                                                                                       | *Ora disponibile*                                                    | *Ora disponibile -* [Collegamento](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2)                                                      |
| PowerShell - Gestione e file system | [Collegamento](https://docs.microsoft.com/powershell/module/az.datalakestore)                                                                                                                                                                                                                        | Gestione - File system non supportato - *Non ancora disponibile*        | Gestione - *Ora disponibile -* [Collegamento](https://docs.microsoft.com/powershell/module/az.storage) File system - *Non ancora disponibile* |
| Interfaccia della riga di comando - gestione                       | [Collegamento](https://docs.microsoft.com/cli/azure/dls/account?view=azure-cli-latest)                                                                                                                                                                                                                                          | *Non supportato*                                                      | *Ora disponibile -* [Collegamento](https://docs.microsoft.com/cli/azure/storage?view=azure-cli-latest)                                                              |
| Interfaccia della riga di comando - file system                       | [Collegamento](https://docs.microsoft.com/cli/azure/dls/fs?view=azure-cli-latest)                                                                                                                                                                                                                                               | *Non ancora disponibile*                                                | *Non ancora disponibile*                                                                                                                                             |
| Modelli di Azure Resource Manager - gestione             | [Modello 1](https://azure.microsoft.com/resources/templates/101-data-lake-store-no-encryption/)  [Modello 2](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-adls/)  [Modello 3](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-key-vault/)  | *Non supportato*                                                      | *Ora disponibile -* [Collegamento](https://docs.microsoft.com/azure/templates/microsoft.storage/2018-07-01/storageaccounts)                                         |

### <a name="azure-ecosystem"></a>Ecosistema di Azure

Quando si usa Data Lake Storage Gen1, è possibile usare un'ampia gamma di prodotti e servizi Microsoft nelle pipeline end-to-end. Questi servizi e prodotti usano Data Lake Storage Gen1 direttamente o indirettamente. Questa tabella mostra un elenco dei servizi modificati per usare Data Lake Storage Gen1 e illustra quali sono attualmente compatibili con Data Lake Storage Gen2.

| **Area**             | **Disponibilità per Data Lake Storage Gen1**                                                                                                                                    | **Disponibilità per Data Lake Storage Gen2 - Con autenticazione con chiave condivisa**                                                                                                           | **Disponibilità per Data Lake Storage Gen2 - Con OAuth**                                                                                        |
|----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|
| Framework di analisi  | [Apache Hadoop](https://hadoop.apache.org/docs/current/hadoop-azure-datalake/index.html)                                                                                       | *Ora disponibile*                                                                                                                                                              | *Ora disponibile*                                                                                                                                 |
|                      | [HDInsight ](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-hdinsight-hadoop-use-portal)                                                               | [HDInsight](https://docs.microsoft.com/azure/storage/data-lake-storage/quickstart-create-connect-hdi-cluster) 3.6 - *Ora disponibile ora* HDInsight 4.0 - *Non ancora disponibile*      | HDInsight 3.6 ESP - *Non ancora disponibile* HDInsight 4.0 ESP - *Non ancora disponibile*                                                                 |
|                      | Databricks Runtime 3.1 e versioni successive                                                                                                                                               | [Databricks Runtime 5.1 e versioni successive](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html#azure-data-lake-storage-gen2): *ora disponibile* | [Databricks Runtime 5.1 e versioni successive](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html#azure-data-lake-storage-gen2): *ora disponibile*                                                                                              |
|                      | [SQL Data Warehouse ](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store)                                            | *Non supportato*                                                                                                                                                              | *Ora disponibile* [Collegamento](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql?view=sql-server-2017) |
| Integrazione dei dati     | [Data Factory ](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-store)                                                                                | [Versione 2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage): *ora disponibile* Versione 1: *Non supportata*                               | [Versione 2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage): *ora disponibile* Versione 1: *Non supportata*  |
|                      | [AdlCopy](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)                                                                 | *Non supportato*                                                                                                                                                              | *Non supportato*                                                                                                                                 |
|                      | [SQL Server Integration Services ](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager?view=sql-server-2017) | *Non ancora disponibile*                                                                                                                                                          | *Non ancora disponibile*                                                                                                                             |
|                      | [Data Catalog](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-with-data-catalog)                                                                       | *Non ancora disponibile*                                                                                                                                                          | *Non ancora disponibile*                                                                                                                             |
|                      | [App per la logica ](https://docs.microsoft.com/connectors/azuredatalake/)                                                                                                      | *Non ancora disponibile*                                                                                                                                                          | *Non ancora disponibile*                                                                                                                             |
| IoT                  | [Hub eventi - Acquisizione ](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-archive-eventhub-capture)                                                       | *Non ancora disponibile*                                                                                                                                                          | *Non ancora disponibile*                                                                                                                             |
|                      | [Analisi dei flussi ](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-stream-analytics)                                                                   | *Non ancora disponibile*                                                                                                                                                          | *Non ancora disponibile*                                                                                                                             |
| Consumo          | [PowerBI Desktop  ](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-power-bi)                                                                           | *Non ancora disponibile*                                                                                                                                                          | *Non ancora disponibile*                                                                                                                             |
|                      | [Excel ](https://techcommunity.microsoft.com/t5/Excel-Blog/Announcing-the-Azure-Data-Lake-Store-Connector-in-Excel/ba-p/91677)                                                 | *Non ancora disponibile*                                                                                                                                                          | *Non ancora disponibile*                                                                                                                             |
|                      | [Analysis Services ](https://blogs.msdn.microsoft.com/analysisservices/2017/09/05/using-azure-analysis-services-on-top-of-azure-data-lake-storage/)                            | *Non ancora disponibile*                                                                                                                                                          | *Non ancora disponibile*                                                                                                                             |
| Produttività         | [Portale di Azure](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal)                                                                      | *Non supportato*                                                                                                                                                              | Gestione account *- Ora disponibile* Operazioni dati *-*  *Non ancora disponibili*                                                                   |
|                      | [Strumenti di Data Lake per Visual Studio ](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-data-lake-tools-install)                                   | *Non ancora disponibile*                                                                                                                                                          | *Non ancora disponibile*                                                                                                                             |
|                      | [Azure Storage Explorer ](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-in-storage-explorer)                                                          | *Ora disponibile*                                                                                                                                                              | *Ora disponibile*                                                                                                                                 |
|                      | [Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=usqlextpublisher.usql-vscode-ext)                                                                     | *Non ancora disponibile*                                                                                                                                                          | *Non ancora disponibile*                                                                                                                             |

### <a name="partner-ecosystem"></a>Ecosistema di partner

Questa tabella mostra un elenco dei servizi e prodotti di terze parti che sono stati modificati per usare Data Lake Storage Gen1. Mostra anche quelli attualmente compatibili con Data Lake Storage Gen2.

| Area            | Partner  | Prodotto o servizio  | Disponibilità per Data Lake Storage Gen1                                                                                                                                               | Disponibilità per Data Lake Storage Gen2 - Con autenticazione con chiave condivisa                                                   | Disponibilità per Data Lake Storage Gen2 - Con OAuth                                                             |
|---------------------|--------------|----------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------|
| Framework di analisi | Cloudera     | CDH                  | [Collegamento](https://www.cloudera.com/documentation/enterprise/5-11-x/topics/admin_adls_config.html)                                                                                            | *Non ancora disponibile*                                                                                                  | *Non ancora disponibile*                                                                                                  |
|                     | Cloudera     | Altus                | [Collegamento](https://www.cloudera.com/more/news-and-blogs/press-releases/2017-09-27-cloudera-introduces-altus-data-engineering-microsoft-azure-hybrid-multi-cloud-data-analytic-workflows.html) | *Non supportato*                                                                                                                  | *Non ancora disponibile*                                                                                                  |
|                     | HortonWorks  | HDP 3.0              | [Collegamento](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.3/bk_cloud-data-access/content/adls-get-started.html)                                                                       | *Non ancora disponibile*                                                                                                  | *Non ancora disponibile*                                                                                                  |
|                     | Qubole       |                      | [Collegamento](https://www.qubole.com/blog/big-data-analytics-microsoft-azure-data-lake-store-qubole/)                                                                                            | *Non ancora disponibile*                                                                                                  | *Non ancora disponibile*                                                                                                  |
| ETL                 | StreamSets   |                      | [Collegamento](https://streamsets.com/blog/ingest-data-azure)                                                                                                                                     | *Non ancora disponibile*                                                                                                  | *Non ancora disponibile*                                                                                                  |
|                     | Informatica  |                      | [Collegamento](https://kb.informatica.com/proddocs/Product%20Documentation/6/IC_Spring2017_MicrosoftAzureDataLakeStoreV2ConnectorGuide_en.pdf)                                                    | *Non ancora disponibile*                                                                                                  | *Non ancora disponibile*                                                                                                  |
|                     | Attunity     |                      | [Collegamento](https://www.attunity.com/company/press-releases/microsoft-and-attunity-announce-strategic-partnership-for-data-migration/)                                                         | *Non ancora disponibile*                                                                                                  | *Non ancora disponibile*                                                                                                  |
|                     | Alteryx      |                      | [Collegamento](https://help.alteryx.com/2018.2/DataSources/AzureDataLake.htm)                                                                                                                     | *Non ancora disponibile*                                                                                                  | *Non ancora disponibile*                                                                                                  |
|                     | ImanisData   |                      | [Collegamento](https://www.imanisdata.com/expansion-azure-support-azure-data-lake-store-integration/)                                                                                             | *Non ancora disponibile*                                                                                                  | *Non ancora disponibile*                                                                                                  |
|                     | WANdisco     |                      | [Collegamento](https://azure.microsoft.com/blog/globally-replicated-data-lakes-with-livedata-using-wandisco-on-azure/)                                                                      | [Collegamento](https://azure.microsoft.com/blog/globally-replicated-data-lakes-with-livedata-using-wandisco-on-azure/) | [Collegamento](https://azure.microsoft.com/blog/globally-replicated-data-lakes-with-livedata-using-wandisco-on-azure/) |

### <a name="operational-information"></a>Informazioni operative

Data Lake Storage Gen1 esegue il push di informazioni e dati specifici ad altri servizi e ciò permette di rendere operative le pipeline. Questa tabella mostra la disponibilità del supporto corrispondente in Data Lake Storage Gen2.

| Tipo di dati                                                                                       | Disponibilità per Data Lake Storage Gen1                                                                 | Disponibilità per Data Lake Storage Gen2                                                                                               |
|--------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------|
| Dati di fatturazione: contatori inviati al team commerciale per la fatturazione e quindi resi disponibili ai clienti  | *Ora disponibile*                                                                                             | *Ora disponibile*                                                                                                                           |
| Log attività                                                                                          | [Collegamento](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs#audit-logs)   | Richieste occasionali di log per una durata specifica tramite ticket di supporto: *ora disponibile*, Integrazione di Monitoraggio di Azure: *non ancora disponibile* |
| Log di diagnostica                                                                                        | [Collegamento](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs#request-logs) | Richieste occasionali di log per una durata specifica tramite ticket di supporto: *ora disponibile*, Integrazione di Monitoraggio di Azure: *non ancora disponibile* |
| Metriche                                                                                                | *Non supportato*                                                                                               | *Ora disponibile -* [Collegamento](https://docs.microsoft.com/azure/storage/common/storage-metrics-in-azure-monitor)                          |

## <a name="planning-for-an-upgrade"></a>Pianificazione di un aggiornamento

Questa sezione presuppone che sia già stata letta la sezione [Valutare la conformità all'aggiornamento](#assess-your-upgrade-readiness) di questa guida e che tutte le dipendenze siano soddisfatte. Se sono presenti funzionalità che non sono ancora disponibili in Data Lake Storage Gen2, procedere solo se si conoscono le corrispondenti soluzioni alternative. Le sezioni seguenti forniscono indicazioni sul modo in cui è possibile pianificare l'aggiornamento delle pipeline. L'esecuzione dell'aggiornamento vero e proprio verrà descritta nella sezione [Esecuzione dell'aggiornamento](#performing-the-upgrade) di questa guida.

### <a name="upgrade-strategy"></a>Strategia di aggiornamento

La parte più importante dell'aggiornamento consiste nel decidere la strategia. Questa decisione determinerà le scelte disponibili.

Questa tabella elenca alcune strategie ben note che sono state usate per eseguire la migrazione di database, cluster Hadoop e così via. In queste linee guida verranno adottate strategie simili che saranno adattate al contesto.

| Strategia                   | Vantaggi                                                                                  | Svantaggi                                                           | Quando usare la strategia                                                                                                                                                                                             |
|--------------------------------|-------------------------------------------------------------------------------------------|--------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Trasferimento in modalità lift-and-shift                 | Molto semplice.                                                                                 | Richiede un tempo di inattività per la copia dei dati, lo spostamento dei processi e lo spostamento in ingresso e in uscita                                             | Per le soluzioni più semplici, in cui non sono presenti più soluzioni che accedono allo stesso account Gen1 e di conseguenza possono essere spostate insieme in modo controllato e rapido.                                                  |
| Copia singola e copia incrementale | Riduce il tempo di inattività eseguendo la copia in background mentre il sistema di origine è ancora attivo. | Richiede un tempo di inattività per lo spostamento in ingresso e in uscita.                   | La quantità di dati da copiare è considerevole e il tempo di inattività associato al trasferimento in modalità lift-and-shift non è accettabile. Prima della transizione potrebbe essere necessario eseguire test con dati di produzione significativi sul sistema di destinazione. |
| Adozione parallela              | Tempo di inattività minimo. Fornisce alle applicazioni il tempo di eseguire la migrazione a propria discrezione.           | Molto complessa perché è necessaria la sincronizzazione bidirezionale tra i due sistemi. | Per scenari complessi, in cui non è possibile eseguire il cutover di tutte le applicazioni basate su Data Lake Storage Gen1 contemporaneamente ed è necessario spostarle in modo incrementale.                                                      |

Di seguito sono fornite altre informazioni dettagliate sui passaggi necessari per ognuna delle strategie. I passaggi elencano le operazioni da eseguire con i componenti coinvolti nell'aggiornamento, inclusi l'intero sistema di origine, l'intero sistema di destinazione, le origini in ingresso per il sistema di origine, le destinazioni in uscita per il sistema di origine e i processi in esecuzione nel sistema di origine.

Non è obbligatorio eseguire questi passaggi, che servono solo a creare la struttura per mettere in atto ogni strategia. Verranno forniti case study per ogni strategia non appena saranno implementate.

#### <a name="lift-and-shift"></a>Trasferimento in modalità lift-and-shift

1. Sospendere il sistema di origine: origini in ingresso, processi, destinazioni in uscita.

2. Copiare tutti i dati dal sistema di origine al sistema di destinazione.

3. Riferire tutte le origini in ingresso al sistema di destinazione. Puntare alla destinazione in uscita dal sistema di destinazione.

4. Spostare, modificare, eseguire tutti i processi nel sistema di destinazione.

5. Disattivare il sistema di origine.

#### <a name="copy-once-and-copy-incremental"></a>Copia singole e copia incrementale

1. Copiare i dati dal sistema di origine al sistema di destinazione.

2. Copiare i dati incrementali dal sistema di origine al sistema di destinazione a intervalli regolari.

3. Puntare alla destinazione in uscita dal sistema di destinazione.

4. Spostare, modificare, eseguire tutti i processi nel sistema di destinazione.

5. Riferire le origini in ingresso in modo incrementale al sistema di destinazione in base alle proprie esigenze.

6. Dopo che tutte le origini in ingresso puntano al sistema di destinazione:

    1. Disattivare la copia incrementale.

    2. Disattivare il sistema di origine.

#### <a name="parallel-adoption"></a>Adozione parallela

1. Configurare il sistema di destinazione.

2. Configurare una replica bidirezionale tra il sistema di origine e quello di destinazione.

3. Riferire le origini in ingresso in modo incrementale al sistema di destinazione.

4. Spostare, modificare, eseguire i processi in modo incrementale nel sistema di destinazione.

5. Riferire le destinazioni in uscita in modo incrementale dal sistema di destinazione.

6. Dopo che tutte le origini in ingresso, i processi e la destinazione in uscita originali usano il sistema di destinazione, disattivare il sistema di origine.

### <a name="data-upgrade"></a>Aggiornamento dei dati

La strategia generale usata per eseguire l'aggiornamento (descritta nella sezione [Strategia di aggiornamento](#upgrade-strategy) di questa guida) determinerà gli strumenti che è possibile usare per l'aggiornamento dei dati. Gli strumenti elencati di seguito si basano sulle informazioni correnti e sono solo suggerimenti. 

#### <a name="tools-guidance"></a>Linee guida agli strumenti

| Strategia                       | Strumenti                                                                                                             | Vantaggi                                                                                                                             | Considerazioni                                                                                                                                                                                                                                                                                                                |
|------------------------------------|-----------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Trasferimento in modalità lift-and-shift**                 | [Data factory di Azure](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2-from-gen1) | Servizio cloud gestito                                                                                                                | Copia solo i dati. Gli elenchi di controllo di accesso attualmente non possono essere copiati.                                                                                                                                                                                                                                                                      |
|                                    | [Distcp](https://hadoop.apache.org/docs/r1.2.1/distcp.html)                                                           | Strumento noto fornito da Hadoop. Le autorizzazioni, ovvero gli elenchi di controllo di accesso, possono essere copiate con questo strumento                                                   | Richiede un cluster che può connettersi a Data Lake Storage Gen1 e Gen2 nello stesso momento.                                                                                                                                                                                   |
| **Copia singola e copia incrementale** | Data factory di Azure                                                                                                    | Servizio cloud gestito                                                                                                                | Per supportare la copia incrementale in Azure Data Factory, i dati devono essere organizzati in serie temporali. L'intervallo più breve tra copie incrementali è di [15 minuti](https://docs.microsoft.com/azure/data-factory/how-to-create-tumbling-window-trigger). Per intervalli più brevi, Azure Data Factory non funzionerà. Gli elenchi di controllo di accesso attualmente non possono essere copiati. |
| **Adozione parallela**              | [WANdisco](http://docs.wandisco.com/bigdata/wdfusion/adls/)                                                           | Supporta la replica coerente. Se si usa un ambiente Hadoop puro connesso ad Azure Data Lake Storage, supporta la replica bidirezionale | Se non si usa un ambiente Hadoop puro, potrebbe verificarsi un ritardo nella replica.                                                                                                                                                                                                                                                  |

Tenere presente che alcune terze parti possono gestire l'aggiornamento da Data Lake Storage Gen1 a Data Lake Storage Gen2 senza usare gli strumenti di copia di dati/metadati indicati sopra (ad esempio: [Cloudera](http://blog.cloudera.com/blog/2017/08/use-amazon-s3-with-cloudera-bdr/)). Forniscono un'esperienza "centralizzata" che esegue la migrazione dei dati, nonché la migrazione del carico di lavoro. Potrebbe essere necessario eseguire un aggiornamento fuori programma per eventuali strumenti non compresi nell'ecosistema.

#### <a name="considerations"></a>Considerazioni

* Sarà necessario creare manualmente l'account Data Lake Storage Gen2 prima di iniziare qualsiasi parte dell'aggiornamento, perché le linee guida correnti non includono processi dell'account Gen2 automatici basati sulle informazioni dell'account Gen1. Assicurarsi di confrontare i processi di creazione degli account per [Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal) e [Gen2](https://docs.microsoft.com/azure/storage/data-lake-storage/quickstart-create-account).

* Data Lake Storage Gen2 supporta solo file con dimensioni massime pari a 5 TB. Per eseguire l'aggiornamento a Data Lake Storage Gen2, potrebbe essere necessario ridimensionare i file in Data Lake Storage Gen1 in modo che le dimensioni siano inferiori a 5 TB.

* Se si usa uno strumento che non copia gli elenchi di controllo di accesso o non si vuole copiare gli elenchi di controllo di accesso, sarà necessario impostare manualmente gli elenchi di controllo di accesso sulla destinazione al livello superiore appropriato. È possibile farlo usando Storage Explorer. Assicurarsi che tali elenchi di controllo di accesso siano quelli predefiniti in modo che i file e le cartelle copiati li ereditino.

* In Data Lake Storage Gen1 il livello più elevato su cui è possibile impostare gli elenchi di controllo di accesso è la radice dell'account. In Data Lake Storage Gen1, tuttavia, il livello più elevato su cui è possibile impostare gli elenchi di controllo di accesso è la cartella radice in un file system, non nell'intero account. Se quindi si vuole impostare gli elenchi di controllo di accesso predefiniti a livello di account, sarà necessario duplicarli in tutti i file system dell'account Data Lake Storage Gen2.

* Le restrizioni di denominazione dei file sono diverse per i due sistemi di archiviazione. Queste differenze riguardano soprattutto la copia da Data Lake Storage Gen2 a Data Lake Storage Gen1, perché il secondo presenta restrizioni più vincolanti.

### <a name="application-upgrade"></a>Aggiornamento dell'applicazione

Quando è necessario compilare applicazioni in Data Lake Storage Gen1 o Data Lake Storage Gen2, è necessario in primo luogo scegliere un'interfaccia di programmazione appropriata. Quando si chiama un'API su tale interfaccia, è necessario specificare l'URI appropriato e le credenziali appropriate. La rappresentazione di questi tre elementi, l'API, l'URI e il modo in cui vengono fornite le credenziali, varia da Data Lake Storage Gen1 a Data Lake Storage Gen2. Durante l'aggiornamento dell'applicazione, sarà quindi necessario eseguire il mapping di questi tre costrutti in modo appropriato.

#### <a name="uri-changes"></a>Modifiche all'URI

L'attività principale in questo caso consiste nel convertire l'URI adl:// usato nei carichi di lavoro esistenti in un URI abfss://.

Lo schema URI dettagliato per Data Lake Storage Gen1 è riportato [qui](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-store), ma quello generale è *adl://mydatalakestore.azuredatalakestore.net/\<percorso_file\>.*

Lo schema URI dettagliato per accedere ai file di Data Lake Storage Gen2 è riportato [qui](https://docs.microsoft.com/azure/storage/data-lake-storage/use-hdi-cluster?branch=release-preview-abfs), ma quello generale è *abfss://\<NOME_FILE_SYSTEM\>\@\<NOME_ACCOUNT\>.dfs.core.widows.net/\<PERCORSO\>.*

Sarà necessario esaminare le applicazioni esistenti e verificare di aver modificato correttamente gli URI in modo che puntino a quelli di Data Lake Storage Gen2. Sarà anche necessario aggiungere le credenziali appropriate. Infine, il modo in cui si ritirano le applicazioni originali e le si sostituiscono con la nuova applicazione dovrà essere strettamente allineato alla strategia di aggiornamento generale.

#### <a name="custom-applications"></a>Applicazioni personalizzate

A seconda dell'interfaccia usata dall'applicazione con Data Lake Storage Gen1, sarà necessario modificarla per adattarla a Data Lake Storage Gen2.

##### <a name="rest-apis"></a>API REST

Se l'applicazione usa le API REST di Data Lake Storage, sarà necessario modificare l'applicazione per usare le API REST di Data Lake Storage Gen2. I collegamenti sono disponibili nella sezione *Interfacce di programmazione*.

##### <a name="sdks"></a>SDK

Come illustrato nella sezione *Valutare la conformità all'aggiornamento*, gli SDK non sono attualmente disponibili. Per convertire le applicazioni a Data Lake Storage Gen2, è consigliabile attendere che siano disponibili gli SDK supportati.

##### <a name="powershell"></a>PowerShell

Come illustrato nella sezione Valutare la conformità all'aggiornamento, il supporto di PowerShell non è attualmente disponibile per il piano dati.

È possibile sostituire i commandlet del piano di gestione con quelli appropriati in Data Lake Storage Gen2. I collegamenti sono disponibili nella sezione *Interfacce di programmazione*.

##### <a name="cli"></a>CLI

Come illustrato nella sezione *Valutare la conformità all'aggiornamento*, il supporto dell'interfaccia della riga di comando non è attualmente disponibile per il piano dati.

È possibile sostituire i comandi del piano di gestione con quelli appropriati in Data Lake Storage Gen2. I collegamenti sono disponibili nella sezione *Interfacce di programmazione*.

### <a name="analytics-frameworks-upgrade"></a>Aggiornamento dei framework di analisi

Se l'applicazione crea metadati sulle informazioni nell'archivio, ad esempio percorsi di file e cartelle espliciti, sarà necessario eseguire altre azioni dopo l'aggiornamento dei dati/metadati dell'archivio, in particolare per i framework di analisi, ad esempio Azure HDInsight, Databricks e così via, che in genere creano dati di catalogo nei dati dell'archivio.

I framework di analisi usano i dati e i metadati memorizzati negli archivi remoti, ad esempio Data Lake Storage Gen1 e Gen2. In teoria, i motori possono quindi essere temporanei ed essere attivati solo quando è necessario eseguire processi sui dati archiviati.

Per ottimizzare le prestazioni, tuttavia, i framework di analisi potrebbero creare riferimenti espliciti ai file e alle cartelle memorizzati nell'archivio remoto e quindi creare una cache per conservarli. Se l'URI dei dati remoti viene modificato, ad esempio nel caso di un cluster che prima archiviava i dati in Data Lake Storage Gen1, ma che ora deve archiviarli in Data Lake Storage Gen2, l'URI per lo stesso contenuto copiato sarà diverso. Quindi dopo l'aggiornamento dei dati e dei metadati, devono essere aggiornate o reinizializzate anche le cache di questi motori

Come parte del processo di pianificazione, sarà necessario identificare l'applicazione e determinare come reinizializzare le informazioni dei metadati in modo che puntino ai dati che sono ora archiviati in Data Lake Storage Gen2. Di seguito sono riportate le linee guida per aggiornare i framework di analisi comunemente adottati.

#### <a name="azure-databricks"></a>Azure Databricks

A seconda della strategia di aggiornamento scelta, la procedura sarà diversa. La sezione corrente presuppone che sia stata scelta la strategia di "trasferimento in modalità lift-and-shift". Si presume anche che l'area di lavoro di Databricks esistente, che accedeva ai dati in un account Data Lake Storage Gen1, usi i dati copiati nell'account Data Lake Storage Gen2.

In primo luogo, verificare di aver creato l'account Gen2 e di aver quindi copiato i dati e i metadati da Gen1 a Gen2 usando uno strumento appropriato. Tali strumenti sono indicati nella sezione [Aggiornamento dei dati](#data-upgrade) di questa guida.

[Aggiornare](https://docs.azuredatabricks.net/user-guide/clusters/index.html) quindi il cluster Databricks esistente per iniziare a usare Databricks Runtime 5.1 o versione successiva, che dovrebbe supportare Data Lake Storage Gen2.

I passaggi successivi si basano sul modo in cui l'area di lavoro di Databricks esistente accede ai dati nell'account Data Lake Storage Gen1, ovvero chiamando gli URI adl:// [direttamente](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html#access-azure-data-lake-store-directly) dai notebook o tramite [punti di montaggio](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html#mount-azure-data-lake-store-with-dbfs).

Se si accede direttamente dai notebook fornendo gli URI adl:// completi, sarà necessario scorrere ogni notebook e modificare la configurazione per accedere all'URI Data Lake Storage Gen2 corrispondente.

In futuro, sarà necessario riconfigurarlo in modo che punti all'account Data Lake Storage Gen2. Non sono necessarie altre modifiche e i notebook dovrebbero poter funzionare come prima.

Se si usa una delle altre strategie di aggiornamento, è possibile modificare i passaggi precedenti in base ai requisiti.

### <a name="azure-ecosystem-upgrade"></a>Aggiornamento dell'ecosistema di Azure

Ognuno degli strumenti e dei servizi indicati nella sezione [Ecosistema di Azure](#azure-ecosystem) di questa guida dovrà essere configurato per usare Data Lake Storage Gen2.

In primo luogo, assicurarsi che sia disponibile l'integrazione con Data Lake Storage Gen2.

Quindi, gli elementi indicati sopra (ad esempio: l'URI e le credenziali) dovranno essere modificati. È possibile modificare l'istanza esistente che funziona con Data Lake Storage Gen1 oppure è possibile creare una nuova istanza che funzioni con Data Lake Storage Gen2.

### <a name="partner-ecosystem-upgrade"></a>Aggiornamento dell'ecosistema del partner

Collaborare con il partner che fornisce il componente e gli strumenti per assicurarsi che possano usare Data Lake Storage Gen2. 

## <a name="performing-the-upgrade"></a>Esecuzione dell'aggiornamento

### <a name="pre-upgrade"></a>Prima dell'aggiornamento

Nell'ambito di questo processo sono state lette le sezioni *Valutare la conformità all'aggiornamento* e [Pianificazione di un aggiornamento](#planning-for-an-upgrade) di questa guida, sono state ottenute tutte le informazioni necessarie ed è stato creato un piano rispondente alle proprie esigenze. Durante questa fase si eseguirà probabilmente un'attività di test.

### <a name="in-upgrade"></a>Durante l'aggiornamento

A seconda della strategia scelta e della complessità della soluzione, questa fase potrebbe durare poco o molto, ad esempio se sono presenti più carichi di lavoro in attesa di essere spostati in modo incrementale a Data Lake Storage Gen2. Questa sarà la parte più critica dell'aggiornamento.

### <a name="post-upgrade"></a>Dopo l'aggiornamento

Dopo aver completato l'operazione di transizione, i passaggi finali comporteranno una verifica completa, per controllare, ad esempio, che i dati siano stati copiati in modo affidabile, che gli elenchi di controllo di accesso siano stati impostati correttamente, che le pipeline end-to-end funzionino correttamente e così via. Dopo aver completato le verifiche, è possibile disattivare le pipeline precedenti, eliminare gli account Data Lake Storage Gen1 di origine e passare velocemente alle soluzioni basate su Data Lake Storage Gen2.

## <a name="conclusion"></a>Conclusioni

Le indicazioni fornite in questo documento hanno illustrato come aggiornare la soluzione per usare Data Lake Storage Gen2. 

Per altre domande o per inviare suggerimenti, aggiungere sotto i commenti o inviare commenti e suggerimenti nel [forum di commenti e suggerimenti su Azure](https://feedback.azure.com/forums/327234-data-lake).
