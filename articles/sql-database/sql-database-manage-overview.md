---
title: Strumenti per la gestione e lo sviluppo con il database SQL di Azure | Microsoft Docs
description: Introduce le opzioni e gli strumenti per la gestione e lo sviluppo per il database SQL di Azure
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 37767380-975f-4dee-a28d-80bc2036dda3
ms.service: sql-database
ms.custom: monitor & manage
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/03/2017
ms.author: carlrab
ms.translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: 9b0a3a314e576db2133e5c63fada43bb11a4e520
ms.contentlocale: it-it
ms.lasthandoff: 03/04/2017


---
# <a name="overview-tools-to-manage--develop-with-azure-sql-database"></a>Panoramica: Strumenti per la gestione e lo sviluppo con il database SQL di Azure
In questo argomento vengono descritti gli strumenti per la gestione e lo sviluppo con i database SQL di Azure.

> [!IMPORTANT]
> Questo insieme di documentazione include guide introduttive, esempi e procedure che mostrano come eseguire operazioni di gestione e sviluppo con il database SQL di Azure tramite gli strumenti presentati nei paragrafi seguenti. Usare il riquadro di spostamento e la casella di filtro a sinistra per trovare contenuti specifici per il Portale di Azure, PowerShell e T-SQL.
>

## <a name="azure-portal"></a>Portale di Azure
Il [portale di Azure](https://portal.azure.com) è un'applicazione basata su Web dove è possibile creare, aggiornare ed eliminare i database e i server logici e monitorare le attività del database. Si tratta dello strumento ideale se si sta appena iniziando a usare Azure, se si gestiscono pochi database o se è necessario operare rapidamente.

## <a name="sql-server-management-studio-and-transact-sql"></a>SQL Server Management Studio e Transact-SQL
SQL Server Management Studio (SSMS) è uno strumento client che viene eseguito nel computer in uso per gestire il database nel cloud tramite Transact-SQL. Molti amministratori di database hanno familiarità con SSMS, che può essere usato con i database SQL di Azure. [Scaricare la versione più recente di SSMS](https://msdn.microsoft.com/library/mt238290) e usare sempre la versione più recente quando si lavora con database SQL di Azure. 

> [!IMPORTANT]
> Usare sempre la versione più recente di [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290).
>  

## <a name="sql-server-data-tools-in-visual-studio"></a>SQL Server Data Tools in Visual Studio
SQL Server Data Tools (SSDT) è uno strumento client eseguito sul computer per sviluppare il database nel cloud. È opportuno che gli sviluppatori di applicazioni con una certa familiarità con Visual Studio o altri ambienti di sviluppo integrato (IDE), [provino a usare SSDT in Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx).  

> [!IMPORTANT]
> Usare sempre la versione più aggiornata di [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) per restare al passo con gli aggiornamenti di Microsoft Azure e del database SQL.
>  
## <a name="powershell"></a>PowerShell
È possibile usare PowerShell per gestire database e pool elastici e automatizzare le distribuzioni di risorse Azure. Microsoft consiglia questo strumento per la gestione di un numero elevato di database e automatizzare la distribuzione delle modifiche delle risorse in un ambiente di produzione.

## <a name="elastic-database-tools"></a>Strumenti di database elastici
Usare gli strumenti di database elastico per eseguire azioni quali: 

* Esecuzione di uno script T-SQL su un set di database usando un [processo elastico](sql-database-elastic-jobs-overview.md)
* Spostamento di database modello multi-tenant in un modello single-tenant con lo [strumento di divisione-unione](sql-database-elastic-scale-overview-split-and-merge.md)
* Gestione di database in un modello single-tenant o multi-tenant tramite la [libreria client della scalabilità elastica](sql-database-elastic-database-client-library.md).

## <a name="additional-resources"></a>Risorse aggiuntive
* [Gestione risorse di Azure](https://azure.microsoft.com/features/resource-manager/)
* [Automazione di Azure](https://azure.microsoft.com/documentation/services/automation/)
* [Utilità di pianificazione di Azure](https://azure.microsoft.com/documentation/services/scheduler/)


