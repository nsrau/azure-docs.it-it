---
title: Note sulla versione di Database SQL di Azure | Microsoft Docs
description: Scopri le nuove funzionalità e miglioramenti nel servizio Database SQL di Azure e nella documentazione del Database SQL di Azure
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.subservice: service
ms.devlang: ''
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: sstein
ms.openlocfilehash: 04c19c9a8603fee46914eb94b50c4f97e34e9c42
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67070223"
---
# <a name="sql-database-release-notes"></a>Note sulla versione di Database SQL

Questo articolo elenca le nuove funzionalità e miglioramenti nel servizio Database SQL e nella documentazione del Database SQL. Per il miglioramento del servizio di Database SQL, vedere anche [aggiornamenti del servizio Database SQL](https://azure.microsoft.com/updates/?product=sql-database). Per apportare miglioramenti ad altri servizi di Azure, vedere [aggiornamenti del servizio](https://azure.microsoft.com/updates).

## <a name="features-in-public-preview"></a>Funzionalità in anteprima pubblica

| Funzionalità | Dettagli |
| ---| --- |
| Recupero di database accelerata con i database singoli e pool elastici | Per informazioni, vedere [ripristino del Database con accelerazione](sql-database-accelerated-database-recovery.md).|
|Numero approssimativo di Distinct|Per informazioni, vedere [approssimativo Count Distinct](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#approximate-query-processing).|
|Modalità batch a Rowstore (con il livello di compatibilità 150)|Per informazioni, vedere [la modalità Batch a Rowstore](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#batch-mode-on-rowstore).|
| Individuazione dati e classificazione  |Per informazioni, vedere [Database SQL di Azure e SQL Data Warehouse individuazione dati e classificazione](sql-database-data-discovery-and-classification.md).|
| Processi di database elastici | Per informazioni, vedere [crea, configurare e gestire processi elastici](elastic-jobs-overview.md). |
| Query elastiche | Per informazioni, vedere [panoramica delle query elastiche](sql-database-elastic-query-overview.md). |
| Transazioni elastiche | [Le transazioni distribuite tra database cloud](sql-database-elastic-transactions-overview.md). |
|Feedback delle concessioni di memoria (modalità riga) (con il livello di compatibilità 150)|Per informazioni, vedere [Feedback delle concessioni di memoria (modalità riga)](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#row-mode-memory-grant-feedback).|
| Editor di query nel portale di Azure |Per informazioni, vedere [utilizzare l'editor di query SQL del portale di Azure per connettersi ed eseguire query sui dati](sql-database-connect-query-portal.md).|
| R services / machine learning con i database singoli e pool elastici |Per informazioni, vedere [servizi di Machine Learning in Azure SQL Database](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services?view=sql-server-2017#machine-learning-services-in-azure-sql-database).|
| Ricrea database eliminati con le istanze gestite |Per informazioni, vedere [ricreare eliminato i database nell'istanza gestita SQL di Azure](https://medium.com/azure-sqldb-managed-instance/re-create-dropped-databases-in-azure-sql-managed-instance-dc369ed60266).|
| Replica con le istanze gestite |Per informazioni, vedere [configurare la replica in un database di istanza gestita di Database SQL di Azure](replication-with-sql-database-managed-instance.md).|
| Livello di elaborazione serverless | Per informazioni, vedere [Database SQL senza server (anteprima)](sql-database-serverless.md).|
|Analitica SQL|Per informazioni, vedere [Analitica SQL di Azure](../azure-monitor/insights/azure-sql.md).|
|Tabella variabili posticipata compilazione (con il livello di compatibilità 150)|Per informazioni, vedere [compilazione posticipata variabili tabella](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#table-variable-deferred-compilation).|
| Rilevamento delle minacce con le istanze gestite |Per informazioni, vedere [istanza gestita di configurare il rilevamento delle minacce nel Database SQL di Azure](sql-database-managed-instance-threat-detection.md).|
| Transparent data encryption (TDE) con Bring Your Own Key (BYOK) con le istanze gestite |Per informazioni, vedere [Azure SQL Transparent Data Encryption con chiavi gestite dal cliente in Azure Key Vault: Bring Your Own Key support](transparent-data-encryption-byok-azure-sql.md).|
| &nbsp; |

## <a name="june-2019"></a>Giugno 2019

### <a name="service-improvements"></a>Miglioramenti al servizio

| Miglioramenti al servizio | Dettagli |
| --- | --- |
| 4 Vcore di istanza gestita nella generazione di hardware Gen5| Per altre informazioni, vedere https://aka.ms/four-cores-sql-mi-update. |
| Istanza gestita: supporto per le sottoscrizioni con carta di credito Azure mensile per sottoscrittori di Visual Studio | Per altre informazioni, vedere https://aka.ms/sql-mi-visual-studio-subscribers. |
|Maggiori limiti a livello di area per l'istanza gestita| Per altre informazioni, vedere [limitazioni delle risorse a livello di area](sql-database-managed-instance-resource-limits.md#regional-resource-limitations).
| &nbsp; |


## <a name="may-2019"></a>Maggio 2019

### <a name="service-improvements"></a>Miglioramenti al servizio

| Miglioramenti al servizio | Dettagli |
| --- | --- |
|Livello di servizio su scala molto vasta rilasciato per la disponibilità generale| Per altre informazioni, vedere [livello di servizio con Iperscalabilità per fino a 100 TB](sql-database-service-tier-hyperscale.md) e [ottenere scalabilità per i carichi di lavoro per database di Azure con Iperscalabilità ad alte prestazioni](https://azure.microsoft.com/blog/get-high-performance-scaling-for-your-azure-database-workloads-with-hyperscale/).|
|Livello di calcolo senza server rilasciato per l'anteprima pubblica | Per altre informazioni, vedere [Database SQL senza server (anteprima)](sql-database-serverless.md).|
|Supporto del fuso orario per le istanze gestite rilasciato per la disponibilità generale |Per altre informazioni, vedere [fuso orario in istanza gestita di Azure SQL Database](sql-database-managed-instance-timezone.md).|
|Regole di confronto di istanza con istanze gestite rilasciato per la disponibilità generale |Per informazioni, vedere [uso di PowerShell con modelli Azure Resource Manager per creare un'istanza gestita di Database SQL di Azure](./scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md).|
| Frequenza di log delle transazioni e di destinazione IOPS aumentata per i database utilizzando il modello di acquisto basato su vCore| Per altre informazioni, vedere [limiti delle risorse per i database singoli con il modello di acquisto basato su vCore](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-single-databases) e [limiti delle risorse per i database singoli con il modello di acquisto basato su DTU](https://docs.microsoft.com/azure/sql-database/sql-database-dtu-resource-limits-single-databases).
| &nbsp; |

### <a name="documentation-improvements"></a>Miglioramenti alla documentazione

| Miglioramenti alla documentazione | Dettagli |
| --- | --- |
| Documentazione di livello di servizio su scala molto vasta aggiornati per la versione disponibile a livello generale| Per altre informazioni, vedere [livello di servizio con Iperscalabilità per fino a 100 TB](sql-database-service-tier-hyperscale.md).|
|Documentazione di livello di calcolo senza server rilasciati con versione di anteprima pubblica| Per altre informazioni, vedere [Database SQL senza server (anteprima)](sql-database-serverless.md).|
| &nbsp; |

## <a name="april-2019"></a>Aprile 2019

### <a name="service-improvements"></a>Miglioramenti al servizio

| Miglioramenti al servizio | Dettagli |
| --- | --- |
| Endpoint pubblici per l'istanza gestita in anteprima pubblica| Per altre informazioni, vedere [istanza in modo sicuro con endpoint pubblici gestita di Database SQL di Azure usando](sql-database-managed-instance-public-endpoint-securely.md).|
| Supporto del fuso orario per istanza disponibile in anteprima pubblica gestita| Per altre informazioni, vedere [fuso orario in istanza gestita di Azure SQL Database](sql-database-managed-instance-timezone.md).|
| Protezione dei database SQL di Azure con identità gestite nella seconda versione di anteprima pubblica di rilascio| Visualizzare [protezione di database SQL di Azure con identità gestite è stati semplificati](https://azure.microsoft.com/blog/securing-azure-sql-databases-with-managed-identities-just-got-easier/).|
| &nbsp; |

### <a name="documentation-improvements"></a>Miglioramenti alla documentazione

| Miglioramenti alla documentazione | Dettagli |
| --- | --- |
| Endpoint pubblici per l'istanza gestita in anteprima pubblica| Per altre informazioni, vedere [istanza in modo sicuro con endpoint pubblici gestita di Database SQL di Azure usando](sql-database-managed-instance-public-endpoint-securely.md).|
| Supporto del fuso orario per istanza disponibile in anteprima pubblica gestita| Per altre informazioni, vedere [fuso orario in istanza gestita di Azure SQL Database](sql-database-managed-instance-timezone.md). |
| Governance delle risorse nel Database SQL di Azure | Per altre informazioni, vedere [governance delle risorse nel Database SQL di Azure](https://azure.microsoft.com/blog/resource-governance-in-azure-sql-database/). || &nbsp; |

## <a name="march-2019"></a>Marzo 2019

### <a name="service-improvements"></a>Miglioramenti al servizio

| Miglioramenti al servizio | Dettagli |
| --- | --- |
| Leggere il supporto di scalabilità orizzontale per il Database SQL di Azure rilasciati a livello generale | Per altre informazioni, vedere [scalabilità in lettura](sql-database-read-scale-out.md).|
| &nbsp; |

### <a name="documentation-improvements"></a>Miglioramenti alla documentazione

| Miglioramenti alla documentazione | Dettagli |
| --- | --- |
| Aggiunta log limiti per i database singoli|Per altre informazioni, vedere [limiti delle risorse di Vcore per database singolo](sql-database-vcore-resource-limits-single-databases.md).|
| Aggiunta log limiti dei pool elastici e i database in pool|Per altre informazioni, vedere [dei limiti delle risorse di Vcore per pool elastici](sql-database-vcore-resource-limits-elastic-pools.md).|
| Governance delle velocità di log delle transazioni aggiunto| Aggiunto nuovo contenuto per [governance delle velocità di log delle transazioni](sql-database-resource-limits-database-server.md#transaction-log-rate-governance).|
| Esempi aggiornati di PowerShell per database singoli e pool elastici per usare il modulo az.sql | Per altre informazioni, vedere [esempi di PowerShell per database singoli e pool elastici](sql-database-powershell-samples.md#single-database-and-elastic-pools).|
| &nbsp; |

## <a name="february-2019"></a>Febbraio 2019

### <a name="service-improvements"></a>Miglioramenti al servizio

| Miglioramenti al servizio | Dettagli |
| --- | --- |
|Creazione di un indice online ripristinabile è ora disponibile a livello generale| Per altre informazioni, vedere [Create Index](https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql).|
|Supporto per le tabelle di route migliorati per istanza gestita| Per altre informazioni, vedere [requisiti di rete](sql-database-managed-instance-connectivity-architecture.md#network-requirements).|
|Ridenominazione del database è supportata in istanza gestita | Per altre informazioni, vedere la [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-mi-current) e [sp_rename](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-rename-transact-sql) sintassi.|
|Database SQL come origine dei dati di riferimento per Stream Analitica. | Per altre informazioni, vedere [Analitica Stream](https://azure.microsoft.com/services/stream-analytics/) e [Analitica Stream di Azure supporta ora Database SQL di Azure come input di dati di riferimento](https://azure.microsoft.com/blog/azure-stream-analytics-now-supports-azure-sql-database-as-reference-data-input/).|
|Data Migration Assistant aggiunge il supporto per l'istanza gestita. |Per altre informazioni, vedere [novità in DMA](https://docs.microsoft.com/sql/dma/dma-whatsnew).|
|SQL Server Migration Assistant aggiunge il supporto per la valutazione della conformità di destinazione per l'istanza gestita. | Per altre informazioni, vedere [SQL Server Migration Assistant](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant).
|Servizio migrazione del database supporta la migrazione da Amazon Servizi Desktop remoto a istanza gestita | Per altre informazioni, vedere [Esercitazione: Eseguire la migrazione di servizi desktop remoto SQL Server al Database SQL di Azure o un'istanza gestita Azure SQL Database online usando servizio migrazione del database](../dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online.md).|
| &nbsp; |

### <a name="documentation-improvements"></a>Miglioramenti alla documentazione

| Miglioramenti alla documentazione | Dettagli |
| --- | --- |
|Aggiunta di gestiti chiarimenti opzione di distribuzione istanza|Aggiornato molti articoli per chiarire l'applicabilità al singolo database, pool elastico e opzioni di distribuzione dell'istanza gestita. |
|Dimensioni di tempdb aggiornato per il modello di acquisto basato su DTU | Per altre informazioni, vedere [database Tempdb nel Database SQL](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).|
|Aggiornata di importazione / esportazione con il file bacpac per il supporto di istanza gestita| Per altre informazioni, vedere [importare da BACPAC](sql-database-import.md) e [esportare in file BACPAC](sql-database-export.md). |
| &nbsp; |

## <a name="january-2019"></a>Gennaio 2019

### <a name="service-improvements"></a>Miglioramenti al servizio

| Miglioramenti al servizio | Dettagli |
| --- | --- |
| Opzioni di granularità aggiuntivo per le risorse di calcolo | Livelli per di servizio utilizzo generico e business critica [database singoli](sql-database-vcore-resource-limits-single-databases.md) e [i pool elastici](sql-database-vcore-resource-limits-elastic-pools.md) ora disponibili più opzioni di calcolo con granularità fine.|
| Record di controllo di visualizzazione per istanza gestita nel portale di Azure | Visualizzando [record per le istanze gestite di controllo](sql-database-managed-instance-auditing.md) nel portale di Azure è ora supportato portale.|
| Funzionalità di rilevamento delle minacce avanzato rinominato in protezione dati avanzata | Funzionalità di rilevamento delle minacce avanzato rinominata [protezione dati avanzata](sql-advanced-threat-protection.md) per singoli database, pool elastici e istanze gestite. |
| &nbsp; |

### <a name="documentation-improvements"></a>Miglioramenti alla documentazione

| Miglioramenti alla documentazione | Dettagli |
| --- | --- |
| Le istanze gestite e la replica transazionale | Aggiunta articolo sull'uso [la replica transazionale con le istanze gestite](replication-with-sql-database-managed-instance.md). |
| Aggiunta AD Azure con l'esercitazione per istanza gestita | Ciò [Azure AD con istanza gestita](sql-database-managed-instance-aad-security-tutorial.md) Mostra dell'esercitazione, è necessario configurare e testare gestiti sicurezza istanza usando gli account di accesso di Azure AD. |
| Contenuto aggiornato per l'automazione di processi usando gli script Transact-SQL | Aggiornamento e il contenuto per l'uso di chiarimento [automazione di processi tramite Transact-SQL script](sql-database-job-automation-overview.md) per singoli database, pool elastici e istanze gestite. |
| Contenuto di protezione per le istanze gestite aggiornato | Aggiornamento e chiarimento contenuto per il [modello di sicurezza per le istanze gestite](sql-database-security-overview.md), analogie con il modello di sicurezza per i database singoli e pool elastici e. |
| Aggiornare tutte le guide introduttive ed esercitazioni | Tutte le guide introduttive ed esercitazioni nel [documentazione](https://docs.microsoft.com/azure/sql-database) sono stati aggiornati e aggiornata in modo da riflettere le modifiche nel portale di Azure. |
| Aggiunta di guide di panoramica | Una Guida di panoramica introduttiva per l'aggiunta [database singoli](sql-database-quickstart-guide.md) e per [istanze gestite](sql-database-managed-instance-quickstart-guide.md). |
| Glossario di Database SQL aggiunta dei termini | Ciò [glossario di termini](sql-database-glossary-terms.md) articolo fornisce un elenco definitivo di termini di Database SQL e i collegamenti alla pagina concettuale principale che illustra il termine nel contesto. |
| &nbsp; |

## <a name="contribute-to-content-improvement"></a>Contribuire al miglioramento del contenuto

Set di documentazione di SQL di Azure è open source. Operare in offre diversi vantaggi:

- Repository open source la pianificazione per ottenere commenti e suggerimenti su quali docs sono più necessari.
- Revisione dei repository open source di open per pubblicare il contenuto più utile nella prima versione.
- Repository open source gli aggiornamenti per renderne più semplice migliorare continuamente il contenuto.

Per contribuire al contenuto della documentazione di Database SQL di Azure, vedere la [Panoramica della Guida per i collaboratori Microsoft Docs](https://docs.microsoft.com/contribute/). L'esperienza utente in [docs.microsoft.com](https://docs.microsoft.com/) integra [GitHub](https://github.com/) flussi di lavoro direttamente per renderne ancora più semplice. Avviarsi [modificando il documento che si sta visualizzando](https://docs.microsoft.com/contribute/#quick-edits-to-existing-documents). O, aiutare [revisionando i nuovi argomenti](https://docs.microsoft.com/contribute/#review-open-prs), o [creare problemi di qualità](https://docs.microsoft.com/contribute/#create-quality-issues).
