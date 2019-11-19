---
title: Note sulla versione
description: Informazioni sulle nuove funzionalità e miglioramenti nel servizio database SQL di Azure e nella documentazione del database SQL di Azure
services: sql-database
author: stevestein
ms.service: sql-database
ms.subservice: service
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: sstein
ms.openlocfilehash: 5c70d1d7d62b41965d4c81e8bbe0e5b2dc3cbb92
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74166251"
---
# <a name="sql-database-release-notes"></a>Note sulla versione del database SQL

Questo articolo elenca le funzionalità del database SQL attualmente disponibili in anteprima pubblica. Per aggiornamenti e miglioramenti del database SQL, vedere [aggiornamenti del servizio di database SQL](https://azure.microsoft.com/updates/?product=sql-database). Per gli aggiornamenti e i miglioramenti apportati ad altri servizi di Azure, vedere [aggiornamenti dei servizi](https://azure.microsoft.com/updates).

## <a name="features-in-public-preview"></a>Funzionalità disponibili in anteprima pubblica

### <a name="single-databasetabsingle-database"></a>[Database singolo](#tab/single-database)

| Funzionalità | Dettagli |
| ---| --- |
| Nuove generazioni di hardware serie Fsv2 e serie M| Per informazioni, vedere [generazioni di hardware](sql-database-service-tiers-vcore.md#hardware-generations).|
| [Collegamento privato di Azure](https://azure.microsoft.com/updates/private-link-now-available-in-preview/)| Collegamento privato semplifica l'architettura di rete e protegge la connessione tra endpoint in Azure mantenendo i dati sulla rete di Azure ed eliminando quindi l'esposizione a Internet. Collegamento privato ti permette anche di creare ed eseguire il rendering di servizi personalizzati in Azure. |
| Recupero accelerato del database con singoli database e pool elastici | Per informazioni, vedere [accelerazione del ripristino del database](sql-database-accelerated-database-recovery.md).|
|Conteggio approssimativo Distinct|Per informazioni, vedere [conteggio approssimativo Distinct](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#approximate-query-processing).|
|Modalità batch su rowstore (con livello di compatibilità 150)|Per informazioni, vedere [modalità batch su rowstore](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#batch-mode-on-rowstore).|
| Individuazione dati e classificazione  |Per informazioni, vedere [database SQL di Azure e SQL data warehouse individuazione dati & classificazione](sql-database-data-discovery-and-classification.md).|
| Processi di database elastici | Per informazioni, vedere [creare, configurare e gestire processi elastici](elastic-jobs-overview.md). |
| Query elastiche | Per informazioni, vedere [Panoramica delle query elastiche](sql-database-elastic-query-overview.md). |
| Transazioni elastiche | [Transazioni distribuite tra database cloud](sql-database-elastic-transactions-overview.md). |
|Feedback delle concessioni di memoria (modalità riga) (con livello di compatibilità 150)|Per informazioni, vedere [feedback delle concessioni di memoria (modalità riga)](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#row-mode-memory-grant-feedback).|
| Editor di query nel portale di Azure |Per informazioni, vedere [usare l'editor di query SQL di portale di Azure per connettersi ed eseguire query sui dati](sql-database-connect-query-portal.md).|
| R Services/Machine Learning con database singoli e pool elastici |Per informazioni, vedere [Machine Learning Services nel database SQL di Azure](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services?view=sql-server-2017#machine-learning-services-in-azure-sql-database).|
|Analitica SQL|Per informazioni, vedere [analisi SQL di Azure](../azure-monitor/insights/azure-sql.md).|
|Compilazione posticipata della variabile di tabella (con livello di compatibilità 150)|Per informazioni, vedere [compilazione posticipata della variabile di tabella](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#table-variable-deferred-compilation).|
| &nbsp; |

### <a name="managed-instancetabmanaged-instance"></a>[Istanza gestita](#tab/managed-instance)

| Funzionalità | Dettagli |
| ---| --- |
| <a href="/azure/sql-database/sql-database-managed-instance-connectivity-architecture#service-aided-subnet-configuration-public-preview-in-east-us-and-west-us">Configurazione della subnet assistita dal servizio</a> | Un modo sicuro e pratico per gestire la configurazione della subnet. |
| <a href="/azure/sql-database/sql-database-instance-pools">Pool di istanze</a> | Un modo pratico ed economicamente conveniente per eseguire la migrazione di istanze SQL più piccole al cloud. |
| <a href="https://aka.ms/managed-instance-tde-byok">Transparent Data Encryption (Transparent Data Encryption) con Bring Your Own Key (BYOK)</a> |Per informazioni, vedere [Transparent Data Encryption SQL di Azure con chiavi gestite dal cliente in Azure Key Vault: Bring your own key supporto](transparent-data-encryption-byok-azure-sql.md).|
| <a href="https://aka.ms/managed-instance-aadlogins">Entità del server Azure AD a livello di istanza (account di accesso)</a> | Creare account di accesso a livello di server utilizzando l'istruzione <a href="https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">create login from External provider</a> . |
| [Replica transazionale](sql-database-managed-instance-transactional-replication.md) | Replicare le modifiche dalle tabelle in altri database posizionati in istanze gestite, database singoli o istanze di SQL Server o aggiornare le tabelle quando alcune righe vengono modificate in altre istanze gestite o SQL Server istanza. Per informazioni, vedere [configurare la replica in un database dell'istanza gestita di database SQL di Azure](replication-with-sql-database-managed-instance.md). |
| Introduzione al rilevamento delle minacce |Per informazioni, vedere [configurare il rilevamento delle minacce in istanza gestita di database SQL di Azure](sql-database-managed-instance-threat-detection.md).|
| Ricreare database eliminati con istanze gestite |Per informazioni, vedere [ricreare database rimossi in Azure SQL istanza gestita](https://medium.com/azure-sqldb-managed-instance/re-create-dropped-databases-in-azure-sql-managed-instance-dc369ed60266).|
| &nbsp; |

---

## <a name="new-features"></a>Nuove funzionalità

### <a name="managed-instance-h2-2019-updates"></a>Aggiornamenti dell'istanza gestita H2 2019

- I [gruppi di failover automatico](https://azure.microsoft.com/updates/azure-sql-database-auto-failover-groups-feature-now-available-in-all-regions/) consentono di replicare tutti i database dall'istanza primaria a un'istanza secondaria in un'altra area.
- Configurare il comportamento dell'istanza gestita con i [flag di traccia globali](https://azure.microsoft.com/updates/global-trace-flags-are-now-available-in-azure-sql-database-managed-instance/).

### <a name="managed-instance-h1-2019-updates"></a>Aggiornamenti H1 2019 per istanza gestita

Le funzionalità seguenti sono abilitate nel modello di distribuzione dell'istanza gestita in H1 2019:
  - Supporto per le sottoscrizioni con <a href="https://aka.ms/sql-mi-visual-studio-subscribers">credito mensile di Azure per Sottoscrittori di Visual Studio</a> e maggiori [limiti a livello](sql-database-managed-instance-resource-limits.md#regional-resource-limitations)di area.
  - Supporto per <a href="https://docs.microsoft.com/sharepoint/administration/deploy-azure-sql-managed-instance-with-sharepoint-servers-2016-2019"> SharePoint 2016 e SharePoint 2019 </a> e per <a href="https://docs.microsoft.com/business-applications-release-notes/october18/dynamics365-business-central/support-for-azure-sql-database-managed-instance"> Dynamics 365 Business Central </a>
  - Creare istanze con <a href="https://aka.ms/managed-instance-collation">regole di confronto a livello di server</a> e il <a href="https://azure.microsoft.com/updates/managed-instance-time-zone-ga/">fuso orario</a> desiderato.
  - Le istanze gestite sono ora protette con il <a href="sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md">firewall incorporato</a>.
  - Configurare le istanze per l'uso di [endpoint pubblici](sql-database-managed-instance-public-endpoint-configure.md), la connessione per [l'override del proxy](sql-database-connectivity-architecture.md#connection-policy) per ottenere prestazioni di rete migliori, <a href="https://aka.ms/four-cores-sql-mi-update">4 vcore di generazione hardware quinta generazione</a> o <a href="https://aka.ms/managed-instance-configurable-backup-retention">configurare la conservazione dei backup fino a 35 giorni per il</a> ripristino temporizzato. La conservazione dei backup a lungo termine (fino a 10 anni) non è ancora abilitata, quindi è possibile usare i <a href="https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server">backup di sola copia</a> come alternativa.
  - Le nuove funzionalità consentono di eseguire <a href="https://medium.com/@jocapc/geo-restore-your-databases-on-azure-sql-instances-1451480e90fa">il ripristino geografico del database in un altro Data Center tramite PowerShell</a>, [Rename database](https://azure.microsoft.com/updates/azure-sql-database-managed-instance-database-rename-is-supported/), [Delete Virtual cluster](sql-database-managed-instance-delete-virtual-cluster.md).
  - Il nuovo [ruolo Collaboratore istanza](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-managed-instance-contributor) incorporato consente la conformità alla separazione dei compiti (SOD) con i principi di sicurezza e la conformità agli standard aziendali.
  - Istanza gestita è disponibile nelle aree di Azure per enti pubblici seguenti per la versione GA (US Gov Texas, US Gov Arizona), nonché in Cina settentrionale 2 e Cina orientale 2. È disponibile anche nelle aree pubbliche seguenti: Australia centrale, Australia centrale 2, Brasile meridionale, Francia meridionale, Emirati Arabi Uniti centrali, Emirati Arabi Uniti settentrionali, Sudafrica settentrionale, Sudafrica occidentale.

## <a name="fixed-known-issues"></a>Problemi noti risolti

- **Aug 2019** : i database indipendenti sono completamente supportati nell'istanza gestita.
- **Ottobre 2019** : il ripristino temporizzato del database predefinito dal livello business critical al livello per utilizzo generico non riuscirà se il database di origine contiene oggetti OLTP in memoria.
- **2019 novembre** -la coerenza del database viene verificata usando `DBCC CHECKDB` dopo il ripristino del database dall'archiviazione BLOB di Azure.

## <a name="updates"></a>aggiornamenti

Per un elenco degli aggiornamenti e dei miglioramenti del database SQL, vedere [aggiornamenti del servizio di database SQL](https://azure.microsoft.com/updates/?product=sql-database).

Per aggiornamenti e miglioramenti a tutti i servizi di Azure, vedere [aggiornamenti dei servizi](https://azure.microsoft.com/updates).

## <a name="contribute-to-content"></a>Contribuisci al contenuto

Per contribuire alla documentazione del database SQL di Azure, vedere la guida per i [collaboratori di docs](https://docs.microsoft.com/contribute/).
