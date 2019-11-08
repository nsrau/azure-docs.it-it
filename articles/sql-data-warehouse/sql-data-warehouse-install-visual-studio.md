---
title: Installare Visual Studio 2019
description: Installare Visual Studio e SQL Server Data Tools (SSDT) per Azure SQL Data Warehouse
services: sql-data-warehouse
ms.custom: seo-lt-2019
ms.workload: azure-vs
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 11/06/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 96d66e6190732d88ba9a33094b6c27bbadfd4aae
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747798"
---
# <a name="getting-started-with-visual-studio-2019-for-sql-data-warehouse"></a>Introduzione a Visual Studio 2019 per SQL Data Warehouse
Visual Studio **2019** SQL Server Data Tools (SSDT) è un singolo strumento che consente di eseguire le operazioni seguenti:

- Connessione, query e sviluppo di applicazioni per SQL Data Warehouse 
- Utilizzare Esplora oggetti per esplorare visivamente tutti gli oggetti nel modello di dati, tra cui tabelle, viste, stored procedure e così via.
- Genera script T-SQL Data Definition Language (DDL) per gli oggetti
- Sviluppare il data warehouse usando un approccio basato sullo stato con i progetti di database SSDT
- Integrare il progetto di database con sistemi di controllo del codice sorgente come Git con Azure Repos
- Configurare pipeline di integrazione e distribuzione continue con server di automazione come Azure DevOps

## <a name="install-visual-studio-2019"></a>Installare Visual Studio 2019
Vedere [Scarica Visual Studio 2019][] per scaricare e installare visual Studio **16,3 e versioni successive**. Durante l'installazione, selezionare il carico di lavoro elaborazione ed archiviazione dati. L'installazione autonoma di SSDT non è più necessaria in Visual Studio 2019.

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
