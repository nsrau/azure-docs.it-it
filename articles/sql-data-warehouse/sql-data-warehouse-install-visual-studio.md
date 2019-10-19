---
title: Installare Visual Studio 2019 per SQL Data Warehouse | Microsoft Docs
description: Installare Visual Studio e SQL Server Data Tools (SSDT) per Azure SQL Data Warehouse
services: sql-data-warehouse
ms.custom: vs-azure
ms.workload: azure-vs
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 10/17/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 81c709e7705e16484438ab684a6b1591e5e624ba
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72553506"
---
# <a name="getting-started-with-visual-studio-2019-for-sql-data-warehouse"></a>Introduzione a Visual Studio 2019 per SQL Data Warehouse
Visual Studio **2019** SQL Server Data Tools (SSDT) è un singolo strumento che consente di eseguire le operazioni seguenti:

- Connessione, query e sviluppo di applicazioni per SQL Data Warehouse 
- Utilizzare Esplora oggetti per esplorare visivamente tutti gli oggetti nel modello di dati, tra cui tabelle, viste, stored procedure e così via.
- Genera script T-SQL Data Definition Language (DDL) per gli oggetti
- Sviluppare il data warehouse usando un approccio basato sullo stato con i progetti di database SSDT
- Integrare il progetto di database con sistemi di controllo del codice sorgente come Git con Azure DevOps repository
- Configurare pipeline di integrazione e distribuzione continue con server di automazione come Azure DevOps

> [!NOTE]
> Attualmente i progetti di database di Visual Studio SSDT sono disponibili in anteprima. Per ricevere aggiornamenti periodici su questa funzionalità, votare in [UserVoice].

## <a name="install-visual-studio-2019-preview"></a>Installare Visual Studio 2019 Preview
Vedere [Scarica Visual Studio 2019][] per scaricare e installare visual Studio **16,3 e versioni successive**. Durante l'installazione, selezionare il carico di lavoro elaborazione ed archiviazione dati. L'installazione autonoma di SSDT non è più necessaria in Visual Studio 2019.

## <a name="reporting-issues-with-ssdt-visual-studio-2019-preview"></a>Segnalazione di problemi con SSDT Visual Studio 2019 (anteprima)
Per segnalare problemi quando si usa SSDT con SQL Data Warehouse, inviare tramite posta elettronica la seguente lista di distribuzione di posta elettronica: <sqldwssdtpreview@service.microsoft.com>

## <a name="next-steps"></a>Passaggi successivi
Ora che è disponibile la versione più recente di SSDT, è possibile [connettersi][connect] al SQL data warehouse.

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[connect]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[Scarica Visual Studio 2019]: https://visualstudio.microsoft.com/downloads/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
