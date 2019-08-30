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
ms.date: 08/15/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: e08d0a52903fb488359356707ecedfd81d49084a
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70165077"
---
# <a name="getting-started-with-visual-studio-2019-for-sql-data-warehouse"></a>Introduzione a Visual Studio 2019 per SQL Data Warehouse
Visual Studio **2019** SQL Server Data Tools (SSDT) è un singolo strumento che consente di eseguire le operazioni seguenti:

- Connessione, query e sviluppo di applicazioni per SQL Data Warehouse 
- Utilizzare Esplora oggetti per esplorare visivamente tutti gli oggetti nel modello di dati, tra cui tabelle, viste, stored procedure e così via.
- Genera script T-SQL Data Definition Language (DDL) per gli oggetti
- Sviluppare il data warehouse usando un approccio basato sullo stato con i progetti di database SSDT
- Integrare il progetto di database con sistemi di controllo del codice sorgente come Git con Azure DevOps repository
- Configurare pipeline di distribuzione e integrazione continua con server di automazione come Azure DevOps [presto disponibile]

> [!NOTE]
> Attualmente i progetti di database di Visual Studio SSDT sono disponibili in anteprima. Per ricevere aggiornamenti periodici su questa funzionalità, votare in [UserVoice].

## <a name="install-visual-studio-2019-preview"></a>Installare Visual Studio 2019 Preview
Vedere [Scarica Visual Studio 2019 Preview][] per scaricare e installare Visual Studio. Durante l'installazione, selezionare il carico di lavoro elaborazione ed archiviazione dati. L'installazione autonoma di SSDT non è più necessaria in Visual Studio 2019.

## <a name="reporting-issues-with-ssdt-visual-studio-2019-preview"></a>Segnalazione di problemi con SSDT Visual Studio 2019 (anteprima)

Per segnalare problemi quando si usa SSDT con SQL Data Warehouse, inviare tramite posta elettronica la seguente lista di distribuzione di posta elettronica:<sqldwssdtpreview@service.microsoft.com>

## <a name="next-steps"></a>Passaggi successivi

Ora che è disponibile la versione più recente di SSDT, è possibile [connettersi][connect] al SQL data warehouse.

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[connect]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[Scarica Visual Studio 2019 Preview]: https://visualstudio.microsoft.com/vs/preview/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
