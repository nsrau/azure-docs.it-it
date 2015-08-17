<properties 
	pageTitle="Panoramica: strumenti di gestione per database SQL" 
	description="Confronta strumenti e le opzioni per la gestione di database SQL di Azure" 
	services="sql-database" 
	documentationCenter="" 
	authors="TigerMint" 
	manager="" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/15/2015" 
	ms.author="vinsonyu"/>

# Panoramica: strumenti di gestione per database SQL

In questo argomento vengono analizzati e confrontati strumenti e opzioni per la gestione di database SQL in modo da selezionare lo strumento appropriato per il processo, l'azienda e l’utente. La scelta dello strumento appropriato dipende dal numero di database gestiti, dall'attività e dalla frequenza con cui viene eseguita un'attività.



## Portale di Azure


Il [Portale di Azure](http://portal.azure.com) è un portale di gestione basato su Web dove è possibile creare, aggiornare ed eliminare i database SQL di Azure e i server logici e monitorare le risorse del database. Si tratta dello strumento ideale se si sta appena iniziando a utilizzare Azure, se si gestisce un numero ridotto di database, o se è necessario operare rapidamente.

Per informazioni più dettagliate sull’utilizzo del portale, vedere [Gestione di database SQL tramite il portale di Azure](sql-database-manage-portal.md).

## SQL Server Management Studio e SQL Server Data Tools in Visual Studio


SQL Server Management Studio (SSMS) e SQL Server Data Tools (SSDT) in Visual Studio sono gli strumenti client che vengono eseguiti nel computer e consentono di connettersi, gestire e sviluppare il database nel cloud. È opportuno che gli sviluppatori di applicazioni con una certa familiarità con Visual Studio o altri ambienti di sviluppo integrato (IDE), provino a utilizzare SSDT in Visual Studio. Se è necessario disporre di funzionalità avanzate di SQL che non sono già presenti in SSDT, ad esempio la gestione di database SQL Server in ambienti ibridi, utilizzare SSMS.

Per ulteriori informazioni sulla gestione di database SQL di Azure con SSMS e SSDT, vedere [Gestire database SQL tramite SSMS](sql-database-manage-azure-ssms.md)


## Strumenti da riga di comando

È possibile utilizzare strumenti da riga di comando come PowerShell per gestire database SQL di Azure e automatizzare le distribuzioni di risorse Azure. Microsoft consiglia questo strumento per la gestione di un numero elevato di database SQL di Azure e la distribuzione delle modifiche delle risorse in un ambiente di produzione.

Per ulteriori informazioni sulla gestione dei database SQL di Azure con gli strumenti da riga di comando [fare clic qui](sql-database-command-line-tools.md)
 

<!---HONumber=August15_HO6-->