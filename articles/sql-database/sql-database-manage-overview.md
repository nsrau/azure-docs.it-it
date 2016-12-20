---
title: 'Panoramica: strumenti di gestione per database SQL | Documentazione Microsoft'
description: Confronta strumenti e le opzioni per la gestione di database SQL di Azure
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 37767380-975f-4dee-a28d-80bc2036dda3
ms.service: sql-database
ms.custom: overview
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 5adb83a7b8e19ca78dd4d837b497e1ceb4444f86


---
# <a name="overview-management-tools-for-sql-database"></a>Panoramica: strumenti di gestione per database SQL
Questo argomento analizza e mette a confronto strumenti e opzioni per la gestione di database SQL di Azure in modo da selezionare lo strumento appropriato per il processo, l'azienda e l'utente. La scelta dello strumento appropriato dipende dal numero di database gestiti, dall'attività e dalla frequenza con cui viene eseguita un'attività.

## <a name="azure-portal"></a>Portale di Azure
Il [portale di Azure](https://portal.azure.com) è un'applicazione basata su Web dove è possibile creare, aggiornare ed eliminare i database e i server logici e monitorare le attività del database. Si tratta dello strumento ideale se si sta appena iniziando a usare Azure, se si gestiscono pochi database o se è necessario operare rapidamente.

Per altre informazioni sull’uso del portale, vedere [Gestione di database SQL tramite il portale di Azure](sql-database-manage-portal.md).

## <a name="sql-server-management-studio-and-sql-server-data-tools-in-visual-studio"></a>SQL Server Management Studio e SQL Server Data Tools in Visual Studio
SQL Server Management Studio (SSMS) e SQL Server Data Tools (SSDT) sono gli strumenti client che vengono eseguiti sul computer per gestire e sviluppare il database nel cloud. È opportuno che gli sviluppatori di applicazioni con una certa familiarità con Visual Studio o altri ambienti di sviluppo integrato (IDE), [provino a usare SSDT in Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). Molti amministratori di database hanno familiarità con SSMS, che può essere usato con i database SQL di Azure. [Scaricare la versione più recente di SSMS](https://msdn.microsoft.com/library/mt238290) e usare sempre la versione più recente quando si lavora con database SQL di Azure. Per altre informazioni sulla gestione dei database SQL di Azure con SSMS, vedere [Gestire database SQL tramite SSMS](sql-database-manage-azure-ssms.md).

> [!IMPORTANT]
> Usare sempre la versione più aggiornata di [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290) e [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) per restare sincronizzati con gli aggiornamenti di Microsoft Azure e del database SQL.
>  

## <a name="powershell"></a>PowerShell
È possibile usare PowerShell per gestire database e pool di database elastici e automatizzare le distribuzioni di risorse Azure. Microsoft consiglia questo strumento per la gestione di un numero elevato di database e automatizzare la distribuzione delle modifiche delle risorse in un ambiente di produzione.

Per altre informazioni, vedere [Gestire il database SQL con PowerShell](sql-database-manage-powershell.md)

## <a name="elastic-database-tools"></a>Strumenti di database elastici
Usare gli strumenti di database elastico per eseguire azioni quali: 

* Esecuzione di uno script T-SQL su un set di database usando un [processo elastico](sql-database-elastic-jobs-overview.md)
* Spostamento di database modello multi-tenant in un modello single-tenant con lo [strumento di divisione-unione](sql-database-elastic-scale-overview-split-and-merge.md)
* Gestione di database in un modello single-tenant o multi-tenant tramite la [libreria client della scalabilità elastica](sql-database-elastic-database-client-library.md).

## <a name="additional-resources"></a>Risorse aggiuntive
* [Gestione risorse di Azure](https://azure.microsoft.com/features/resource-manager/)
* [Automazione di Azure](https://azure.microsoft.com/documentation/services/automation/)
* [Utilità di pianificazione di Azure](https://azure.microsoft.com/documentation/services/scheduler/)




<!--HONumber=Nov16_HO3-->


