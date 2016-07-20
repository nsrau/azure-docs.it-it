<properties
	pageTitle="Panoramica: strumenti di gestione per database SQL"
	description="Confronta strumenti e le opzioni per la gestione di database SQL di Azure"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/04/2016"
	ms.author="sstein"/>

# Panoramica: strumenti di gestione per database SQL

Questo argomento analizza e mette a confronto strumenti e opzioni per la gestione di database SQL di Azure in modo da selezionare lo strumento appropriato per il processo, l'azienda e l'utente. La scelta dello strumento appropriato dipende dal numero di database gestiti, dall'attività e dalla frequenza con cui viene eseguita un'attività.

## Portale di Azure

Il [portale di Azure](https://portal.azure.com) è un'applicazione basata su Web dove è possibile creare, aggiornare ed eliminare i database e i server logici e monitorare le attività del database. Si tratta dello strumento ideale se si sta appena iniziando a usare Azure, se si gestisce un numero ridotto di database o se è necessario operare rapidamente.

Per informazioni più dettagliate sull’utilizzo del portale, vedere [Gestione di database SQL tramite il portale di Azure classico](sql-database-manage-portal.md).

## SQL Server Management Studio e SQL Server Data Tools in Visual Studio

SQL Server Management Studio (SSMS) e SQL Server Data Tools (SSDT) in Visual Studio sono gli strumenti client che vengono eseguiti nel computer e consentono di connettersi, gestire e sviluppare il database nel cloud. È opportuno che gli sviluppatori di applicazioni con una certa familiarità con Visual Studio o altri ambienti di sviluppo integrato (IDE), [provino a usare SSDT in Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). Molti amministratori di database hanno familiarità con SSMS, che può essere usato con i database SQL di Azure. [Scaricare la versione più recente di SSMS](https://msdn.microsoft.com/library/mt238290) e usare sempre la versione più recente quando si lavora con database SQL di Azure. Per altre informazioni sulla gestione dei database SQL di Azure con SSMS, vedere [Gestire database SQL tramite SSMS](sql-database-manage-azure-ssms.md).

> [AZURE.IMPORTANT] Usare sempre la versione più aggiornata di SQL Server Management Studio e SQL Server Data Tools per Visual Studio per restare sincronizzati con gli aggiornamenti di Microsoft Azure e del database SQL. Le versioni precedenti di questi strumenti non funzionano correttamente con il database SQL.

## Strumenti da riga di comando

È possibile utilizzare strumenti da riga di comando come PowerShell per gestire database e pool di database elastici e automatizzare le distribuzioni di risorse Azure. Microsoft consiglia questo strumento per la gestione di un numero elevato di database e automatizzare la distribuzione delle modifiche delle risorse in un ambiente di produzione.

Per ulteriori informazioni sulla gestione dei database SQL di Azure con gli strumenti da riga di comando vedere[Gestione di Database SQL con PowerShell](sql-database-command-line-tools.md)

## Strumenti di database elastici
Usare gli strumenti di database elastico per eseguire azioni quali:

* Esecuzione di uno script T-SQL su un set di database usando un [processo elastico](sql-database-elastic-jobs-overview.md)
* Spostamento di database modello multi-tenant in un modello single-tenant con lo [strumento di divisione-unione](sql-database-elastic-scale-overview-split-and-merge.md)
* Gestione di database in un modello single-tenant o multi-tenant tramite la [libreria client della scalabilità elastica](sql-database-elastic-database-client-library.md).
 

<!---HONumber=AcomDC_0706_2016-->