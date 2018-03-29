---
title: Strumento di valutazione delle soluzioni Cortana Intelligence | Microsoft Docs
description: Di seguito è illustrata la procedura che i partner Microsoft devono seguire per pubblicare la propria soluzione Cortana Intelligence in AppSource.
services: machine-learning
documentationcenter: ''
author: AnupamMicrosoft
manager: jhubbard
editor: cgronlun
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/07/2017
ms.author: anupams
ms.openlocfilehash: f678cb9819a2c03f97bcca5a2092160212779328
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2018
---
# <a name="cortana-intelligence-solution-evaluation-tool"></a>Strumento di valutazione delle soluzioni Cortana Intelligence
## <a name="overview"></a>Panoramica
È possibile usare lo strumento di valutazione delle soluzioni Cortana Intelligence per valutare la conformità delle soluzioni di analisi avanzata alle procedure consigliate di Microsoft. Microsoft interagisce costantemente con i partner (ISV o SI) per offrire soluzioni di alta qualità per i clienti, i rivenditori e l'implementazione. Questa guida descrive nel dettaglio l'utilizzo dello strumento di valutazione delle soluzioni con la soluzione corrente e le procedure consigliate specifiche per i controlli.

## <a name="getting-started"></a>Introduzione
[Scaricare](https://aka.ms/aa-evaluation-tool-download) e installare lo strumento di valutazione delle soluzioni Cortana Intelligence.

Prerequisiti:
- Windows 10: [Sito ufficiale di Windows 10](https://www.microsoft.com/en-us/windows)
- Azure Powershell: [Installare e configurare Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.0.0).

## <a name="identifying-your-app"></a>Identificazione dell'app
Al termine dell'installazione aprire lo strumento e iniziare la prima valutazione.

![Aprire lo strumento di valutazione](./media/cortana-intelligence-appsource-evaluation-tool/1-open-evaluation-tool.png)

Specificare le informazioni di identificazione della soluzione.

![Connettersi alla sottoscrizione di Azure](./media/cortana-intelligence-appsource-evaluation-tool/2-connect-azure-subscription.png)

Connettersi alla sottoscrizione di Azure e specificare il gruppo di risorse contenente l'app.

![Selezionare le risorse](./media/cortana-intelligence-appsource-evaluation-tool/3-select-resources.png)

Dopo aver caricato il gruppo di risorse, selezionare le risorse incluse nella soluzione e identificare l'accessibilità delle eventuali risorse dati come segue:
- Ingestion
- Consumo
- Interno

Queste informazioni sono utili per capire meglio l'utilizzo dei diversi componenti della soluzione e per garantire che i componenti esposti all'utente siano coerenti con le procedure consigliate.

### <a name="ingestion"></a>Ingestion
Ingestion (Inserimento) in questo caso indica tutte le origini dati usate per inserire dati dall'esterno della soluzione o i servizi esterni alla soluzione usati per inserire dati nella soluzione tramite push.

### <a name="consumption"></a>Consumo
Con Consumo vengono indicati in questo caso i set di dati usati per il push diretto o indiretto dei dati agli utenti finali. Ad esempio: 
- Set di dati usati in query dirette da Power BI.
- Set di dati sottoposti a query in un'app Web.

>[!NOTE]
Se una risorsa specifica viene usata sia per l'inserimento che per il consumo, scegliere **Consumo**.

### <a name="internal"></a>Interno
Usare Interno per le risorse di dati usate solo nell'elaborazione interna delle applicazioni.

Viene quindi richiesta l'immissione di credenziali valide per i database specificati nel passaggio precedente:

![Prerequisiti set di test](./media/cortana-intelligence-appsource-evaluation-tool/4-set-test-prerequisites.png)

## <a name="solution-test-cases"></a>Test case per la soluzione
Lo strumento per la soluzione esegue una serie di test automatizzati sulla soluzione.

![Esecuzione set di test](./media/cortana-intelligence-appsource-evaluation-tool/5-set-test-execution.png)

Dopo il completamento dei test, se necessario viene richiesta una spiegazione o una giustificazione per la mancata conformità della soluzione a uno o più requisiti.

![Offrire la giustificazione operativa](./media/cortana-intelligence-appsource-evaluation-tool/6-provide-business-justification.png)

Se, ad esempio, la soluzione pubblica in Azure SQL DW, per i test di valutazione è necessaria anche la pubblicazione in Azure Analysis Services. 

È possibile che la soluzione usi macchine virtuali IaaS che eseguono SQL Server Analysis Services anziché Azure Analysis Services. Questa giustificazione è accettabile per il risultato del test.
## <a name="packaging-your-evaluation-results"></a>Gestione dei risultati della valutazione
Dopo il completamento dei test case il pacchetto di valutazione viene esportato in un file con estensione zip. Viene anche richiesto di aggiungere commenti e suggerimenti sullo strumento di valutazione. 

È necessario condividere il file ZIP dei risultati del test con Microsoft affinché possa valutare la soluzione prima di concedere l'approvazione per la pubblicazione in AppSource

![Commenti sullo strumento di valutazione](./media/cortana-intelligence-appsource-evaluation-tool/7-grade-evaluation-tool.png)

Nella sezione precedente di questo articolo sono state illustrate varie funzionalità dello strumento. Verranno ora esaminati i tipi di procedure consigliate che possono essere valutate da questo strumento.

## <a name="security-evaluation-considerations"></a>Considerazioni sulla valutazione della protezione
### <a name="databases-should-use-azure-active-directory-authentication"></a>I database devono usare l'autenticazione di Azure Active Directory
L'autenticazione di Azure Active Directory (AAD) deve essere abilitata per tutte le risorse Azure SQL o Azure SQL DW della soluzione. AAD offre un'unica posizione per la gestione di tutti i ruoli e le identità.

| Per altre informazioni su | Vedere questo articolo |
| --- | --- |
| AAD con SQL Database e SQL Data Warehouse | [Usare l'autenticazione di Azure Active Directory per l'autenticazione di un database SQL o di SQL Data Warehouse](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) |
| Configurare e gestire AAD | [Configurare e gestire l'autenticazione di Azure Active Directory con il database SQL oppure con SQL Data Warehouse](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure) |
| Autenticazione delle app Web di Azure | [Autenticazione e autorizzazione nel servizio app di Azure](https://docs.microsoft.com/azure/app-service/app-service-authentication-overview) |
| Configurare le app Web con AAD | [Come configurare un'applicazione del servizio app per l'uso dell'account di accesso di Azure Active Directory](https://docs.microsoft.com/azure/app-service/app-service-mobile-how-to-configure-active-directory-authentication)|

### <a name="datasets-accessible-to-end-users-should-support-role-based-access-control"></a>I set di dati accessibili agli utenti finali devono supportare il controllo degli accessi in base al ruolo
Durante l'esecuzione dello strumento di valutazione, verrà richiesto di specificare le risorse di pubblicazione o creazione di report. Si presuppone che queste risorse siano destinate all'accesso degli utenti finali e non degli sviluppatori. Tali risorse dovranno essere configurate con il controllo degli accessi in base al ruolo (RBAC) per garantire che gli utenti finali possano accedere solo ai dati per i quali dispongono di autorizzazioni.

In particolare, tutte le seguenti risorse di Azure sono configurabili con il controllo degli accessi in base al ruolo e sono considerate accettabili:
- Secure HDInsight, vedere [Introduzione alla sicurezza Hadoop con i cluster HDInsight aggiunti al dominio (anteprima)](https://docs.microsoft.com/azure/hdinsight/hdinsight-domain-joined-introduction)
- SQL Azure, vedere [AAD authentication with Azure SQL]( https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) (Autenticazione AAD con SQL Azure)
- Azure Analysis Services, vedere la sezione corrispondente in [Manage database roles and users](https://docs.microsoft.com/azure/analysis-services/analysis-services-database-users) (Gestire ruoli e utenti di database)
- Azure SQL Data Warehouse (ricordare che SQL DW non è consigliabile per l'accesso diretto degli utenti finali perché supporta RBAC).

Se si usa un altro tipo di risorsa che supporta RBAC, specificarlo nella giustificazione del test case.

### <a name="azure-data-lake-store-should-use-at-rest-encryption"></a>Azure Data Lake Store deve usare la crittografia dei dati inattivi
Azure Data Lake Store (ADLS) supporta la crittografia dei dati inattivi per impostazione predefinita mediante chiavi di crittografia gestite internamente. È anche possibile configurare la crittografia con Azure Key Vault.

Per informazioni su come specificare le impostazioni di crittografia di ADLS, vedere [Creare un account di Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal#create-an-azure-data-lake-store-account).

### <a name="azure-sql-and-azure-sql-data-warehouse-should-use-encryption"></a>Azure SQL e Azure SQL Data Warehouse devono usare la crittografia
Sia Azure SQL che Azure SQL DW supportano Transparent Data Encryption (TDE), che offre la crittografia e la decrittografia in tempo reale di file di dati e file di log.

| Per altre informazioni su | Vedere questo articolo |
| --- | --- |
| Transparent data encryption (TDE) | [Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) |
| Azure SQL Data Warehouse con TDE | [Introduzione a Transparent Data Encryption (TDE)](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-encryption-tde-tsql) |
| Configurare Azure SQL con TDE | [Transparent Data Encryption con il database SQL di Azure](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-with-azure-sql-database) |
| Configurare Azure SQL con Always Encrypted | [Always Encrypted: proteggere i dati sensibili nel database SQL e archiviare le chiavi di crittografia in Azure Key Vault](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)|

Oltre a TDE, Azure SQL supporta Always Encrypted, una nuova tecnologia di crittografia che garantisce la protezione dei dati non solo quando sono inattivi o quando vengono trasferiti tra client e server, ma anche quando sono in uso per l'esecuzione di comandi nel server.

### <a name="any-virtual-machines-must-be-deployed-from-the-azure-marketplace"></a>Tutte le macchine virtuali devono essere distribuite da Azure Marketplace
Per garantire un livello di protezione uniforme in AppSource, tutte le macchine virtuali distribuite come parte di una soluzione Cortana Intelligence devono essere certificate e pubblicate in Azure Marketplace.

Per eseguire una ricerca nell'elenco di immagini corrente di Azure Marketplace, vedere [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/compute).

Per informazioni su come pubblicare un'immagine di macchina virtuale per Azure Marketplace, vedere [Guida alla creazione di un'immagine di macchina virtuale per Azure Marketplace](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation).

## <a name="scalability-evaluation-considerations"></a>Considerazioni per la valutazione della scalabilità
### <a name="cortana-intelligence-solutions-should-include-a-scalable-big-data-platform"></a>Le soluzioni Cortana Intelligence devono includere una piattaforma Big Data scalabile
Le soluzioni Cortana Intelligence devono supportare la scalabilità fino a volumi di dati molto grandi. In Azure ciò significa che devono includere una delle due piattaforme con volumi dati dell'ordine dei petabyte:
- Archivio Azure Data Lake
- Azure SQL Data Warehouse

Se la soluzione non richiede il supporto di questi volumi di dati o se si usa una piattaforma di dati alternativa, specificarlo nella giustificazione del test case.
### <a name="cortana-intelligence-solutions-should-include-dedicated-ingestion-data-environments"></a>Le soluzioni Cortana Intelligence devono includere ambienti di dati dedicati per l'inserimento
In genere è consigliabile evitare che le soluzioni Cortana Intelligence inseriscano direttamente dati in origini dati relazionali. I dati non elaborati dovranno essere archiviati in un ambiente non strutturato, con inserimenti/aggiornamenti idempotent in qualsiasi archivio relazionale mediante Azure Data Factory.

Per altre informazioni sulla copia di dati con Azure Data Factory, vedere [Esercitazione: Creare una pipeline con l’attività Copia usando Visual Studio](https://docs.microsoft.com/azure/data-factory/v1/data-factory-copy-activity-tutorial-using-visual-studio).

### <a name="azure-sql-data-warehouse-should-use-polybase-for-data-ingestion"></a>Azure SQL Data Warehouse deve usare PolyBase per l'inserimento di dati
Azure SQL Data Warehouse supporta PolyBase, che dispone di modalità di inserimento dati altamente scalabili e in parallelo. Con PolyBase è possibile usare Azure SQL DW per eseguire query su set di dati esterni archiviati in Archiviazione BLOB di Azure o in Azure Data Lake Store. Questa funzionalità offre prestazioni superiori rispetto agli altri metodi di aggiornamento in blocco.

Per un'introduzione all'uso di PolyBase con Azure SQL Data Warehouse, vedere [Caricare dati con PolyBase in SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-load-with-polybase).

Per informazioni sulle procedure consigliate con PolyBase e Azure SQL Data Warehouse, vedere [Guida per l'uso di PolyBase in SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-polybase-guide).

## <a name="availability-evaluation-considerations"></a>Considerazioni per la valutazione della disponibilità

### <a name="datasets-accessible-to-end-users-should-support-a-large-volume-of-concurrent-users"></a>I set di dati accessibili agli utenti finali devono supportare un volume elevato di utenti simultanei
Durante l'esecuzione dello strumento di valutazione, verrà richiesto di specificare le risorse di pubblicazione o creazione di report. Si presuppone che queste risorse siano destinate all'accesso degli utenti finali e non degli sviluppatori. Queste risorse devono supportare un numero medio/grande di utenti simultanei.

In particolare, Azure SQL Data Warehouse NON deve essere l'unica origine dati disponibile per gli utenti finali. Se Azure SQL DW viene incluso come risorsa per il gruppo Power Users, è necessario rendere disponibile Azure Analysis Services per gli utenti comuni.

Per altre informazioni sui limiti di concorrenza di Azure SQL DW, vedere [Gestione della concorrenza e del carico di lavoro in SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-develop-concurrency).

Per altre informazioni su Azure Analysis Services, vedere [Informazioni su Azure Analysis Services](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview).

### <a name="azure-sql-resources-should-have-a-read-only-replica-for-failover"></a>Le risorse Azure SQL devono avere una replica di sola lettura per il failover
I database SQL di Azure supportano la replica geografica in un'istanza secondaria. Questa istanza può quindi essere usata come istanza di failover per offrire applicazioni a disponibilità elevata.

Per altre informazioni sulla replica geografica per i database SQL di Azure, vedere [Panoramica: gruppi di failover e replica geografica attiva](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview).

Per istruzioni su come configurare la replica geografica per SQL Azure, vedere [Configurare la replica geografica attiva per il database SQL di Azure con Transact-SQL](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-transact-sql).

### <a name="azure-sql-data-warehouse-should-have-geo-redundant-backups-enabled"></a>Azure SQL Data Warehouse deve avere i backup con ridondanza geografica attivati
Azure SQL DW supporta i backup giornalieri su strutture archiviazione con ridondanza geografica. La replica geografica garantisce la possibilità di ripristinare il data warehouse anche quando non si può accedere agli snapshot nell'area primaria. Questa funzionalità è attivata per impostazione predefinita e non deve essere disattivata per le soluzioni Cortana Intelligence.

Per altre informazioni su backup e ripristino in Azure SQL DW, vedere [Backup di SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-backups).

### <a name="virtual-machines-should-be-configured-with-availability-sets"></a>Le macchine virtuali devono essere configurate in set di disponibilità
Le macchine virtuali di Azure devono essere configurate in set di disponibilità, per ridurre al minimo l'impatto di eventi di manutenzione pianificati e non.

Per altre informazioni sulla disponibilità delle macchine virtuali di Azure, vedere [Gestire la disponibilità delle macchine virtuali Windows in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability).

## <a name="other-evaluation-considerations"></a>Altre considerazioni per la valutazione
### <a name="cortana-intelligence-apps-should-use-a-centralized-tool-for-data-orchestration"></a>Le applicazioni Cortana Intelligence devono usare uno strumento centralizzato per l'orchestrazione dei dati
L'uso di un unico strumento per la gestione e la pianificazione dello spostamento e della trasformazione dei dati garantisce la coerenza per i dati di importanza critica. Questo approccio garantisce logica chiara intorno alla logica di retry, gestione delle dipendenze, avvisi/registrazione e così via. Per l'orchestrazione di dati in Azure è consigliabile l'uso di [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/v1/data-factory-introduction).

Se si usa uno strumento diverso da Azure Data Factory per l'orchestrazione di dati, descrivere lo strumento o gli strumenti in uso.
### <a name="azure-machine-learning-models-should-be-retrained-using-azure-data-factory"></a>È necessario ripetere il training dei modelli di Azure Machine Learning con Azure Data Factory
Azure Machine Learning (AzureML) offre strumenti intuitivi per la creazione e la distribuzione di pipeline di modellazione predittiva e machine learning. È tuttavia è importante che le distribuzioni di produzione di questi modelli di AzureML non siano basate su un singolo set di dati predefinito, ma siano in grado di adattarsi alle mutevoli dinamiche dei fenomeni del mondo reale.

Per informazioni sulla creazione di servizi Web per la ripetizione del training in AzureML, vedere [Ripetere il training dei modelli di Machine Learning a livello di codice](https://docs.microsoft.com/azure/machine-learning/machine-learning-retrain-models-programmatically).

Per altre informazioni sull'automatizzazione del processo di training del modello con Azure Data Factory, vedere [Aggiornamento dei modelli di Azure Machine Learning con Attività della risorsa di aggiornamento](https://docs.microsoft.com/azure//data-factory/v1/data-factory-azure-ml-update-resource-activity).

## <a name="existing-documentation"></a>Documentazione esistente
[Programma Microsoft Azure Certified per la crescita dell'attività cloud](https://azure.microsoft.com/en-us/marketplace/programs/certified/)

[Microsoft Azure Certified per Cortana Intelligence](https://azure.microsoft.com/en-us/marketplace/programs/certified/cortana/)

