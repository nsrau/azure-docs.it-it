---
title: Note sulla versione di Database SQL di Azure | Microsoft Docs
description: Scopri le nuove funzionalità e miglioramenti nel servizio Database SQL di Azure e nella documentazione del Database SQL di Azure
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.subservice: service
ms.devlang: ''
ms.topic: conceptual
ms.date: 03/05/2019
ms.author: carlrab
ms.openlocfilehash: 6600a578ba9c73c8a2c71466fd0b008f19058b80
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57861291"
---
# <a name="sql-database-release-notes"></a>Note sulla versione di Database SQL

Questo articolo elenca le nuove funzionalità e miglioramenti nel servizio Database SQL e nella documentazione del Database SQL. Per il miglioramento del servizio di Database SQL, vedere anche [aggiornamenti del servizio Database SQL](https://azure.microsoft.com/updates/?product=sql-database). Per apportare miglioramenti ad altri servizi di Azure, vedere [aggiornamenti del servizio](https://azure.microsoft.com/updates).

## <a name="march-2019"></a>Marzo 2019

### <a name="service-improvements"></a>Miglioramenti al servizio

| Miglioramenti al servizio | Dettagli |
| --- | --- |
| Presto disponibile ||
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
|Database SQL come origine dei dati di riferimento per Stream Analitica. | Per altre informazioni, vedere [Analitica Stream](https://azure.microsoft.com/services/stream-analytics/).|
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
| Le istanze gestite e la replica transazionale | Aggiunta articolo sull'uso di [la replica transazionale con le istanze gestite](replication-with-sql-database-managed-instance.md) |
| Aggiunta AD Azure con l'esercitazione per istanza gestita | Ciò [Azure AD con istanza gestita](sql-database-managed-instance-aad-security-tutorial.md) Mostra dell'esercitazione, è necessario configurare e testare gestiti sicurezza istanza usando gli account di accesso di Azure AD. |
| Contenuto aggiornato per l'automazione di processi usando gli script Transact-SQL | Aggiornamento e il contenuto per l'uso di chiarimento [automazione di processi tramite Transact-SQL script](sql-database-job-automation-overview.md) per singoli database, pool elastici e istanze gestite |
| Contenuto di protezione per le istanze gestite aggiornato | Aggiornamento e chiarimento contenuto per il [modello di sicurezza per le istanze gestite](sql-database-security-overview.md), analogie con il modello di sicurezza per i database singoli e pool elastici e |
| Aggiornare tutte le guide introduttive ed esercitazioni | Tutte le guide introduttive ed esercitazioni nel [documentazione](https://docs.microsoft.com/azure/sql-database) sono stati aggiornati e aggiornata in modo da riflettere le modifiche nel portale di Azure |
| Aggiunta di guide di panoramica | Una Guida di panoramica introduttiva per l'aggiunta [database singoli](sql-database-quickstart-guide.md) e per [istanze gestite](sql-database-managed-instance-quickstart-guide.md) |
| Glossario di Database SQL aggiunta dei termini | Ciò [glossario di termini](sql-database-glossary-terms.md) articolo fornisce un elenco definitivo di termini di Database SQL e i collegamenti alla pagina concettuale principale che illustra il termine nel contesto. |
| &nbsp; |

## <a name="contribute-to-content-improvement"></a>Contribuire al miglioramento del contenuto

Set di documentazione di SQL di Azure è open source. Operare in offre diversi vantaggi:

- Repository open source la pianificazione per ottenere commenti e suggerimenti su quali docs sono più necessari.
- Revisione dei repository open source di open per pubblicare il contenuto più utile nella prima versione.
- Repository open source gli aggiornamenti per renderne più semplice migliorare continuamente il contenuto.

Per contribuire al contenuto della documentazione di Database SQL di Azure, vedere la [Panoramica della Guida per i collaboratori Microsoft Docs](https://docs.microsoft.com/contribute/). L'esperienza utente in [docs.microsoft.com](https://docs.microsoft.com/) integra [GitHub](https://github.com/) flussi di lavoro direttamente per renderne ancora più semplice. Avviarsi [modificando il documento che si sta visualizzando](https://docs.microsoft.com/contribute/#quick-edits-to-existing-documents). O, aiutare [revisionando i nuovi argomenti](https://docs.microsoft.com/contribute/#review-open-prs), o [creare problemi di qualità](https://docs.microsoft.com/contribute/#create-quality-issues).
